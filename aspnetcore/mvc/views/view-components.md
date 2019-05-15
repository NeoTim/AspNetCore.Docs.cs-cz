---
title: Zobrazení komponenty v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak komponenty zobrazení se používají v ASP.NET Core a jejich přidání do aplikací.
ms.author: riande
ms.custom: mvc
ms.date: 5/14/2019
uid: mvc/views/view-components
ms.openlocfilehash: 17fd7aa977868d522df9f27e0c23d07b016bfb7c
ms.sourcegitcommit: 3ee6ee0051c3d2c8d47a58cb17eef1a84a4c46a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65621069"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="93fab-103">Zobrazení komponenty v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="93fab-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="93fab-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="93fab-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="93fab-105">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="93fab-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="93fab-106">Komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="93fab-106">View components</span></span>

<span data-ttu-id="93fab-107">Zobrazení komponenty jsou podobné částečná zobrazení, ale jsou výrazně výkonnější.</span><span class="sxs-lookup"><span data-stu-id="93fab-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="93fab-108">Zobrazení komponenty nepoužívejte vazby modelu a pouze závisí na poskytnutý při volání do něj data.</span><span class="sxs-lookup"><span data-stu-id="93fab-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="93fab-109">Tento článek byl zapsán pomocí kontrolerů a zobrazení, ale zobrazení komponenty také pracovat se stránkami Razor.</span><span class="sxs-lookup"><span data-stu-id="93fab-109">This article was written using controllers and views, but view components also work with Razor Pages.</span></span>

<span data-ttu-id="93fab-110">Zobrazení komponenty:</span><span class="sxs-lookup"><span data-stu-id="93fab-110">A view component:</span></span>

* <span data-ttu-id="93fab-111">Vykreslí blok dat, ne celou odpověď.</span><span class="sxs-lookup"><span data-stu-id="93fab-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="93fab-112">Zahrnuje stejné oddělení z otázky a výhody testovatelnosti najít mezi kontroler a zobrazení.</span><span class="sxs-lookup"><span data-stu-id="93fab-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="93fab-113">Můžete mít parametry a obchodní logiku.</span><span class="sxs-lookup"><span data-stu-id="93fab-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="93fab-114">Obvykle je vyvolána z rozložení stránky.</span><span class="sxs-lookup"><span data-stu-id="93fab-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="93fab-115">Zobrazení komponenty jsou určeny kdekoli, že máte opakovaně použitelný vykreslování logiku, která je příliš složitý pro částečné zobrazení, jako například:</span><span class="sxs-lookup"><span data-stu-id="93fab-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="93fab-116">Dynamické navigační nabídky</span><span class="sxs-lookup"><span data-stu-id="93fab-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="93fab-117">Značka cloudu (kde dotazuje databázi)</span><span class="sxs-lookup"><span data-stu-id="93fab-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="93fab-118">Panel přihlášení</span><span class="sxs-lookup"><span data-stu-id="93fab-118">Login panel</span></span>
* <span data-ttu-id="93fab-119">Nákupní košík</span><span class="sxs-lookup"><span data-stu-id="93fab-119">Shopping cart</span></span>
* <span data-ttu-id="93fab-120">Nedávno publikovaných článků</span><span class="sxs-lookup"><span data-stu-id="93fab-120">Recently published articles</span></span>
* <span data-ttu-id="93fab-121">Obsah bočního panelu na typické blogu</span><span class="sxs-lookup"><span data-stu-id="93fab-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="93fab-122">Panel přihlášení, který by být vykreslen na každé stránce a zobrazit odkazy na odhlášení nebo se přihlaste, v závislosti na protokolu ve stavu uživatele</span><span class="sxs-lookup"><span data-stu-id="93fab-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="93fab-123">Komponenty zobrazení se skládá ze dvou částí: třídy (obvykle odvozen z [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) a výsledek se vrátí (obvykle zobrazení).</span><span class="sxs-lookup"><span data-stu-id="93fab-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="93fab-124">Jako jsou řadiče, může být zobrazení komponenty POCO, ale Většina vývojářů budete chtít využívat výhod metod a vlastností, které jsou k dispozici odvozením z `ViewComponent`.</span><span class="sxs-lookup"><span data-stu-id="93fab-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="93fab-125">Při zvažování, pokud zobrazení komponenty splňovat požadavky vaší aplikace, zvažte místo toho použití součásti syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="93fab-125">When considering if view components meet an app's specifications, consider using Razor Components instead.</span></span> <span data-ttu-id="93fab-126">Součásti Razor také kombinovat kód s C# kód k vytvoření opakovaně použitelné uživatelské rozhraní jednotky.</span><span class="sxs-lookup"><span data-stu-id="93fab-126">Razor Components also combine markup with C# code to produce reusable UI units.</span></span> <span data-ttu-id="93fab-127">Razor komponenty jsou navržené pro produktivitu vývojářů, při zadávání složení a logika uživatelského rozhraní na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="93fab-127">Razor Components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="93fab-128">Další informace naleznete v tématu <xref:blazor/components>.</span><span class="sxs-lookup"><span data-stu-id="93fab-128">For more information, see <xref:blazor/components>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="93fab-129">Vytvoření zobrazení komponenty</span><span class="sxs-lookup"><span data-stu-id="93fab-129">Creating a view component</span></span>

<span data-ttu-id="93fab-130">Tato část obsahuje základní požadavky na vytvoření komponenty zobrazení.</span><span class="sxs-lookup"><span data-stu-id="93fab-130">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="93fab-131">Později v tomto článku vytvoříme Zkontrolujte každý krok podrobně a vytvořte komponentu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="93fab-131">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="93fab-132">Zobrazení komponentní třída</span><span class="sxs-lookup"><span data-stu-id="93fab-132">The view component class</span></span>

<span data-ttu-id="93fab-133">Komponentní třída zobrazení lze vytvořit pomocí některé z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="93fab-133">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="93fab-134">Odvozování z *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="93fab-134">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="93fab-135">Upravení třída s atributem `[ViewComponent]` atribut nebo odvozování z třídy s `[ViewComponent]` atribut</span><span class="sxs-lookup"><span data-stu-id="93fab-135">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="93fab-136">Vytvoření třídy, kde název končí příponou *ViewComponent*</span><span class="sxs-lookup"><span data-stu-id="93fab-136">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="93fab-137">Jako jsou řadiče zobrazení komponenty musí být veřejné, bez vnoření a neabstraktní třídy.</span><span class="sxs-lookup"><span data-stu-id="93fab-137">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="93fab-138">Název komponenty zobrazení je název třídy s příponou "ViewComponent" odebrat.</span><span class="sxs-lookup"><span data-stu-id="93fab-138">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="93fab-139">To se dá také explicitně nastavit pomocí `ViewComponentAttribute.Name` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="93fab-139">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="93fab-140">Zobrazení komponentní třída:</span><span class="sxs-lookup"><span data-stu-id="93fab-140">A view component class:</span></span>

* <span data-ttu-id="93fab-141">Plně podporuje konstruktor [injektáž závislostí](../../fundamentals/dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="93fab-141">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="93fab-142">Není účastnit životního cyklu kontroleru, což znamená, že nemůžete použít [filtry](../controllers/filters.md) v komponentě zobrazení</span><span class="sxs-lookup"><span data-stu-id="93fab-142">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="93fab-143">Zobrazení komponenty metody</span><span class="sxs-lookup"><span data-stu-id="93fab-143">View component methods</span></span>

<span data-ttu-id="93fab-144">Zobrazení komponenty definuje svou logikou v `InvokeAsync` metodu, která vrací `Task<IViewComponentResult>` nebo v synchronního `Invoke` metodu, která vrátí `IViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="93fab-144">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="93fab-145">Parametry pocházejí přímo z volání zobrazení komponenty, nikoli z vazby modelu.</span><span class="sxs-lookup"><span data-stu-id="93fab-145">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="93fab-146">Zobrazení komponenty nikdy přímo zpracovává žádost.</span><span class="sxs-lookup"><span data-stu-id="93fab-146">A view component never directly handles a request.</span></span> <span data-ttu-id="93fab-147">Obvykle inicializuje model zobrazení komponenty a předá ji do zobrazení voláním `View` metody.</span><span class="sxs-lookup"><span data-stu-id="93fab-147">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="93fab-148">Stručně řečeno zobrazte metody komponenty:</span><span class="sxs-lookup"><span data-stu-id="93fab-148">In summary, view component methods:</span></span>

* <span data-ttu-id="93fab-149">Definování `InvokeAsync` metodu, která vrací `Task<IViewComponentResult>` nebo synchronního `Invoke` metodu, která vrátí `IViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="93fab-149">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="93fab-150">Obvykle inicializuje model a předává je do zobrazení pomocí volání `ViewComponent` `View` metody.</span><span class="sxs-lookup"><span data-stu-id="93fab-150">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="93fab-151">Parametry pocházejí z volání metody, ne HTTP.</span><span class="sxs-lookup"><span data-stu-id="93fab-151">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="93fab-152">Neexistuje žádná vazba modelu.</span><span class="sxs-lookup"><span data-stu-id="93fab-152">There's no model binding.</span></span>
* <span data-ttu-id="93fab-153">Nejsou dostupné přímo jako koncový bod HTTP.</span><span class="sxs-lookup"><span data-stu-id="93fab-153">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="93fab-154">Se už vyvolána z uživatelského kódu (obvykle v zobrazení).</span><span class="sxs-lookup"><span data-stu-id="93fab-154">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="93fab-155">Zobrazení komponenty nikdy zpracuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="93fab-155">A view component never handles a request.</span></span>
* <span data-ttu-id="93fab-156">Jsou přetížené na podpis a nikoli na jakékoli podrobnosti z aktuální žádosti HTTP.</span><span class="sxs-lookup"><span data-stu-id="93fab-156">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="93fab-157">Zobrazení cesty pro hledání</span><span class="sxs-lookup"><span data-stu-id="93fab-157">View search path</span></span>

<span data-ttu-id="93fab-158">Modul runtime vyhledává zobrazení v následující cesty:</span><span class="sxs-lookup"><span data-stu-id="93fab-158">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="93fab-159">/Components/ /views/ {název řadiče} {název komponenty zobrazení} / {název zobrazení}</span><span class="sxs-lookup"><span data-stu-id="93fab-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="93fab-160">/ Zobrazení/Shared/Components / {View název komponenty} / {název zobrazení}</span><span class="sxs-lookup"><span data-stu-id="93fab-160">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="93fab-161">/ Stránek/Shared/Components / {View název komponenty} / {název zobrazení}</span><span class="sxs-lookup"><span data-stu-id="93fab-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="93fab-162">Cesty pro hledání se vztahují na projekty na použití kontrolerů a zobrazení a stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="93fab-162">The search path applies to projects using controllers + views and Razor Pages.</span></span>

<span data-ttu-id="93fab-163">Výchozí název zobrazení pro součást zobrazení je *výchozí*, což znamená, že váš soubor zobrazení se obvykle nazývá *stránku Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="93fab-163">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="93fab-164">Můžete zadat název jiné zobrazení, při vytváření komponenty výsledný objekt zobrazení, nebo při volání `View` metody.</span><span class="sxs-lookup"><span data-stu-id="93fab-164">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="93fab-165">Doporučujeme pojmenovat soubor zobrazení *stránku Default.cshtml* a použít *zobrazení/Shared/Components / {název komponenty zobrazení} / {název zobrazení}* cestu.</span><span class="sxs-lookup"><span data-stu-id="93fab-165">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="93fab-166">`PriorityList` Komponenta zobrazení používané v tomto příkladu používá *Views/Shared/Components/PriorityList/Default.cshtml* pro součásti zobrazení.</span><span class="sxs-lookup"><span data-stu-id="93fab-166">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="93fab-167">Vyvolání komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="93fab-167">Invoking a view component</span></span>

<span data-ttu-id="93fab-168">Chcete-li použít komponentu zobrazení, zavolejte následující uvnitř zobrazení:</span><span class="sxs-lookup"><span data-stu-id="93fab-168">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="93fab-169">Parametry předávané `InvokeAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="93fab-169">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="93fab-170">`PriorityList` z je vyvolána zobrazení komponenty vyvinuté v následujícím článku *Views/ToDo/Index.cshtml* zobrazení souboru.</span><span class="sxs-lookup"><span data-stu-id="93fab-170">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="93fab-171">V následujícím příkladu `InvokeAsync` metoda je volána s dva parametry:</span><span class="sxs-lookup"><span data-stu-id="93fab-171">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="93fab-172">Vyvolání komponenty zobrazení jako pomocné rutiny značky</span><span class="sxs-lookup"><span data-stu-id="93fab-172">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="93fab-173">Pro ASP.NET Core 1.1 a vyšší, můžete vyvolat komponentu zobrazení jako [pomocné rutiny značky](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="93fab-173">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="93fab-174">Jazyka Pascal – třídy a metody parametry pro pomocné rutiny značek jsou přeloženy do jejich [kebab případ](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span><span class="sxs-lookup"><span data-stu-id="93fab-174">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="93fab-175">Pomocná rutina značky k vyvolání komponenty zobrazení používá `<vc></vc>` elementu.</span><span class="sxs-lookup"><span data-stu-id="93fab-175">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="93fab-176">Zobrazení komponenty je určena následujícím způsobem:</span><span class="sxs-lookup"><span data-stu-id="93fab-176">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="93fab-177">Použití zobrazení komponenty jako pomocné rutiny značky, zaregistrovat sestavení obsahující pomocí zobrazení komponenty `@addTagHelper` směrnice.</span><span class="sxs-lookup"><span data-stu-id="93fab-177">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="93fab-178">Pokud vaše komponenta zobrazení je v sestavení nazvané `MyWebApp`, přidejte následující direktivy k *_ViewImports.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="93fab-178">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="93fab-179">Zobrazení komponenty můžete zaregistrovat jako pomocné rutiny značky do souboru, která odkazuje na součást zobrazení.</span><span class="sxs-lookup"><span data-stu-id="93fab-179">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="93fab-180">Zobrazit [Správa oboru pomocné rutiny značky](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) Další informace o tom, jak zaregistrovat pomocných rutin značek.</span><span class="sxs-lookup"><span data-stu-id="93fab-180">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="93fab-181">`InvokeAsync` Metodu použitou v tomto kurzu:</span><span class="sxs-lookup"><span data-stu-id="93fab-181">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="93fab-182">Ve značkách pomocné rutiny značky:</span><span class="sxs-lookup"><span data-stu-id="93fab-182">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="93fab-183">V příkladu výše `PriorityList` stane součástí zobrazení `priority-list`.</span><span class="sxs-lookup"><span data-stu-id="93fab-183">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="93fab-184">Parametry pro zobrazení komponenty jsou předány jako atributy v případě kebab.</span><span class="sxs-lookup"><span data-stu-id="93fab-184">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="93fab-185">Vyvolání komponenty zobrazení přímo z kontroleru</span><span class="sxs-lookup"><span data-stu-id="93fab-185">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="93fab-186">Zobrazení komponenty jsou obvykle vyvolány ze zobrazení, ale můžete je vyvolat přímo z metody kontroleru.</span><span class="sxs-lookup"><span data-stu-id="93fab-186">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="93fab-187">Při zobrazení komponenty nebudete definovat koncových bodů, jako jsou řadiče, je možné snadno implementovat akce kontroleru, který vrátí obsah `ViewComponentResult`.</span><span class="sxs-lookup"><span data-stu-id="93fab-187">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="93fab-188">V tomto příkladu je součásti zobrazení volání přímo z kontroleru:</span><span class="sxs-lookup"><span data-stu-id="93fab-188">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="93fab-189">Návod: Vytvoření jednoduché zobrazení komponenty</span><span class="sxs-lookup"><span data-stu-id="93fab-189">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="93fab-190">[Stáhněte si](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), sestavení a testování počátečního kódu.</span><span class="sxs-lookup"><span data-stu-id="93fab-190">[Download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="93fab-191">Je to Jednoduchý projekt s `ToDo` kontroler, který zobrazí seznam *ToDo* položky.</span><span class="sxs-lookup"><span data-stu-id="93fab-191">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![Seznam úloh, ať už](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="93fab-193">Přidejte třídu ViewComponent</span><span class="sxs-lookup"><span data-stu-id="93fab-193">Add a ViewComponent class</span></span>

<span data-ttu-id="93fab-194">Vytvoření *ViewComponents* složky a přidejte následující `PriorityListViewComponent` třídy:</span><span class="sxs-lookup"><span data-stu-id="93fab-194">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="93fab-195">Poznámky k kód:</span><span class="sxs-lookup"><span data-stu-id="93fab-195">Notes on the code:</span></span>

* <span data-ttu-id="93fab-196">Zobrazení komponentní třídy mohou být obsaženy v **jakékoli** složky v projektu.</span><span class="sxs-lookup"><span data-stu-id="93fab-196">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="93fab-197">Protože třída název PriorityList**ViewComponent** končí příponou **ViewComponent**, modul runtime použije řetězec "PriorityList" při odkazování na komponentě třídy ze zobrazení.</span><span class="sxs-lookup"><span data-stu-id="93fab-197">Because the class name PriorityList**ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="93fab-198">Já zatím vysvětlím, které podrobněji později.</span><span class="sxs-lookup"><span data-stu-id="93fab-198">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="93fab-199">`[ViewComponent]` Atribut můžete změnit název slouží jako odkaz na komponentu zobrazení.</span><span class="sxs-lookup"><span data-stu-id="93fab-199">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="93fab-200">Například jsme mohli jsme s názvem třídy `XYZ` a použít `ViewComponent` atribut:</span><span class="sxs-lookup"><span data-stu-id="93fab-200">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="93fab-201">`[ViewComponent]` Výše uvedený atribut říká Výběr komponent zobrazení použít název `PriorityList` při hledání zobrazení související s komponentou a použít řetězec "PriorityList" při odkazování na komponentě třídy ze zobrazení.</span><span class="sxs-lookup"><span data-stu-id="93fab-201">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="93fab-202">Já zatím vysvětlím, které podrobněji později.</span><span class="sxs-lookup"><span data-stu-id="93fab-202">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="93fab-203">Součást používá [injektáž závislostí](../../fundamentals/dependency-injection.md) zpřístupnit datového kontextu.</span><span class="sxs-lookup"><span data-stu-id="93fab-203">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="93fab-204">`InvokeAsync` Zpřístupní metodu, která může být volána z zobrazení a to může trvat libovolný počet argumentů.</span><span class="sxs-lookup"><span data-stu-id="93fab-204">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="93fab-205">`InvokeAsync` Metoda vrátí sadu `ToDo` položky, které splňují `isDone` a `maxPriority` parametry.</span><span class="sxs-lookup"><span data-stu-id="93fab-205">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-razor-view"></a><span data-ttu-id="93fab-206">Vytvořit zobrazení Razor komponenty zobrazení</span><span class="sxs-lookup"><span data-stu-id="93fab-206">Create the view component Razor view</span></span>

* <span data-ttu-id="93fab-207">Vytvořte *zobrazení/Shared/Components* složky.</span><span class="sxs-lookup"><span data-stu-id="93fab-207">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="93fab-208">Tato složka **musí** jmenovat *komponenty*.</span><span class="sxs-lookup"><span data-stu-id="93fab-208">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="93fab-209">Vytvořte *zobrazení/Shared/součásti/PriorityList* složky.</span><span class="sxs-lookup"><span data-stu-id="93fab-209">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="93fab-210">Tento název složky musí odpovídat názvu třídy zobrazení komponenty nebo název třídy minus příponu (Pokud jsme postupovali podle úmluvy a použít *ViewComponent* přípony v názvu třídy).</span><span class="sxs-lookup"><span data-stu-id="93fab-210">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="93fab-211">Pokud jste použili `ViewComponent` atribut, název třídy by musí odpovídat atributu označení.</span><span class="sxs-lookup"><span data-stu-id="93fab-211">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="93fab-212">Vytvoření *Views/Shared/Components/PriorityList/Default.cshtml* zobrazení Razor:</span><span class="sxs-lookup"><span data-stu-id="93fab-212">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="93fab-213">Zobrazení Razor přebírá seznam `TodoItem` a zobrazí je.</span><span class="sxs-lookup"><span data-stu-id="93fab-213">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="93fab-214">Pokud komponentu zobrazení `InvokeAsync` metoda neprojde název zobrazení (jako v naší ukázce) *výchozí* používají konvence pro název zobrazení.</span><span class="sxs-lookup"><span data-stu-id="93fab-214">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="93fab-215">Později v tomto kurzu můžu ukážeme, jak předat název zobrazení.</span><span class="sxs-lookup"><span data-stu-id="93fab-215">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="93fab-216">Pokud chcete přepsat výchozí styl k určitému kontroleru, přidejte do specifické pro kontroler zobrazení složky zobrazení (například *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span><span class="sxs-lookup"><span data-stu-id="93fab-216">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span></span>

    <span data-ttu-id="93fab-217">Pokud je součást zobrazení specifické pro kontroler, můžete ho přidat do složky specifické pro kontroler (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="93fab-217">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="93fab-218">Přidat `div` obsahujícím volání do seznamu součástí priority k dolnímu okraji *Views/ToDo/index.cshtml* souboru:</span><span class="sxs-lookup"><span data-stu-id="93fab-218">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="93fab-219">Značky `@await Component.InvokeAsync` ukazuje syntaxi pro volání komponenty zobrazení.</span><span class="sxs-lookup"><span data-stu-id="93fab-219">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="93fab-220">Prvním argumentem je název komponenty, které chceme volání nebo volání.</span><span class="sxs-lookup"><span data-stu-id="93fab-220">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="93fab-221">Následující parametry jsou předány do komponenty.</span><span class="sxs-lookup"><span data-stu-id="93fab-221">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="93fab-222">`InvokeAsync` můžete využít libovolný počet argumentů.</span><span class="sxs-lookup"><span data-stu-id="93fab-222">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="93fab-223">Testování aplikace.</span><span class="sxs-lookup"><span data-stu-id="93fab-223">Test the app.</span></span> <span data-ttu-id="93fab-224">Následující obrázek ukazuje seznam úkolů a prioritu položky:</span><span class="sxs-lookup"><span data-stu-id="93fab-224">The following image shows the ToDo list and the priority items:</span></span>

![seznam a prioritu položek todo](view-components/_static/pi.png)

<span data-ttu-id="93fab-226">Komponenty zobrazení můžete také volat přímo z kontroleru:</span><span class="sxs-lookup"><span data-stu-id="93fab-226">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![prioritu položek z IndexVC akce](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="93fab-228">Zadejte název zobrazení</span><span class="sxs-lookup"><span data-stu-id="93fab-228">Specifying a view name</span></span>

<span data-ttu-id="93fab-229">Komponenta komplexní zobrazení může být nutné určit jiné než výchozí zobrazení za určitých podmínek.</span><span class="sxs-lookup"><span data-stu-id="93fab-229">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="93fab-230">Následující kód ukazuje, jak zadat "PVC" zobrazení z `InvokeAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="93fab-230">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="93fab-231">Aktualizace `InvokeAsync` metodu `PriorityListViewComponent` třídy.</span><span class="sxs-lookup"><span data-stu-id="93fab-231">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="93fab-232">Kopírovat *Views/Shared/Components/PriorityList/Default.cshtml* soubor k zobrazení s názvem *Views/Shared/Components/PriorityList/PVC.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="93fab-232">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="93fab-233">Přidáte záhlaví označující, že se používá PVC zobrazení.</span><span class="sxs-lookup"><span data-stu-id="93fab-233">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="93fab-234">Update *Views/ToDo/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="93fab-234">Update *Views/ToDo/Index.cshtml*:</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="93fab-235">Spusťte aplikaci a ověřte PVC zobrazení.</span><span class="sxs-lookup"><span data-stu-id="93fab-235">Run the app and verify PVC view.</span></span>

![Komponenty zobrazení prioritní](view-components/_static/pvc.png)

<span data-ttu-id="93fab-237">Pokud není PVC zobrazení vykresleno, ověřte, zda že jsou volání komponenty zobrazení s prioritou 4 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="93fab-237">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="93fab-238">Zkontrolujte cestu zobrazení</span><span class="sxs-lookup"><span data-stu-id="93fab-238">Examine the view path</span></span>

* <span data-ttu-id="93fab-239">Proto se vrátí zobrazení prioritní změňte parametr priority na tři nebo i rychleji.</span><span class="sxs-lookup"><span data-stu-id="93fab-239">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="93fab-240">Dočasně přejmenujte *Views/ToDo/Components/PriorityList/Default.cshtml* k *1Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="93fab-240">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="93fab-241">Testování aplikace, získáte následující chybu:</span><span class="sxs-lookup"><span data-stu-id="93fab-241">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="93fab-242">Kopírování *Views/ToDo/Components/PriorityList/1Default.cshtml* k *Views/Shared/Components/PriorityList/Default.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="93fab-242">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="93fab-243">Přidat některé značky *Shared* ToDo zobrazení komponenty k označení zobrazení je z *Shared* složky.</span><span class="sxs-lookup"><span data-stu-id="93fab-243">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="93fab-244">Test **Shared** součásti zobrazení.</span><span class="sxs-lookup"><span data-stu-id="93fab-244">Test the **Shared** component view.</span></span>

![Výstup úkolů s sdílené komponenty zobrazení](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="93fab-246">Jak se vyhnout pevně zakódované řetězce</span><span class="sxs-lookup"><span data-stu-id="93fab-246">Avoiding hard-coded strings</span></span>

<span data-ttu-id="93fab-247">Pokud chcete kompilovat bezpečný přístup z více času, můžete nahradit název komponenty pevně zakódované zobrazení s názvem třídy.</span><span class="sxs-lookup"><span data-stu-id="93fab-247">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="93fab-248">Vytvoření zobrazení komponenty bez přípony "ViewComponent":</span><span class="sxs-lookup"><span data-stu-id="93fab-248">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="93fab-249">Přidat `using` příkazu vaše Razor zobrazení souboru a použít `nameof` operátor:</span><span class="sxs-lookup"><span data-stu-id="93fab-249">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="93fab-250">Provedení synchronní práce</span><span class="sxs-lookup"><span data-stu-id="93fab-250">Perform synchronous work</span></span>

<span data-ttu-id="93fab-251">Rozhraní framework zpracovává volání synchronního `Invoke` metodu, pokud není nutné provádět asynchronní práce.</span><span class="sxs-lookup"><span data-stu-id="93fab-251">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="93fab-252">Následující metoda vytvoří synchronního `Invoke` zobrazení komponenty:</span><span class="sxs-lookup"><span data-stu-id="93fab-252">The following method creates a synchronous `Invoke` view component:</span></span>

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

<span data-ttu-id="93fab-253">Dílčí zobrazení Razor soubor obsahuje řetězce předaný `Invoke` – metoda (*Views/Home/Components/PriorityList/Default.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="93fab-253">The view component's Razor file lists the strings passed to the `Invoke` method (*Views/Home/Components/PriorityList/Default.cshtml*):</span></span>

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

<span data-ttu-id="93fab-254">Zobrazení komponenty je vyvolán v souboru Razor (například *Views/Home/Index.cshtml*) pomocí některého z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="93fab-254">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="93fab-255">Pomocná rutina značky</span><span class="sxs-lookup"><span data-stu-id="93fab-255">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="93fab-256">Použít <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> přístup, zavolejte `Component.InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="93fab-256">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="93fab-257">Zobrazení komponenty je vyvolán v souboru Razor (například *Views/Home/Index.cshtml*) s <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span><span class="sxs-lookup"><span data-stu-id="93fab-257">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="93fab-258">Volání `Component.InvokeAsync`:</span><span class="sxs-lookup"><span data-stu-id="93fab-258">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="93fab-259">Použití pomocné rutiny značky, zaregistrovat sestavení obsahující pomocí zobrazení komponenty `@addTagHelper` – direktiva (součást zobrazení je v sestavení nazvané `MyWebApp`):</span><span class="sxs-lookup"><span data-stu-id="93fab-259">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="93fab-260">Použití zobrazení komponenty pomocné rutiny značky v souboru kódu Razor:</span><span class="sxs-lookup"><span data-stu-id="93fab-260">Use the view component Tag Helper in the Razor markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="93fab-261">Označení metody `PriorityList.Invoke` je synchronní, ale Razor najde a volá metodu s `Component.InvokeAsync` v souboru označení.</span><span class="sxs-lookup"><span data-stu-id="93fab-261">The method signature of `PriorityList.Invoke` is synchronous, but Razor finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="93fab-262">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="93fab-262">Additional resources</span></span>

* [<span data-ttu-id="93fab-263">Injektáž závislostí do zobrazení</span><span class="sxs-lookup"><span data-stu-id="93fab-263">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)
