---
title: Filtry v ASP.NET Core
author: Rick-Anderson
description: Přečtěte si, jak filtry fungují a jak je používat v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 11d0c514dd15e787224510991ffb81680c9fc479
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019339"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="2948e-103">Filtry v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2948e-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2948e-104">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2948e-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2948e-105">*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="2948e-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="2948e-106">Předdefinované filtry zpracovávají úlohy, jako například:</span><span class="sxs-lookup"><span data-stu-id="2948e-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="2948e-107">Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).</span><span class="sxs-lookup"><span data-stu-id="2948e-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="2948e-108">Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="2948e-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="2948e-109">Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek.</span><span class="sxs-lookup"><span data-stu-id="2948e-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="2948e-110">Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="2948e-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="2948e-111">Filtry zabraňují duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="2948e-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="2948e-112">Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="2948e-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="2948e-113">Tento dokument se týká Razor stránek, řadičů rozhraní API a řadičů se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="2948e-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="2948e-114">Filtry nefungují přímo s [ Razor komponentami](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="2948e-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="2948e-115">Filtr může nepřímo ovlivnit komponentu pouze v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="2948e-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="2948e-116">Komponenta je vložena do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="2948e-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="2948e-117">Stránka nebo kontroler nebo zobrazení používá filtr.</span><span class="sxs-lookup"><span data-stu-id="2948e-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="2948e-118">[Zobrazit nebo Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2948e-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="2948e-119">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="2948e-119">How filters work</span></span>

<span data-ttu-id="2948e-120">Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.</span><span class="sxs-lookup"><span data-stu-id="2948e-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="2948e-121">Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="2948e-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="2948e-124">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="2948e-124">Filter types</span></span>

<span data-ttu-id="2948e-125">Každý typ filtru se spustí v jiné fázi kanálu filtru:</span><span class="sxs-lookup"><span data-stu-id="2948e-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="2948e-126">[Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="2948e-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="2948e-127">Filtry autorizace pro krátkodobé vynechání kanálu, pokud žádost není autorizována.</span><span class="sxs-lookup"><span data-stu-id="2948e-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="2948e-128">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="2948e-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="2948e-129">Spusťte po autorizaci.</span><span class="sxs-lookup"><span data-stu-id="2948e-129">Run after authorization.</span></span>  
  * <span data-ttu-id="2948e-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>spustí kód před zbytek kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="2948e-131">Například `OnResourceExecuting` spustí kód před vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="2948e-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="2948e-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>spustí kód po dokončení zbývající části kanálu.</span><span class="sxs-lookup"><span data-stu-id="2948e-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="2948e-133">[Filtry akcí](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="2948e-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="2948e-134">Spustit kód bezprostředně před a po volání metody Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="2948e-135">Může změnit argumenty předané do akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="2948e-136">Může změnit výsledek vrácený z akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="2948e-137">Nejsou **na** stránkách podporovány Razor .</span><span class="sxs-lookup"><span data-stu-id="2948e-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="2948e-138">[Filtry výjimek](#exception-filters) aplikují globální zásady na neošetřené výjimky, ke kterým došlo před zápisem textu odpovědi do.</span><span class="sxs-lookup"><span data-stu-id="2948e-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="2948e-139">[Filtry výsledků](#result-filters) spouštějí kód bezprostředně před a po provedení výsledků akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="2948e-140">Spustí se pouze v případě, že metoda akce byla úspěšně provedena.</span><span class="sxs-lookup"><span data-stu-id="2948e-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="2948e-141">Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="2948e-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="2948e-142">Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="2948e-145">Implementace</span><span class="sxs-lookup"><span data-stu-id="2948e-145">Implementation</span></span>

<span data-ttu-id="2948e-146">Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2948e-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="2948e-147">Synchronní filtry spouštějí kód před a po fázi zřetězení.</span><span class="sxs-lookup"><span data-stu-id="2948e-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="2948e-148">Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> je volána před voláním metody Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="2948e-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>je volána po návratu metody Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="2948e-150">V předchozím kódu je [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) funkcí nástroje v [ukázkovém souboru ke stažení](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span><span class="sxs-lookup"><span data-stu-id="2948e-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="2948e-151">Asynchronní filtry definují `On-Stage-ExecutionAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="2948e-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="2948e-152">Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> :</span><span class="sxs-lookup"><span data-stu-id="2948e-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="2948e-153">V předchozím kódu `SampleAsyncActionFilter` obsahuje znak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), který provádí metodu Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="2948e-154">Několik fází filtru</span><span class="sxs-lookup"><span data-stu-id="2948e-154">Multiple filter stages</span></span>

<span data-ttu-id="2948e-155">Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="2948e-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="2948e-156">Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Třída implementuje:</span><span class="sxs-lookup"><span data-stu-id="2948e-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="2948e-157">Synchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> a<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="2948e-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="2948e-158">Asynchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> a<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="2948e-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="2948e-159">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="2948e-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="2948e-160">Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá.</span><span class="sxs-lookup"><span data-stu-id="2948e-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="2948e-161">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2948e-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="2948e-162">Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda.</span><span class="sxs-lookup"><span data-stu-id="2948e-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="2948e-163">Při použití abstraktních tříd <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , jako je přepsání pouze synchronních metod nebo asynchronní metody pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="2948e-164">Předdefinované atributy filtru</span><span class="sxs-lookup"><span data-stu-id="2948e-164">Built-in filter attributes</span></span>

<span data-ttu-id="2948e-165">ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené.</span><span class="sxs-lookup"><span data-stu-id="2948e-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="2948e-166">Například následující filtr výsledků přidá hlavičku do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="2948e-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="2948e-167">Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="2948e-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="2948e-168">Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu HLAVIČKY http:</span><span class="sxs-lookup"><span data-stu-id="2948e-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="2948e-169">K prohlédnutí hlaviček použijte nástroj, jako je například nástroj pro [vývojáře v prohlížeči](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) .</span><span class="sxs-lookup"><span data-stu-id="2948e-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="2948e-170">V části **hlavičky odpovědi** `author: Rick Anderson` se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="2948e-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="2948e-171">Následující kód implementuje `ActionFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="2948e-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="2948e-172">Přečte název a název z konfiguračního systému.</span><span class="sxs-lookup"><span data-stu-id="2948e-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="2948e-173">Na rozdíl od předchozí ukázky, následující kód nevyžaduje přidání parametrů filtru do kódu.</span><span class="sxs-lookup"><span data-stu-id="2948e-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="2948e-174">Přidá název a název do hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="2948e-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="2948e-175">Možnosti konfigurace se poskytují z [konfiguračního systému](xref:fundamentals/configuration/index) pomocí [vzoru možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="2948e-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="2948e-176">Například z *appsettings.js* souboru:</span><span class="sxs-lookup"><span data-stu-id="2948e-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="2948e-177">V `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2948e-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="2948e-178">`PositionOptions`Třída je přidána do kontejneru služby s `"Position"` oblastí konfigurace.</span><span class="sxs-lookup"><span data-stu-id="2948e-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="2948e-179">`MyActionFilterAttribute`Přidá se do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="2948e-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="2948e-180">Následující kód ukazuje `PositionOptions` třídu:</span><span class="sxs-lookup"><span data-stu-id="2948e-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="2948e-181">Následující kód platí `MyActionFilterAttribute` pro `Index2` metodu:</span><span class="sxs-lookup"><span data-stu-id="2948e-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="2948e-182">V části **hlavičky odpovědi**, `author: Rick Anderson` a `Editor: Joe Smith` se zobrazí při `Sample/Index2` volání koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="2948e-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="2948e-183">Následující kód platí `MyActionFilterAttribute` pro a na `AddHeaderAttribute` Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="2948e-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="2948e-184">Filtry nelze použít na Razor metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="2948e-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="2948e-185">Lze je použít buď na Razor model stránky, nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="2948e-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="2948e-186">Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="2948e-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="2948e-187">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="2948e-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="2948e-188">Filtrovat obory a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="2948e-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="2948e-189">Filtr lze přidat do kanálu v jednom ze tří *oborů*:</span><span class="sxs-lookup"><span data-stu-id="2948e-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="2948e-190">Použití atributu u akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2948e-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="2948e-191">Atributy filtru nelze použít na Razor stránky metod obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="2948e-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="2948e-192">Použití atributu na řadiči nebo na Razor stránce.</span><span class="sxs-lookup"><span data-stu-id="2948e-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="2948e-193">Globálně pro všechny řadiče, akce a Razor stránky, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="2948e-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="2948e-194">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="2948e-194">Default order of execution</span></span>

<span data-ttu-id="2948e-195">Pokud existuje více filtrů pro určitou fázi kanálu, rozsah Určuje výchozí pořadí provádění filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="2948e-196">Globální filtry obklopují filtry tříd, které jsou ve filtru metod Surround.</span><span class="sxs-lookup"><span data-stu-id="2948e-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="2948e-197">V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem.</span><span class="sxs-lookup"><span data-stu-id="2948e-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="2948e-198">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="2948e-198">The filter sequence:</span></span>

* <span data-ttu-id="2948e-199">*Před* kódem globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="2948e-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="2948e-200">*Před* kódem kontroleru a Razor filtru stránky.</span><span class="sxs-lookup"><span data-stu-id="2948e-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="2948e-201">Kód *před* filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="2948e-202">*Po* kódu filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="2948e-203">*Po* kódu filtru řadiče a Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="2948e-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="2948e-204">*Po* kódu globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="2948e-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="2948e-205">Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="2948e-206">Sequence</span><span class="sxs-lookup"><span data-stu-id="2948e-206">Sequence</span></span> | <span data-ttu-id="2948e-207">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="2948e-207">Filter scope</span></span> | <span data-ttu-id="2948e-208">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="2948e-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="2948e-209">1</span><span class="sxs-lookup"><span data-stu-id="2948e-209">1</span></span> | <span data-ttu-id="2948e-210">Globální</span><span class="sxs-lookup"><span data-stu-id="2948e-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2948e-211">2</span><span class="sxs-lookup"><span data-stu-id="2948e-211">2</span></span> | <span data-ttu-id="2948e-212">Kontroler nebo Razor Stránka</span><span class="sxs-lookup"><span data-stu-id="2948e-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="2948e-213">3</span><span class="sxs-lookup"><span data-stu-id="2948e-213">3</span></span> | <span data-ttu-id="2948e-214">Metoda</span><span class="sxs-lookup"><span data-stu-id="2948e-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2948e-215">4</span><span class="sxs-lookup"><span data-stu-id="2948e-215">4</span></span> | <span data-ttu-id="2948e-216">Metoda</span><span class="sxs-lookup"><span data-stu-id="2948e-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="2948e-217">5</span><span class="sxs-lookup"><span data-stu-id="2948e-217">5</span></span> | <span data-ttu-id="2948e-218">Kontroler nebo Razor Stránka</span><span class="sxs-lookup"><span data-stu-id="2948e-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="2948e-219">6</span><span class="sxs-lookup"><span data-stu-id="2948e-219">6</span></span> | <span data-ttu-id="2948e-220">Globální</span><span class="sxs-lookup"><span data-stu-id="2948e-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="2948e-221">Filtry na úrovni řadiče</span><span class="sxs-lookup"><span data-stu-id="2948e-221">Controller level filters</span></span>

<span data-ttu-id="2948e-222">Každý kontroler, který dědí ze <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="2948e-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="2948e-223">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="2948e-223">These methods:</span></span>

* <span data-ttu-id="2948e-224">Zabalte filtry, které se spouštějí pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="2948e-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="2948e-225">`OnActionExecuting`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="2948e-226">`OnActionExecuted`se volá po všech filtrech akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="2948e-227">`OnActionExecutionAsync`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="2948e-228">Kód v filtru po `next` spuštění po metodě Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="2948e-229">Například v ukázce ke stažení `MySampleActionFilter` se použije globálně při spuštění.</span><span class="sxs-lookup"><span data-stu-id="2948e-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="2948e-230">Pomocná rutina `TestController`:</span><span class="sxs-lookup"><span data-stu-id="2948e-230">The `TestController`:</span></span>

* <span data-ttu-id="2948e-231">Aplikuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) na `FilterTest2` akci.</span><span class="sxs-lookup"><span data-stu-id="2948e-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="2948e-232">Přepisuje `OnActionExecuting` a `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="2948e-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="2948e-233">Navigace pro `https://localhost:5001/Test2/FilterTest2` spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="2948e-233">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="2948e-234">Filtry na úrovni řadiče nastaví vlastnost [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="2948e-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="2948e-235">Filtry na úrovni řadiče **nelze nastavit** tak, aby běžely po filtrech použitých na metody.</span><span class="sxs-lookup"><span data-stu-id="2948e-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="2948e-236">Pořadí je vysvětleno v další části.</span><span class="sxs-lookup"><span data-stu-id="2948e-236">Order is explained in the next section.</span></span>

<span data-ttu-id="2948e-237">RazorInformace o stránkách naleznete v tématu [implementace Razor filtrů stránek pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="2948e-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="2948e-238">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="2948e-238">Overriding the default order</span></span>

<span data-ttu-id="2948e-239">Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="2948e-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="2948e-240">`IOrderedFilter`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před rozsahem, aby určila pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="2948e-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="2948e-241">Filtr s nižší `Order` hodnotou:</span><span class="sxs-lookup"><span data-stu-id="2948e-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="2948e-242">Spustí *před* kódem filtr s vyšší hodnotou `Order` .</span><span class="sxs-lookup"><span data-stu-id="2948e-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="2948e-243">Spustí *za* kódem za filtr s vyšší `Order` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="2948e-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="2948e-244">`Order`Vlastnost je nastavena s parametrem konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="2948e-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="2948e-245">Vezměte v úvahu dva filtry akcí v následujícím kontroleru:</span><span class="sxs-lookup"><span data-stu-id="2948e-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="2948e-246">Globální filtr je přidaný v `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2948e-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="2948e-247">Tři filtry jsou spouštěny v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="2948e-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="2948e-248">`Order`Vlastnost Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="2948e-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="2948e-249">Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor.</span><span class="sxs-lookup"><span data-stu-id="2948e-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="2948e-250">Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0.</span><span class="sxs-lookup"><span data-stu-id="2948e-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="2948e-251">Jak už bylo uvedeno výše, filtry na úrovni řadiče nastaví vlastnost [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` pro předdefinované filtry. Scope určí pořadí, pokud `Order` není nastavená na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2948e-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="2948e-252">V předchozím kódu `MySampleActionFilter` má globální rozsah, takže se spustí před `MyAction2FilterAttribute` , který má obor kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2948e-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="2948e-253">Aby bylo možné `MyAction2FilterAttribute` Spustit jako první, nastavte pořadí na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="2948e-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="2948e-254">Aby se globální filtr `MySampleActionFilter` spouštěl jako první, nastavte `Order` na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="2948e-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="2948e-255">Zrušení a zkrácení okruhů</span><span class="sxs-lookup"><span data-stu-id="2948e-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="2948e-256">Kanál filtru může být v krátkém okruhu nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnosti pro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr poskytnutý metodě Filter.</span><span class="sxs-lookup"><span data-stu-id="2948e-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="2948e-257">Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:</span><span class="sxs-lookup"><span data-stu-id="2948e-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="2948e-258">V následujícím kódu, `ShortCircuitingResourceFilter` a jako `AddHeader` cíl filtru i cílovou `SomeResource` metodu Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="2948e-259">Pomocná rutina `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="2948e-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="2948e-260">Nejprve se spustí, protože se jedná o filtr prostředků a `AddHeader` je filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="2948e-261">Krátké okruhy zbývajícího kanálu.</span><span class="sxs-lookup"><span data-stu-id="2948e-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="2948e-262">Proto `AddHeader` Filtr pro akci nikdy neběží `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="2948e-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="2948e-263">Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to za předpokladu, že byla `ShortCircuitingResourceFilter` spuštěna jako první.</span><span class="sxs-lookup"><span data-stu-id="2948e-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="2948e-264">`ShortCircuitingResourceFilter`Nejprve se spustí z důvodu jeho typu filtru nebo explicitního použití `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="2948e-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="2948e-265">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="2948e-265">Dependency injection</span></span>

<span data-ttu-id="2948e-266">Filtry lze přidat podle typu nebo podle instance.</span><span class="sxs-lookup"><span data-stu-id="2948e-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="2948e-267">Pokud je přidána instance, bude tato instance použita pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="2948e-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="2948e-268">Pokud je přidán typ, je aktivován typ.</span><span class="sxs-lookup"><span data-stu-id="2948e-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="2948e-269">Filtr aktivovaný typu znamená:</span><span class="sxs-lookup"><span data-stu-id="2948e-269">A type-activated filter means:</span></span>

* <span data-ttu-id="2948e-270">Instance se vytvoří pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="2948e-270">An instance is created for each request.</span></span>
* <span data-ttu-id="2948e-271">Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="2948e-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="2948e-272">Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="2948e-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="2948e-273">Pomocí DI nelze poskytnout závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="2948e-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="2948e-274">V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány.</span><span class="sxs-lookup"><span data-stu-id="2948e-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="2948e-275">Toto je omezení způsobu fungování atributů.</span><span class="sxs-lookup"><span data-stu-id="2948e-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="2948e-276">Následující filtry podporují závislosti konstruktoru poskytované od DI:</span><span class="sxs-lookup"><span data-stu-id="2948e-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="2948e-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementováno u atributu.</span><span class="sxs-lookup"><span data-stu-id="2948e-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="2948e-278">Předchozí filtry lze použít pro metodu kontroleru nebo akce:</span><span class="sxs-lookup"><span data-stu-id="2948e-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="2948e-279">Protokolovací nástroje jsou k dispozici z DI.</span><span class="sxs-lookup"><span data-stu-id="2948e-279">Loggers are available from DI.</span></span> <span data-ttu-id="2948e-280">Ale nevytvářejte a používejte filtry čistě pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="2948e-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="2948e-281">[Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="2948e-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="2948e-282">Protokolování přidáno do filtrů:</span><span class="sxs-lookup"><span data-stu-id="2948e-282">Logging added to filters:</span></span>

* <span data-ttu-id="2948e-283">Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="2948e-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="2948e-284">Neměli **byste** protokolovat akce nebo jiné události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2948e-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="2948e-285">Vestavěné filtry protokolují akce a události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2948e-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="2948e-286">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="2948e-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="2948e-287">Typy implementace filtru služby jsou zaregistrované v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2948e-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="2948e-288"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Načte instanci filtru z di.</span><span class="sxs-lookup"><span data-stu-id="2948e-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="2948e-289">Následující kód ukazuje `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="2948e-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="2948e-290">V následujícím kódu `AddHeaderResultServiceFilter` je přidán do kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="2948e-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="2948e-291">V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="2948e-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="2948e-292">Při použití `ServiceFilterAttribute` nastavte [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="2948e-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="2948e-293">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="2948e-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="2948e-294">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="2948e-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="2948e-295">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="2948e-296">Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.</span><span class="sxs-lookup"><span data-stu-id="2948e-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="2948e-297">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="2948e-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="2948e-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="2948e-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="2948e-299">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="2948e-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="2948e-300">`CreateInstance`Načte zadaný typ z DI.</span><span class="sxs-lookup"><span data-stu-id="2948e-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="2948e-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="2948e-301">TypeFilterAttribute</span></span>

<span data-ttu-id="2948e-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jeho typ není vyřešen přímo z kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="2948e-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="2948e-303">Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="2948e-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="2948e-304">Protože `TypeFilterAttribute` typy nejsou vyřešeny přímo z kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="2948e-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="2948e-305">Typy, na které se odkazuje pomocí, `TypeFilterAttribute` není nutné registrovat v kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="2948e-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="2948e-306">Mají své závislosti splněné kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="2948e-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="2948e-307">`TypeFilterAttribute`může volitelně přijmout argumenty konstruktoru pro typ.</span><span class="sxs-lookup"><span data-stu-id="2948e-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="2948e-308">Při použití `TypeFilterAttribute` nastavte [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="2948e-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="2948e-309">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="2948e-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="2948e-310">Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="2948e-311">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="2948e-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="2948e-312">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="2948e-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="2948e-313">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="2948e-313">Authorization filters</span></span>

<span data-ttu-id="2948e-314">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="2948e-314">Authorization filters:</span></span>

* <span data-ttu-id="2948e-315">Jsou první filtry spouštěny v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="2948e-316">Řízení přístupu k metodám akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-316">Control access to action methods.</span></span>
* <span data-ttu-id="2948e-317">Před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="2948e-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="2948e-318">Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2948e-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="2948e-319">Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="2948e-320">Vestavěný autorizační filtr:</span><span class="sxs-lookup"><span data-stu-id="2948e-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="2948e-321">Volá autorizační systém.</span><span class="sxs-lookup"><span data-stu-id="2948e-321">Calls the authorization system.</span></span>
* <span data-ttu-id="2948e-322">Neautorizuje požadavky.</span><span class="sxs-lookup"><span data-stu-id="2948e-322">Does not authorize requests.</span></span>

<span data-ttu-id="2948e-323">Negenerovat **výjimky** v rámci autorizačních filtrů:</span><span class="sxs-lookup"><span data-stu-id="2948e-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="2948e-324">Výjimka nebude zpracována.</span><span class="sxs-lookup"><span data-stu-id="2948e-324">The exception will not be handled.</span></span>
* <span data-ttu-id="2948e-325">Filtry výjimek nebudou zpracovávat výjimku.</span><span class="sxs-lookup"><span data-stu-id="2948e-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="2948e-326">Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="2948e-327">Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="2948e-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="2948e-328">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="2948e-328">Resource filters</span></span>

<span data-ttu-id="2948e-329">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="2948e-329">Resource filters:</span></span>

* <span data-ttu-id="2948e-330">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="2948e-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="2948e-331">Provádění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="2948e-332">Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .</span><span class="sxs-lookup"><span data-stu-id="2948e-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="2948e-333">Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu.</span><span class="sxs-lookup"><span data-stu-id="2948e-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="2948e-334">Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="2948e-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="2948e-335">Příklady filtru prostředků:</span><span class="sxs-lookup"><span data-stu-id="2948e-335">Resource filter examples:</span></span>

* <span data-ttu-id="2948e-336">[Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.</span><span class="sxs-lookup"><span data-stu-id="2948e-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="2948e-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="2948e-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="2948e-338">Zabraňuje vazbě modelu v přístupu k datům formuláře.</span><span class="sxs-lookup"><span data-stu-id="2948e-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="2948e-339">Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.</span><span class="sxs-lookup"><span data-stu-id="2948e-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="2948e-340">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="2948e-340">Action filters</span></span>

<span data-ttu-id="2948e-341">Filtry **akcí se nevztahují** na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="2948e-341">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="2948e-342">RazorStránky podporují <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="2948e-342">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="2948e-343">Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="2948e-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="2948e-344">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="2948e-344">Action filters:</span></span>

* <span data-ttu-id="2948e-345">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="2948e-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="2948e-346">Jejich spuštění obklopuje provádění metod akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="2948e-347">Následující kód ukazuje filtr vzorové akce:</span><span class="sxs-lookup"><span data-stu-id="2948e-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="2948e-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2948e-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="2948e-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>– povolí čtení vstupů do metody Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="2948e-350"><xref:Microsoft.AspNetCore.Mvc.Controller>– povolí manipulaci s instancí kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2948e-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="2948e-351"><xref:System.Web.Mvc.ActionExecutingContext.Result>– nastavení `Result` krátkodobého provádění metody akce a následných filtrů akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="2948e-352">Vyvolání výjimky v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="2948e-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="2948e-353">Zabraňuje spuštění dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="2948e-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="2948e-354">Na rozdíl od nastavení `Result` se místo úspěšného výsledku považuje za selhání.</span><span class="sxs-lookup"><span data-stu-id="2948e-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="2948e-355"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Poskytuje `Controller` a `Result` plus následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2948e-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="2948e-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="2948e-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="2948e-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Hodnota není null, pokud akce nebo dříve vyvolala filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="2948e-358">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="2948e-358">Setting this property to null:</span></span>

  * <span data-ttu-id="2948e-359">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="2948e-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="2948e-360">`Result`je spuštěn, jako kdyby byl vrácen z metody Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="2948e-361">V případě `IAsyncActionFilter` volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="2948e-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="2948e-362">Provede všechny následné filtry akcí a metodu Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="2948e-363">Vrací objekt `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="2948e-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="2948e-364">Do krátkodobého okruhu, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> k instanci výsledku a Nevolejte `next` (a `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="2948e-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="2948e-365">Rozhraní poskytuje abstrakci <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="2948e-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="2948e-366">`OnActionExecuting`Filtr akcí lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="2948e-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="2948e-367">Ověří stav modelu.</span><span class="sxs-lookup"><span data-stu-id="2948e-367">Validate model state.</span></span>
* <span data-ttu-id="2948e-368">Pokud je stav neplatný, vrátí se chyba.</span><span class="sxs-lookup"><span data-stu-id="2948e-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="2948e-369">Řadiče s poznámkou s `[ApiController]` atributem automaticky ověří stav modelu a vrátí odpověď 400.</span><span class="sxs-lookup"><span data-stu-id="2948e-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="2948e-370">Další informace najdete v tématu [Automatické odpovědi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="2948e-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="2948e-371">`OnActionExecuted`Metoda se spustí za metodou akce:</span><span class="sxs-lookup"><span data-stu-id="2948e-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="2948e-372">A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="2948e-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="2948e-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>je nastaven na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="2948e-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="2948e-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="2948e-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="2948e-375">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="2948e-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="2948e-376">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="2948e-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="2948e-377">`ActionExecutedContext.Result`je spuštěn, jako kdyby byl vrácen normálně z metody Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="2948e-378">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="2948e-378">Exception filters</span></span>

<span data-ttu-id="2948e-379">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="2948e-379">Exception filters:</span></span>

* <span data-ttu-id="2948e-380">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="2948e-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="2948e-381">Dá se použít k implementaci běžných zásad zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="2948e-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="2948e-382">Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:</span><span class="sxs-lookup"><span data-stu-id="2948e-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="2948e-383">Následující kód testuje filtr výjimky:</span><span class="sxs-lookup"><span data-stu-id="2948e-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="2948e-384">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="2948e-384">Exception filters:</span></span>

* <span data-ttu-id="2948e-385">Nemusíte mít události před a po.</span><span class="sxs-lookup"><span data-stu-id="2948e-385">Don't have before and after events.</span></span>
* <span data-ttu-id="2948e-386">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="2948e-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="2948e-387">Zpracování neošetřených výjimek, ke kterým dochází při Razor vytváření stránky nebo kontroleru, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="2948e-388">**Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.</span><span class="sxs-lookup"><span data-stu-id="2948e-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="2948e-389">Chcete-li zpracovat výjimku, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost na `true` nebo zapište odpověď.</span><span class="sxs-lookup"><span data-stu-id="2948e-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="2948e-390">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="2948e-390">This stops propagation of the exception.</span></span> <span data-ttu-id="2948e-391">Filtr výjimek nemůže vypnout výjimku na "úspěch".</span><span class="sxs-lookup"><span data-stu-id="2948e-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="2948e-392">To může provést pouze filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-392">Only an action filter can do that.</span></span>

<span data-ttu-id="2948e-393">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="2948e-393">Exception filters:</span></span>

* <span data-ttu-id="2948e-394">Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="2948e-395">Nejsou tak flexibilní jako middleware při zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="2948e-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="2948e-396">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="2948e-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="2948e-397">Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána.</span><span class="sxs-lookup"><span data-stu-id="2948e-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="2948e-398">Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML.</span><span class="sxs-lookup"><span data-stu-id="2948e-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="2948e-399">Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.</span><span class="sxs-lookup"><span data-stu-id="2948e-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="2948e-400">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="2948e-400">Result filters</span></span>

<span data-ttu-id="2948e-401">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="2948e-401">Result filters:</span></span>

* <span data-ttu-id="2948e-402">Implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="2948e-402">Implement an interface:</span></span>
  * <span data-ttu-id="2948e-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="2948e-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="2948e-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="2948e-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="2948e-405">Jejich spuštění obklopuje provádění výsledků akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="2948e-406">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="2948e-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="2948e-407">Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="2948e-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="2948e-408">Typ výsledku, který se má provést, závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="2948e-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="2948e-409">Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást prováděné <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="2948e-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="2948e-410">Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku.</span><span class="sxs-lookup"><span data-stu-id="2948e-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="2948e-411">Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="2948e-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="2948e-412">Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="2948e-413">Filtry výsledků nejsou provedeny v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="2948e-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="2948e-414">Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.</span><span class="sxs-lookup"><span data-stu-id="2948e-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="2948e-415">Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="2948e-416"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda může zkrátit provádění výsledků akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true` .</span><span class="sxs-lookup"><span data-stu-id="2948e-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="2948e-417">Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="2948e-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="2948e-418">Vyvolání výjimky v `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="2948e-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="2948e-419">Zabraňuje spuštění výsledků akce a následných filtrů.</span><span class="sxs-lookup"><span data-stu-id="2948e-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="2948e-420">Se považuje za selhání namísto úspěšného výsledku.</span><span class="sxs-lookup"><span data-stu-id="2948e-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="2948e-421">Při <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> spuštění metody je odpověď pravděpodobně již odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="2948e-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="2948e-422">Pokud byla odpověď již odeslána klientovi, nelze ji změnit.</span><span class="sxs-lookup"><span data-stu-id="2948e-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="2948e-423">`ResultExecutedContext.Canceled`je nastaven na hodnotu, `true` Pokud je spuštění výsledku akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="2948e-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="2948e-424">`ResultExecutedContext.Exception`je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku.</span><span class="sxs-lookup"><span data-stu-id="2948e-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="2948e-425">Nastavení `Exception` na hodnotu null efektivně zpracuje výjimku a zabrání vyvolání výjimky později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="2948e-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="2948e-426">Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="2948e-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="2948e-427">Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.</span><span class="sxs-lookup"><span data-stu-id="2948e-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="2948e-428">V případě <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> volání `await next` na se <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné filtry výsledků a výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="2948e-429">Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a Nevolejte na `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="2948e-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="2948e-430">Rozhraní poskytuje abstrakci `ResultFilterAttribute` , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="2948e-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="2948e-431">Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="2948e-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="2948e-432">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="2948e-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="2948e-433"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Rozhraní a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklaruje <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která je spuštěna pro všechny výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="2948e-434">To zahrnuje výsledky akcí, které vytvořil:</span><span class="sxs-lookup"><span data-stu-id="2948e-434">This includes action results produced by:</span></span>

* <span data-ttu-id="2948e-435">Filtry autorizace a filtry prostředků, které mají krátký okruh.</span><span class="sxs-lookup"><span data-stu-id="2948e-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="2948e-436">Filtry výjimek.</span><span class="sxs-lookup"><span data-stu-id="2948e-436">Exception filters.</span></span>

<span data-ttu-id="2948e-437">Například následující filtr vždy spustí a nastaví výsledek akce ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="2948e-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="2948e-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="2948e-438">IFilterFactory</span></span>

<span data-ttu-id="2948e-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="2948e-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="2948e-440">Proto `IFilterFactory` může být instance použita jako `IFilterMetadata` instance kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="2948e-441">Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="2948e-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="2948e-442">Pokud je toto přetypování úspěšné, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> je volána metoda pro vytvoření `IFilterMetadata` instance, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="2948e-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="2948e-443">To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="2948e-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="2948e-444">`IFilterFactory`může být implementováno pomocí implementace vlastního atributu jako jiný přístup k vytváření filtrů:</span><span class="sxs-lookup"><span data-stu-id="2948e-444">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="2948e-445">Filtr je použit v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="2948e-445">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="2948e-446">Otestujte předchozí kód spuštěním [ukázky stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="2948e-446">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="2948e-447">Vyvolejte vývojářské nástroje F12.</span><span class="sxs-lookup"><span data-stu-id="2948e-447">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="2948e-448">Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="2948e-448">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="2948e-449">Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:</span><span class="sxs-lookup"><span data-stu-id="2948e-449">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="2948e-450">**Autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="2948e-450">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="2948e-451">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="2948e-451">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="2948e-452">**interní:**`My header`</span><span class="sxs-lookup"><span data-stu-id="2948e-452">**internal:** `My header`</span></span>

<span data-ttu-id="2948e-453">Předchozí kód vytvoří hlavičku **interního:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="2948e-453">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="2948e-454">IFilterFactory implementované u atributu</span><span class="sxs-lookup"><span data-stu-id="2948e-454">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="2948e-455">Filtry, které implementují, `IFilterFactory` jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="2948e-455">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="2948e-456">Nevyžadovat předávání parametrů</span><span class="sxs-lookup"><span data-stu-id="2948e-456">Don't require passing parameters.</span></span>
* <span data-ttu-id="2948e-457">Musí mít závislosti konstruktoru, které musí vyplnit DI.</span><span class="sxs-lookup"><span data-stu-id="2948e-457">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="2948e-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="2948e-458"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="2948e-459">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="2948e-459">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="2948e-460">`CreateInstance`Načte zadaný typ z kontejneru Services (DI).</span><span class="sxs-lookup"><span data-stu-id="2948e-460">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="2948e-461">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="2948e-461">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="2948e-462">V předchozím kódu upravení metoda s jako `[SampleActionFilter]` upřednostňovaným přístupem k použití `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="2948e-462">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="2948e-463">Použití middlewaru v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="2948e-463">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="2948e-464">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="2948e-464">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="2948e-465">Filtry se ale liší od middlewaru v tom, že jsou součástí modulu runtime, což znamená, že mají přístup k kontextu a sestavám.</span><span class="sxs-lookup"><span data-stu-id="2948e-465">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="2948e-466">Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-466">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="2948e-467">Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:</span><span class="sxs-lookup"><span data-stu-id="2948e-467">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="2948e-468">Pomocí <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Spusťte middleware:</span><span class="sxs-lookup"><span data-stu-id="2948e-468">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="2948e-469">Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="2948e-469">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="2948e-470">Další akce</span><span class="sxs-lookup"><span data-stu-id="2948e-470">Next actions</span></span>

* <span data-ttu-id="2948e-471">Viz [metody filtru pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="2948e-471">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="2948e-472">Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="2948e-472">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2948e-473">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2948e-473">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2948e-474">*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="2948e-474">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="2948e-475">Předdefinované filtry zpracovávají úlohy, jako například:</span><span class="sxs-lookup"><span data-stu-id="2948e-475">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="2948e-476">Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).</span><span class="sxs-lookup"><span data-stu-id="2948e-476">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="2948e-477">Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="2948e-477">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="2948e-478">Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek.</span><span class="sxs-lookup"><span data-stu-id="2948e-478">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="2948e-479">Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="2948e-479">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="2948e-480">Filtry zabraňují duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="2948e-480">Filters avoid duplicating code.</span></span> <span data-ttu-id="2948e-481">Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="2948e-481">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="2948e-482">Tento dokument se týká Razor stránek, řadičů rozhraní API a řadičů se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="2948e-482">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="2948e-483">[Zobrazit nebo Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2948e-483">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="2948e-484">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="2948e-484">How filters work</span></span>

<span data-ttu-id="2948e-485">Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.</span><span class="sxs-lookup"><span data-stu-id="2948e-485">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="2948e-486">Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="2948e-486">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="2948e-489">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="2948e-489">Filter types</span></span>

<span data-ttu-id="2948e-490">Každý typ filtru se spustí v jiné fázi kanálu filtru:</span><span class="sxs-lookup"><span data-stu-id="2948e-490">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="2948e-491">[Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="2948e-491">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="2948e-492">Filtry autorizace pro krátkodobé vynechání kanálu, pokud je požadavek neautorizovaný</span><span class="sxs-lookup"><span data-stu-id="2948e-492">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="2948e-493">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="2948e-493">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="2948e-494">Spusťte po autorizaci.</span><span class="sxs-lookup"><span data-stu-id="2948e-494">Run after authorization.</span></span>  
  * <span data-ttu-id="2948e-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>může spustit kód před zbytek kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-495"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="2948e-496">Například `OnResourceExecuting` může spustit kód před vytvořením vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="2948e-496">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="2948e-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>může spustit kód po dokončení zbývající části kanálu.</span><span class="sxs-lookup"><span data-stu-id="2948e-497"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="2948e-498">[Filtry akcí](#action-filters) mohou spustit kód bezprostředně před a po volání jednotlivé metody akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-498">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="2948e-499">Lze je použít k manipulaci s argumenty předaných do akce a výsledek vráceného z akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-499">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="2948e-500">Na stránkách se **nepodporují filtry** akcí Razor .</span><span class="sxs-lookup"><span data-stu-id="2948e-500">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="2948e-501">[Filtry výjimek](#exception-filters) slouží k aplikování globálních zásad na neošetřené výjimky, ke kterým dojde před zapsáním cokoli do těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="2948e-501">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="2948e-502">[Filtry výsledků](#result-filters) můžou spustit kód hned před a po provedení jednotlivých výsledků akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-502">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="2948e-503">Spustí se pouze v případě, že metoda akce byla úspěšně provedena.</span><span class="sxs-lookup"><span data-stu-id="2948e-503">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="2948e-504">Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="2948e-504">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="2948e-505">Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-505">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="2948e-508">Implementace</span><span class="sxs-lookup"><span data-stu-id="2948e-508">Implementation</span></span>

<span data-ttu-id="2948e-509">Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2948e-509">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="2948e-510">Synchronní filtry mohou spustit kód před ( `On-Stage-Executing` ) a po ( `On-Stage-Executed` ) jejich fáze zřetězení.</span><span class="sxs-lookup"><span data-stu-id="2948e-510">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="2948e-511">Například `OnActionExecuting` je volána před voláním metody Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-511">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="2948e-512">`OnActionExecuted`je volána po návratu metody Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-512">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="2948e-513">Asynchronní filtry definují `On-Stage-ExecutionAsync` metodu:</span><span class="sxs-lookup"><span data-stu-id="2948e-513">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="2948e-514">V předchozím kódu `SampleAsyncActionFilter` obsahuje znak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), který provádí metodu Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-514">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="2948e-515">Každá z těchto `On-Stage-ExecutionAsync` metod vezme `FilterType-ExecutionDelegate` fázi zřetězení filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-515">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="2948e-516">Několik fází filtru</span><span class="sxs-lookup"><span data-stu-id="2948e-516">Multiple filter stages</span></span>

<span data-ttu-id="2948e-517">Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="2948e-517">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="2948e-518">Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Třída implementuje `IActionFilter` , `IResultFilter` a jejich asynchronní ekvivalenty.</span><span class="sxs-lookup"><span data-stu-id="2948e-518">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="2948e-519">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="2948e-519">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="2948e-520">Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá.</span><span class="sxs-lookup"><span data-stu-id="2948e-520">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="2948e-521">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2948e-521">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="2948e-522">Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda.</span><span class="sxs-lookup"><span data-stu-id="2948e-522">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="2948e-523">Při použití abstraktních tříd, jako <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> je například přepsání pouze synchronních metod nebo asynchronní metody pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-523">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="2948e-524">Předdefinované atributy filtru</span><span class="sxs-lookup"><span data-stu-id="2948e-524">Built-in filter attributes</span></span>

<span data-ttu-id="2948e-525">ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené.</span><span class="sxs-lookup"><span data-stu-id="2948e-525">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="2948e-526">Například následující filtr výsledků přidá hlavičku do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="2948e-526">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="2948e-527">Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="2948e-527">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="2948e-528">Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu HLAVIČKY http:</span><span class="sxs-lookup"><span data-stu-id="2948e-528">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="2948e-529">Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="2948e-529">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="2948e-530">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="2948e-530">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="2948e-531">Filtrovat obory a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="2948e-531">Filter scopes and order of execution</span></span>

<span data-ttu-id="2948e-532">Filtr lze přidat do kanálu v jednom ze tří *oborů*:</span><span class="sxs-lookup"><span data-stu-id="2948e-532">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="2948e-533">Použití atributu pro akci.</span><span class="sxs-lookup"><span data-stu-id="2948e-533">Using an attribute on an action.</span></span>
* <span data-ttu-id="2948e-534">Použití atributu na řadiči.</span><span class="sxs-lookup"><span data-stu-id="2948e-534">Using an attribute on a controller.</span></span>
* <span data-ttu-id="2948e-535">Globálně pro všechny řadiče a akce, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="2948e-535">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="2948e-536">Předchozí kód přičítá tři filtry globálně pomocí kolekce [MvcOptions. filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) .</span><span class="sxs-lookup"><span data-stu-id="2948e-536">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="2948e-537">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="2948e-537">Default order of execution</span></span>

<span data-ttu-id="2948e-538">Pokud existuje více filtrů *stejného typu*, určuje obor výchozí pořadí provádění filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-538">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="2948e-539">Globální filtry filtr tříd Surround.</span><span class="sxs-lookup"><span data-stu-id="2948e-539">Global filters surround class filters.</span></span> <span data-ttu-id="2948e-540">Filtry tříd obklopují filtry metod Surround.</span><span class="sxs-lookup"><span data-stu-id="2948e-540">Class filters surround method filters.</span></span>

<span data-ttu-id="2948e-541">V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem.</span><span class="sxs-lookup"><span data-stu-id="2948e-541">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="2948e-542">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="2948e-542">The filter sequence:</span></span>

* <span data-ttu-id="2948e-543">*Před* kódem globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="2948e-543">The *before* code of global filters.</span></span>
  * <span data-ttu-id="2948e-544">*Před* kódem filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2948e-544">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="2948e-545">Kód *před* filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-545">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="2948e-546">*Po* kódu filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-546">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="2948e-547">*Po* kódu filtry kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2948e-547">The *after* code of controller filters.</span></span>
* <span data-ttu-id="2948e-548">*Po* kódu globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="2948e-548">The *after* code of global filters.</span></span>
  
<span data-ttu-id="2948e-549">Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-549">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="2948e-550">Sequence</span><span class="sxs-lookup"><span data-stu-id="2948e-550">Sequence</span></span> | <span data-ttu-id="2948e-551">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="2948e-551">Filter scope</span></span> | <span data-ttu-id="2948e-552">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="2948e-552">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="2948e-553">1</span><span class="sxs-lookup"><span data-stu-id="2948e-553">1</span></span> | <span data-ttu-id="2948e-554">Globální</span><span class="sxs-lookup"><span data-stu-id="2948e-554">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2948e-555">2</span><span class="sxs-lookup"><span data-stu-id="2948e-555">2</span></span> | <span data-ttu-id="2948e-556">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="2948e-556">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2948e-557">3</span><span class="sxs-lookup"><span data-stu-id="2948e-557">3</span></span> | <span data-ttu-id="2948e-558">Metoda</span><span class="sxs-lookup"><span data-stu-id="2948e-558">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2948e-559">4</span><span class="sxs-lookup"><span data-stu-id="2948e-559">4</span></span> | <span data-ttu-id="2948e-560">Metoda</span><span class="sxs-lookup"><span data-stu-id="2948e-560">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="2948e-561">5</span><span class="sxs-lookup"><span data-stu-id="2948e-561">5</span></span> | <span data-ttu-id="2948e-562">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="2948e-562">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="2948e-563">6</span><span class="sxs-lookup"><span data-stu-id="2948e-563">6</span></span> | <span data-ttu-id="2948e-564">Globální</span><span class="sxs-lookup"><span data-stu-id="2948e-564">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="2948e-565">Tato posloupnost zobrazuje:</span><span class="sxs-lookup"><span data-stu-id="2948e-565">This sequence shows:</span></span>

* <span data-ttu-id="2948e-566">Filtr metod je vnořený do filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2948e-566">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="2948e-567">Filtr kontroleru je vnořený do globálního filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-567">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-no-locrazor-page-level-filters"></a><span data-ttu-id="2948e-568">Filtry na Razor úrovni řadiče a stránky</span><span class="sxs-lookup"><span data-stu-id="2948e-568">Controller and Razor Page level filters</span></span>

<span data-ttu-id="2948e-569">Každý kontroler, který dědí ze <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="2948e-569">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="2948e-570">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="2948e-570">These methods:</span></span>

* <span data-ttu-id="2948e-571">Zabalte filtry, které se spouštějí pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="2948e-571">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="2948e-572">`OnActionExecuting`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-572">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="2948e-573">`OnActionExecuted`se volá po všech filtrech akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-573">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="2948e-574">`OnActionExecutionAsync`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-574">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="2948e-575">Kód v filtru po `next` spuštění po metodě Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-575">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="2948e-576">Například v ukázce ke stažení `MySampleActionFilter` se použije globálně při spuštění.</span><span class="sxs-lookup"><span data-stu-id="2948e-576">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="2948e-577">Pomocná rutina `TestController`:</span><span class="sxs-lookup"><span data-stu-id="2948e-577">The `TestController`:</span></span>

* <span data-ttu-id="2948e-578">Aplikuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) na `FilterTest2` akci.</span><span class="sxs-lookup"><span data-stu-id="2948e-578">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="2948e-579">Přepisuje `OnActionExecuting` a `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="2948e-579">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="2948e-580">Navigace pro `https://localhost:5001/Test/FilterTest2` spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="2948e-580">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="2948e-581">RazorInformace o stránkách naleznete v tématu [implementace Razor filtrů stránek pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="2948e-581">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="2948e-582">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="2948e-582">Overriding the default order</span></span>

<span data-ttu-id="2948e-583">Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="2948e-583">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="2948e-584">`IOrderedFilter`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před rozsahem, aby určila pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="2948e-584">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="2948e-585">Filtr s nižší `Order` hodnotou:</span><span class="sxs-lookup"><span data-stu-id="2948e-585">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="2948e-586">Spustí *před* kódem filtr s vyšší hodnotou `Order` .</span><span class="sxs-lookup"><span data-stu-id="2948e-586">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="2948e-587">Spustí *za* kódem za filtr s vyšší `Order` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="2948e-587">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="2948e-588">`Order`Vlastnost lze nastavit s parametrem konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="2948e-588">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="2948e-589">Vezměte v úvahu stejné 3 filtry akcí, které jsou uvedené v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="2948e-589">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="2948e-590">Pokud `Order` je vlastnost Controller a globální filtry nastavená na 1 a 2 v uvedeném pořadí, pořadí spouštění se vrátí zpět.</span><span class="sxs-lookup"><span data-stu-id="2948e-590">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="2948e-591">Sequence</span><span class="sxs-lookup"><span data-stu-id="2948e-591">Sequence</span></span> | <span data-ttu-id="2948e-592">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="2948e-592">Filter scope</span></span> | <span data-ttu-id="2948e-593">`Order`majetek</span><span class="sxs-lookup"><span data-stu-id="2948e-593">`Order` property</span></span> | <span data-ttu-id="2948e-594">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="2948e-594">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="2948e-595">1</span><span class="sxs-lookup"><span data-stu-id="2948e-595">1</span></span> | <span data-ttu-id="2948e-596">Metoda</span><span class="sxs-lookup"><span data-stu-id="2948e-596">Method</span></span> | <span data-ttu-id="2948e-597">0</span><span class="sxs-lookup"><span data-stu-id="2948e-597">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="2948e-598">2</span><span class="sxs-lookup"><span data-stu-id="2948e-598">2</span></span> | <span data-ttu-id="2948e-599">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="2948e-599">Controller</span></span> | <span data-ttu-id="2948e-600">1</span><span class="sxs-lookup"><span data-stu-id="2948e-600">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="2948e-601">3</span><span class="sxs-lookup"><span data-stu-id="2948e-601">3</span></span> | <span data-ttu-id="2948e-602">Globální</span><span class="sxs-lookup"><span data-stu-id="2948e-602">Global</span></span> | <span data-ttu-id="2948e-603">2</span><span class="sxs-lookup"><span data-stu-id="2948e-603">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="2948e-604">4</span><span class="sxs-lookup"><span data-stu-id="2948e-604">4</span></span> | <span data-ttu-id="2948e-605">Globální</span><span class="sxs-lookup"><span data-stu-id="2948e-605">Global</span></span> | <span data-ttu-id="2948e-606">2</span><span class="sxs-lookup"><span data-stu-id="2948e-606">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="2948e-607">5</span><span class="sxs-lookup"><span data-stu-id="2948e-607">5</span></span> | <span data-ttu-id="2948e-608">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="2948e-608">Controller</span></span> | <span data-ttu-id="2948e-609">1</span><span class="sxs-lookup"><span data-stu-id="2948e-609">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="2948e-610">6</span><span class="sxs-lookup"><span data-stu-id="2948e-610">6</span></span> | <span data-ttu-id="2948e-611">Metoda</span><span class="sxs-lookup"><span data-stu-id="2948e-611">Method</span></span> | <span data-ttu-id="2948e-612">0</span><span class="sxs-lookup"><span data-stu-id="2948e-612">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="2948e-613">`Order`Vlastnost Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="2948e-613">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="2948e-614">Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor.</span><span class="sxs-lookup"><span data-stu-id="2948e-614">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="2948e-615">Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0.</span><span class="sxs-lookup"><span data-stu-id="2948e-615">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="2948e-616">Pro předdefinované filtry rozsah určuje pořadí, pokud `Order` není nastaven na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2948e-616">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="2948e-617">Zrušení a zkrácení okruhů</span><span class="sxs-lookup"><span data-stu-id="2948e-617">Cancellation and short-circuiting</span></span>

<span data-ttu-id="2948e-618">Kanál filtru může být v krátkém okruhu nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnosti pro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr poskytnutý metodě Filter.</span><span class="sxs-lookup"><span data-stu-id="2948e-618">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="2948e-619">Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:</span><span class="sxs-lookup"><span data-stu-id="2948e-619">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="2948e-620">V následujícím kódu, `ShortCircuitingResourceFilter` a jako `AddHeader` cíl filtru i cílovou `SomeResource` metodu Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-620">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="2948e-621">Pomocná rutina `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="2948e-621">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="2948e-622">Nejprve se spustí, protože se jedná o filtr prostředků a `AddHeader` je filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-622">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="2948e-623">Krátké okruhy zbývajícího kanálu.</span><span class="sxs-lookup"><span data-stu-id="2948e-623">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="2948e-624">Proto `AddHeader` Filtr pro akci nikdy neběží `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="2948e-624">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="2948e-625">Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to za předpokladu, že byla `ShortCircuitingResourceFilter` spuštěna jako první.</span><span class="sxs-lookup"><span data-stu-id="2948e-625">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="2948e-626">`ShortCircuitingResourceFilter`Nejprve se spustí z důvodu jeho typu filtru nebo explicitního použití `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="2948e-626">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="2948e-627">Injektáž závislostí</span><span class="sxs-lookup"><span data-stu-id="2948e-627">Dependency injection</span></span>

<span data-ttu-id="2948e-628">Filtry lze přidat podle typu nebo podle instance.</span><span class="sxs-lookup"><span data-stu-id="2948e-628">Filters can be added by type or by instance.</span></span> <span data-ttu-id="2948e-629">Pokud je přidána instance, bude tato instance použita pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="2948e-629">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="2948e-630">Pokud je přidán typ, je aktivován typ.</span><span class="sxs-lookup"><span data-stu-id="2948e-630">If a type is added, it's type-activated.</span></span> <span data-ttu-id="2948e-631">Filtr aktivovaný typu znamená:</span><span class="sxs-lookup"><span data-stu-id="2948e-631">A type-activated filter means:</span></span>

* <span data-ttu-id="2948e-632">Instance se vytvoří pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="2948e-632">An instance is created for each request.</span></span>
* <span data-ttu-id="2948e-633">Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="2948e-633">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="2948e-634">Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="2948e-634">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="2948e-635">Pomocí DI nelze poskytnout závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="2948e-635">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="2948e-636">V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány.</span><span class="sxs-lookup"><span data-stu-id="2948e-636">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="2948e-637">Toto je omezení způsobu fungování atributů.</span><span class="sxs-lookup"><span data-stu-id="2948e-637">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="2948e-638">Následující filtry podporují závislosti konstruktoru poskytované od DI:</span><span class="sxs-lookup"><span data-stu-id="2948e-638">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="2948e-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementováno u atributu.</span><span class="sxs-lookup"><span data-stu-id="2948e-639"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="2948e-640">Předchozí filtry lze použít pro metodu kontroleru nebo akce:</span><span class="sxs-lookup"><span data-stu-id="2948e-640">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="2948e-641">Protokolovací nástroje jsou k dispozici z DI.</span><span class="sxs-lookup"><span data-stu-id="2948e-641">Loggers are available from DI.</span></span> <span data-ttu-id="2948e-642">Ale nevytvářejte a používejte filtry čistě pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="2948e-642">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="2948e-643">[Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="2948e-643">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="2948e-644">Protokolování přidáno do filtrů:</span><span class="sxs-lookup"><span data-stu-id="2948e-644">Logging added to filters:</span></span>

* <span data-ttu-id="2948e-645">Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="2948e-645">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="2948e-646">Neměli **byste** protokolovat akce nebo jiné události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2948e-646">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="2948e-647">Vestavěné filtry protokolují akce a události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2948e-647">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="2948e-648">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="2948e-648">ServiceFilterAttribute</span></span>

<span data-ttu-id="2948e-649">Typy implementace filtru služby jsou zaregistrované v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2948e-649">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="2948e-650"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Načte instanci filtru z di.</span><span class="sxs-lookup"><span data-stu-id="2948e-650">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="2948e-651">Následující kód ukazuje `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="2948e-651">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="2948e-652">V následujícím kódu `AddHeaderResultServiceFilter` je přidán do kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="2948e-652">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="2948e-653">V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="2948e-653">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="2948e-654">Při použití `ServiceFilterAttribute` nastavte [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="2948e-654">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="2948e-655">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="2948e-655">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="2948e-656">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="2948e-656">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="2948e-657">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-657">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="2948e-658">Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.</span><span class="sxs-lookup"><span data-stu-id="2948e-658">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="2948e-659">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="2948e-659">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="2948e-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="2948e-660"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="2948e-661">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="2948e-661">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="2948e-662">`CreateInstance`Načte zadaný typ z DI.</span><span class="sxs-lookup"><span data-stu-id="2948e-662">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="2948e-663">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="2948e-663">TypeFilterAttribute</span></span>

<span data-ttu-id="2948e-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jeho typ není vyřešen přímo z kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="2948e-664"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="2948e-665">Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="2948e-665">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="2948e-666">Protože `TypeFilterAttribute` typy nejsou vyřešeny přímo z kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="2948e-666">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="2948e-667">Typy, na které se odkazuje pomocí, `TypeFilterAttribute` není nutné registrovat v kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="2948e-667">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="2948e-668">Mají své závislosti splněné kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="2948e-668">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="2948e-669">`TypeFilterAttribute`může volitelně přijmout argumenty konstruktoru pro typ.</span><span class="sxs-lookup"><span data-stu-id="2948e-669">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="2948e-670">Při použití `TypeFilterAttribute` nastavte [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="2948e-670">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="2948e-671">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="2948e-671">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="2948e-672">Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-672">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="2948e-673">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="2948e-673">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="2948e-674">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="2948e-674">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="2948e-675">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="2948e-675">Authorization filters</span></span>

<span data-ttu-id="2948e-676">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="2948e-676">Authorization filters:</span></span>

* <span data-ttu-id="2948e-677">Jsou první filtry spouštěny v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-677">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="2948e-678">Řízení přístupu k metodám akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-678">Control access to action methods.</span></span>
* <span data-ttu-id="2948e-679">Před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="2948e-679">Have a before method, but no after method.</span></span>

<span data-ttu-id="2948e-680">Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2948e-680">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="2948e-681">Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-681">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="2948e-682">Vestavěný autorizační filtr:</span><span class="sxs-lookup"><span data-stu-id="2948e-682">The built-in authorization filter:</span></span>

* <span data-ttu-id="2948e-683">Volá autorizační systém.</span><span class="sxs-lookup"><span data-stu-id="2948e-683">Calls the authorization system.</span></span>
* <span data-ttu-id="2948e-684">Neautorizuje požadavky.</span><span class="sxs-lookup"><span data-stu-id="2948e-684">Does not authorize requests.</span></span>

<span data-ttu-id="2948e-685">Negenerovat **výjimky** v rámci autorizačních filtrů:</span><span class="sxs-lookup"><span data-stu-id="2948e-685">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="2948e-686">Výjimka nebude zpracována.</span><span class="sxs-lookup"><span data-stu-id="2948e-686">The exception will not be handled.</span></span>
* <span data-ttu-id="2948e-687">Filtry výjimek nebudou zpracovávat výjimku.</span><span class="sxs-lookup"><span data-stu-id="2948e-687">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="2948e-688">Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-688">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="2948e-689">Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="2948e-689">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="2948e-690">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="2948e-690">Resource filters</span></span>

<span data-ttu-id="2948e-691">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="2948e-691">Resource filters:</span></span>

* <span data-ttu-id="2948e-692">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="2948e-692">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="2948e-693">Provádění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-693">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="2948e-694">Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .</span><span class="sxs-lookup"><span data-stu-id="2948e-694">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="2948e-695">Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu.</span><span class="sxs-lookup"><span data-stu-id="2948e-695">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="2948e-696">Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="2948e-696">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="2948e-697">Příklady filtru prostředků:</span><span class="sxs-lookup"><span data-stu-id="2948e-697">Resource filter examples:</span></span>

* <span data-ttu-id="2948e-698">[Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.</span><span class="sxs-lookup"><span data-stu-id="2948e-698">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="2948e-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="2948e-699">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="2948e-700">Zabraňuje vazbě modelu v přístupu k datům formuláře.</span><span class="sxs-lookup"><span data-stu-id="2948e-700">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="2948e-701">Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.</span><span class="sxs-lookup"><span data-stu-id="2948e-701">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="2948e-702">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="2948e-702">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2948e-703">Filtry **akcí se nevztahují** na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="2948e-703">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="2948e-704">RazorStránky podporují <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="2948e-704">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="2948e-705">Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="2948e-705">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="2948e-706">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="2948e-706">Action filters:</span></span>

* <span data-ttu-id="2948e-707">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="2948e-707">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="2948e-708">Jejich spuštění obklopuje provádění metod akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-708">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="2948e-709">Následující kód ukazuje filtr vzorové akce:</span><span class="sxs-lookup"><span data-stu-id="2948e-709">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="2948e-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2948e-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="2948e-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>– povolí čtení vstupů do metody akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-711"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="2948e-712"><xref:Microsoft.AspNetCore.Mvc.Controller>– povolí manipulaci s instancí kontroleru.</span><span class="sxs-lookup"><span data-stu-id="2948e-712"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="2948e-713"><xref:System.Web.Mvc.ActionExecutingContext.Result>– nastavení `Result` krátkodobého provádění metody akce a následných filtrů akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-713"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="2948e-714">Vyvolání výjimky v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="2948e-714">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="2948e-715">Zabraňuje spuštění dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="2948e-715">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="2948e-716">Na rozdíl od nastavení `Result` se místo úspěšného výsledku považuje za selhání.</span><span class="sxs-lookup"><span data-stu-id="2948e-716">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="2948e-717"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Poskytuje `Controller` a `Result` plus následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2948e-717">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="2948e-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="2948e-718"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="2948e-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Hodnota není null, pokud akce nebo dříve vyvolala filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-719"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="2948e-720">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="2948e-720">Setting this property to null:</span></span>

  * <span data-ttu-id="2948e-721">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="2948e-721">Effectively handles the exception.</span></span>
  * <span data-ttu-id="2948e-722">`Result`je spuštěn, jako kdyby byl vrácen z metody Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-722">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="2948e-723">V případě `IAsyncActionFilter` volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="2948e-723">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="2948e-724">Provede všechny následné filtry akcí a metodu Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-724">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="2948e-725">Vrací objekt `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="2948e-725">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="2948e-726">Do krátkodobého okruhu, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> k instanci výsledku a Nevolejte `next` (a `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="2948e-726">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="2948e-727">Rozhraní poskytuje abstrakci <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="2948e-727">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="2948e-728">`OnActionExecuting`Filtr akcí lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="2948e-728">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="2948e-729">Ověří stav modelu.</span><span class="sxs-lookup"><span data-stu-id="2948e-729">Validate model state.</span></span>
* <span data-ttu-id="2948e-730">Pokud je stav neplatný, vrátí se chyba.</span><span class="sxs-lookup"><span data-stu-id="2948e-730">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="2948e-731">`OnActionExecuted`Metoda se spustí za metodou akce:</span><span class="sxs-lookup"><span data-stu-id="2948e-731">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="2948e-732">A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="2948e-732">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="2948e-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>je nastaven na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="2948e-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="2948e-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="2948e-734"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="2948e-735">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="2948e-735">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="2948e-736">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="2948e-736">Effectively handles an exception.</span></span>
  * <span data-ttu-id="2948e-737">`ActionExecutedContext.Result`je spuštěn, jako kdyby byl vrácen normálně z metody Action.</span><span class="sxs-lookup"><span data-stu-id="2948e-737">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="2948e-738">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="2948e-738">Exception filters</span></span>

<span data-ttu-id="2948e-739">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="2948e-739">Exception filters:</span></span>

* <span data-ttu-id="2948e-740">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="2948e-740">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="2948e-741">Dá se použít k implementaci běžných zásad zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="2948e-741">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="2948e-742">Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:</span><span class="sxs-lookup"><span data-stu-id="2948e-742">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="2948e-743">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="2948e-743">Exception filters:</span></span>

* <span data-ttu-id="2948e-744">Nemusíte mít události před a po.</span><span class="sxs-lookup"><span data-stu-id="2948e-744">Don't have before and after events.</span></span>
* <span data-ttu-id="2948e-745">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="2948e-745">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="2948e-746">Zpracování neošetřených výjimek, ke kterým dochází při Razor vytváření stránky nebo kontroleru, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-746">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="2948e-747">**Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.</span><span class="sxs-lookup"><span data-stu-id="2948e-747">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="2948e-748">Chcete-li zpracovat výjimku, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost na `true` nebo zapište odpověď.</span><span class="sxs-lookup"><span data-stu-id="2948e-748">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="2948e-749">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="2948e-749">This stops propagation of the exception.</span></span> <span data-ttu-id="2948e-750">Filtr výjimek nemůže vypnout výjimku na "úspěch".</span><span class="sxs-lookup"><span data-stu-id="2948e-750">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="2948e-751">To může provést pouze filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-751">Only an action filter can do that.</span></span>

<span data-ttu-id="2948e-752">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="2948e-752">Exception filters:</span></span>

* <span data-ttu-id="2948e-753">Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-753">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="2948e-754">Nejsou tak flexibilní jako middleware při zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="2948e-754">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="2948e-755">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="2948e-755">Prefer middleware for exception handling.</span></span> <span data-ttu-id="2948e-756">Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána.</span><span class="sxs-lookup"><span data-stu-id="2948e-756">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="2948e-757">Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML.</span><span class="sxs-lookup"><span data-stu-id="2948e-757">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="2948e-758">Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.</span><span class="sxs-lookup"><span data-stu-id="2948e-758">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="2948e-759">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="2948e-759">Result filters</span></span>

<span data-ttu-id="2948e-760">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="2948e-760">Result filters:</span></span>

* <span data-ttu-id="2948e-761">Implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="2948e-761">Implement an interface:</span></span>
  * <span data-ttu-id="2948e-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="2948e-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="2948e-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="2948e-763"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="2948e-764">Jejich spuštění obklopuje provádění výsledků akcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-764">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="2948e-765">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="2948e-765">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="2948e-766">Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="2948e-766">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="2948e-767">Typ výsledku, který se má provést, závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="2948e-767">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="2948e-768">Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást prováděné <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="2948e-768">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="2948e-769">Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku.</span><span class="sxs-lookup"><span data-stu-id="2948e-769">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="2948e-770">Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="2948e-770">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="2948e-771">Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-771">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="2948e-772">Filtry výsledků nejsou provedeny v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="2948e-772">Result filters are not executed when:</span></span>

* <span data-ttu-id="2948e-773">Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.</span><span class="sxs-lookup"><span data-stu-id="2948e-773">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="2948e-774">Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-774">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="2948e-775"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda může zkrátit provádění výsledků akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true` .</span><span class="sxs-lookup"><span data-stu-id="2948e-775">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="2948e-776">Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="2948e-776">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="2948e-777">Vyvolání výjimky v `IResultFilter.OnResultExecuting` bude:</span><span class="sxs-lookup"><span data-stu-id="2948e-777">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="2948e-778">Zabraňte provádění výsledků akce a dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="2948e-778">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="2948e-779">Být považována za selhání namísto úspěšného výsledku.</span><span class="sxs-lookup"><span data-stu-id="2948e-779">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="2948e-780">Při <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> spuštění metody je odpověď pravděpodobně již odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="2948e-780">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="2948e-781">Pokud byla odpověď již odeslána klientovi, nelze ji dále změnit.</span><span class="sxs-lookup"><span data-stu-id="2948e-781">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="2948e-782">`ResultExecutedContext.Canceled`je nastaven na hodnotu, `true` Pokud je spuštění výsledku akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="2948e-782">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="2948e-783">`ResultExecutedContext.Exception`je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku.</span><span class="sxs-lookup"><span data-stu-id="2948e-783">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="2948e-784">Nastavení `Exception` na hodnotu null efektivně zpracovává výjimku a brání výjimce znovu vyvolat ASP.NET Core později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="2948e-784">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="2948e-785">Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="2948e-785">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="2948e-786">Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.</span><span class="sxs-lookup"><span data-stu-id="2948e-786">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="2948e-787">V případě <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> volání `await next` na se <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné filtry výsledků a výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-787">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="2948e-788">Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a Nevolejte na `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="2948e-788">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="2948e-789">Rozhraní poskytuje abstrakci `ResultFilterAttribute` , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="2948e-789">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="2948e-790">Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="2948e-790">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="2948e-791">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="2948e-791">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="2948e-792"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Rozhraní a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklaruje <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která je spuštěna pro všechny výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="2948e-792">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="2948e-793">To zahrnuje výsledky akcí, které vytvořil:</span><span class="sxs-lookup"><span data-stu-id="2948e-793">This includes action results produced by:</span></span>

* <span data-ttu-id="2948e-794">Filtry autorizace a filtry prostředků, které mají krátký okruh.</span><span class="sxs-lookup"><span data-stu-id="2948e-794">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="2948e-795">Filtry výjimek.</span><span class="sxs-lookup"><span data-stu-id="2948e-795">Exception filters.</span></span>

<span data-ttu-id="2948e-796">Například následující filtr vždy spustí a nastaví výsledek akce ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="2948e-796">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="2948e-797">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="2948e-797">IFilterFactory</span></span>

<span data-ttu-id="2948e-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="2948e-798"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="2948e-799">Proto `IFilterFactory` může být instance použita jako `IFilterMetadata` instance kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-799">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="2948e-800">Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="2948e-800">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="2948e-801">Pokud je toto přetypování úspěšné, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> je volána metoda pro vytvoření `IFilterMetadata` instance, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="2948e-801">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="2948e-802">To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="2948e-802">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="2948e-803">`IFilterFactory`může být implementováno pomocí implementace vlastního atributu jako jiný přístup k vytváření filtrů:</span><span class="sxs-lookup"><span data-stu-id="2948e-803">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="2948e-804">Předchozí kód lze otestovat spuštěním [ukázky stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="2948e-804">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="2948e-805">Vyvolejte vývojářské nástroje F12.</span><span class="sxs-lookup"><span data-stu-id="2948e-805">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="2948e-806">Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="2948e-806">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="2948e-807">Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:</span><span class="sxs-lookup"><span data-stu-id="2948e-807">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="2948e-808">**Autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="2948e-808">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="2948e-809">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="2948e-809">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="2948e-810">**interní:**`My header`</span><span class="sxs-lookup"><span data-stu-id="2948e-810">**internal:** `My header`</span></span>

<span data-ttu-id="2948e-811">Předchozí kód vytvoří hlavičku **interního:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="2948e-811">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="2948e-812">IFilterFactory implementované u atributu</span><span class="sxs-lookup"><span data-stu-id="2948e-812">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="2948e-813">Filtry, které implementují, `IFilterFactory` jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="2948e-813">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="2948e-814">Nevyžadovat předávání parametrů</span><span class="sxs-lookup"><span data-stu-id="2948e-814">Don't require passing parameters.</span></span>
* <span data-ttu-id="2948e-815">Musí mít závislosti konstruktoru, které musí vyplnit DI.</span><span class="sxs-lookup"><span data-stu-id="2948e-815">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="2948e-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="2948e-816"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="2948e-817">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="2948e-817">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="2948e-818">`CreateInstance`Načte zadaný typ z kontejneru Services (DI).</span><span class="sxs-lookup"><span data-stu-id="2948e-818">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="2948e-819">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="2948e-819">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="2948e-820">V předchozím kódu upravení metoda s jako `[SampleActionFilter]` upřednostňovaným přístupem k použití `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="2948e-820">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="2948e-821">Použití middlewaru v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="2948e-821">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="2948e-822">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="2948e-822">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="2948e-823">Filtry se ale liší od middlewaru v tom, že jsou součástí modulu ASP.NET Core Runtime, což znamená, že mají přístup k ASP.NET Core kontextu a konstrukcí.</span><span class="sxs-lookup"><span data-stu-id="2948e-823">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="2948e-824">Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="2948e-824">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="2948e-825">Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:</span><span class="sxs-lookup"><span data-stu-id="2948e-825">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="2948e-826">Pomocí <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Spusťte middleware:</span><span class="sxs-lookup"><span data-stu-id="2948e-826">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="2948e-827">Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="2948e-827">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="2948e-828">Další akce</span><span class="sxs-lookup"><span data-stu-id="2948e-828">Next actions</span></span>

* <span data-ttu-id="2948e-829">Viz [metody filtru pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="2948e-829">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="2948e-830">Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="2948e-830">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
