---
title: Zobrazit součásti v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak se v ASP.NET Core používají komponenty a jak je přidat do aplikací.
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2019
uid: mvc/views/view-components
ms.openlocfilehash: e6990368519857a27b291d7d565c09072f23f1b0
ms.sourcegitcommit: 7001657c00358b082734ba4273693b9b3ed35d2a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68670083"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="75bb3-103">Zobrazit součásti v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="75bb3-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="75bb3-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="75bb3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="75bb3-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="75bb3-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="75bb3-106">Komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="75bb3-106">View components</span></span>

<span data-ttu-id="75bb3-107">Zobrazení součástí se podobá částečným zobrazením, ale jsou mnohem výkonnější.</span><span class="sxs-lookup"><span data-stu-id="75bb3-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="75bb3-108">Zobrazení komponent nepoužívá vazbu modelu a závisí pouze na datech poskytnutých při jejich volání.</span><span class="sxs-lookup"><span data-stu-id="75bb3-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="75bb3-109">Tento článek byl napsaný pomocí řadičů a zobrazení, ale kromě toho se v Razor Pages používají i komponenty.</span><span class="sxs-lookup"><span data-stu-id="75bb3-109">This article was written using controllers and views, but view components also work with Razor Pages.</span></span>

<span data-ttu-id="75bb3-110">Součást zobrazení:</span><span class="sxs-lookup"><span data-stu-id="75bb3-110">A view component:</span></span>

* <span data-ttu-id="75bb3-111">Vykreslí blok dat, nikoli celou odpověď.</span><span class="sxs-lookup"><span data-stu-id="75bb3-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="75bb3-112">Zahrnuje stejné výhody a testování, které byly nalezeny mezi kontrolkou a zobrazením.</span><span class="sxs-lookup"><span data-stu-id="75bb3-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="75bb3-113">Může mít parametry a obchodní logiku.</span><span class="sxs-lookup"><span data-stu-id="75bb3-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="75bb3-114">Je obvykle vyvolána ze stránky rozložení.</span><span class="sxs-lookup"><span data-stu-id="75bb3-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="75bb3-115">Zobrazit součásti jsou určeny kdekoli, kde máte opakovaně použitelnou logiku vykreslování, která je příliš složitá pro částečné zobrazení, například:</span><span class="sxs-lookup"><span data-stu-id="75bb3-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="75bb3-116">Dynamické navigační nabídky</span><span class="sxs-lookup"><span data-stu-id="75bb3-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="75bb3-117">Označení cloudu (kde se dotazuje databáze)</span><span class="sxs-lookup"><span data-stu-id="75bb3-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="75bb3-118">Panel přihlášení</span><span class="sxs-lookup"><span data-stu-id="75bb3-118">Login panel</span></span>
* <span data-ttu-id="75bb3-119">Nákupní košík</span><span class="sxs-lookup"><span data-stu-id="75bb3-119">Shopping cart</span></span>
* <span data-ttu-id="75bb3-120">Nedávno publikované články</span><span class="sxs-lookup"><span data-stu-id="75bb3-120">Recently published articles</span></span>
* <span data-ttu-id="75bb3-121">Obsah bočního panelu na typickém blogu</span><span class="sxs-lookup"><span data-stu-id="75bb3-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="75bb3-122">Přihlašovací panel, který se vykreslí na každé stránce, a zobrazí buď odkazy pro odhlášení nebo přihlášení, v závislosti na stavu přihlášení uživatele.</span><span class="sxs-lookup"><span data-stu-id="75bb3-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="75bb3-123">Součást zobrazení se skládá ze dvou částí: třídy (obvykle odvozené od [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) a výsledek, který vrátí (obvykle zobrazení).</span><span class="sxs-lookup"><span data-stu-id="75bb3-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="75bb3-124">Podobně jako řadiče se může jednat o součást zobrazení POCO, ale většina vývojářů bude chtít využít metody a vlastnosti, které jsou k dispozici odvozením `ViewComponent`z.</span><span class="sxs-lookup"><span data-stu-id="75bb3-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="75bb3-125">Při zvažování, zda zobrazit součásti vyhovují specifikacím aplikace, zvažte místo toho použití součástí Razor.</span><span class="sxs-lookup"><span data-stu-id="75bb3-125">When considering if view components meet an app's specifications, consider using Razor Components instead.</span></span> <span data-ttu-id="75bb3-126">Komponenty Razor také kombinují značky s C# kódem k tvorbě opakovaně použitelných jednotek uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="75bb3-126">Razor Components also combine markup with C# code to produce reusable UI units.</span></span> <span data-ttu-id="75bb3-127">Komponenty Razor jsou navržené pro produktivitu vývojářů při poskytování logiky uživatelského rozhraní a způsobu složení na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="75bb3-127">Razor Components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="75bb3-128">Další informace naleznete v tématu <xref:blazor/components>.</span><span class="sxs-lookup"><span data-stu-id="75bb3-128">For more information, see <xref:blazor/components>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="75bb3-129">Vytvoření komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="75bb3-129">Creating a view component</span></span>

<span data-ttu-id="75bb3-130">Tato část obsahuje požadavky na vysoké úrovni pro vytvoření komponenty zobrazení.</span><span class="sxs-lookup"><span data-stu-id="75bb3-130">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="75bb3-131">Později v tomto článku probereme podrobný přehled jednotlivých kroků a vytvoříme pohledovou komponentu.</span><span class="sxs-lookup"><span data-stu-id="75bb3-131">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="75bb3-132">Třída zobrazení součásti</span><span class="sxs-lookup"><span data-stu-id="75bb3-132">The view component class</span></span>

<span data-ttu-id="75bb3-133">Třídu zobrazení komponenty lze vytvořit pomocí kterékoli z následujících možností:</span><span class="sxs-lookup"><span data-stu-id="75bb3-133">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="75bb3-134">Odvození z *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="75bb3-134">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="75bb3-135">Upravení třídu s `[ViewComponent]` atributem nebo odvozením z třídy `[ViewComponent]` s atributem.</span><span class="sxs-lookup"><span data-stu-id="75bb3-135">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="75bb3-136">Vytvoření třídy, kde název končí příponou *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="75bb3-136">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="75bb3-137">Stejně jako řadiče musí být zobrazení součásti veřejné, nevnořené a neabstraktní třídy.</span><span class="sxs-lookup"><span data-stu-id="75bb3-137">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="75bb3-138">Název komponenty zobrazení je název třídy s odebraným příponou "ViewComponent".</span><span class="sxs-lookup"><span data-stu-id="75bb3-138">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="75bb3-139">Dá se taky explicitně zadat pomocí `ViewComponentAttribute.Name` vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="75bb3-139">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="75bb3-140">Třída zobrazení komponenty:</span><span class="sxs-lookup"><span data-stu-id="75bb3-140">A view component class:</span></span>

* <span data-ttu-id="75bb3-141">Plně podporuje [vkládání závislostí](../../fundamentals/dependency-injection.md) konstruktoru</span><span class="sxs-lookup"><span data-stu-id="75bb3-141">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="75bb3-142">Netrvá v životním cyklu kontroléru, což znamená, že nemůžete použít [filtry](../controllers/filters.md) v součásti zobrazení</span><span class="sxs-lookup"><span data-stu-id="75bb3-142">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="75bb3-143">Zobrazit metody komponenty</span><span class="sxs-lookup"><span data-stu-id="75bb3-143">View component methods</span></span>

<span data-ttu-id="75bb3-144">Komponenta zobrazení `InvokeAsync` definuje svou logiku v metodě, která `Task<IViewComponentResult>` vrací nebo v `IViewComponentResult`synchronní `Invoke` metodě, která vrací.</span><span class="sxs-lookup"><span data-stu-id="75bb3-144">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="75bb3-145">Parametry přicházejí přímo z vyvolání součásti zobrazení, nikoli z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="75bb3-145">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="75bb3-146">Součást zobrazení nikdy nezpracovává požadavek přímo.</span><span class="sxs-lookup"><span data-stu-id="75bb3-146">A view component never directly handles a request.</span></span> <span data-ttu-id="75bb3-147">Obvykle komponenta zobrazení inicializuje model a předá ho zobrazení voláním `View` metody.</span><span class="sxs-lookup"><span data-stu-id="75bb3-147">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="75bb3-148">V části Souhrn si prohlédněte metody komponenty:</span><span class="sxs-lookup"><span data-stu-id="75bb3-148">In summary, view component methods:</span></span>

* <span data-ttu-id="75bb3-149">Definujte metodu, která `Task<IViewComponentResult>` vrátí `Invoke` nebo`IViewComponentResult`synchronní metodu, která vrátí. `InvokeAsync`</span><span class="sxs-lookup"><span data-stu-id="75bb3-149">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="75bb3-150">Obvykle inicializuje model a předá ho zobrazení voláním `ViewComponent` `View` metody.</span><span class="sxs-lookup"><span data-stu-id="75bb3-150">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="75bb3-151">Parametry pocházejí z volající metody, nikoli HTTP.</span><span class="sxs-lookup"><span data-stu-id="75bb3-151">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="75bb3-152">Neexistuje žádná vazba modelu.</span><span class="sxs-lookup"><span data-stu-id="75bb3-152">There's no model binding.</span></span>
* <span data-ttu-id="75bb3-153">Nejsou dostupné přímo jako koncový bod HTTP.</span><span class="sxs-lookup"><span data-stu-id="75bb3-153">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="75bb3-154">Jsou vyvolány z vašeho kódu (obvykle v zobrazení).</span><span class="sxs-lookup"><span data-stu-id="75bb3-154">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="75bb3-155">Součást zobrazení nikdy nezpracovává požadavek.</span><span class="sxs-lookup"><span data-stu-id="75bb3-155">A view component never handles a request.</span></span>
* <span data-ttu-id="75bb3-156">Jsou v signatuře přetížené místo jakýchkoli podrobností z aktuální žádosti HTTP.</span><span class="sxs-lookup"><span data-stu-id="75bb3-156">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="75bb3-157">Zobrazit cestu pro hledání</span><span class="sxs-lookup"><span data-stu-id="75bb3-157">View search path</span></span>

<span data-ttu-id="75bb3-158">Modul runtime vyhledává zobrazení v následujících cestách:</span><span class="sxs-lookup"><span data-stu-id="75bb3-158">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="75bb3-159">/Views/{Controller} název součásti/Components/{View}/{View}</span><span class="sxs-lookup"><span data-stu-id="75bb3-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="75bb3-160">Název součásti/Views/Shared/Components/{View}/{View}</span><span class="sxs-lookup"><span data-stu-id="75bb3-160">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="75bb3-161">Název součásti/Pages/Shared/Components/{View}/{View}</span><span class="sxs-lookup"><span data-stu-id="75bb3-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="75bb3-162">Cesta pro hledání se vztahuje na projekty pomocí řadičů a zobrazení a Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="75bb3-162">The search path applies to projects using controllers + views and Razor Pages.</span></span>

<span data-ttu-id="75bb3-163">Výchozí název zobrazení pro součást zobrazení je *výchozí*, což znamená, že váš soubor zobrazení bude obvykle pojmenovaný *Default. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="75bb3-163">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="75bb3-164">Při vytváření výsledku zobrazení součásti nebo při volání `View` metody lze zadat jiný název zobrazení.</span><span class="sxs-lookup"><span data-stu-id="75bb3-164">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="75bb3-165">Doporučujeme pojmenovat soubor zobrazení *Default. cshtml* a použít cestu views */Shared/Components/{View Component Name}/{View Name}* .</span><span class="sxs-lookup"><span data-stu-id="75bb3-165">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="75bb3-166">Komponenta zobrazení použitá v tomto příkladu používá zobrazení */Shared/Components/PriorityList/default. cshtml* pro zobrazení součástí zobrazení. `PriorityList`</span><span class="sxs-lookup"><span data-stu-id="75bb3-166">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="75bb3-167">Vyvolání komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="75bb3-167">Invoking a view component</span></span>

<span data-ttu-id="75bb3-168">Chcete-li použít součást zobrazení, zavolejte v rámci zobrazení následující:</span><span class="sxs-lookup"><span data-stu-id="75bb3-168">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="75bb3-169">Parametry budou předány `InvokeAsync` metodě.</span><span class="sxs-lookup"><span data-stu-id="75bb3-169">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="75bb3-170">Součást zobrazení vyvinutá v článku se vyvolá z zobrazení zobrazení */TODO/index. cshtml.* `PriorityList`</span><span class="sxs-lookup"><span data-stu-id="75bb3-170">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="75bb3-171">V následujícím `InvokeAsync` postupu je metoda volána se dvěma parametry:</span><span class="sxs-lookup"><span data-stu-id="75bb3-171">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="75bb3-172">Vyvolání komponenty zobrazení jako pomocné rutiny značky</span><span class="sxs-lookup"><span data-stu-id="75bb3-172">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="75bb3-173">Pro ASP.NET Core 1,1 a vyšší můžete vyvolat součást zobrazení jako [pomocníka značky](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="75bb3-173">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="75bb3-174">Parametry třídy a metody použita jazyka Pascal pro pomocníky značek jsou přeloženy do jejich [kebabho případu](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span><span class="sxs-lookup"><span data-stu-id="75bb3-174">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="75bb3-175">Pomocný pomocník značek k vyvolání komponenty zobrazení používá `<vc></vc>` element.</span><span class="sxs-lookup"><span data-stu-id="75bb3-175">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="75bb3-176">Součást zobrazení je určena následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="75bb3-176">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="75bb3-177">Chcete-li použít zobrazení komponenty jako pomocníka značky, zaregistrujte sestavení obsahující komponentu zobrazení pomocí `@addTagHelper` direktivy.</span><span class="sxs-lookup"><span data-stu-id="75bb3-177">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="75bb3-178">Pokud je vaše součást zobrazení v sestavení s názvem `MyWebApp`, přidejte následující direktivu do souboru *_ViewImports. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="75bb3-178">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="75bb3-179">Můžete zaregistrovat součást zobrazení jako pomocný modul značek pro libovolný soubor, který odkazuje na součást zobrazení.</span><span class="sxs-lookup"><span data-stu-id="75bb3-179">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="75bb3-180">Další informace o registraci pomocných rutin značek najdete v tématu [Správa oboru pomocných značek](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) .</span><span class="sxs-lookup"><span data-stu-id="75bb3-180">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="75bb3-181">`InvokeAsync` Metoda použitá v tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="75bb3-181">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="75bb3-182">Značka pomocníka značky:</span><span class="sxs-lookup"><span data-stu-id="75bb3-182">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="75bb3-183">V ukázce výše `PriorityList` se zobrazí `priority-list`součást zobrazení.</span><span class="sxs-lookup"><span data-stu-id="75bb3-183">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="75bb3-184">Parametry pro zobrazení komponenty jsou předány jako atributy v případě kebab.</span><span class="sxs-lookup"><span data-stu-id="75bb3-184">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="75bb3-185">Vyvolání komponenty zobrazení přímo z kontroleru</span><span class="sxs-lookup"><span data-stu-id="75bb3-185">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="75bb3-186">Zobrazení komponent se obvykle vyvolají ze zobrazení, ale můžete je vyvolat přímo z metody kontroleru.</span><span class="sxs-lookup"><span data-stu-id="75bb3-186">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="75bb3-187">Přestože funkce View Components nedefinuje koncové body jako řadiče, můžete snadno implementovat akci kontroleru, která vrací obsah `ViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="75bb3-187">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="75bb3-188">V tomto příkladu je součást zobrazení volána přímo z kontroleru:</span><span class="sxs-lookup"><span data-stu-id="75bb3-188">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="75bb3-189">Návod: Vytvoření jednoduché součásti zobrazení</span><span class="sxs-lookup"><span data-stu-id="75bb3-189">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="75bb3-190">[Stažení](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), sestavení a otestování počátečního kódu.</span><span class="sxs-lookup"><span data-stu-id="75bb3-190">[Download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="75bb3-191">Jedná se o jednoduchý projekt s `ToDo` řadičem, který zobrazuje seznam položek *TODO* .</span><span class="sxs-lookup"><span data-stu-id="75bb3-191">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![Seznam ToDo](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="75bb3-193">Přidat třídu ViewComponent</span><span class="sxs-lookup"><span data-stu-id="75bb3-193">Add a ViewComponent class</span></span>

<span data-ttu-id="75bb3-194">Vytvořte složku *ViewComponents* a přidejte následující `PriorityListViewComponent` třídu:</span><span class="sxs-lookup"><span data-stu-id="75bb3-194">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="75bb3-195">Poznámky k kódu:</span><span class="sxs-lookup"><span data-stu-id="75bb3-195">Notes on the code:</span></span>

* <span data-ttu-id="75bb3-196">Třídy zobrazení komponent mohou být obsaženy v **jakékoli** složce projektu.</span><span class="sxs-lookup"><span data-stu-id="75bb3-196">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="75bb3-197">Vzhledem k tomu, že název třídy PriorityList**ViewComponent** končí příponou **ViewComponent**, modul runtime při odkazování na komponentu třídy ze zobrazení použije řetězec "PriorityList".</span><span class="sxs-lookup"><span data-stu-id="75bb3-197">Because the class name PriorityList**ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="75bb3-198">Později vám ukážeme, že se dozvíte podrobněji.</span><span class="sxs-lookup"><span data-stu-id="75bb3-198">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="75bb3-199">`[ViewComponent]` Atribut může změnit název, který se používá k odkazování na součást zobrazení.</span><span class="sxs-lookup"><span data-stu-id="75bb3-199">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="75bb3-200">Mohli bychom například jmenovat třídu `XYZ` a `ViewComponent` použít atribut:</span><span class="sxs-lookup"><span data-stu-id="75bb3-200">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="75bb3-201">Výše uvedený `PriorityList` atribut oznamuje, že selektor komponent zobrazí název při hledání zobrazení přidružených k komponentě a při odkazování na komponentu třídy ze zobrazení pomocí řetězce "PriorityList". `[ViewComponent]`</span><span class="sxs-lookup"><span data-stu-id="75bb3-201">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="75bb3-202">Později vám ukážeme, že se dozvíte podrobněji.</span><span class="sxs-lookup"><span data-stu-id="75bb3-202">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="75bb3-203">Komponenta používá [vkládání závislostí](../../fundamentals/dependency-injection.md) k zpřístupnění kontextu dat.</span><span class="sxs-lookup"><span data-stu-id="75bb3-203">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="75bb3-204">`InvokeAsync`zpřístupní metodu, která může být volána ze zobrazení a může mít libovolný počet argumentů.</span><span class="sxs-lookup"><span data-stu-id="75bb3-204">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="75bb3-205">Metoda vrátí `isDone` sadu položek, které odpovídají parametrům a `maxPriority`. `ToDo` `InvokeAsync`</span><span class="sxs-lookup"><span data-stu-id="75bb3-205">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-razor-view"></a><span data-ttu-id="75bb3-206">Vytvoření zobrazení pro zobrazení Razor komponenty</span><span class="sxs-lookup"><span data-stu-id="75bb3-206">Create the view component Razor view</span></span>

* <span data-ttu-id="75bb3-207">Vytvořte složku *views/Shared/Components* .</span><span class="sxs-lookup"><span data-stu-id="75bb3-207">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="75bb3-208">Tato složka **musí** být pojmenována *součásti*.</span><span class="sxs-lookup"><span data-stu-id="75bb3-208">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="75bb3-209">Vytvořte složku *views/Shared/Components/PriorityList* .</span><span class="sxs-lookup"><span data-stu-id="75bb3-209">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="75bb3-210">Název této složky se musí shodovat s názvem třídy zobrazení, nebo názvem třídy minus přípona (Pokud následovala konvence a použila v názvu třídy příponu *ViewComponent* ).</span><span class="sxs-lookup"><span data-stu-id="75bb3-210">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="75bb3-211">Pokud jste použili `ViewComponent` atribut, musí název třídy odpovídat označení atributu.</span><span class="sxs-lookup"><span data-stu-id="75bb3-211">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="75bb3-212">Vytvoření zobrazení */Shared/Components/PriorityList/default. cshtml* Razor View:</span><span class="sxs-lookup"><span data-stu-id="75bb3-212">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="75bb3-213">Zobrazení Razor převezme seznam `TodoItem` a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="75bb3-213">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="75bb3-214">Pokud metoda View komponenty `InvokeAsync` nepředá název zobrazení (jako v naší ukázce), je použita *Výchozí hodnota* pro název zobrazení podle konvence.</span><span class="sxs-lookup"><span data-stu-id="75bb3-214">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="75bb3-215">Později v tomto kurzu ukážeme, jak předat název zobrazení.</span><span class="sxs-lookup"><span data-stu-id="75bb3-215">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="75bb3-216">Chcete-li přepsat výchozí styl pro určitý kontroler, přidejte zobrazení do složky zobrazení pro konkrétní řadič (například *zobrazení/TODO/komponenty/PriorityList/default. cshtml)* .</span><span class="sxs-lookup"><span data-stu-id="75bb3-216">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span></span>

    <span data-ttu-id="75bb3-217">Pokud je součást zobrazení specifická pro konkrétního řadiče, můžete ji přidat do složky pro konkrétního řadiče (*zobrazení/TODO/komponenty/PriorityList/default. cshtml*).</span><span class="sxs-lookup"><span data-stu-id="75bb3-217">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="75bb3-218">Přidejte objekt `div` obsahující volání do komponenty seznamu priorit do dolní části souboru *views/index. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="75bb3-218">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="75bb3-219">Značka `@await Component.InvokeAsync` zobrazuje syntaxi pro volání komponent zobrazení.</span><span class="sxs-lookup"><span data-stu-id="75bb3-219">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="75bb3-220">První argument je název součásti, kterou chceme vyvolat nebo zavolat.</span><span class="sxs-lookup"><span data-stu-id="75bb3-220">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="75bb3-221">Další parametry jsou předány do komponenty.</span><span class="sxs-lookup"><span data-stu-id="75bb3-221">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="75bb3-222">`InvokeAsync`může mít libovolný počet argumentů.</span><span class="sxs-lookup"><span data-stu-id="75bb3-222">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="75bb3-223">Otestujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="75bb3-223">Test the app.</span></span> <span data-ttu-id="75bb3-224">Následující obrázek znázorňuje seznam ToDo a prioritní položky:</span><span class="sxs-lookup"><span data-stu-id="75bb3-224">The following image shows the ToDo list and the priority items:</span></span>

![položky seznamu úkolů a prioritní](view-components/_static/pi.png)

<span data-ttu-id="75bb3-226">Můžete také volat součást zobrazení přímo z kontroleru:</span><span class="sxs-lookup"><span data-stu-id="75bb3-226">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![prioritní položky z akce IndexVC](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="75bb3-228">Zadání názvu zobrazení</span><span class="sxs-lookup"><span data-stu-id="75bb3-228">Specifying a view name</span></span>

<span data-ttu-id="75bb3-229">Složitá součást zobrazení může při některých podmínkách určit jiné než výchozí zobrazení.</span><span class="sxs-lookup"><span data-stu-id="75bb3-229">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="75bb3-230">Následující kód ukazuje, jak zadat zobrazení "PVC" z `InvokeAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="75bb3-230">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="75bb3-231">Aktualizujte `PriorityListViewComponent` metodu ve třídě. `InvokeAsync`</span><span class="sxs-lookup"><span data-stu-id="75bb3-231">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="75bb3-232">Zkopírujte soubor *views/Shared/Components/PriorityList/default. cshtml* do zobrazení s názvem views */Shared/Components/PriorityList/PVC. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="75bb3-232">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="75bb3-233">Přidejte záhlaví, které indikuje, že se používá zobrazení typu PVC.</span><span class="sxs-lookup"><span data-stu-id="75bb3-233">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="75bb3-234">Aktualizace *zobrazení/TODO/index. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="75bb3-234">Update *Views/ToDo/Index.cshtml*:</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="75bb3-235">Spusťte aplikaci a ověřte zobrazení PVC.</span><span class="sxs-lookup"><span data-stu-id="75bb3-235">Run the app and verify PVC view.</span></span>

![Prioritní zobrazení součásti](view-components/_static/pvc.png)

<span data-ttu-id="75bb3-237">Pokud se zobrazení typu PVC nevykresluje, ověřte, že voláte komponentu zobrazení s prioritou 4 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="75bb3-237">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="75bb3-238">Prohlédněte si cestu zobrazení</span><span class="sxs-lookup"><span data-stu-id="75bb3-238">Examine the view path</span></span>

* <span data-ttu-id="75bb3-239">Změňte parametr priority na tři nebo méně, aby zobrazení priority nebylo vráceno.</span><span class="sxs-lookup"><span data-stu-id="75bb3-239">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="75bb3-240">Dočasně přejmenujte *zobrazení/TODO/komponenty/PriorityList/default. cshtml* na *1Default. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="75bb3-240">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="75bb3-241">Otestujte aplikaci, zobrazí se následující chyba:</span><span class="sxs-lookup"><span data-stu-id="75bb3-241">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="75bb3-242">Kopírování *views/TODO/Components/PriorityList/1Default. cshtml* na *views/Shared/Components/PriorityList/default. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="75bb3-242">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="75bb3-243">Přidáním kódu do zobrazení *sdílené* složky zobrazení TODO můžete zobrazit, že se zobrazení nachází ze *sdílené* složky.</span><span class="sxs-lookup"><span data-stu-id="75bb3-243">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="75bb3-244">Otestujte zobrazení **sdílené** komponenty.</span><span class="sxs-lookup"><span data-stu-id="75bb3-244">Test the **Shared** component view.</span></span>

![Výstup ToDo se zobrazením sdílené komponenty](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="75bb3-246">Zamezení pevně zakódovaných řetězců</span><span class="sxs-lookup"><span data-stu-id="75bb3-246">Avoiding hard-coded strings</span></span>

<span data-ttu-id="75bb3-247">Pokud chcete zabezpečení času kompilace, můžete nahradit pevně kódovaný název komponenty zobrazení názvem třídy.</span><span class="sxs-lookup"><span data-stu-id="75bb3-247">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="75bb3-248">Vytvořte součást zobrazení bez přípony "ViewComponent":</span><span class="sxs-lookup"><span data-stu-id="75bb3-248">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="75bb3-249">Přidejte příkaz do souboru zobrazení Razor a `nameof` použijte operátor: `using`</span><span class="sxs-lookup"><span data-stu-id="75bb3-249">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="75bb3-250">Provést synchronní práci</span><span class="sxs-lookup"><span data-stu-id="75bb3-250">Perform synchronous work</span></span>

<span data-ttu-id="75bb3-251">Architektura zpracovává synchronní `Invoke` metodu, pokud nepotřebujete provádět asynchronní práci.</span><span class="sxs-lookup"><span data-stu-id="75bb3-251">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="75bb3-252">Následující metoda vytvoří synchronní `Invoke` součást zobrazení:</span><span class="sxs-lookup"><span data-stu-id="75bb3-252">The following method creates a synchronous `Invoke` view component:</span></span>

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

<span data-ttu-id="75bb3-253">Soubor Razor komponenty zobrazit obsahuje seznam řetězců předaných `Invoke` metodě (views */Home/Components/PriorityList/default. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="75bb3-253">The view component's Razor file lists the strings passed to the `Invoke` method (*Views/Home/Components/PriorityList/Default.cshtml*):</span></span>

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="75bb3-254">Zobrazení komponenty je vyvoláno v souboru Razor (například views */Home/index. cshtml*) pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="75bb3-254">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="75bb3-255">Pomocník značek</span><span class="sxs-lookup"><span data-stu-id="75bb3-255">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="75bb3-256">Chcete-li <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> použít přístup, `Component.InvokeAsync`zavolejte:</span><span class="sxs-lookup"><span data-stu-id="75bb3-256">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="75bb3-257">Zobrazení komponenty je vyvoláno v souboru Razor (například views */Home/index. cshtml*) pomocí <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span><span class="sxs-lookup"><span data-stu-id="75bb3-257">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="75bb3-258">Volání `Component.InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="75bb3-258">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="75bb3-259">Chcete-li použít pomocníka značky, zaregistrujte sestavení obsahující komponentu zobrazení `@addTagHelper` pomocí direktivy (zobrazení součásti je v sestavení s `MyWebApp`názvem):</span><span class="sxs-lookup"><span data-stu-id="75bb3-259">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="75bb3-260">Použijte nápovědu pro zobrazení značky komponenty v souboru značek Razor:</span><span class="sxs-lookup"><span data-stu-id="75bb3-260">Use the view component Tag Helper in the Razor markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="75bb3-261">Signatura `PriorityList.Invoke` metody je synchronní, ale Razor najde a volá `Component.InvokeAsync` metodu v souboru označení.</span><span class="sxs-lookup"><span data-stu-id="75bb3-261">The method signature of `PriorityList.Invoke` is synchronous, but Razor finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="all-view-component-parameters-are-required"></a><span data-ttu-id="75bb3-262">Všechny parametry komponenty View jsou povinné.</span><span class="sxs-lookup"><span data-stu-id="75bb3-262">All view component parameters are required</span></span>

<span data-ttu-id="75bb3-263">Každý parametr v součásti zobrazení je povinný atribut.</span><span class="sxs-lookup"><span data-stu-id="75bb3-263">Each parameter in a view component is a required attribute.</span></span> <span data-ttu-id="75bb3-264">Podívejte se na [Tento problém GitHubu](https://github.com/aspnet/AspNetCore/issues/5011).</span><span class="sxs-lookup"><span data-stu-id="75bb3-264">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5011).</span></span> <span data-ttu-id="75bb3-265">Pokud je vynechán libovolný parametr:</span><span class="sxs-lookup"><span data-stu-id="75bb3-265">If any  parameter is omitted:</span></span>

* <span data-ttu-id="75bb3-266">Signatura `InvokeAsync` metody se neshoduje, proto se metoda neprovede.</span><span class="sxs-lookup"><span data-stu-id="75bb3-266">The `InvokeAsync` method signature won't match, therefore the method won't execute.</span></span>
* <span data-ttu-id="75bb3-267">ViewComponent nevykresluje žádné značky.</span><span class="sxs-lookup"><span data-stu-id="75bb3-267">The ViewComponent won't render any markup.</span></span>
* <span data-ttu-id="75bb3-268">Nebudou vyvolány žádné chyby.</span><span class="sxs-lookup"><span data-stu-id="75bb3-268">No errors will be thrown.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="75bb3-269">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="75bb3-269">Additional resources</span></span>

* [<span data-ttu-id="75bb3-270">Injektáž závislostí do zobrazení</span><span class="sxs-lookup"><span data-stu-id="75bb3-270">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)
