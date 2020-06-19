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
ms.openlocfilehash: 407583533939ec1077af8e1a1511ed187ef9de69
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103006"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="a10a5-103">Filtry v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a10a5-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a10a5-104">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a10a5-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a10a5-105">*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="a10a5-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="a10a5-106">Předdefinované filtry zpracovávají úlohy, jako například:</span><span class="sxs-lookup"><span data-stu-id="a10a5-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="a10a5-107">Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).</span><span class="sxs-lookup"><span data-stu-id="a10a5-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="a10a5-108">Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="a10a5-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="a10a5-109">Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek.</span><span class="sxs-lookup"><span data-stu-id="a10a5-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="a10a5-110">Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="a10a5-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="a10a5-111">Filtry zabraňují duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="a10a5-112">Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="a10a5-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="a10a5-113">Tento dokument se týká Razor stránek, řadičů rozhraní API a řadičů se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="a10a5-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="a10a5-114">Filtry nefungují přímo s [ Razor komponentami](xref:blazor/components/index).</span><span class="sxs-lookup"><span data-stu-id="a10a5-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="a10a5-115">Filtr může nepřímo ovlivnit komponentu pouze v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="a10a5-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="a10a5-116">Komponenta je vložena do stránky nebo zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a10a5-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="a10a5-117">Stránka nebo kontroler nebo zobrazení používá filtr.</span><span class="sxs-lookup"><span data-stu-id="a10a5-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="a10a5-118">[Zobrazit nebo Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a10a5-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="a10a5-119">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="a10a5-119">How filters work</span></span>

<span data-ttu-id="a10a5-120">Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.</span><span class="sxs-lookup"><span data-stu-id="a10a5-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="a10a5-121">Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="a10a5-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="a10a5-124">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="a10a5-124">Filter types</span></span>

<span data-ttu-id="a10a5-125">Každý typ filtru se spustí v jiné fázi kanálu filtru:</span><span class="sxs-lookup"><span data-stu-id="a10a5-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="a10a5-126">[Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="a10a5-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="a10a5-127">Filtry autorizace pro krátkodobé vynechání kanálu, pokud žádost není autorizována.</span><span class="sxs-lookup"><span data-stu-id="a10a5-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="a10a5-128">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="a10a5-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="a10a5-129">Spusťte po autorizaci.</span><span class="sxs-lookup"><span data-stu-id="a10a5-129">Run after authorization.</span></span>  
  * <span data-ttu-id="a10a5-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>spustí kód před zbytek kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="a10a5-131">Například `OnResourceExecuting` spustí kód před vazbou modelu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="a10a5-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>spustí kód po dokončení zbývající části kanálu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="a10a5-133">[Filtry akcí](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="a10a5-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="a10a5-134">Spustit kód bezprostředně před a po volání metody Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="a10a5-135">Může změnit argumenty předané do akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="a10a5-136">Může změnit výsledek vrácený z akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="a10a5-137">Nejsou **na** stránkách podporovány Razor .</span><span class="sxs-lookup"><span data-stu-id="a10a5-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="a10a5-138">[Filtry výjimek](#exception-filters) aplikují globální zásady na neošetřené výjimky, ke kterým došlo před zápisem textu odpovědi do.</span><span class="sxs-lookup"><span data-stu-id="a10a5-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="a10a5-139">[Filtry výsledků](#result-filters) spouštějí kód bezprostředně před a po provedení výsledků akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="a10a5-140">Spustí se pouze v případě, že metoda akce byla úspěšně provedena.</span><span class="sxs-lookup"><span data-stu-id="a10a5-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="a10a5-141">Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="a10a5-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="a10a5-142">Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="a10a5-145">Implementace</span><span class="sxs-lookup"><span data-stu-id="a10a5-145">Implementation</span></span>

<span data-ttu-id="a10a5-146">Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a10a5-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="a10a5-147">Synchronní filtry spouštějí kód před a po fázi zřetězení.</span><span class="sxs-lookup"><span data-stu-id="a10a5-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="a10a5-148">Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> je volána před voláním metody Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="a10a5-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*>je volána po návratu metody Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="a10a5-150">Asynchronní filtry definují `On-Stage-ExecutionAsync` metodu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="a10a5-151">Například <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*> :</span><span class="sxs-lookup"><span data-stu-id="a10a5-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="a10a5-152">V předchozím kódu `SampleAsyncActionFilter` obsahuje znak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), který provádí metodu Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="a10a5-153">Několik fází filtru</span><span class="sxs-lookup"><span data-stu-id="a10a5-153">Multiple filter stages</span></span>

<span data-ttu-id="a10a5-154">Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="a10a5-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="a10a5-155">Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Třída implementuje:</span><span class="sxs-lookup"><span data-stu-id="a10a5-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="a10a5-156">Synchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> a<xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="a10a5-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="a10a5-157">Asynchronní: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> a<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="a10a5-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="a10a5-158">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="a10a5-159">Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá.</span><span class="sxs-lookup"><span data-stu-id="a10a5-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="a10a5-160">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a10a5-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="a10a5-161">Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda.</span><span class="sxs-lookup"><span data-stu-id="a10a5-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="a10a5-162">Při použití abstraktních tříd <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , jako je přepsání pouze synchronních metod nebo asynchronní metody pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="a10a5-163">Předdefinované atributy filtru</span><span class="sxs-lookup"><span data-stu-id="a10a5-163">Built-in filter attributes</span></span>

<span data-ttu-id="a10a5-164">ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené.</span><span class="sxs-lookup"><span data-stu-id="a10a5-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="a10a5-165">Například následující filtr výsledků přidá hlavičku do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="a10a5-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="a10a5-166">Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="a10a5-167">Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu HLAVIČKY http:</span><span class="sxs-lookup"><span data-stu-id="a10a5-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="a10a5-168">K prohlédnutí hlaviček použijte nástroj, jako je například nástroj pro [vývojáře v prohlížeči](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) .</span><span class="sxs-lookup"><span data-stu-id="a10a5-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="a10a5-169">V části **hlavičky odpovědi** `author: Rick Anderson` se zobrazí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="a10a5-170">Následující kód implementuje `ActionFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="a10a5-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="a10a5-171">Přečte název a název z konfiguračního systému.</span><span class="sxs-lookup"><span data-stu-id="a10a5-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="a10a5-172">Na rozdíl od předchozí ukázky, následující kód nevyžaduje přidání parametrů filtru do kódu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="a10a5-173">Přidá název a název do hlavičky odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a10a5-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="a10a5-174">Možnosti konfigurace se poskytují z [konfiguračního systému](xref:fundamentals/configuration/index) pomocí [vzoru možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="a10a5-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="a10a5-175">Například z *appsettings.js* souboru:</span><span class="sxs-lookup"><span data-stu-id="a10a5-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="a10a5-176">V `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a10a5-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="a10a5-177">`PositionOptions`Třída je přidána do kontejneru služby s `"Position"` oblastí konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a10a5-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="a10a5-178">`MyActionFilterAttribute`Přidá se do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="a10a5-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="a10a5-179">Následující kód ukazuje `PositionOptions` třídu:</span><span class="sxs-lookup"><span data-stu-id="a10a5-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="a10a5-180">Následující kód platí `MyActionFilterAttribute` pro `Index2` metodu:</span><span class="sxs-lookup"><span data-stu-id="a10a5-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="a10a5-181">V části **hlavičky odpovědi**, `author: Rick Anderson` a `Editor: Joe Smith` se zobrazí při `Sample/Index2` volání koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="a10a5-182">Následující kód platí `MyActionFilterAttribute` pro a na `AddHeaderAttribute` Razor stránce:</span><span class="sxs-lookup"><span data-stu-id="a10a5-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="a10a5-183">Filtry nelze použít na Razor metody obslužné rutiny stránky.</span><span class="sxs-lookup"><span data-stu-id="a10a5-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="a10a5-184">Lze je použít buď na Razor model stránky, nebo globálně.</span><span class="sxs-lookup"><span data-stu-id="a10a5-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="a10a5-185">Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="a10a5-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="a10a5-186">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="a10a5-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="a10a5-187">Filtrovat obory a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="a10a5-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="a10a5-188">Filtr lze přidat do kanálu v jednom ze tří *oborů*:</span><span class="sxs-lookup"><span data-stu-id="a10a5-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="a10a5-189">Použití atributu u akce kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="a10a5-190">Atributy filtru nelze použít na Razor stránky metod obslužných rutin.</span><span class="sxs-lookup"><span data-stu-id="a10a5-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="a10a5-191">Použití atributu na řadiči nebo na Razor stránce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="a10a5-192">Globálně pro všechny řadiče, akce a Razor stránky, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="a10a5-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="a10a5-193">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="a10a5-193">Default order of execution</span></span>

<span data-ttu-id="a10a5-194">Pokud existuje více filtrů pro určitou fázi kanálu, rozsah Určuje výchozí pořadí provádění filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="a10a5-195">Globální filtry obklopují filtry tříd, které jsou ve filtru metod Surround.</span><span class="sxs-lookup"><span data-stu-id="a10a5-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="a10a5-196">V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem.</span><span class="sxs-lookup"><span data-stu-id="a10a5-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="a10a5-197">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="a10a5-197">The filter sequence:</span></span>

* <span data-ttu-id="a10a5-198">*Před* kódem globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="a10a5-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="a10a5-199">*Před* kódem kontroleru a Razor filtru stránky.</span><span class="sxs-lookup"><span data-stu-id="a10a5-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="a10a5-200">Kód *před* filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="a10a5-201">*Po* kódu filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="a10a5-202">*Po* kódu filtru řadiče a Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="a10a5-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="a10a5-203">*Po* kódu globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="a10a5-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="a10a5-204">Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="a10a5-205">Sequence</span><span class="sxs-lookup"><span data-stu-id="a10a5-205">Sequence</span></span> | <span data-ttu-id="a10a5-206">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="a10a5-206">Filter scope</span></span> | <span data-ttu-id="a10a5-207">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="a10a5-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="a10a5-208">1</span><span class="sxs-lookup"><span data-stu-id="a10a5-208">1</span></span> | <span data-ttu-id="a10a5-209">Globální</span><span class="sxs-lookup"><span data-stu-id="a10a5-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a10a5-210">2</span><span class="sxs-lookup"><span data-stu-id="a10a5-210">2</span></span> | <span data-ttu-id="a10a5-211">Kontroler nebo Razor Stránka</span><span class="sxs-lookup"><span data-stu-id="a10a5-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="a10a5-212">3</span><span class="sxs-lookup"><span data-stu-id="a10a5-212">3</span></span> | <span data-ttu-id="a10a5-213">Metoda</span><span class="sxs-lookup"><span data-stu-id="a10a5-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a10a5-214">4</span><span class="sxs-lookup"><span data-stu-id="a10a5-214">4</span></span> | <span data-ttu-id="a10a5-215">Metoda</span><span class="sxs-lookup"><span data-stu-id="a10a5-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="a10a5-216">5</span><span class="sxs-lookup"><span data-stu-id="a10a5-216">5</span></span> | <span data-ttu-id="a10a5-217">Kontroler nebo Razor Stránka</span><span class="sxs-lookup"><span data-stu-id="a10a5-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="a10a5-218">6</span><span class="sxs-lookup"><span data-stu-id="a10a5-218">6</span></span> | <span data-ttu-id="a10a5-219">Globální</span><span class="sxs-lookup"><span data-stu-id="a10a5-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="a10a5-220">Filtry na úrovni řadiče</span><span class="sxs-lookup"><span data-stu-id="a10a5-220">Controller level filters</span></span>

<span data-ttu-id="a10a5-221">Každý kontroler, který dědí ze <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="a10a5-222">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="a10a5-222">These methods:</span></span>

* <span data-ttu-id="a10a5-223">Zabalte filtry, které se spouštějí pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="a10a5-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="a10a5-224">`OnActionExecuting`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="a10a5-225">`OnActionExecuted`se volá po všech filtrech akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="a10a5-226">`OnActionExecutionAsync`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="a10a5-227">Kód v filtru po `next` spuštění po metodě Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="a10a5-228">Například v ukázce ke stažení `MySampleActionFilter` se použije globálně při spuštění.</span><span class="sxs-lookup"><span data-stu-id="a10a5-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="a10a5-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="a10a5-229">The `TestController`:</span></span>

* <span data-ttu-id="a10a5-230">Aplikuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) na `FilterTest2` akci.</span><span class="sxs-lookup"><span data-stu-id="a10a5-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="a10a5-231">Přepisuje `OnActionExecuting` a `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="a10a5-232">Navigace pro `https://localhost:5001/Test2/FilterTest2` spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="a10a5-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="a10a5-233">Filtry na úrovni řadiče nastaví vlastnost [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="a10a5-234">Filtry na úrovni řadiče **nelze nastavit** tak, aby běžely po filtrech použitých na metody.</span><span class="sxs-lookup"><span data-stu-id="a10a5-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="a10a5-235">Pořadí je vysvětleno v další části.</span><span class="sxs-lookup"><span data-stu-id="a10a5-235">Order is explained in the next section.</span></span>

<span data-ttu-id="a10a5-236">RazorInformace o stránkách naleznete v tématu [implementace Razor filtrů stránek pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="a10a5-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="a10a5-237">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="a10a5-237">Overriding the default order</span></span>

<span data-ttu-id="a10a5-238">Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="a10a5-239">`IOrderedFilter`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před rozsahem, aby určila pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="a10a5-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="a10a5-240">Filtr s nižší `Order` hodnotou:</span><span class="sxs-lookup"><span data-stu-id="a10a5-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="a10a5-241">Spustí *před* kódem filtr s vyšší hodnotou `Order` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="a10a5-242">Spustí *za* kódem za filtr s vyšší `Order` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="a10a5-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="a10a5-243">`Order`Vlastnost je nastavena s parametrem konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="a10a5-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="a10a5-244">Vezměte v úvahu dva filtry akcí v následujícím kontroleru:</span><span class="sxs-lookup"><span data-stu-id="a10a5-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="a10a5-245">Globální filtr je přidaný v `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a10a5-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="a10a5-246">Tři filtry jsou spouštěny v následujícím pořadí:</span><span class="sxs-lookup"><span data-stu-id="a10a5-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="a10a5-247">`Order`Vlastnost Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="a10a5-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="a10a5-248">Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor.</span><span class="sxs-lookup"><span data-stu-id="a10a5-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="a10a5-249">Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0.</span><span class="sxs-lookup"><span data-stu-id="a10a5-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="a10a5-250">Jak už bylo uvedeno výše, filtry na úrovni řadiče nastaví vlastnost [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) na `int.MinValue` pro předdefinované filtry. Scope určí pořadí, pokud `Order` není nastavená na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="a10a5-251">V předchozím kódu `MySampleActionFilter` má globální rozsah, takže se spustí před `MyAction2FilterAttribute` , který má obor kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="a10a5-252">Aby bylo možné `MyAction2FilterAttribute` Spustit jako první, nastavte pořadí na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="a10a5-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="a10a5-253">Aby se globální filtr `MySampleActionFilter` spouštěl jako první, nastavte `Order` na `int.MinValue` :</span><span class="sxs-lookup"><span data-stu-id="a10a5-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="a10a5-254">Zrušení a zkrácení okruhů</span><span class="sxs-lookup"><span data-stu-id="a10a5-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="a10a5-255">Kanál filtru může být v krátkém okruhu nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnosti pro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr poskytnutý metodě Filter.</span><span class="sxs-lookup"><span data-stu-id="a10a5-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="a10a5-256">Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:</span><span class="sxs-lookup"><span data-stu-id="a10a5-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="a10a5-257">V následujícím kódu, `ShortCircuitingResourceFilter` a jako `AddHeader` cíl filtru i cílovou `SomeResource` metodu Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="a10a5-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="a10a5-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="a10a5-259">Nejprve se spustí, protože se jedná o filtr prostředků a `AddHeader` je filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="a10a5-260">Krátké okruhy zbývajícího kanálu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="a10a5-261">Proto `AddHeader` Filtr pro akci nikdy neběží `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="a10a5-262">Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to za předpokladu, že byla `ShortCircuitingResourceFilter` spuštěna jako první.</span><span class="sxs-lookup"><span data-stu-id="a10a5-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="a10a5-263">`ShortCircuitingResourceFilter`Nejprve se spustí z důvodu jeho typu filtru nebo explicitního použití `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a10a5-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="a10a5-264">Injektáž závislosti</span><span class="sxs-lookup"><span data-stu-id="a10a5-264">Dependency injection</span></span>

<span data-ttu-id="a10a5-265">Filtry lze přidat podle typu nebo podle instance.</span><span class="sxs-lookup"><span data-stu-id="a10a5-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="a10a5-266">Pokud je přidána instance, bude tato instance použita pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="a10a5-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="a10a5-267">Pokud je přidán typ, je aktivován typ.</span><span class="sxs-lookup"><span data-stu-id="a10a5-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="a10a5-268">Filtr aktivovaný typu znamená:</span><span class="sxs-lookup"><span data-stu-id="a10a5-268">A type-activated filter means:</span></span>

* <span data-ttu-id="a10a5-269">Instance se vytvoří pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="a10a5-269">An instance is created for each request.</span></span>
* <span data-ttu-id="a10a5-270">Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="a10a5-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="a10a5-271">Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="a10a5-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="a10a5-272">Pomocí DI nelze poskytnout závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="a10a5-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="a10a5-273">V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány.</span><span class="sxs-lookup"><span data-stu-id="a10a5-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="a10a5-274">Toto je omezení způsobu fungování atributů.</span><span class="sxs-lookup"><span data-stu-id="a10a5-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="a10a5-275">Následující filtry podporují závislosti konstruktoru poskytované od DI:</span><span class="sxs-lookup"><span data-stu-id="a10a5-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="a10a5-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementováno u atributu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="a10a5-277">Předchozí filtry lze použít pro metodu kontroleru nebo akce:</span><span class="sxs-lookup"><span data-stu-id="a10a5-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="a10a5-278">Protokolovací nástroje jsou k dispozici z DI.</span><span class="sxs-lookup"><span data-stu-id="a10a5-278">Loggers are available from DI.</span></span> <span data-ttu-id="a10a5-279">Ale nevytvářejte a používejte filtry čistě pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="a10a5-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="a10a5-280">[Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="a10a5-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="a10a5-281">Protokolování přidáno do filtrů:</span><span class="sxs-lookup"><span data-stu-id="a10a5-281">Logging added to filters:</span></span>

* <span data-ttu-id="a10a5-282">Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="a10a5-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="a10a5-283">Neměli **byste** protokolovat akce nebo jiné události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a10a5-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="a10a5-284">Vestavěné filtry protokolují akce a události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a10a5-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="a10a5-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="a10a5-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="a10a5-286">Typy implementace filtru služby jsou zaregistrované v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="a10a5-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Načte instanci filtru z di.</span><span class="sxs-lookup"><span data-stu-id="a10a5-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="a10a5-288">Následující kód ukazuje `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="a10a5-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="a10a5-289">V následujícím kódu `AddHeaderResultServiceFilter` je přidán do kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="a10a5-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="a10a5-290">V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="a10a5-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="a10a5-291">Při použití `ServiceFilterAttribute` nastavte [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="a10a5-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="a10a5-292">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="a10a5-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="a10a5-293">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="a10a5-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="a10a5-294">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="a10a5-295">Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.</span><span class="sxs-lookup"><span data-stu-id="a10a5-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="a10a5-296">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="a10a5-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="a10a5-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="a10a5-298">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="a10a5-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="a10a5-299">`CreateInstance`Načte zadaný typ z DI.</span><span class="sxs-lookup"><span data-stu-id="a10a5-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="a10a5-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="a10a5-300">TypeFilterAttribute</span></span>

<span data-ttu-id="a10a5-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jeho typ není vyřešen přímo z kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="a10a5-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="a10a5-302">Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="a10a5-303">Protože `TypeFilterAttribute` typy nejsou vyřešeny přímo z kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="a10a5-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="a10a5-304">Typy, na které se odkazuje pomocí, `TypeFilterAttribute` není nutné registrovat v kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="a10a5-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="a10a5-305">Mají své závislosti splněné kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="a10a5-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="a10a5-306">`TypeFilterAttribute`může volitelně přijmout argumenty konstruktoru pro typ.</span><span class="sxs-lookup"><span data-stu-id="a10a5-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="a10a5-307">Při použití `TypeFilterAttribute` nastavte [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="a10a5-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="a10a5-308">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="a10a5-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="a10a5-309">Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="a10a5-310">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="a10a5-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="a10a5-311">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="a10a5-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="a10a5-312">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="a10a5-312">Authorization filters</span></span>

<span data-ttu-id="a10a5-313">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="a10a5-313">Authorization filters:</span></span>

* <span data-ttu-id="a10a5-314">Jsou první filtry spouštěny v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="a10a5-315">Řízení přístupu k metodám akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-315">Control access to action methods.</span></span>
* <span data-ttu-id="a10a5-316">Před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="a10a5-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="a10a5-317">Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a10a5-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="a10a5-318">Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="a10a5-319">Vestavěný autorizační filtr:</span><span class="sxs-lookup"><span data-stu-id="a10a5-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="a10a5-320">Volá autorizační systém.</span><span class="sxs-lookup"><span data-stu-id="a10a5-320">Calls the authorization system.</span></span>
* <span data-ttu-id="a10a5-321">Neautorizuje požadavky.</span><span class="sxs-lookup"><span data-stu-id="a10a5-321">Does not authorize requests.</span></span>

<span data-ttu-id="a10a5-322">Negenerovat **výjimky** v rámci autorizačních filtrů:</span><span class="sxs-lookup"><span data-stu-id="a10a5-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="a10a5-323">Výjimka nebude zpracována.</span><span class="sxs-lookup"><span data-stu-id="a10a5-323">The exception will not be handled.</span></span>
* <span data-ttu-id="a10a5-324">Filtry výjimek nebudou zpracovávat výjimku.</span><span class="sxs-lookup"><span data-stu-id="a10a5-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="a10a5-325">Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="a10a5-326">Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="a10a5-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="a10a5-327">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="a10a5-327">Resource filters</span></span>

<span data-ttu-id="a10a5-328">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="a10a5-328">Resource filters:</span></span>

* <span data-ttu-id="a10a5-329">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="a10a5-330">Provádění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="a10a5-331">Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .</span><span class="sxs-lookup"><span data-stu-id="a10a5-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="a10a5-332">Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="a10a5-333">Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="a10a5-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="a10a5-334">Příklady filtru prostředků:</span><span class="sxs-lookup"><span data-stu-id="a10a5-334">Resource filter examples:</span></span>

* <span data-ttu-id="a10a5-335">[Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.</span><span class="sxs-lookup"><span data-stu-id="a10a5-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="a10a5-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="a10a5-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="a10a5-337">Zabraňuje vazbě modelu v přístupu k datům formuláře.</span><span class="sxs-lookup"><span data-stu-id="a10a5-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="a10a5-338">Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.</span><span class="sxs-lookup"><span data-stu-id="a10a5-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="a10a5-339">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="a10a5-339">Action filters</span></span>

<span data-ttu-id="a10a5-340">Filtry **akcí se nevztahují** na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="a10a5-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="a10a5-341">Stránky podporují <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="a10a5-342">Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="a10a5-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="a10a5-343">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="a10a5-343">Action filters:</span></span>

* <span data-ttu-id="a10a5-344">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="a10a5-345">Jejich spuštění obklopuje provádění metod akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="a10a5-346">Následující kód ukazuje filtr vzorové akce:</span><span class="sxs-lookup"><span data-stu-id="a10a5-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="a10a5-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="a10a5-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="a10a5-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>– povolí čtení vstupů do metody Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="a10a5-349"><xref:Microsoft.AspNetCore.Mvc.Controller>– povolí manipulaci s instancí kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="a10a5-350"><xref:System.Web.Mvc.ActionExecutingContext.Result>– nastavení `Result` krátkodobého provádění metody akce a následných filtrů akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="a10a5-351">Vyvolání výjimky v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="a10a5-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="a10a5-352">Zabraňuje spuštění dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="a10a5-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="a10a5-353">Na rozdíl od nastavení `Result` se místo úspěšného výsledku považuje za selhání.</span><span class="sxs-lookup"><span data-stu-id="a10a5-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="a10a5-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Poskytuje `Controller` a `Result` plus následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="a10a5-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="a10a5-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="a10a5-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="a10a5-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Hodnota není null, pokud akce nebo dříve vyvolala filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="a10a5-357">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="a10a5-357">Setting this property to null:</span></span>

  * <span data-ttu-id="a10a5-358">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="a10a5-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="a10a5-359">`Result`je spuštěn, jako kdyby byl vrácen z metody Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="a10a5-360">V případě `IAsyncActionFilter` volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="a10a5-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="a10a5-361">Provede všechny následné filtry akcí a metodu Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="a10a5-362">Vrací objekt `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="a10a5-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="a10a5-363">Do krátkodobého okruhu, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> k instanci výsledku a Nevolejte `next` (a `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="a10a5-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="a10a5-364">Rozhraní poskytuje abstrakci <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="a10a5-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="a10a5-365">`OnActionExecuting`Filtr akcí lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="a10a5-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="a10a5-366">Ověří stav modelu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-366">Validate model state.</span></span>
* <span data-ttu-id="a10a5-367">Pokud je stav neplatný, vrátí se chyba.</span><span class="sxs-lookup"><span data-stu-id="a10a5-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="a10a5-368">`OnActionExecuted`Metoda se spustí za metodou akce:</span><span class="sxs-lookup"><span data-stu-id="a10a5-368">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="a10a5-369">A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a10a5-369">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="a10a5-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>je nastaven na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="a10a5-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="a10a5-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="a10a5-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="a10a5-372">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="a10a5-372">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="a10a5-373">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="a10a5-373">Effectively handles an exception.</span></span>
  * <span data-ttu-id="a10a5-374">`ActionExecutedContext.Result`je spuštěn, jako kdyby byl vrácen normálně z metody Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-374">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="a10a5-375">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="a10a5-375">Exception filters</span></span>

<span data-ttu-id="a10a5-376">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="a10a5-376">Exception filters:</span></span>

* <span data-ttu-id="a10a5-377">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-377">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="a10a5-378">Dá se použít k implementaci běžných zásad zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="a10a5-378">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="a10a5-379">Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:</span><span class="sxs-lookup"><span data-stu-id="a10a5-379">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="a10a5-380">Následující kód testuje filtr výjimky:</span><span class="sxs-lookup"><span data-stu-id="a10a5-380">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="a10a5-381">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="a10a5-381">Exception filters:</span></span>

* <span data-ttu-id="a10a5-382">Nemusíte mít události před a po.</span><span class="sxs-lookup"><span data-stu-id="a10a5-382">Don't have before and after events.</span></span>
* <span data-ttu-id="a10a5-383">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-383">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="a10a5-384">Zpracování neošetřených výjimek, ke kterým dochází při Razor vytváření stránky nebo kontroleru, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-384">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="a10a5-385">**Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.</span><span class="sxs-lookup"><span data-stu-id="a10a5-385">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="a10a5-386">Chcete-li zpracovat výjimku, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost na `true` nebo zapište odpověď.</span><span class="sxs-lookup"><span data-stu-id="a10a5-386">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="a10a5-387">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="a10a5-387">This stops propagation of the exception.</span></span> <span data-ttu-id="a10a5-388">Filtr výjimek nemůže vypnout výjimku na "úspěch".</span><span class="sxs-lookup"><span data-stu-id="a10a5-388">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="a10a5-389">To může provést pouze filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-389">Only an action filter can do that.</span></span>

<span data-ttu-id="a10a5-390">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="a10a5-390">Exception filters:</span></span>

* <span data-ttu-id="a10a5-391">Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-391">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="a10a5-392">Nejsou tak flexibilní jako middleware při zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="a10a5-392">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="a10a5-393">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="a10a5-393">Prefer middleware for exception handling.</span></span> <span data-ttu-id="a10a5-394">Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána.</span><span class="sxs-lookup"><span data-stu-id="a10a5-394">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="a10a5-395">Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML.</span><span class="sxs-lookup"><span data-stu-id="a10a5-395">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="a10a5-396">Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.</span><span class="sxs-lookup"><span data-stu-id="a10a5-396">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="a10a5-397">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="a10a5-397">Result filters</span></span>

<span data-ttu-id="a10a5-398">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="a10a5-398">Result filters:</span></span>

* <span data-ttu-id="a10a5-399">Implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="a10a5-399">Implement an interface:</span></span>
  * <span data-ttu-id="a10a5-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="a10a5-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="a10a5-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="a10a5-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="a10a5-402">Jejich spuštění obklopuje provádění výsledků akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-402">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="a10a5-403">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="a10a5-403">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="a10a5-404">Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="a10a5-404">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="a10a5-405">Typ výsledku, který se má provést, závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="a10a5-405">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="a10a5-406">Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást prováděné <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-406">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="a10a5-407">Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku.</span><span class="sxs-lookup"><span data-stu-id="a10a5-407">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="a10a5-408">Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="a10a5-408">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="a10a5-409">Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-409">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="a10a5-410">Filtry výsledků nejsou provedeny v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="a10a5-410">Result filters are not executed when:</span></span>

* <span data-ttu-id="a10a5-411">Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-411">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="a10a5-412">Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-412">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="a10a5-413"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda může zkrátit provádění výsledků akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-413">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="a10a5-414">Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a10a5-414">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="a10a5-415">Vyvolání výjimky v `IResultFilter.OnResultExecuting` :</span><span class="sxs-lookup"><span data-stu-id="a10a5-415">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="a10a5-416">Zabraňuje spuštění výsledků akce a následných filtrů.</span><span class="sxs-lookup"><span data-stu-id="a10a5-416">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="a10a5-417">Se považuje za selhání namísto úspěšného výsledku.</span><span class="sxs-lookup"><span data-stu-id="a10a5-417">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="a10a5-418">Při <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> spuštění metody je odpověď pravděpodobně již odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="a10a5-418">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="a10a5-419">Pokud byla odpověď již odeslána klientovi, nelze ji změnit.</span><span class="sxs-lookup"><span data-stu-id="a10a5-419">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="a10a5-420">`ResultExecutedContext.Canceled`je nastaven na hodnotu, `true` Pokud je spuštění výsledku akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="a10a5-420">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="a10a5-421">`ResultExecutedContext.Exception`je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku.</span><span class="sxs-lookup"><span data-stu-id="a10a5-421">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="a10a5-422">Nastavení `Exception` na hodnotu null efektivně zpracuje výjimku a zabrání vyvolání výjimky později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-422">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="a10a5-423">Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="a10a5-423">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="a10a5-424">Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.</span><span class="sxs-lookup"><span data-stu-id="a10a5-424">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="a10a5-425">V případě <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> volání `await next` na se <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné filtry výsledků a výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-425">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="a10a5-426">Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a Nevolejte na `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="a10a5-426">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="a10a5-427">Rozhraní poskytuje abstrakci `ResultFilterAttribute` , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="a10a5-427">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="a10a5-428">Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="a10a5-428">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="a10a5-429">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="a10a5-429">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="a10a5-430"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Rozhraní a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklaruje <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která je spuštěna pro všechny výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-430">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="a10a5-431">To zahrnuje výsledky akcí, které vytvořil:</span><span class="sxs-lookup"><span data-stu-id="a10a5-431">This includes action results produced by:</span></span>

* <span data-ttu-id="a10a5-432">Filtry autorizace a filtry prostředků, které mají krátký okruh.</span><span class="sxs-lookup"><span data-stu-id="a10a5-432">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="a10a5-433">Filtry výjimek.</span><span class="sxs-lookup"><span data-stu-id="a10a5-433">Exception filters.</span></span>

<span data-ttu-id="a10a5-434">Například následující filtr vždy spustí a nastaví výsledek akce ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="a10a5-434">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="a10a5-435">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="a10a5-435">IFilterFactory</span></span>

<span data-ttu-id="a10a5-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="a10a5-437">Proto `IFilterFactory` může být instance použita jako `IFilterMetadata` instance kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-437">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="a10a5-438">Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-438">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="a10a5-439">Pokud je toto přetypování úspěšné, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> je volána metoda pro vytvoření `IFilterMetadata` instance, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="a10a5-439">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="a10a5-440">To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="a10a5-440">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="a10a5-441">`IFilterFactory`může být implementováno pomocí implementace vlastního atributu jako jiný přístup k vytváření filtrů:</span><span class="sxs-lookup"><span data-stu-id="a10a5-441">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="a10a5-442">Filtr je použit v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="a10a5-442">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="a10a5-443">Otestujte předchozí kód spuštěním [ukázky stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="a10a5-443">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="a10a5-444">Vyvolejte vývojářské nástroje F12.</span><span class="sxs-lookup"><span data-stu-id="a10a5-444">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="a10a5-445">Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="a10a5-445">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="a10a5-446">Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:</span><span class="sxs-lookup"><span data-stu-id="a10a5-446">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="a10a5-447">**Autor:**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="a10a5-447">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="a10a5-448">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="a10a5-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="a10a5-449">**interní:**`My header`</span><span class="sxs-lookup"><span data-stu-id="a10a5-449">**internal:** `My header`</span></span>

<span data-ttu-id="a10a5-450">Předchozí kód vytvoří hlavičku **interního:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="a10a5-450">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="a10a5-451">IFilterFactory implementované u atributu</span><span class="sxs-lookup"><span data-stu-id="a10a5-451">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="a10a5-452">Filtry, které implementují, `IFilterFactory` jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="a10a5-452">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="a10a5-453">Nevyžadovat předávání parametrů</span><span class="sxs-lookup"><span data-stu-id="a10a5-453">Don't require passing parameters.</span></span>
* <span data-ttu-id="a10a5-454">Musí mít závislosti konstruktoru, které musí vyplnit DI.</span><span class="sxs-lookup"><span data-stu-id="a10a5-454">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="a10a5-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="a10a5-456">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="a10a5-456">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="a10a5-457">`CreateInstance`Načte zadaný typ z kontejneru Services (DI).</span><span class="sxs-lookup"><span data-stu-id="a10a5-457">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="a10a5-458">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="a10a5-458">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="a10a5-459">V předchozím kódu upravení metoda s jako `[SampleActionFilter]` upřednostňovaným přístupem k použití `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-459">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="a10a5-460">Použití middlewaru v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="a10a5-460">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="a10a5-461">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-461">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="a10a5-462">Filtry se ale liší od middlewaru v tom, že jsou součástí modulu runtime, což znamená, že mají přístup k kontextu a sestavám.</span><span class="sxs-lookup"><span data-stu-id="a10a5-462">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="a10a5-463">Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-463">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="a10a5-464">Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:</span><span class="sxs-lookup"><span data-stu-id="a10a5-464">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="a10a5-465">Pomocí <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Spusťte middleware:</span><span class="sxs-lookup"><span data-stu-id="a10a5-465">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="a10a5-466">Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-466">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="a10a5-467">Další akce</span><span class="sxs-lookup"><span data-stu-id="a10a5-467">Next actions</span></span>

* <span data-ttu-id="a10a5-468">Viz [metody filtru pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="a10a5-468">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="a10a5-469">Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="a10a5-469">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a10a5-470">[Kirka Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra/)a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a10a5-470">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a10a5-471">*Filtry* v ASP.NET Core umožňují spuštění kódu před nebo po určitých fázích v kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="a10a5-471">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="a10a5-472">Předdefinované filtry zpracovávají úlohy, jako například:</span><span class="sxs-lookup"><span data-stu-id="a10a5-472">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="a10a5-473">Autorizace (zabránění přístupu k prostředkům, ke kterým uživatel nemá oprávnění).</span><span class="sxs-lookup"><span data-stu-id="a10a5-473">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="a10a5-474">Ukládání odpovědí do mezipaměti (při krátkém okruhu kanálu požadavků, který vrátí odpověď uloženou v mezipaměti).</span><span class="sxs-lookup"><span data-stu-id="a10a5-474">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="a10a5-475">Vlastní filtry je možné vytvořit pro zpracování vzájemně se týkajících se otázek.</span><span class="sxs-lookup"><span data-stu-id="a10a5-475">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="a10a5-476">Mezi obavy mezi průřezy patří zpracování chyb, ukládání do mezipaměti, konfigurace, autorizace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="a10a5-476">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="a10a5-477">Filtry zabraňují duplikování kódu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-477">Filters avoid duplicating code.</span></span> <span data-ttu-id="a10a5-478">Například filtr výjimek zpracování chyb může konsolidovat zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="a10a5-478">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="a10a5-479">Tento dokument se týká Razor stránek, řadičů rozhraní API a řadičů se zobrazeními.</span><span class="sxs-lookup"><span data-stu-id="a10a5-479">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="a10a5-480">[Zobrazit nebo Stáhnout ukázku](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a10a5-480">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="a10a5-481">Jak fungují filtry</span><span class="sxs-lookup"><span data-stu-id="a10a5-481">How filters work</span></span>

<span data-ttu-id="a10a5-482">Filtry se spouštějí v *kanálu vyvolání akce ASP.NET Core*, někdy označované jako *kanál filtru*.</span><span class="sxs-lookup"><span data-stu-id="a10a5-482">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="a10a5-483">Kanál filtru se spustí po ASP.NET Core vybere akci, která se má provést.</span><span class="sxs-lookup"><span data-stu-id="a10a5-483">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![Požadavek se zpracovává prostřednictvím jiného middlewaru, middleware směrování, výběru akcí a kanálu vyvolání akce ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="a10a5-486">Typy filtrů</span><span class="sxs-lookup"><span data-stu-id="a10a5-486">Filter types</span></span>

<span data-ttu-id="a10a5-487">Každý typ filtru se spustí v jiné fázi kanálu filtru:</span><span class="sxs-lookup"><span data-stu-id="a10a5-487">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="a10a5-488">[Filtry autorizace](#authorization-filters) se spouštějí jako první a používají se k určení, jestli je uživatel autorizovaný pro požadavek.</span><span class="sxs-lookup"><span data-stu-id="a10a5-488">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="a10a5-489">Filtry autorizace pro krátkodobé vynechání kanálu, pokud je požadavek neautorizovaný</span><span class="sxs-lookup"><span data-stu-id="a10a5-489">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="a10a5-490">[Filtry prostředků](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="a10a5-490">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="a10a5-491">Spusťte po autorizaci.</span><span class="sxs-lookup"><span data-stu-id="a10a5-491">Run after authorization.</span></span>  
  * <span data-ttu-id="a10a5-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*>může spustit kód před zbytek kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="a10a5-493">Například `OnResourceExecuting` může spustit kód před vytvořením vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-493">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="a10a5-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*>může spustit kód po dokončení zbývající části kanálu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="a10a5-495">[Filtry akcí](#action-filters) mohou spustit kód bezprostředně před a po volání jednotlivé metody akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-495">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="a10a5-496">Lze je použít k manipulaci s argumenty předaných do akce a výsledek vráceného z akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-496">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="a10a5-497">Na stránkách se **nepodporují filtry** akcí Razor .</span><span class="sxs-lookup"><span data-stu-id="a10a5-497">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="a10a5-498">[Filtry výjimek](#exception-filters) slouží k aplikování globálních zásad na neošetřené výjimky, ke kterým dojde před zapsáním cokoli do těla odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a10a5-498">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="a10a5-499">[Filtry výsledků](#result-filters) můžou spustit kód hned před a po provedení jednotlivých výsledků akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-499">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="a10a5-500">Spustí se pouze v případě, že metoda akce byla úspěšně provedena.</span><span class="sxs-lookup"><span data-stu-id="a10a5-500">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="a10a5-501">Jsou užitečné pro logiku, která musí obklopit zobrazení nebo formátovací modul.</span><span class="sxs-lookup"><span data-stu-id="a10a5-501">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="a10a5-502">Následující diagram znázorňuje způsob interakce typů filtrů v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-502">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![Požadavek se zpracovává pomocí autorizačních filtrů, filtrů prostředků, vazeb modelů, filtrů akcí, provádění akcí a konverze výsledků akcí, filtrů výjimek, výsledných filtrů a provádění výsledků.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="a10a5-505">Implementace</span><span class="sxs-lookup"><span data-stu-id="a10a5-505">Implementation</span></span>

<span data-ttu-id="a10a5-506">Filtry podporují synchronní i asynchronní implementace prostřednictvím různých definic rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a10a5-506">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="a10a5-507">Synchronní filtry mohou spustit kód před ( `On-Stage-Executing` ) a po ( `On-Stage-Executed` ) jejich fáze zřetězení.</span><span class="sxs-lookup"><span data-stu-id="a10a5-507">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="a10a5-508">Například `OnActionExecuting` je volána před voláním metody Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-508">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="a10a5-509">`OnActionExecuted`je volána po návratu metody Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-509">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="a10a5-510">Asynchronní filtry definují `On-Stage-ExecutionAsync` metodu:</span><span class="sxs-lookup"><span data-stu-id="a10a5-510">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="a10a5-511">V předchozím kódu `SampleAsyncActionFilter` obsahuje znak <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> ( `next` ), který provádí metodu Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-511">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="a10a5-512">Každá z těchto `On-Stage-ExecutionAsync` metod vezme `FilterType-ExecutionDelegate` fázi zřetězení filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-512">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="a10a5-513">Několik fází filtru</span><span class="sxs-lookup"><span data-stu-id="a10a5-513">Multiple filter stages</span></span>

<span data-ttu-id="a10a5-514">Rozhraní pro více fází filtru lze implementovat v rámci jedné třídy.</span><span class="sxs-lookup"><span data-stu-id="a10a5-514">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="a10a5-515">Například <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> Třída implementuje `IActionFilter` , `IResultFilter` a jejich asynchronní ekvivalenty.</span><span class="sxs-lookup"><span data-stu-id="a10a5-515">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="a10a5-516">Implementujte **buď** synchronní, nebo asynchronní verzi rozhraní filtru, **nikoli** obojí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-516">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="a10a5-517">Modul runtime nejprve kontroluje, zda filtr implementuje asynchronní rozhraní a v takovém případě jej zavolá.</span><span class="sxs-lookup"><span data-stu-id="a10a5-517">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="a10a5-518">V takovém případě volá metody synchronního rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a10a5-518">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="a10a5-519">Pokud jsou asynchronní i synchronní rozhraní implementovány v jedné třídě, je volána pouze asynchronní metoda.</span><span class="sxs-lookup"><span data-stu-id="a10a5-519">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="a10a5-520">Při použití abstraktních tříd, jako <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> je například přepsání pouze synchronních metod nebo asynchronní metody pro každý typ filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-520">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="a10a5-521">Předdefinované atributy filtru</span><span class="sxs-lookup"><span data-stu-id="a10a5-521">Built-in filter attributes</span></span>

<span data-ttu-id="a10a5-522">ASP.NET Core obsahuje integrované filtry založené na atributech, které mohou být roztříděné a přizpůsobené.</span><span class="sxs-lookup"><span data-stu-id="a10a5-522">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="a10a5-523">Například následující filtr výsledků přidá hlavičku do odpovědi:</span><span class="sxs-lookup"><span data-stu-id="a10a5-523">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="a10a5-524">Atributy umožňují filtrům přijímat argumenty, jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-524">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="a10a5-525">Použijte `AddHeaderAttribute` pro metodu Controller nebo Action a zadejte název a hodnotu HLAVIČKY http:</span><span class="sxs-lookup"><span data-stu-id="a10a5-525">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="a10a5-526">Některé z rozhraní filtru mají odpovídající atributy, které lze použít jako základní třídy pro vlastní implementace.</span><span class="sxs-lookup"><span data-stu-id="a10a5-526">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="a10a5-527">Atributy filtru:</span><span class="sxs-lookup"><span data-stu-id="a10a5-527">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="a10a5-528">Filtrovat obory a pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="a10a5-528">Filter scopes and order of execution</span></span>

<span data-ttu-id="a10a5-529">Filtr lze přidat do kanálu v jednom ze tří *oborů*:</span><span class="sxs-lookup"><span data-stu-id="a10a5-529">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="a10a5-530">Použití atributu pro akci.</span><span class="sxs-lookup"><span data-stu-id="a10a5-530">Using an attribute on an action.</span></span>
* <span data-ttu-id="a10a5-531">Použití atributu na řadiči.</span><span class="sxs-lookup"><span data-stu-id="a10a5-531">Using an attribute on a controller.</span></span>
* <span data-ttu-id="a10a5-532">Globálně pro všechny řadiče a akce, jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="a10a5-532">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a10a5-533">Předchozí kód přičítá tři filtry globálně pomocí kolekce [MvcOptions. filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) .</span><span class="sxs-lookup"><span data-stu-id="a10a5-533">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="a10a5-534">Výchozí pořadí provádění</span><span class="sxs-lookup"><span data-stu-id="a10a5-534">Default order of execution</span></span>

<span data-ttu-id="a10a5-535">Pokud existuje více filtrů *stejného typu*, určuje obor výchozí pořadí provádění filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-535">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="a10a5-536">Globální filtry filtr tříd Surround.</span><span class="sxs-lookup"><span data-stu-id="a10a5-536">Global filters surround class filters.</span></span> <span data-ttu-id="a10a5-537">Filtry tříd obklopují filtry metod Surround.</span><span class="sxs-lookup"><span data-stu-id="a10a5-537">Class filters surround method filters.</span></span>

<span data-ttu-id="a10a5-538">V důsledku vnořování filtru je *po* kódu spuštěn v obráceném pořadí *před* kódem.</span><span class="sxs-lookup"><span data-stu-id="a10a5-538">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="a10a5-539">Pořadí filtru:</span><span class="sxs-lookup"><span data-stu-id="a10a5-539">The filter sequence:</span></span>

* <span data-ttu-id="a10a5-540">*Před* kódem globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="a10a5-540">The *before* code of global filters.</span></span>
  * <span data-ttu-id="a10a5-541">*Před* kódem filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-541">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="a10a5-542">Kód *před* filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-542">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="a10a5-543">*Po* kódu filtry metody Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-543">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="a10a5-544">*Po* kódu filtry kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-544">The *after* code of controller filters.</span></span>
* <span data-ttu-id="a10a5-545">*Po* kódu globálních filtrů.</span><span class="sxs-lookup"><span data-stu-id="a10a5-545">The *after* code of global filters.</span></span>
  
<span data-ttu-id="a10a5-546">Následující příklad ilustruje pořadí, ve kterém jsou metody filtru volány pro filtry synchronních akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-546">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="a10a5-547">Sequence</span><span class="sxs-lookup"><span data-stu-id="a10a5-547">Sequence</span></span> | <span data-ttu-id="a10a5-548">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="a10a5-548">Filter scope</span></span> | <span data-ttu-id="a10a5-549">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="a10a5-549">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="a10a5-550">1</span><span class="sxs-lookup"><span data-stu-id="a10a5-550">1</span></span> | <span data-ttu-id="a10a5-551">Globální</span><span class="sxs-lookup"><span data-stu-id="a10a5-551">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a10a5-552">2</span><span class="sxs-lookup"><span data-stu-id="a10a5-552">2</span></span> | <span data-ttu-id="a10a5-553">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="a10a5-553">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a10a5-554">3</span><span class="sxs-lookup"><span data-stu-id="a10a5-554">3</span></span> | <span data-ttu-id="a10a5-555">Metoda</span><span class="sxs-lookup"><span data-stu-id="a10a5-555">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a10a5-556">4</span><span class="sxs-lookup"><span data-stu-id="a10a5-556">4</span></span> | <span data-ttu-id="a10a5-557">Metoda</span><span class="sxs-lookup"><span data-stu-id="a10a5-557">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="a10a5-558">5</span><span class="sxs-lookup"><span data-stu-id="a10a5-558">5</span></span> | <span data-ttu-id="a10a5-559">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="a10a5-559">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="a10a5-560">6</span><span class="sxs-lookup"><span data-stu-id="a10a5-560">6</span></span> | <span data-ttu-id="a10a5-561">Globální</span><span class="sxs-lookup"><span data-stu-id="a10a5-561">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="a10a5-562">Tato posloupnost zobrazuje:</span><span class="sxs-lookup"><span data-stu-id="a10a5-562">This sequence shows:</span></span>

* <span data-ttu-id="a10a5-563">Filtr metod je vnořený do filtru kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-563">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="a10a5-564">Filtr kontroleru je vnořený do globálního filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-564">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="a10a5-565">Filtry na Razor úrovni řadiče a stránky</span><span class="sxs-lookup"><span data-stu-id="a10a5-565">Controller and Razor Page level filters</span></span>

<span data-ttu-id="a10a5-566">Každý kontroler, který dědí ze <xref:Microsoft.AspNetCore.Mvc.Controller> základní třídy, zahrnuje metody [Controller. OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller. OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*)a [Controller. OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*) 
 `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-566">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="a10a5-567">Tyto metody:</span><span class="sxs-lookup"><span data-stu-id="a10a5-567">These methods:</span></span>

* <span data-ttu-id="a10a5-568">Zabalte filtry, které se spouštějí pro danou akci.</span><span class="sxs-lookup"><span data-stu-id="a10a5-568">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="a10a5-569">`OnActionExecuting`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-569">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="a10a5-570">`OnActionExecuted`se volá po všech filtrech akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-570">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="a10a5-571">`OnActionExecutionAsync`je volána před jakýmkoli filtrem akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-571">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="a10a5-572">Kód v filtru po `next` spuštění po metodě Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-572">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="a10a5-573">Například v ukázce ke stažení `MySampleActionFilter` se použije globálně při spuštění.</span><span class="sxs-lookup"><span data-stu-id="a10a5-573">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="a10a5-574">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="a10a5-574">The `TestController`:</span></span>

* <span data-ttu-id="a10a5-575">Aplikuje `SampleActionFilterAttribute` ( `[SampleActionFilter]` ) na `FilterTest2` akci.</span><span class="sxs-lookup"><span data-stu-id="a10a5-575">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="a10a5-576">Přepisuje `OnActionExecuting` a `OnActionExecuted` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-576">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="a10a5-577">Navigace pro `https://localhost:5001/Test/FilterTest2` spustí následující kód:</span><span class="sxs-lookup"><span data-stu-id="a10a5-577">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="a10a5-578">RazorInformace o stránkách naleznete v tématu [implementace Razor filtrů stránek pomocí přepsání metod filtru](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="a10a5-578">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="a10a5-579">Přepsání výchozího pořadí</span><span class="sxs-lookup"><span data-stu-id="a10a5-579">Overriding the default order</span></span>

<span data-ttu-id="a10a5-580">Výchozí sekvenci spuštění lze přepsat implementací <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-580">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="a10a5-581">`IOrderedFilter`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> vlastnost, která má přednost před rozsahem, aby určila pořadí provádění.</span><span class="sxs-lookup"><span data-stu-id="a10a5-581">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="a10a5-582">Filtr s nižší `Order` hodnotou:</span><span class="sxs-lookup"><span data-stu-id="a10a5-582">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="a10a5-583">Spustí *před* kódem filtr s vyšší hodnotou `Order` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-583">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="a10a5-584">Spustí *za* kódem za filtr s vyšší `Order` hodnotou.</span><span class="sxs-lookup"><span data-stu-id="a10a5-584">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="a10a5-585">`Order`Vlastnost lze nastavit s parametrem konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="a10a5-585">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="a10a5-586">Vezměte v úvahu stejné 3 filtry akcí, které jsou uvedené v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-586">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="a10a5-587">Pokud `Order` je vlastnost Controller a globální filtry nastavená na 1 a 2 v uvedeném pořadí, pořadí spouštění se vrátí zpět.</span><span class="sxs-lookup"><span data-stu-id="a10a5-587">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="a10a5-588">Sequence</span><span class="sxs-lookup"><span data-stu-id="a10a5-588">Sequence</span></span> | <span data-ttu-id="a10a5-589">Rozsah filtru</span><span class="sxs-lookup"><span data-stu-id="a10a5-589">Filter scope</span></span> | <span data-ttu-id="a10a5-590">`Order`majetek</span><span class="sxs-lookup"><span data-stu-id="a10a5-590">`Order` property</span></span> | <span data-ttu-id="a10a5-591">Filter – metoda</span><span class="sxs-lookup"><span data-stu-id="a10a5-591">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="a10a5-592">1</span><span class="sxs-lookup"><span data-stu-id="a10a5-592">1</span></span> | <span data-ttu-id="a10a5-593">Metoda</span><span class="sxs-lookup"><span data-stu-id="a10a5-593">Method</span></span> | <span data-ttu-id="a10a5-594">0</span><span class="sxs-lookup"><span data-stu-id="a10a5-594">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a10a5-595">2</span><span class="sxs-lookup"><span data-stu-id="a10a5-595">2</span></span> | <span data-ttu-id="a10a5-596">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="a10a5-596">Controller</span></span> | <span data-ttu-id="a10a5-597">1</span><span class="sxs-lookup"><span data-stu-id="a10a5-597">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="a10a5-598">3</span><span class="sxs-lookup"><span data-stu-id="a10a5-598">3</span></span> | <span data-ttu-id="a10a5-599">Globální</span><span class="sxs-lookup"><span data-stu-id="a10a5-599">Global</span></span> | <span data-ttu-id="a10a5-600">2</span><span class="sxs-lookup"><span data-stu-id="a10a5-600">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="a10a5-601">4</span><span class="sxs-lookup"><span data-stu-id="a10a5-601">4</span></span> | <span data-ttu-id="a10a5-602">Globální</span><span class="sxs-lookup"><span data-stu-id="a10a5-602">Global</span></span> | <span data-ttu-id="a10a5-603">2</span><span class="sxs-lookup"><span data-stu-id="a10a5-603">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="a10a5-604">5</span><span class="sxs-lookup"><span data-stu-id="a10a5-604">5</span></span> | <span data-ttu-id="a10a5-605">Kontrolér</span><span class="sxs-lookup"><span data-stu-id="a10a5-605">Controller</span></span> | <span data-ttu-id="a10a5-606">1</span><span class="sxs-lookup"><span data-stu-id="a10a5-606">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="a10a5-607">6</span><span class="sxs-lookup"><span data-stu-id="a10a5-607">6</span></span> | <span data-ttu-id="a10a5-608">Metoda</span><span class="sxs-lookup"><span data-stu-id="a10a5-608">Method</span></span> | <span data-ttu-id="a10a5-609">0</span><span class="sxs-lookup"><span data-stu-id="a10a5-609">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="a10a5-610">`Order`Vlastnost Přepisuje obor při určování pořadí, ve kterém jsou filtry spouštěny.</span><span class="sxs-lookup"><span data-stu-id="a10a5-610">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="a10a5-611">Filtry jsou seřazené podle pořadí, pak se k přerušení vztahů používá obor.</span><span class="sxs-lookup"><span data-stu-id="a10a5-611">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="a10a5-612">Všechny předdefinované filtry implementují `IOrderedFilter` a nastaví výchozí `Order` hodnotu na 0.</span><span class="sxs-lookup"><span data-stu-id="a10a5-612">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="a10a5-613">Pro předdefinované filtry rozsah určuje pořadí, pokud `Order` není nastaven na nenulovou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-613">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="a10a5-614">Zrušení a zkrácení okruhů</span><span class="sxs-lookup"><span data-stu-id="a10a5-614">Cancellation and short-circuiting</span></span>

<span data-ttu-id="a10a5-615">Kanál filtru může být v krátkém okruhu nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> vlastnosti pro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parametr poskytnutý metodě Filter.</span><span class="sxs-lookup"><span data-stu-id="a10a5-615">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="a10a5-616">Například následující filtr prostředků zabrání ve spuštění zbývajícího kanálu:</span><span class="sxs-lookup"><span data-stu-id="a10a5-616">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="a10a5-617">V následujícím kódu, `ShortCircuitingResourceFilter` a jako `AddHeader` cíl filtru i cílovou `SomeResource` metodu Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-617">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="a10a5-618">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="a10a5-618">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="a10a5-619">Nejprve se spustí, protože se jedná o filtr prostředků a `AddHeader` je filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-619">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="a10a5-620">Krátké okruhy zbývajícího kanálu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-620">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="a10a5-621">Proto `AddHeader` Filtr pro akci nikdy neběží `SomeResource` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-621">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="a10a5-622">Toto chování by bylo stejné, pokud byly oba filtry aplikovány na úrovni metody akce, a to za předpokladu, že byla `ShortCircuitingResourceFilter` spuštěna jako první.</span><span class="sxs-lookup"><span data-stu-id="a10a5-622">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="a10a5-623">`ShortCircuitingResourceFilter`Nejprve se spustí z důvodu jeho typu filtru nebo explicitního použití `Order` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a10a5-623">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="a10a5-624">Injektáž závislosti</span><span class="sxs-lookup"><span data-stu-id="a10a5-624">Dependency injection</span></span>

<span data-ttu-id="a10a5-625">Filtry lze přidat podle typu nebo podle instance.</span><span class="sxs-lookup"><span data-stu-id="a10a5-625">Filters can be added by type or by instance.</span></span> <span data-ttu-id="a10a5-626">Pokud je přidána instance, bude tato instance použita pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="a10a5-626">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="a10a5-627">Pokud je přidán typ, je aktivován typ.</span><span class="sxs-lookup"><span data-stu-id="a10a5-627">If a type is added, it's type-activated.</span></span> <span data-ttu-id="a10a5-628">Filtr aktivovaný typu znamená:</span><span class="sxs-lookup"><span data-stu-id="a10a5-628">A type-activated filter means:</span></span>

* <span data-ttu-id="a10a5-629">Instance se vytvoří pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="a10a5-629">An instance is created for each request.</span></span>
* <span data-ttu-id="a10a5-630">Jakékoli závislosti konstruktoru jsou vyplněny pomocí [Injektáže závislosti](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="a10a5-630">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="a10a5-631">Filtry, které jsou implementovány jako atributy a přidány přímo do tříd kontroleru nebo metody akcí, nemohou mít závislosti konstruktoru poskytované [vkládáním závislostí](xref:fundamentals/dependency-injection) (di).</span><span class="sxs-lookup"><span data-stu-id="a10a5-631">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="a10a5-632">Pomocí DI nelze poskytnout závislosti konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="a10a5-632">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="a10a5-633">V atributech musí být zadány parametry konstruktoru, kde jsou aplikovány.</span><span class="sxs-lookup"><span data-stu-id="a10a5-633">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="a10a5-634">Toto je omezení způsobu fungování atributů.</span><span class="sxs-lookup"><span data-stu-id="a10a5-634">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="a10a5-635">Následující filtry podporují závislosti konstruktoru poskytované od DI:</span><span class="sxs-lookup"><span data-stu-id="a10a5-635">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="a10a5-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementováno u atributu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="a10a5-637">Předchozí filtry lze použít pro metodu kontroleru nebo akce:</span><span class="sxs-lookup"><span data-stu-id="a10a5-637">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="a10a5-638">Protokolovací nástroje jsou k dispozici z DI.</span><span class="sxs-lookup"><span data-stu-id="a10a5-638">Loggers are available from DI.</span></span> <span data-ttu-id="a10a5-639">Ale nevytvářejte a používejte filtry čistě pro účely protokolování.</span><span class="sxs-lookup"><span data-stu-id="a10a5-639">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="a10a5-640">[Integrované protokolování rozhraní](xref:fundamentals/logging/index) obvykle zajišťuje, co je potřeba pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="a10a5-640">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="a10a5-641">Protokolování přidáno do filtrů:</span><span class="sxs-lookup"><span data-stu-id="a10a5-641">Logging added to filters:</span></span>

* <span data-ttu-id="a10a5-642">Měl by se soustředit na obavy a chování obchodních domén, které jsou specifické pro filtr.</span><span class="sxs-lookup"><span data-stu-id="a10a5-642">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="a10a5-643">Neměli **byste** protokolovat akce nebo jiné události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a10a5-643">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="a10a5-644">Vestavěné filtry protokolují akce a události rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a10a5-644">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="a10a5-645">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="a10a5-645">ServiceFilterAttribute</span></span>

<span data-ttu-id="a10a5-646">Typy implementace filtru služby jsou zaregistrované v `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-646">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="a10a5-647"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>Načte instanci filtru z di.</span><span class="sxs-lookup"><span data-stu-id="a10a5-647">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="a10a5-648">Následující kód ukazuje `AddHeaderResultServiceFilter` :</span><span class="sxs-lookup"><span data-stu-id="a10a5-648">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="a10a5-649">V následujícím kódu `AddHeaderResultServiceFilter` je přidán do kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="a10a5-649">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="a10a5-650">V následujícím kódu `ServiceFilter` atribut načte instanci `AddHeaderResultServiceFilter` filtru z di:</span><span class="sxs-lookup"><span data-stu-id="a10a5-650">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="a10a5-651">Při použití `ServiceFilterAttribute` nastavte [ServiceFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="a10a5-651">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="a10a5-652">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="a10a5-652">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="a10a5-653">Modul runtime ASP.NET Core nezaručuje:</span><span class="sxs-lookup"><span data-stu-id="a10a5-653">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="a10a5-654">Vytvoří se jedna instance filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-654">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="a10a5-655">Filtr nebude znovu vyžádán z kontejneru DI v pozdějším bodě.</span><span class="sxs-lookup"><span data-stu-id="a10a5-655">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="a10a5-656">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="a10a5-656">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="a10a5-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="a10a5-658">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="a10a5-658">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="a10a5-659">`CreateInstance`Načte zadaný typ z DI.</span><span class="sxs-lookup"><span data-stu-id="a10a5-659">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="a10a5-660">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="a10a5-660">TypeFilterAttribute</span></span>

<span data-ttu-id="a10a5-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>je podobný <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> , ale jeho typ není vyřešen přímo z kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="a10a5-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="a10a5-662">Vytvoří instanci typu pomocí <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-662">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="a10a5-663">Protože `TypeFilterAttribute` typy nejsou vyřešeny přímo z kontejneru di:</span><span class="sxs-lookup"><span data-stu-id="a10a5-663">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="a10a5-664">Typy, na které se odkazuje pomocí, `TypeFilterAttribute` není nutné registrovat v kontejneru di.</span><span class="sxs-lookup"><span data-stu-id="a10a5-664">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="a10a5-665">Mají své závislosti splněné kontejnerem DI.</span><span class="sxs-lookup"><span data-stu-id="a10a5-665">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="a10a5-666">`TypeFilterAttribute`může volitelně přijmout argumenty konstruktoru pro typ.</span><span class="sxs-lookup"><span data-stu-id="a10a5-666">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="a10a5-667">Při použití `TypeFilterAttribute` nastavte [TypeFilterAttribute. IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="a10a5-667">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="a10a5-668">Poskytuje nápovědu, že se instance filtru *dá* znovu použít mimo rozsah požadavku, který byl vytvořen v rámci.</span><span class="sxs-lookup"><span data-stu-id="a10a5-668">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="a10a5-669">Modul runtime ASP.NET Core neposkytuje žádné záruky, že se vytvoří jediná instance filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-669">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="a10a5-670">Neměl by se používat s filtrem, který závisí na službách s jinou životností než singleton.</span><span class="sxs-lookup"><span data-stu-id="a10a5-670">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="a10a5-671">Následující příklad ukazuje, jak předat argumenty typu pomocí `TypeFilterAttribute` :</span><span class="sxs-lookup"><span data-stu-id="a10a5-671">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="a10a5-672">Filtry autorizace</span><span class="sxs-lookup"><span data-stu-id="a10a5-672">Authorization filters</span></span>

<span data-ttu-id="a10a5-673">Filtry autorizace:</span><span class="sxs-lookup"><span data-stu-id="a10a5-673">Authorization filters:</span></span>

* <span data-ttu-id="a10a5-674">Jsou první filtry spouštěny v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-674">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="a10a5-675">Řízení přístupu k metodám akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-675">Control access to action methods.</span></span>
* <span data-ttu-id="a10a5-676">Před metodou, ale ne po metodě.</span><span class="sxs-lookup"><span data-stu-id="a10a5-676">Have a before method, but no after method.</span></span>

<span data-ttu-id="a10a5-677">Vlastní autorizační filtry vyžadují vlastní autorizační rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a10a5-677">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="a10a5-678">Preferovat konfiguraci autorizačních zásad nebo psaní vlastních zásad autorizace při psaní vlastního filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-678">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="a10a5-679">Vestavěný autorizační filtr:</span><span class="sxs-lookup"><span data-stu-id="a10a5-679">The built-in authorization filter:</span></span>

* <span data-ttu-id="a10a5-680">Volá autorizační systém.</span><span class="sxs-lookup"><span data-stu-id="a10a5-680">Calls the authorization system.</span></span>
* <span data-ttu-id="a10a5-681">Neautorizuje požadavky.</span><span class="sxs-lookup"><span data-stu-id="a10a5-681">Does not authorize requests.</span></span>

<span data-ttu-id="a10a5-682">Negenerovat **výjimky** v rámci autorizačních filtrů:</span><span class="sxs-lookup"><span data-stu-id="a10a5-682">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="a10a5-683">Výjimka nebude zpracována.</span><span class="sxs-lookup"><span data-stu-id="a10a5-683">The exception will not be handled.</span></span>
* <span data-ttu-id="a10a5-684">Filtry výjimek nebudou zpracovávat výjimku.</span><span class="sxs-lookup"><span data-stu-id="a10a5-684">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="a10a5-685">Zvažte vydání výzvy, pokud dojde k výjimce ve autorizačním filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-685">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="a10a5-686">Přečtěte si další informace o [autorizaci](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="a10a5-686">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="a10a5-687">Filtry prostředků</span><span class="sxs-lookup"><span data-stu-id="a10a5-687">Resource filters</span></span>

<span data-ttu-id="a10a5-688">Filtry prostředků:</span><span class="sxs-lookup"><span data-stu-id="a10a5-688">Resource filters:</span></span>

* <span data-ttu-id="a10a5-689">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-689">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="a10a5-690">Provádění zabalí většinu kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-690">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="a10a5-691">Před filtry prostředků se spouštějí jenom [filtry autorizace](#authorization-filters) .</span><span class="sxs-lookup"><span data-stu-id="a10a5-691">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="a10a5-692">Filtry prostředků jsou užitečné pro krátkodobé okruhy v kanálu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-692">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="a10a5-693">Například filtr ukládání do mezipaměti může zabránit zbývajícímu kanálu v případě přístupů do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="a10a5-693">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="a10a5-694">Příklady filtru prostředků:</span><span class="sxs-lookup"><span data-stu-id="a10a5-694">Resource filter examples:</span></span>

* <span data-ttu-id="a10a5-695">[Filtr prostředků se zkrácenými okruhy](#short-circuiting-resource-filter) zobrazenými dříve.</span><span class="sxs-lookup"><span data-stu-id="a10a5-695">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="a10a5-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="a10a5-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="a10a5-697">Zabraňuje vazbě modelu v přístupu k datům formuláře.</span><span class="sxs-lookup"><span data-stu-id="a10a5-697">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="a10a5-698">Používá se pro nahrávání velkých souborů, aby se zabránilo čtení dat z formuláře do paměti.</span><span class="sxs-lookup"><span data-stu-id="a10a5-698">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="a10a5-699">Filtry akcí</span><span class="sxs-lookup"><span data-stu-id="a10a5-699">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a10a5-700">Filtry **akcí se nevztahují** na Razor stránky.</span><span class="sxs-lookup"><span data-stu-id="a10a5-700">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="a10a5-701">Stránky podporují <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-701"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="a10a5-702">Další informace najdete v tématu [metody filtrování pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="a10a5-702">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="a10a5-703">Filtry akcí:</span><span class="sxs-lookup"><span data-stu-id="a10a5-703">Action filters:</span></span>

* <span data-ttu-id="a10a5-704">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> rozhraní nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-704">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="a10a5-705">Jejich spuštění obklopuje provádění metod akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-705">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="a10a5-706">Následující kód ukazuje filtr vzorové akce:</span><span class="sxs-lookup"><span data-stu-id="a10a5-706">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="a10a5-707"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext>Poskytuje následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="a10a5-707">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="a10a5-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>– povolí čtení vstupů do metody akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="a10a5-709"><xref:Microsoft.AspNetCore.Mvc.Controller>– povolí manipulaci s instancí kontroleru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="a10a5-710"><xref:System.Web.Mvc.ActionExecutingContext.Result>– nastavení `Result` krátkodobého provádění metody akce a následných filtrů akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="a10a5-711">Vyvolání výjimky v metodě akce:</span><span class="sxs-lookup"><span data-stu-id="a10a5-711">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="a10a5-712">Zabraňuje spuštění dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="a10a5-712">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="a10a5-713">Na rozdíl od nastavení `Result` se místo úspěšného výsledku považuje za selhání.</span><span class="sxs-lookup"><span data-stu-id="a10a5-713">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="a10a5-714"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext>Poskytuje `Controller` a `Result` plus následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="a10a5-714">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="a10a5-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled>-True, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="a10a5-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="a10a5-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception>-Hodnota není null, pokud akce nebo dříve vyvolala filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="a10a5-717">Nastavení této vlastnosti na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="a10a5-717">Setting this property to null:</span></span>

  * <span data-ttu-id="a10a5-718">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="a10a5-718">Effectively handles the exception.</span></span>
  * <span data-ttu-id="a10a5-719">`Result`je spuštěn, jako kdyby byl vrácen z metody Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-719">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="a10a5-720">V případě `IAsyncActionFilter` volání <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> :</span><span class="sxs-lookup"><span data-stu-id="a10a5-720">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="a10a5-721">Provede všechny následné filtry akcí a metodu Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-721">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="a10a5-722">Vrací objekt `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="a10a5-722">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="a10a5-723">Do krátkodobého okruhu, přiřaďte <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> k instanci výsledku a Nevolejte `next` (a `ActionExecutionDelegate` ).</span><span class="sxs-lookup"><span data-stu-id="a10a5-723">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="a10a5-724">Rozhraní poskytuje abstrakci <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="a10a5-724">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="a10a5-725">`OnActionExecuting`Filtr akcí lze použít k těmto akcím:</span><span class="sxs-lookup"><span data-stu-id="a10a5-725">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="a10a5-726">Ověří stav modelu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-726">Validate model state.</span></span>
* <span data-ttu-id="a10a5-727">Pokud je stav neplatný, vrátí se chyba.</span><span class="sxs-lookup"><span data-stu-id="a10a5-727">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="a10a5-728">`OnActionExecuted`Metoda se spustí za metodou akce:</span><span class="sxs-lookup"><span data-stu-id="a10a5-728">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="a10a5-729">A mohou zobrazit výsledky akce a manipulovat s nimi prostřednictvím <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="a10a5-729">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="a10a5-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled>je nastaven na hodnotu true, pokud bylo provedení akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="a10a5-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="a10a5-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception>je nastavena na hodnotu jinou než null, pokud akce nebo filtr následné akce vyvolaly výjimku.</span><span class="sxs-lookup"><span data-stu-id="a10a5-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="a10a5-732">Nastavení `Exception` na hodnotu null:</span><span class="sxs-lookup"><span data-stu-id="a10a5-732">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="a10a5-733">Efektivně zpracovává výjimku.</span><span class="sxs-lookup"><span data-stu-id="a10a5-733">Effectively handles an exception.</span></span>
  * <span data-ttu-id="a10a5-734">`ActionExecutedContext.Result`je spuštěn, jako kdyby byl vrácen normálně z metody Action.</span><span class="sxs-lookup"><span data-stu-id="a10a5-734">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="a10a5-735">Filtry výjimek</span><span class="sxs-lookup"><span data-stu-id="a10a5-735">Exception filters</span></span>

<span data-ttu-id="a10a5-736">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="a10a5-736">Exception filters:</span></span>

* <span data-ttu-id="a10a5-737">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-737">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="a10a5-738">Dá se použít k implementaci běžných zásad zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="a10a5-738">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="a10a5-739">Následující ukázkový filtr výjimek používá vlastní zobrazení chyb pro zobrazení podrobností o výjimkách, ke kterým dochází při vývoji aplikace:</span><span class="sxs-lookup"><span data-stu-id="a10a5-739">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="a10a5-740">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="a10a5-740">Exception filters:</span></span>

* <span data-ttu-id="a10a5-741">Nemusíte mít události před a po.</span><span class="sxs-lookup"><span data-stu-id="a10a5-741">Don't have before and after events.</span></span>
* <span data-ttu-id="a10a5-742">Implementujte <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-742">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="a10a5-743">Zpracování neošetřených výjimek, ke kterým dochází při Razor vytváření stránky nebo kontroleru, [vázání modelů](xref:mvc/models/model-binding), filtrů akcí nebo metod akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-743">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="a10a5-744">**Nezachycujte** výjimky, ke kterým dochází v filtrech prostředků, výsledných filtrech nebo v důsledku provádění výsledků MVC.</span><span class="sxs-lookup"><span data-stu-id="a10a5-744">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="a10a5-745">Chcete-li zpracovat výjimku, nastavte <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> vlastnost na `true` nebo zapište odpověď.</span><span class="sxs-lookup"><span data-stu-id="a10a5-745">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="a10a5-746">Tím se zastaví šíření výjimky.</span><span class="sxs-lookup"><span data-stu-id="a10a5-746">This stops propagation of the exception.</span></span> <span data-ttu-id="a10a5-747">Filtr výjimek nemůže vypnout výjimku na "úspěch".</span><span class="sxs-lookup"><span data-stu-id="a10a5-747">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="a10a5-748">To může provést pouze filtr akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-748">Only an action filter can do that.</span></span>

<span data-ttu-id="a10a5-749">Filtry výjimek:</span><span class="sxs-lookup"><span data-stu-id="a10a5-749">Exception filters:</span></span>

* <span data-ttu-id="a10a5-750">Jsou vhodné pro vytváření přesahů výjimek, ke kterým dochází v rámci akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-750">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="a10a5-751">Nejsou tak flexibilní jako middleware při zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="a10a5-751">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="a10a5-752">Preferovat middleware pro zpracování výjimek.</span><span class="sxs-lookup"><span data-stu-id="a10a5-752">Prefer middleware for exception handling.</span></span> <span data-ttu-id="a10a5-753">Filtry výjimek použijte pouze v případě, že se zpracování chyb *liší* v závislosti na tom, která metoda Action je volána.</span><span class="sxs-lookup"><span data-stu-id="a10a5-753">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="a10a5-754">Aplikace může například mít metody akcí pro koncové body rozhraní API i pro zobrazení/HTML.</span><span class="sxs-lookup"><span data-stu-id="a10a5-754">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="a10a5-755">Koncové body rozhraní API mohou vracet informace o chybě jako JSON, zatímco akce na základě zobrazení by mohly vracet chybovou stránku jako HTML.</span><span class="sxs-lookup"><span data-stu-id="a10a5-755">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="a10a5-756">Filtry výsledků</span><span class="sxs-lookup"><span data-stu-id="a10a5-756">Result filters</span></span>

<span data-ttu-id="a10a5-757">Filtry výsledků:</span><span class="sxs-lookup"><span data-stu-id="a10a5-757">Result filters:</span></span>

* <span data-ttu-id="a10a5-758">Implementace rozhraní:</span><span class="sxs-lookup"><span data-stu-id="a10a5-758">Implement an interface:</span></span>
  * <span data-ttu-id="a10a5-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="a10a5-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="a10a5-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> nebo <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="a10a5-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="a10a5-761">Jejich spuštění obklopuje provádění výsledků akcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-761">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="a10a5-762">IResultFilter a IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="a10a5-762">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="a10a5-763">Následující kód ukazuje filtr výsledků, který přidá hlavičku protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="a10a5-763">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="a10a5-764">Typ výsledku, který se má provést, závisí na akci.</span><span class="sxs-lookup"><span data-stu-id="a10a5-764">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="a10a5-765">Akce vracející zobrazení zahrnuje všechny zpracování Razor jako součást prováděné <xref:Microsoft.AspNetCore.Mvc.ViewResult> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-765">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="a10a5-766">Metoda rozhraní API může provést určitou serializaci v rámci provádění výsledku.</span><span class="sxs-lookup"><span data-stu-id="a10a5-766">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="a10a5-767">Přečtěte si další informace o [výsledcích akcí](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="a10a5-767">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="a10a5-768">Filtry výsledků se spustí pouze v případě, že akce nebo filtr akcí vytvoří výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-768">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="a10a5-769">Filtry výsledků nejsou provedeny v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="a10a5-769">Result filters are not executed when:</span></span>

* <span data-ttu-id="a10a5-770">Filtr autorizace nebo filtr prostředků pro krátké okruhy kanálu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-770">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="a10a5-771">Filtr výjimek zpracovává výjimku tím, že vyprodukuje výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-771">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="a10a5-772"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName>Metoda může zkrátit provádění výsledků akce a následných filtrů výsledků nastavením <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> na `true` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-772">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="a10a5-773">Zapište do objektu Response v případě krátkého okruhu, abyste se vyhnuli vygenerování prázdné odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a10a5-773">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="a10a5-774">Vyvolání výjimky v `IResultFilter.OnResultExecuting` bude:</span><span class="sxs-lookup"><span data-stu-id="a10a5-774">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="a10a5-775">Zabraňte provádění výsledků akce a dalších filtrů.</span><span class="sxs-lookup"><span data-stu-id="a10a5-775">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="a10a5-776">Být považována za selhání namísto úspěšného výsledku.</span><span class="sxs-lookup"><span data-stu-id="a10a5-776">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="a10a5-777">Při <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> spuštění metody je odpověď pravděpodobně již odeslána klientovi.</span><span class="sxs-lookup"><span data-stu-id="a10a5-777">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="a10a5-778">Pokud byla odpověď již odeslána klientovi, nelze ji dále změnit.</span><span class="sxs-lookup"><span data-stu-id="a10a5-778">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="a10a5-779">`ResultExecutedContext.Canceled`je nastaven na hodnotu, `true` Pokud je spuštění výsledku akce zkráceno jiným filtrem.</span><span class="sxs-lookup"><span data-stu-id="a10a5-779">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="a10a5-780">`ResultExecutedContext.Exception`je nastavena na hodnotu jinou než null, pokud výsledek akce nebo následný filtr výsledků vyvolal výjimku.</span><span class="sxs-lookup"><span data-stu-id="a10a5-780">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="a10a5-781">Nastavení `Exception` na hodnotu null efektivně zpracovává výjimku a brání výjimce znovu vyvolat ASP.NET Core později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-781">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="a10a5-782">Neexistuje žádný spolehlivý způsob, jak zapisovat data do odpovědi při zpracování výjimky ve filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="a10a5-782">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="a10a5-783">Pokud byly hlavičky vyprázdněny do klienta, pokud výsledek akce vyvolá výjimku, neexistuje žádný spolehlivý mechanismus pro odeslání kódu chyby.</span><span class="sxs-lookup"><span data-stu-id="a10a5-783">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="a10a5-784">V případě <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> volání `await next` na se <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> spustí všechny následné filtry výsledků a výsledek akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-784">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="a10a5-785">Do krátkodobého okruhu nastavte [ResultExecutingContext. Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) na `true` a Nevolejte na `ResultExecutionDelegate` :</span><span class="sxs-lookup"><span data-stu-id="a10a5-785">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="a10a5-786">Rozhraní poskytuje abstrakci `ResultFilterAttribute` , která může být podtříd.</span><span class="sxs-lookup"><span data-stu-id="a10a5-786">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="a10a5-787">Dříve uvedená třída [AddHeaderAttribute](#add-header-attribute) je příkladem atributu filtru výsledků.</span><span class="sxs-lookup"><span data-stu-id="a10a5-787">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="a10a5-788">IAlwaysRunResultFilter a IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="a10a5-788">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="a10a5-789"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter>Rozhraní a <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklaruje <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementaci, která je spuštěna pro všechny výsledky akce.</span><span class="sxs-lookup"><span data-stu-id="a10a5-789">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="a10a5-790">To zahrnuje výsledky akcí, které vytvořil:</span><span class="sxs-lookup"><span data-stu-id="a10a5-790">This includes action results produced by:</span></span>

* <span data-ttu-id="a10a5-791">Filtry autorizace a filtry prostředků, které mají krátký okruh.</span><span class="sxs-lookup"><span data-stu-id="a10a5-791">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="a10a5-792">Filtry výjimek.</span><span class="sxs-lookup"><span data-stu-id="a10a5-792">Exception filters.</span></span>

<span data-ttu-id="a10a5-793">Například následující filtr vždy spustí a nastaví výsledek akce ( <xref:Microsoft.AspNetCore.Mvc.ObjectResult> ) s *422 nezpracovaným* stavovým kódem entity, pokud se nezdaří vyjednávání obsahu:</span><span class="sxs-lookup"><span data-stu-id="a10a5-793">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="a10a5-794">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="a10a5-794">IFilterFactory</span></span>

<span data-ttu-id="a10a5-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="a10a5-796">Proto `IFilterFactory` může být instance použita jako `IFilterMetadata` instance kdekoli v kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-796">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="a10a5-797">Když se modul runtime připraví k vyvolání filtru, pokusí se ho přetypovat na `IFilterFactory` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-797">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="a10a5-798">Pokud je toto přetypování úspěšné, <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> je volána metoda pro vytvoření `IFilterMetadata` instance, která je vyvolána.</span><span class="sxs-lookup"><span data-stu-id="a10a5-798">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="a10a5-799">To poskytuje flexibilní návrh, protože přesný kanál filtru není nutné nastavit explicitně při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="a10a5-799">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="a10a5-800">`IFilterFactory`může být implementováno pomocí implementace vlastního atributu jako jiný přístup k vytváření filtrů:</span><span class="sxs-lookup"><span data-stu-id="a10a5-800">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="a10a5-801">Předchozí kód lze otestovat spuštěním [ukázky stahování](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="a10a5-801">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="a10a5-802">Vyvolejte vývojářské nástroje F12.</span><span class="sxs-lookup"><span data-stu-id="a10a5-802">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="a10a5-803">Přejděte na adresu `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="a10a5-803">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="a10a5-804">Vývojářské nástroje F12 zobrazují následující hlavičky odpovědí přidané ukázkovým kódem:</span><span class="sxs-lookup"><span data-stu-id="a10a5-804">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="a10a5-805">**Autor:**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="a10a5-805">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="a10a5-806">**globaladdheader:**`Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="a10a5-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="a10a5-807">**interní:**`My header`</span><span class="sxs-lookup"><span data-stu-id="a10a5-807">**internal:** `My header`</span></span>

<span data-ttu-id="a10a5-808">Předchozí kód vytvoří hlavičku **interního:** `My header` Response.</span><span class="sxs-lookup"><span data-stu-id="a10a5-808">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="a10a5-809">IFilterFactory implementované u atributu</span><span class="sxs-lookup"><span data-stu-id="a10a5-809">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="a10a5-810">Filtry, které implementují, `IFilterFactory` jsou užitečné pro filtry, které:</span><span class="sxs-lookup"><span data-stu-id="a10a5-810">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="a10a5-811">Nevyžadovat předávání parametrů</span><span class="sxs-lookup"><span data-stu-id="a10a5-811">Don't require passing parameters.</span></span>
* <span data-ttu-id="a10a5-812">Musí mít závislosti konstruktoru, které musí vyplnit DI.</span><span class="sxs-lookup"><span data-stu-id="a10a5-812">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="a10a5-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>implementuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> .</span><span class="sxs-lookup"><span data-stu-id="a10a5-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="a10a5-814">`IFilterFactory`zpřístupňuje <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> metodu pro vytvoření <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span><span class="sxs-lookup"><span data-stu-id="a10a5-814">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="a10a5-815">`CreateInstance`Načte zadaný typ z kontejneru Services (DI).</span><span class="sxs-lookup"><span data-stu-id="a10a5-815">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="a10a5-816">Následující kód ukazuje tři přístupy k použití `[SampleActionFilter]` :</span><span class="sxs-lookup"><span data-stu-id="a10a5-816">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="a10a5-817">V předchozím kódu upravení metoda s jako `[SampleActionFilter]` upřednostňovaným přístupem k použití `SampleActionFilter` .</span><span class="sxs-lookup"><span data-stu-id="a10a5-817">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="a10a5-818">Použití middlewaru v kanálu filtru</span><span class="sxs-lookup"><span data-stu-id="a10a5-818">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="a10a5-819">Filtry prostředků fungují jako [middleware](xref:fundamentals/middleware/index) v tom, že obklopují provádění všech položek, které jsou později v kanálu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-819">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="a10a5-820">Filtry se ale liší od middlewaru v tom, že jsou součástí modulu ASP.NET Core Runtime, což znamená, že mají přístup k ASP.NET Core kontextu a konstrukcí.</span><span class="sxs-lookup"><span data-stu-id="a10a5-820">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="a10a5-821">Chcete-li použít middleware jako filtr, vytvořte typ s `Configure` metodou, která určuje middleware pro vložení do kanálu filtru.</span><span class="sxs-lookup"><span data-stu-id="a10a5-821">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="a10a5-822">Následující příklad používá middleware Localization k vytvoření aktuální jazykové verze pro požadavek:</span><span class="sxs-lookup"><span data-stu-id="a10a5-822">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="a10a5-823">Pomocí <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> Spusťte middleware:</span><span class="sxs-lookup"><span data-stu-id="a10a5-823">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="a10a5-824">Filtry middlewaru jsou spouštěny ve stejné fázi kanálu filtru jako filtry prostředků, před vazbami modelů a po zbytek kanálu.</span><span class="sxs-lookup"><span data-stu-id="a10a5-824">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="a10a5-825">Další akce</span><span class="sxs-lookup"><span data-stu-id="a10a5-825">Next actions</span></span>

* <span data-ttu-id="a10a5-826">Viz [metody filtru pro Razor stránky](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="a10a5-826">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="a10a5-827">Pro experimentování s filtry, [stažení, otestování a úpravy ukázky GitHubu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="a10a5-827">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
