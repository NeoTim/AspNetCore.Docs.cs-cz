---
title: Zobrazení komponenty v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak komponenty zobrazení se používají v ASP.NET Core a jejich přidání do aplikací.
ms.author: riande
ms.date: 12/03/2018
uid: mvc/views/view-components
ms.openlocfilehash: 31c0d8f2c08eb06633b604c949cadff24ed5cb7e
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236351"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="01f2b-103">Zobrazení komponenty v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="01f2b-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="01f2b-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="01f2b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="01f2b-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="01f2b-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="01f2b-106">Komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="01f2b-106">View components</span></span>

<span data-ttu-id="01f2b-107">Zobrazení komponenty jsou podobné částečná zobrazení, ale jsou výrazně výkonnější.</span><span class="sxs-lookup"><span data-stu-id="01f2b-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="01f2b-108">Zobrazení komponenty nepoužívejte vazby modelu a pouze závisí na poskytnutý při volání do něj data.</span><span class="sxs-lookup"><span data-stu-id="01f2b-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="01f2b-109">Tento článek byl zapsán pomocí kontrolerů a zobrazení, ale zobrazení komponenty také pracovat se stránkami Razor.</span><span class="sxs-lookup"><span data-stu-id="01f2b-109">This article was written using controllers and views, but view components also work with Razor Pages.</span></span>

<span data-ttu-id="01f2b-110">Zobrazení komponenty:</span><span class="sxs-lookup"><span data-stu-id="01f2b-110">A view component:</span></span>

* <span data-ttu-id="01f2b-111">Vykreslí blok dat, ne celou odpověď.</span><span class="sxs-lookup"><span data-stu-id="01f2b-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="01f2b-112">Zahrnuje stejné oddělení z otázky a výhody testovatelnosti najít mezi kontroler a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="01f2b-113">Můžete mít parametry a obchodní logiku.</span><span class="sxs-lookup"><span data-stu-id="01f2b-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="01f2b-114">Obvykle je vyvolána z rozložení stránky.</span><span class="sxs-lookup"><span data-stu-id="01f2b-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="01f2b-115">Zobrazení komponenty jsou určeny kdekoli, že máte opakovaně použitelný vykreslování logiku, která je příliš složitý pro částečné zobrazení, jako například:</span><span class="sxs-lookup"><span data-stu-id="01f2b-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="01f2b-116">Dynamické navigační nabídky</span><span class="sxs-lookup"><span data-stu-id="01f2b-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="01f2b-117">Značka cloudu (kde dotazuje databázi)</span><span class="sxs-lookup"><span data-stu-id="01f2b-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="01f2b-118">Panel přihlášení</span><span class="sxs-lookup"><span data-stu-id="01f2b-118">Login panel</span></span>
* <span data-ttu-id="01f2b-119">Nákupní košík</span><span class="sxs-lookup"><span data-stu-id="01f2b-119">Shopping cart</span></span>
* <span data-ttu-id="01f2b-120">Nedávno publikovaných článků</span><span class="sxs-lookup"><span data-stu-id="01f2b-120">Recently published articles</span></span>
* <span data-ttu-id="01f2b-121">Obsah bočního panelu na typické blogu</span><span class="sxs-lookup"><span data-stu-id="01f2b-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="01f2b-122">Panel přihlášení, který by být vykreslen na každé stránce a zobrazit odkazy na odhlášení nebo se přihlaste, v závislosti na protokolu ve stavu uživatele</span><span class="sxs-lookup"><span data-stu-id="01f2b-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="01f2b-123">Komponenty zobrazení se skládá ze dvou částí: třídy (obvykle odvozen z [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) a výsledek se vrátí (obvykle zobrazení).</span><span class="sxs-lookup"><span data-stu-id="01f2b-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="01f2b-124">Jako jsou řadiče, může být zobrazení komponenty POCO, ale Většina vývojářů budete chtít využívat výhod metod a vlastností, které jsou k dispozici odvozením z `ViewComponent`.</span><span class="sxs-lookup"><span data-stu-id="01f2b-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="01f2b-125">Vytvoření zobrazení komponenty</span><span class="sxs-lookup"><span data-stu-id="01f2b-125">Creating a view component</span></span>

<span data-ttu-id="01f2b-126">Tato část obsahuje základní požadavky na vytvoření komponenty zobrazení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-126">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="01f2b-127">Později v tomto článku vytvoříme Zkontrolujte každý krok podrobně a vytvořte komponentu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-127">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="01f2b-128">Zobrazení komponentní třída</span><span class="sxs-lookup"><span data-stu-id="01f2b-128">The view component class</span></span>

<span data-ttu-id="01f2b-129">Komponentní třída zobrazení lze vytvořit pomocí některé z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="01f2b-129">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="01f2b-130">Odvozování z *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="01f2b-130">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="01f2b-131">Upravení třída s atributem `[ViewComponent]` atribut nebo odvozování z třídy s `[ViewComponent]` atribut</span><span class="sxs-lookup"><span data-stu-id="01f2b-131">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="01f2b-132">Vytvoření třídy, kde název končí příponou *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="01f2b-132">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="01f2b-133">Jako jsou řadiče zobrazení komponenty musí být veřejné, bez vnoření a neabstraktní třídy.</span><span class="sxs-lookup"><span data-stu-id="01f2b-133">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="01f2b-134">Název komponenty zobrazení je název třídy s příponou "ViewComponent" odebrat.</span><span class="sxs-lookup"><span data-stu-id="01f2b-134">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="01f2b-135">To se dá také explicitně nastavit pomocí `ViewComponentAttribute.Name` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="01f2b-135">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="01f2b-136">Zobrazení komponentní třída:</span><span class="sxs-lookup"><span data-stu-id="01f2b-136">A view component class:</span></span>

* <span data-ttu-id="01f2b-137">Plně podporuje konstruktor [injektáž závislostí](../../fundamentals/dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="01f2b-137">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="01f2b-138">Není účastnit životního cyklu kontroleru, což znamená, že nemůžete použít [filtry](../controllers/filters.md) v komponentě zobrazení</span><span class="sxs-lookup"><span data-stu-id="01f2b-138">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="01f2b-139">Zobrazení komponenty metody</span><span class="sxs-lookup"><span data-stu-id="01f2b-139">View component methods</span></span>

<span data-ttu-id="01f2b-140">Zobrazení komponenty definuje svou logikou v `InvokeAsync` metodu, která vrací `Task<IViewComponentResult>` nebo v synchronního `Invoke` metodu, která vrátí `IViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="01f2b-140">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="01f2b-141">Parametry pocházejí přímo z volání zobrazení komponenty, nikoli z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="01f2b-141">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="01f2b-142">Zobrazení komponenty nikdy přímo zpracovává žádost.</span><span class="sxs-lookup"><span data-stu-id="01f2b-142">A view component never directly handles a request.</span></span> <span data-ttu-id="01f2b-143">Obvykle inicializuje model zobrazení komponenty a předá ji do zobrazení voláním `View` metody.</span><span class="sxs-lookup"><span data-stu-id="01f2b-143">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="01f2b-144">Stručně řečeno zobrazte metody komponenty:</span><span class="sxs-lookup"><span data-stu-id="01f2b-144">In summary, view component methods:</span></span>

* <span data-ttu-id="01f2b-145">Definování `InvokeAsync` metodu, která vrací `Task<IViewComponentResult>` nebo synchronního `Invoke` metodu, která vrátí `IViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="01f2b-145">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="01f2b-146">Obvykle inicializuje model a předává je do zobrazení pomocí volání `ViewComponent` `View` metody.</span><span class="sxs-lookup"><span data-stu-id="01f2b-146">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="01f2b-147">Parametry pocházejí z volání metody, ne HTTP.</span><span class="sxs-lookup"><span data-stu-id="01f2b-147">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="01f2b-148">Neexistuje žádná vazba modelu.</span><span class="sxs-lookup"><span data-stu-id="01f2b-148">There's no model binding.</span></span>
* <span data-ttu-id="01f2b-149">Nejsou dostupné přímo jako koncový bod HTTP.</span><span class="sxs-lookup"><span data-stu-id="01f2b-149">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="01f2b-150">Se už vyvolána z uživatelského kódu (obvykle v zobrazení).</span><span class="sxs-lookup"><span data-stu-id="01f2b-150">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="01f2b-151">Zobrazení komponenty nikdy zpracuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="01f2b-151">A view component never handles a request.</span></span>
* <span data-ttu-id="01f2b-152">Jsou přetížené na podpis a nikoli na jakékoli podrobnosti z aktuální žádosti HTTP.</span><span class="sxs-lookup"><span data-stu-id="01f2b-152">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="01f2b-153">Zobrazení cesty pro hledání</span><span class="sxs-lookup"><span data-stu-id="01f2b-153">View search path</span></span>

<span data-ttu-id="01f2b-154">Modul runtime vyhledává zobrazení v následující cesty:</span><span class="sxs-lookup"><span data-stu-id="01f2b-154">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="01f2b-155">Řetězec/Pages/součásti / {název komponenty zobrazení} / {název zobrazení}</span><span class="sxs-lookup"><span data-stu-id="01f2b-155">/Pages/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="01f2b-156">/Components/ /views/ {název řadiče} {název komponenty zobrazení} / {název zobrazení}</span><span class="sxs-lookup"><span data-stu-id="01f2b-156">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="01f2b-157">/ Zobrazení/Shared/Components / {View název komponenty} / {název zobrazení}</span><span class="sxs-lookup"><span data-stu-id="01f2b-157">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="01f2b-158">Výchozí název zobrazení pro součást zobrazení je *výchozí*, což znamená, že váš soubor zobrazení se obvykle nazývá *stránku Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="01f2b-158">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="01f2b-159">Můžete zadat název jiné zobrazení, při vytváření komponenty výsledný objekt zobrazení, nebo při volání `View` metody.</span><span class="sxs-lookup"><span data-stu-id="01f2b-159">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="01f2b-160">Doporučujeme pojmenovat soubor zobrazení *stránku Default.cshtml* a použít *zobrazení/Shared/Components / {název komponenty zobrazení} / {název zobrazení}* cestu.</span><span class="sxs-lookup"><span data-stu-id="01f2b-160">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="01f2b-161">`PriorityList` Komponenta zobrazení používané v tomto příkladu používá *Views/Shared/Components/PriorityList/Default.cshtml* pro součásti zobrazení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-161">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="01f2b-162">Vyvolání komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="01f2b-162">Invoking a view component</span></span>

<span data-ttu-id="01f2b-163">Chcete-li použít komponentu zobrazení, zavolejte následující uvnitř zobrazení:</span><span class="sxs-lookup"><span data-stu-id="01f2b-163">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="01f2b-164">Parametry předávané `InvokeAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="01f2b-164">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="01f2b-165">`PriorityList` z je vyvolána zobrazení komponenty vyvinuté v následujícím článku *Views/ToDo/Index.cshtml* zobrazení souboru.</span><span class="sxs-lookup"><span data-stu-id="01f2b-165">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="01f2b-166">V následujícím příkladu `InvokeAsync` metoda je volána s dva parametry:</span><span class="sxs-lookup"><span data-stu-id="01f2b-166">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="01f2b-167">Vyvolání komponenty zobrazení jako pomocné rutiny značky</span><span class="sxs-lookup"><span data-stu-id="01f2b-167">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="01f2b-168">Pro ASP.NET Core 1.1 a vyšší, můžete vyvolat komponentu zobrazení jako [pomocné rutiny značky](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="01f2b-168">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="01f2b-169">Jazyka Pascal – třídy a metody parametry pro pomocné rutiny značek jsou přeloženy do jejich [kebab případ](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span><span class="sxs-lookup"><span data-stu-id="01f2b-169">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="01f2b-170">Pomocná rutina značky k vyvolání komponenty zobrazení používá `<vc></vc>` elementu.</span><span class="sxs-lookup"><span data-stu-id="01f2b-170">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="01f2b-171">Zobrazení komponenty je určena následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="01f2b-171">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="01f2b-172">Použití zobrazení komponenty jako pomocné rutiny značky, zaregistrovat sestavení obsahující pomocí zobrazení komponenty `@addTagHelper` směrnice.</span><span class="sxs-lookup"><span data-stu-id="01f2b-172">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="01f2b-173">Pokud vaše komponenta zobrazení je v sestavení nazvané `MyWebApp`, přidejte následující direktivy k *_ViewImports.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="01f2b-173">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="01f2b-174">Zobrazení komponenty můžete zaregistrovat jako pomocné rutiny značky do souboru, která odkazuje na součást zobrazení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-174">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="01f2b-175">Zobrazit [Správa oboru pomocné rutiny značky](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) Další informace o tom, jak zaregistrovat pomocných rutin značek.</span><span class="sxs-lookup"><span data-stu-id="01f2b-175">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="01f2b-176">`InvokeAsync` Metodu použitou v tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="01f2b-176">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="01f2b-177">Ve značkách pomocné rutiny značky:</span><span class="sxs-lookup"><span data-stu-id="01f2b-177">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="01f2b-178">V příkladu výše `PriorityList` stane součástí zobrazení `priority-list`.</span><span class="sxs-lookup"><span data-stu-id="01f2b-178">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="01f2b-179">Parametry pro zobrazení komponenty jsou předány jako atributy v případě kebab.</span><span class="sxs-lookup"><span data-stu-id="01f2b-179">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="01f2b-180">Vyvolání komponenty zobrazení přímo z kontroleru</span><span class="sxs-lookup"><span data-stu-id="01f2b-180">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="01f2b-181">Zobrazení komponenty jsou obvykle vyvolány ze zobrazení, ale můžete je vyvolat přímo z metody kontroleru.</span><span class="sxs-lookup"><span data-stu-id="01f2b-181">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="01f2b-182">Při zobrazení komponenty nebudete definovat koncových bodů, jako jsou řadiče, je možné snadno implementovat akce kontroleru, který vrátí obsah `ViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="01f2b-182">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="01f2b-183">V tomto příkladu je součásti zobrazení volání přímo z kontroleru:</span><span class="sxs-lookup"><span data-stu-id="01f2b-183">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="01f2b-184">Průvodce: Vytvoření jednoduché zobrazení komponenty</span><span class="sxs-lookup"><span data-stu-id="01f2b-184">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="01f2b-185">[Stáhněte si](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample), sestavení a testování počátečního kódu.</span><span class="sxs-lookup"><span data-stu-id="01f2b-185">[Download](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="01f2b-186">Je to Jednoduchý projekt s `ToDo` kontroler, který zobrazí seznam *ToDo* položky.</span><span class="sxs-lookup"><span data-stu-id="01f2b-186">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![Seznam úloh, ať už](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="01f2b-188">Přidejte třídu ViewComponent</span><span class="sxs-lookup"><span data-stu-id="01f2b-188">Add a ViewComponent class</span></span>

<span data-ttu-id="01f2b-189">Vytvoření *ViewComponents* složky a přidejte následující `PriorityListViewComponent` třídy:</span><span class="sxs-lookup"><span data-stu-id="01f2b-189">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="01f2b-190">Poznámky k kód:</span><span class="sxs-lookup"><span data-stu-id="01f2b-190">Notes on the code:</span></span>

* <span data-ttu-id="01f2b-191">Zobrazení komponentní třídy mohou být obsaženy v **jakékoli** složky v projektu.</span><span class="sxs-lookup"><span data-stu-id="01f2b-191">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="01f2b-192">Protože třída název PriorityList**ViewComponent** končí příponou **ViewComponent**, modul runtime použije řetězec "PriorityList" při odkazování na komponentě třídy ze zobrazení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-192">Because the class name PriorityList**ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="01f2b-193">Já zatím vysvětlím, které podrobněji později.</span><span class="sxs-lookup"><span data-stu-id="01f2b-193">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="01f2b-194">`[ViewComponent]` Atribut můžete změnit název slouží jako odkaz na komponentu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-194">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="01f2b-195">Například jsme mohli jsme s názvem třídy `XYZ` a použít `ViewComponent` atribut:</span><span class="sxs-lookup"><span data-stu-id="01f2b-195">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="01f2b-196">`[ViewComponent]` Výše uvedený atribut říká Výběr komponent zobrazení použít název `PriorityList` při hledání zobrazení související s komponentou a použít řetězec "PriorityList" při odkazování na komponentě třídy ze zobrazení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-196">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="01f2b-197">Já zatím vysvětlím, které podrobněji později.</span><span class="sxs-lookup"><span data-stu-id="01f2b-197">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="01f2b-198">Součást používá [injektáž závislostí](../../fundamentals/dependency-injection.md) zpřístupnit datového kontextu.</span><span class="sxs-lookup"><span data-stu-id="01f2b-198">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="01f2b-199">`InvokeAsync` Zpřístupní metodu, která může být volána z zobrazení a to může trvat libovolný počet argumentů.</span><span class="sxs-lookup"><span data-stu-id="01f2b-199">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="01f2b-200">`InvokeAsync` Metoda vrátí sadu `ToDo` položky, které splňují `isDone` a `maxPriority` parametry.</span><span class="sxs-lookup"><span data-stu-id="01f2b-200">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-razor-view"></a><span data-ttu-id="01f2b-201">Vytvořit zobrazení Razor komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="01f2b-201">Create the view component Razor view</span></span>

* <span data-ttu-id="01f2b-202">Vytvořte *zobrazení/Shared/Components* složky.</span><span class="sxs-lookup"><span data-stu-id="01f2b-202">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="01f2b-203">Tato složka **musí** jmenovat *komponenty*.</span><span class="sxs-lookup"><span data-stu-id="01f2b-203">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="01f2b-204">Vytvořte *zobrazení/Shared/součásti/PriorityList* složky.</span><span class="sxs-lookup"><span data-stu-id="01f2b-204">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="01f2b-205">Tento název složky musí odpovídat názvu třídy zobrazení komponenty nebo název třídy minus příponu (Pokud jsme postupovali podle úmluvy a použít *ViewComponent* přípony v názvu třídy).</span><span class="sxs-lookup"><span data-stu-id="01f2b-205">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="01f2b-206">Pokud jste použili `ViewComponent` atribut, název třídy by musí odpovídat atributu označení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-206">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="01f2b-207">Vytvoření *Views/Shared/Components/PriorityList/Default.cshtml* zobrazení Razor: [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]</span><span class="sxs-lookup"><span data-stu-id="01f2b-207">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view: [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]</span></span>

   <span data-ttu-id="01f2b-208">Zobrazení Razor přebírá seznam `TodoItem` a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="01f2b-208">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="01f2b-209">Pokud komponentu zobrazení `InvokeAsync` metoda neprojde název zobrazení (jako v naší ukázce) *výchozí* používají konvence pro název zobrazení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-209">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="01f2b-210">Později v tomto kurzu můžu ukážeme, jak předat název zobrazení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-210">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="01f2b-211">Pokud chcete přepsat výchozí styl k určitému kontroleru, přidejte do specifické pro kontroler zobrazení složky zobrazení (například *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span><span class="sxs-lookup"><span data-stu-id="01f2b-211">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span></span>

    <span data-ttu-id="01f2b-212">Pokud je součást zobrazení specifické pro kontroler, můžete ho přidat do složky specifické pro kontroler (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="01f2b-212">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="01f2b-213">Přidat `div` obsahujícím volání do seznamu součástí priority k dolnímu okraji *Views/ToDo/index.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="01f2b-213">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="01f2b-214">Značky `@await Component.InvokeAsync` ukazuje syntaxi pro volání komponenty zobrazení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-214">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="01f2b-215">Prvním argumentem je název komponenty, které chceme volání nebo volání.</span><span class="sxs-lookup"><span data-stu-id="01f2b-215">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="01f2b-216">Následující parametry jsou předány do komponenty.</span><span class="sxs-lookup"><span data-stu-id="01f2b-216">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="01f2b-217">`InvokeAsync` můžete využít libovolný počet argumentů.</span><span class="sxs-lookup"><span data-stu-id="01f2b-217">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="01f2b-218">Testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="01f2b-218">Test the app.</span></span> <span data-ttu-id="01f2b-219">Následující obrázek ukazuje seznam úkolů a prioritu položky:</span><span class="sxs-lookup"><span data-stu-id="01f2b-219">The following image shows the ToDo list and the priority items:</span></span>

![seznam a prioritu položek todo](view-components/_static/pi.png)

<span data-ttu-id="01f2b-221">Komponenty zobrazení můžete také volat přímo z kontroleru:</span><span class="sxs-lookup"><span data-stu-id="01f2b-221">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![prioritu položek z IndexVC akce](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="01f2b-223">Zadejte název zobrazení</span><span class="sxs-lookup"><span data-stu-id="01f2b-223">Specifying a view name</span></span>

<span data-ttu-id="01f2b-224">Komponenta komplexní zobrazení může být nutné určit jiné než výchozí zobrazení za určitých podmínek.</span><span class="sxs-lookup"><span data-stu-id="01f2b-224">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="01f2b-225">Následující kód ukazuje, jak zadat "PVC" zobrazení z `InvokeAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="01f2b-225">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="01f2b-226">Aktualizace `InvokeAsync` metodu `PriorityListViewComponent` třídy.</span><span class="sxs-lookup"><span data-stu-id="01f2b-226">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="01f2b-227">Kopírovat *Views/Shared/Components/PriorityList/Default.cshtml* soubor k zobrazení s názvem *Views/Shared/Components/PriorityList/PVC.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="01f2b-227">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="01f2b-228">Přidáte záhlaví označující, že se používá PVC zobrazení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-228">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="01f2b-229">Update *Views/ToDo/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="01f2b-229">Update *Views/ToDo/Index.cshtml*:</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="01f2b-230">Spusťte aplikaci a ověřte PVC zobrazení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-230">Run the app and verify PVC view.</span></span>

![Komponenty zobrazení prioritní](view-components/_static/pvc.png)

<span data-ttu-id="01f2b-232">Pokud není PVC zobrazení vykresleno, ověřte, zda že jsou volání komponenty zobrazení s prioritou 4 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="01f2b-232">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="01f2b-233">Zkontrolujte cestu zobrazení</span><span class="sxs-lookup"><span data-stu-id="01f2b-233">Examine the view path</span></span>

* <span data-ttu-id="01f2b-234">Proto se vrátí zobrazení prioritní změňte parametr priority na tři nebo i rychleji.</span><span class="sxs-lookup"><span data-stu-id="01f2b-234">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="01f2b-235">Dočasně přejmenujte *Views/ToDo/Components/PriorityList/Default.cshtml* k *1Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="01f2b-235">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="01f2b-236">Testování aplikace, získáte následující chybu:</span><span class="sxs-lookup"><span data-stu-id="01f2b-236">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="01f2b-237">Kopírování *Views/ToDo/Components/PriorityList/1Default.cshtml* k *Views/Shared/Components/PriorityList/Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="01f2b-237">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="01f2b-238">Přidat některé značky *Shared* ToDo zobrazení komponenty k označení zobrazení je z *Shared* složky.</span><span class="sxs-lookup"><span data-stu-id="01f2b-238">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="01f2b-239">Test **Shared** součásti zobrazení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-239">Test the **Shared** component view.</span></span>

![Výstup úkolů s sdílené komponenty zobrazení](view-components/_static/shared.png)

### <a name="avoiding-magic-strings"></a><span data-ttu-id="01f2b-241">Jak se vyhnout magic řetězce</span><span class="sxs-lookup"><span data-stu-id="01f2b-241">Avoiding magic strings</span></span>

<span data-ttu-id="01f2b-242">Pokud chcete kompilovat bezpečný přístup z více času, můžete nahradit název komponenty pevně zakódované zobrazení s názvem třídy.</span><span class="sxs-lookup"><span data-stu-id="01f2b-242">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="01f2b-243">Vytvoření zobrazení komponenty bez přípony "ViewComponent":</span><span class="sxs-lookup"><span data-stu-id="01f2b-243">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="01f2b-244">Přidat `using` příkazu vaše Razor zobrazení souboru a použít `nameof` operátor:</span><span class="sxs-lookup"><span data-stu-id="01f2b-244">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="01f2b-245">Provedení synchronní práce</span><span class="sxs-lookup"><span data-stu-id="01f2b-245">Perform synchronous work</span></span>

<span data-ttu-id="01f2b-246">Rozhraní framework zpracovává volání synchronního `Invoke` metodu, pokud není nutné provádět asynchronní práce.</span><span class="sxs-lookup"><span data-stu-id="01f2b-246">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="01f2b-247">Následující metoda vytvoří synchronního `Invoke` zobrazení komponenty:</span><span class="sxs-lookup"><span data-stu-id="01f2b-247">The following method creates a synchronous `Invoke` view component:</span></span>

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

<span data-ttu-id="01f2b-248">Dílčí zobrazení Razor soubor obsahuje řetězce předaný `Invoke` – metoda (*Views/Home/Components/PriorityList/Default.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="01f2b-248">The view component's Razor file lists the strings passed to the `Invoke` method (*Views/Home/Components/PriorityList/Default.cshtml*):</span></span>

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

<span data-ttu-id="01f2b-249">Zobrazení komponenty je vyvolán v souboru Razor (například *Views/Home/Index.cshtml*) pomocí některého z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="01f2b-249">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="01f2b-250">Pomocná rutina značky</span><span class="sxs-lookup"><span data-stu-id="01f2b-250">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="01f2b-251">Použít <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> přístup, zavolejte `Component.InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="01f2b-251">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="01f2b-252">Zobrazení komponenty je vyvolán v souboru Razor (například *Views/Home/Index.cshtml*) s <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span><span class="sxs-lookup"><span data-stu-id="01f2b-252">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="01f2b-253">Volání `Component.InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="01f2b-253">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="01f2b-254">Použití pomocné rutiny značky, zaregistrovat sestavení obsahující pomocí zobrazení komponenty `@addTagHelper` – direktiva (součást zobrazení je v sestavení nazvané `MyWebApp`):</span><span class="sxs-lookup"><span data-stu-id="01f2b-254">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="01f2b-255">Použití zobrazení komponenty pomocné rutiny značky v souboru kódu Razor:</span><span class="sxs-lookup"><span data-stu-id="01f2b-255">Use the view component Tag Helper in the Razor markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```
::: moniker-end

<span data-ttu-id="01f2b-256">Označení metody `PriorityList.Invoke` je synchronní, ale Razor najde a volá metodu s `Component.InvokeAsync` v souboru označení.</span><span class="sxs-lookup"><span data-stu-id="01f2b-256">The method signature of `PriorityList.Invoke` is synchronous, but Razor finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="01f2b-257">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="01f2b-257">Additional resources</span></span>

* [<span data-ttu-id="01f2b-258">Injektáž závislostí do zobrazení</span><span class="sxs-lookup"><span data-stu-id="01f2b-258">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)
