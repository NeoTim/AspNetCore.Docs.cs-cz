---
title: ASP.NET Core Blazor rozložení
author: guardrex
description: Naučte se vytvářet opakovaně použitelné součásti rozložení pro Blazor aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5c6771dd7249bfb8280ba20e1ce75967f279971c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771582"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="2e14b-103">ASP.NET Core Blazor rozložení</span><span class="sxs-lookup"><span data-stu-id="2e14b-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="2e14b-104">Od [Rainer Stropek](https://www.timecockpit.com) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2e14b-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2e14b-105">Některé prvky aplikace, jako jsou nabídky, zprávy o autorských právech a logo společnosti, jsou obvykle součástí celkového rozložení aplikace a používají se v každé součásti aplikace.</span><span class="sxs-lookup"><span data-stu-id="2e14b-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="2e14b-106">Kopírování kódu těchto prvků do všech komponent aplikace není účinný přístup&mdash;pokaždé, když jeden z elementů vyžaduje aktualizaci, každá součást musí být aktualizována.</span><span class="sxs-lookup"><span data-stu-id="2e14b-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="2e14b-107">Takové duplikace je obtížné udržovat a může v průběhu času vést k nekonzistentnímu obsahu.</span><span class="sxs-lookup"><span data-stu-id="2e14b-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="2e14b-108">*Rozložení* řeší tento problém.</span><span class="sxs-lookup"><span data-stu-id="2e14b-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="2e14b-109">Technicky, rozložení je pouze jiná komponenta.</span><span class="sxs-lookup"><span data-stu-id="2e14b-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="2e14b-110">Rozložení je definováno v Razor šabloně nebo v kódu jazyka C# a může používat [datové vazby](xref:blazor/data-binding), [vkládání závislostí](xref:blazor/dependency-injection)a další scénáře komponent.</span><span class="sxs-lookup"><span data-stu-id="2e14b-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="2e14b-111">Chcete-li změnit *komponentu* na *rozložení*, součást:</span><span class="sxs-lookup"><span data-stu-id="2e14b-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="2e14b-112">Dědí z `LayoutComponentBase`, který definuje `Body` vlastnost pro vykreslený obsah v rozložení.</span><span class="sxs-lookup"><span data-stu-id="2e14b-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="2e14b-113">Používá Razor syntaxi `@Body` k určení umístění v označení rozložení, kde je obsah vykreslen.</span><span class="sxs-lookup"><span data-stu-id="2e14b-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="2e14b-114">Následující ukázka kódu ukazuje Razor šablonu komponenty rozložení *MainLayout. Razor*.</span><span class="sxs-lookup"><span data-stu-id="2e14b-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="2e14b-115">Rozložení dědí `LayoutComponentBase` a nastavuje `@Body` mezi navigačním panelem a zápatím:</span><span class="sxs-lookup"><span data-stu-id="2e14b-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="2e14b-116">V aplikaci založené na Blazor jedné ze šablon aplikací je `MainLayout` součást (*MainLayout. Razor*) ve *sdílené* složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="2e14b-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="2e14b-117">Výchozí rozložení</span><span class="sxs-lookup"><span data-stu-id="2e14b-117">Default layout</span></span>

<span data-ttu-id="2e14b-118">Zadejte výchozí rozložení aplikace v `Router` komponentě v souboru App *. Razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="2e14b-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="2e14b-119">Následující `Router` komponenta, která je poskytována výchozími Blazor šablonami, nastaví výchozí rozložení na `MainLayout` součást:</span><span class="sxs-lookup"><span data-stu-id="2e14b-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="2e14b-120">Chcete-li zadat výchozí rozložení `NotFound` obsahu, zadejte `LayoutView` pro `NotFound` obsah:</span><span class="sxs-lookup"><span data-stu-id="2e14b-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="2e14b-121">Další informace o `Router` komponentě naleznete v tématu <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="2e14b-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="2e14b-122">Určení rozložení jako výchozího rozložení ve směrovači je užitečný postup, protože je možné ho přepsat pro jednotlivé komponenty nebo pro jednotlivé složky.</span><span class="sxs-lookup"><span data-stu-id="2e14b-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="2e14b-123">Upřednostňujete použití směrovače k nastavení výchozího rozložení aplikace, protože se jedná o nejobecnější postup.</span><span class="sxs-lookup"><span data-stu-id="2e14b-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="2e14b-124">Určení rozložení v součásti</span><span class="sxs-lookup"><span data-stu-id="2e14b-124">Specify a layout in a component</span></span>

<span data-ttu-id="2e14b-125">Použijte Razor direktivu `@layout` pro použití rozložení na komponentu.</span><span class="sxs-lookup"><span data-stu-id="2e14b-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="2e14b-126">Kompilátor převede `@layout` na `LayoutAttribute`, který je použit pro třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="2e14b-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="2e14b-127">Obsah následující `MasterList` komponenty je vložen do `MasterLayout` pozice v umístění: `@Body`</span><span class="sxs-lookup"><span data-stu-id="2e14b-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="2e14b-128">Určením rozložení přímo v součásti dojde k přepsání *výchozí sady rozložení* ve směrovači nebo `@layout` direktivy importované z *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="2e14b-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="2e14b-129">Centralizovaný výběr rozložení</span><span class="sxs-lookup"><span data-stu-id="2e14b-129">Centralized layout selection</span></span>

<span data-ttu-id="2e14b-130">Každá složka aplikace může volitelně obsahovat soubor šablony s názvem *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="2e14b-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="2e14b-131">Kompilátor obsahuje direktivy uvedené v souboru Imports ve všech Razor šablonách ve stejné složce a rekurzivně ve všech jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="2e14b-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="2e14b-132">Proto soubor *_Imports. Razor* , který obsahuje `@layout MyCoolLayout` , zajistí, aby všechny součásti ve složce používaly `MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="2e14b-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="2e14b-133">Není nutné opakovaně přidávat `@layout MyCoolLayout` do všech souborů *. Razor* v rámci složky a podsložek.</span><span class="sxs-lookup"><span data-stu-id="2e14b-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="2e14b-134">`@using`direktivy jsou také aplikovány na komponenty stejným způsobem.</span><span class="sxs-lookup"><span data-stu-id="2e14b-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="2e14b-135">Následující soubor *_Imports. Razor* importuje:</span><span class="sxs-lookup"><span data-stu-id="2e14b-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="2e14b-136">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="2e14b-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="2e14b-137">Všechny Razor součásti ve stejné složce a v jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="2e14b-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="2e14b-138">`BlazorApp1.Data` Obor názvů.</span><span class="sxs-lookup"><span data-stu-id="2e14b-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="2e14b-139">Soubor *_Imports. Razor* je podobný [souboru _ViewImports. cshtml pro Razor zobrazení a stránky](xref:mvc/views/layout#importing-shared-directives) , ale používá se konkrétně pro Razor soubory komponent.</span><span class="sxs-lookup"><span data-stu-id="2e14b-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="2e14b-140">Určení rozložení v *_Imports. Razor* přepíše rozložení zadané jako *výchozí rozložení*směrovače.</span><span class="sxs-lookup"><span data-stu-id="2e14b-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="2e14b-141">Vnořená rozložení</span><span class="sxs-lookup"><span data-stu-id="2e14b-141">Nested layouts</span></span>

<span data-ttu-id="2e14b-142">Aplikace se můžou skládat z vnořených rozložení.</span><span class="sxs-lookup"><span data-stu-id="2e14b-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="2e14b-143">Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení.</span><span class="sxs-lookup"><span data-stu-id="2e14b-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="2e14b-144">Například vnořování rozložení slouží k vytvoření struktury nabídky na více úrovních.</span><span class="sxs-lookup"><span data-stu-id="2e14b-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="2e14b-145">Následující příklad ukazuje, jak použít vnořená rozložení.</span><span class="sxs-lookup"><span data-stu-id="2e14b-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="2e14b-146">Soubor *EpisodesComponent. Razor* je komponenta, která se má zobrazit.</span><span class="sxs-lookup"><span data-stu-id="2e14b-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="2e14b-147">Komponenta odkazuje na `MasterListLayout`:</span><span class="sxs-lookup"><span data-stu-id="2e14b-147">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="2e14b-148">Soubor *MasterListLayout. Razor* poskytuje `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="2e14b-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="2e14b-149">Rozložení odkazuje na jiné rozložení, `MasterLayout`, kde je vykresleno.</span><span class="sxs-lookup"><span data-stu-id="2e14b-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="2e14b-150">`EpisodesComponent`je vykreslen, `@Body` kde se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="2e14b-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="2e14b-151">Nakonec `MasterLayout` v *MasterLayout. Razor* obsahuje prvky rozložení na nejvyšší úrovni, jako je záhlaví, hlavní nabídka a zápatí.</span><span class="sxs-lookup"><span data-stu-id="2e14b-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="2e14b-152">`MasterListLayout`tam, `EpisodesComponent` kde `@Body` se zobrazí, se vykreslí:</span><span class="sxs-lookup"><span data-stu-id="2e14b-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="2e14b-153">Sdílení rozložení Razor stránek pomocí integrovaných komponent</span><span class="sxs-lookup"><span data-stu-id="2e14b-153">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="2e14b-154">Pokud jsou směrovatelný komponenty integrovány do Razor aplikace Pages, lze použít sdílené rozložení aplikace spolu s komponentami.</span><span class="sxs-lookup"><span data-stu-id="2e14b-154">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="2e14b-155">Další informace naleznete v tématu <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="2e14b-155">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e14b-156">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2e14b-156">Additional resources</span></span>

* <xref:mvc/views/layout>
