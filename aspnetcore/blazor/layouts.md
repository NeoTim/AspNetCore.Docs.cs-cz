---
title: ASP.NET Core rozložení Blazor
author: guardrex
description: Naučte se vytvářet opakovaně použitelné součásti rozložení pro aplikace Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/layouts
ms.openlocfilehash: 05a38c10e18407d50422192ab1ddf3ff4b0f3a5b
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800372"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="5cae2-103">ASP.NET Core rozložení Blazor</span><span class="sxs-lookup"><span data-stu-id="5cae2-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="5cae2-104">Od [Rainer Stropek](https://www.timecockpit.com) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5cae2-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5cae2-105">Některé prvky aplikace, jako jsou nabídky, zprávy o autorských právech a logo společnosti, jsou obvykle součástí celkového rozložení aplikace a používají se v každé součásti aplikace.</span><span class="sxs-lookup"><span data-stu-id="5cae2-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="5cae2-106">Kopírování kódu těchto prvků do všech komponent aplikace není účinný přístup&mdash;pokaždé, když jeden z elementů vyžaduje aktualizaci, každá součást musí být aktualizována.</span><span class="sxs-lookup"><span data-stu-id="5cae2-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="5cae2-107">Takové duplikace je obtížné udržovat a může v průběhu času vést k nekonzistentnímu obsahu.</span><span class="sxs-lookup"><span data-stu-id="5cae2-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="5cae2-108">*Rozložení* řeší tento problém.</span><span class="sxs-lookup"><span data-stu-id="5cae2-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="5cae2-109">Technicky, rozložení je pouze jiná komponenta.</span><span class="sxs-lookup"><span data-stu-id="5cae2-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="5cae2-110">Rozložení je definováno v šabloně Razor nebo v C# kódu a může používat [datové vazby](xref:blazor/components#data-binding), [vkládání závislostí](xref:blazor/dependency-injection)a další scénáře komponent.</span><span class="sxs-lookup"><span data-stu-id="5cae2-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components#data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="5cae2-111">Chcete-li změnit *komponentu* na *rozložení*, součást:</span><span class="sxs-lookup"><span data-stu-id="5cae2-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="5cae2-112">Dědí z `LayoutComponentBase`, který `Body` definuje vlastnost pro vykreslený obsah v rozložení.</span><span class="sxs-lookup"><span data-stu-id="5cae2-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="5cae2-113">Používá syntaxe Razor `@Body` k určení umístění v označení rozložení, kde se obsah vykresluje.</span><span class="sxs-lookup"><span data-stu-id="5cae2-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="5cae2-114">Následující ukázka kódu ukazuje šablonu Razor komponenty layout *MainLayout. Razor*.</span><span class="sxs-lookup"><span data-stu-id="5cae2-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="5cae2-115">Rozložení dědí `LayoutComponentBase` a `@Body` nastavuje mezi navigačním panelem a zápatím:</span><span class="sxs-lookup"><span data-stu-id="5cae2-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="5cae2-116">V aplikaci založené na jedné z šablon `MainLayout` aplikace Blazor je součást (*MainLayout. Razor*) ve *sdílené* složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="5cae2-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="5cae2-117">Výchozí rozložení</span><span class="sxs-lookup"><span data-stu-id="5cae2-117">Default layout</span></span>

<span data-ttu-id="5cae2-118">Zadejte výchozí rozložení aplikace v `Router` komponentě v souboru App *. Razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="5cae2-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="5cae2-119">Následující `Router` komponenta, která je poskytována výchozími šablonami Blazor, nastaví výchozí rozložení `MainLayout` na součást:</span><span class="sxs-lookup"><span data-stu-id="5cae2-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="5cae2-120">Chcete-li zadat výchozí rozložení `NotFound` obsahu, `LayoutView` zadejte pro `NotFound` obsah:</span><span class="sxs-lookup"><span data-stu-id="5cae2-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="5cae2-121">Další informace o `Router` komponentě naleznete v tématu <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="5cae2-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="5cae2-122">Určení rozložení v součásti</span><span class="sxs-lookup"><span data-stu-id="5cae2-122">Specify a layout in a component</span></span>

<span data-ttu-id="5cae2-123">Použijte direktivu `@layout` Razor pro použití rozložení na komponentu.</span><span class="sxs-lookup"><span data-stu-id="5cae2-123">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="5cae2-124">Kompilátor převede `@layout` `LayoutAttribute`na, který je použit pro třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="5cae2-124">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="5cae2-125">Obsah následující `MasterList` komponenty je vložen `MasterLayout` do pozice v umístění `@Body`:</span><span class="sxs-lookup"><span data-stu-id="5cae2-125">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

## <a name="centralized-layout-selection"></a><span data-ttu-id="5cae2-126">Centralizovaný výběr rozložení</span><span class="sxs-lookup"><span data-stu-id="5cae2-126">Centralized layout selection</span></span>

<span data-ttu-id="5cae2-127">Každá složka aplikace může volitelně obsahovat soubor šablony s názvem *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="5cae2-127">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="5cae2-128">Kompilátor obsahuje direktivy uvedené v souboru Imports ve všech šablonách Razor ve stejné složce a rekurzivně ve všech jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="5cae2-128">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="5cae2-129">Proto soubor *_Imports. Razor* , který obsahuje `@layout MyCoolLayout` , zajistí, aby všechny součásti ve složce používaly `MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="5cae2-129">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="5cae2-130">Není nutné opakovaně přidávat `@layout MyCoolLayout` do všech souborů *. Razor* v rámci složky a podsložek.</span><span class="sxs-lookup"><span data-stu-id="5cae2-130">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="5cae2-131">`@using`direktivy jsou také aplikovány na komponenty stejným způsobem.</span><span class="sxs-lookup"><span data-stu-id="5cae2-131">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="5cae2-132">Následující soubor *_Imports. Razor* importuje:</span><span class="sxs-lookup"><span data-stu-id="5cae2-132">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="5cae2-133">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="5cae2-133">`MyCoolLayout`.</span></span>
* <span data-ttu-id="5cae2-134">Všechny součásti Razor ve stejné složce a v jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="5cae2-134">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="5cae2-135">`BlazorApp1.Data` Obor názvů.</span><span class="sxs-lookup"><span data-stu-id="5cae2-135">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="5cae2-136">Soubor *_Imports. Razor* je podobný [souboru _ViewImports. cshtml pro zobrazení a stránky Razor](xref:mvc/views/layout#importing-shared-directives) , ale používá se konkrétně pro soubory komponenty Razor.</span><span class="sxs-lookup"><span data-stu-id="5cae2-136">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="5cae2-137">Vnořená rozložení</span><span class="sxs-lookup"><span data-stu-id="5cae2-137">Nested layouts</span></span>

<span data-ttu-id="5cae2-138">Aplikace se můžou skládat z vnořených rozložení.</span><span class="sxs-lookup"><span data-stu-id="5cae2-138">Apps can consist of nested layouts.</span></span> <span data-ttu-id="5cae2-139">Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení.</span><span class="sxs-lookup"><span data-stu-id="5cae2-139">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="5cae2-140">Například vnořování rozložení slouží k vytvoření struktury nabídky na více úrovních.</span><span class="sxs-lookup"><span data-stu-id="5cae2-140">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="5cae2-141">Následující příklad ukazuje, jak použít vnořená rozložení.</span><span class="sxs-lookup"><span data-stu-id="5cae2-141">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="5cae2-142">Soubor *EpisodesComponent. Razor* je komponenta, která se má zobrazit.</span><span class="sxs-lookup"><span data-stu-id="5cae2-142">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="5cae2-143">Komponenta odkazuje na `MasterListLayout`:</span><span class="sxs-lookup"><span data-stu-id="5cae2-143">The component references the `MasterListLayout`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="5cae2-144">Soubor *MasterListLayout. Razor* poskytuje `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="5cae2-144">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="5cae2-145">Rozložení odkazuje na jiné rozložení, `MasterLayout`, kde je vykresleno.</span><span class="sxs-lookup"><span data-stu-id="5cae2-145">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="5cae2-146">`EpisodesComponent`je vykreslen, `@Body` kde se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="5cae2-146">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="5cae2-147">Nakonec v *MasterLayout. Razor* obsahuje prvky rozložení na nejvyšší úrovni, jako je záhlaví, hlavní nabídka a zápatí. `MasterLayout`</span><span class="sxs-lookup"><span data-stu-id="5cae2-147">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="5cae2-148">`MasterListLayout`tam, `EpisodesComponent` kde `@Body` se zobrazí, se vykreslí:</span><span class="sxs-lookup"><span data-stu-id="5cae2-148">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a><span data-ttu-id="5cae2-149">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5cae2-149">Additional resources</span></span>

* <xref:mvc/views/layout>
