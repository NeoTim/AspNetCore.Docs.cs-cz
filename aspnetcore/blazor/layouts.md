---
title: ASP.NET Core Blazor rozložení
author: guardrex
description: Naučte se vytvářet opakovaně použitelné součásti rozložení pro aplikace Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 8e7294f6b66d34781473522a71f929ed5f9c33f2
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213373"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a><span data-ttu-id="dbce0-103">ASP.NET Core Blazor rozložení</span><span class="sxs-lookup"><span data-stu-id="dbce0-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="dbce0-104">Od [Rainer Stropek](https://www.timecockpit.com) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="dbce0-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="dbce0-105">Některé prvky aplikace, jako jsou nabídky, zprávy o autorských právech a logo společnosti, jsou obvykle součástí celkového rozložení aplikace a používají se v každé součásti aplikace.</span><span class="sxs-lookup"><span data-stu-id="dbce0-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="dbce0-106">Kopírování kódu těchto prvků do všech komponent aplikace není účinný přístup&mdash;pokaždé, když jeden z elementů vyžaduje aktualizaci, musí být každá součást aktualizována.</span><span class="sxs-lookup"><span data-stu-id="dbce0-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="dbce0-107">Takové duplikace je obtížné udržovat a může v průběhu času vést k nekonzistentnímu obsahu.</span><span class="sxs-lookup"><span data-stu-id="dbce0-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="dbce0-108">*Rozložení* řeší tento problém.</span><span class="sxs-lookup"><span data-stu-id="dbce0-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="dbce0-109">Technicky, rozložení je pouze jiná komponenta.</span><span class="sxs-lookup"><span data-stu-id="dbce0-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="dbce0-110">Rozložení je definováno v šabloně Razor nebo v C# kódu a může používat [datové vazby](xref:blazor/components#data-binding), [vkládání závislostí](xref:blazor/dependency-injection)a další scénáře komponent.</span><span class="sxs-lookup"><span data-stu-id="dbce0-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components#data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="dbce0-111">Chcete-li změnit *komponentu* na *rozložení*, součást:</span><span class="sxs-lookup"><span data-stu-id="dbce0-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="dbce0-112">Dědí z `LayoutComponentBase`, který definuje vlastnost `Body` vykresleného obsahu v rámci rozložení.</span><span class="sxs-lookup"><span data-stu-id="dbce0-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="dbce0-113">Používá `@Body` syntaxe Razor k určení umístění v označení rozložení, kde se obsah vykresluje.</span><span class="sxs-lookup"><span data-stu-id="dbce0-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="dbce0-114">Následující ukázka kódu ukazuje šablonu Razor komponenty layout *MainLayout. Razor*.</span><span class="sxs-lookup"><span data-stu-id="dbce0-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="dbce0-115">Rozložení dědí `LayoutComponentBase` a nastaví `@Body` mezi navigačním panelem a zápatím:</span><span class="sxs-lookup"><span data-stu-id="dbce0-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="dbce0-116">V aplikaci, která je založená na jedné z Blazor šablon aplikací, se `MainLayout` součást (*MainLayout. Razor*) nachází ve *sdílené* složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="dbce0-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="dbce0-117">Výchozí rozložení</span><span class="sxs-lookup"><span data-stu-id="dbce0-117">Default layout</span></span>

<span data-ttu-id="dbce0-118">Zadejte výchozí rozložení aplikace v součásti `Router` v souboru App *. Razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="dbce0-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="dbce0-119">Následující součást `Router`, která je poskytována výchozími šablonami Blazor, nastaví výchozí rozložení na součást `MainLayout`:</span><span class="sxs-lookup"><span data-stu-id="dbce0-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="dbce0-120">Chcete-li zadat výchozí rozložení pro `NotFound` obsah, zadejte `LayoutView` pro `NotFound` obsah:</span><span class="sxs-lookup"><span data-stu-id="dbce0-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="dbce0-121">Další informace o komponentě `Router` najdete v tématu <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="dbce0-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="dbce0-122">Určení rozložení jako výchozího rozložení ve směrovači je užitečný postup, protože je možné ho přepsat pro jednotlivé komponenty nebo pro jednotlivé složky.</span><span class="sxs-lookup"><span data-stu-id="dbce0-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="dbce0-123">Upřednostňujete použití směrovače k nastavení výchozího rozložení aplikace, protože se jedná o nejobecnější postup.</span><span class="sxs-lookup"><span data-stu-id="dbce0-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="dbce0-124">Určení rozložení v součásti</span><span class="sxs-lookup"><span data-stu-id="dbce0-124">Specify a layout in a component</span></span>

<span data-ttu-id="dbce0-125">Použijte `@layout` direktivy Razor pro použití rozložení na komponentu.</span><span class="sxs-lookup"><span data-stu-id="dbce0-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="dbce0-126">Kompilátor převede `@layout` na `LayoutAttribute`, který je použit pro třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="dbce0-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="dbce0-127">Obsah následující `MasterList` komponenty je vložen do `MasterLayout` na pozici `@Body`:</span><span class="sxs-lookup"><span data-stu-id="dbce0-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="dbce0-128">Určením rozložení přímo v součásti dojde k přepsání *výchozí sady rozložení* ve směrovači nebo direktivy `@layout` naimportované z *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="dbce0-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="dbce0-129">Centralizovaný výběr rozložení</span><span class="sxs-lookup"><span data-stu-id="dbce0-129">Centralized layout selection</span></span>

<span data-ttu-id="dbce0-130">Každá složka aplikace může volitelně obsahovat soubor šablony s názvem *_Imports. Razor*.</span><span class="sxs-lookup"><span data-stu-id="dbce0-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="dbce0-131">Kompilátor obsahuje direktivy uvedené v souboru Imports ve všech šablonách Razor ve stejné složce a rekurzivně ve všech jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="dbce0-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="dbce0-132">Proto soubor *_Imports. Razor* obsahující `@layout MyCoolLayout` zajistí, že všechny součásti ve složce používají `MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="dbce0-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="dbce0-133">Nemusíte opakovaně přidávat `@layout MyCoolLayout` do všech souborů *. Razor* v rámci složky a podsložek.</span><span class="sxs-lookup"><span data-stu-id="dbce0-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="dbce0-134">direktivy `@using` jsou také aplikovány na komponenty stejným způsobem.</span><span class="sxs-lookup"><span data-stu-id="dbce0-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="dbce0-135">Následující soubor *_Imports. Razor* importuje:</span><span class="sxs-lookup"><span data-stu-id="dbce0-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="dbce0-136">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="dbce0-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="dbce0-137">Všechny součásti Razor ve stejné složce a v jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="dbce0-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="dbce0-138">Obor názvů `BlazorApp1.Data`.</span><span class="sxs-lookup"><span data-stu-id="dbce0-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="dbce0-139">Soubor *_Imports. Razor* je podobný [souboru _ViewImports. cshtml pro zobrazení a stránky Razor](xref:mvc/views/layout#importing-shared-directives) , ale používá se konkrétně pro soubory komponenty Razor.</span><span class="sxs-lookup"><span data-stu-id="dbce0-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="dbce0-140">Určení rozložení v *_Imports. Razor* přepíše rozložení zadané jako *výchozí rozložení*směrovače.</span><span class="sxs-lookup"><span data-stu-id="dbce0-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="dbce0-141">Vnořená rozložení</span><span class="sxs-lookup"><span data-stu-id="dbce0-141">Nested layouts</span></span>

<span data-ttu-id="dbce0-142">Aplikace se můžou skládat z vnořených rozložení.</span><span class="sxs-lookup"><span data-stu-id="dbce0-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="dbce0-143">Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení.</span><span class="sxs-lookup"><span data-stu-id="dbce0-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="dbce0-144">Například vnořování rozložení slouží k vytvoření struktury nabídky na více úrovních.</span><span class="sxs-lookup"><span data-stu-id="dbce0-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="dbce0-145">Následující příklad ukazuje, jak použít vnořená rozložení.</span><span class="sxs-lookup"><span data-stu-id="dbce0-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="dbce0-146">Soubor *EpisodesComponent. Razor* je komponenta, která se má zobrazit.</span><span class="sxs-lookup"><span data-stu-id="dbce0-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="dbce0-147">Komponenta odkazuje na `MasterListLayout`:</span><span class="sxs-lookup"><span data-stu-id="dbce0-147">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="dbce0-148">Soubor *MasterListLayout. Razor* poskytuje `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="dbce0-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="dbce0-149">Rozložení odkazuje na jiné rozložení, `MasterLayout`, kde je vykresleno.</span><span class="sxs-lookup"><span data-stu-id="dbce0-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="dbce0-150">`EpisodesComponent` se vykreslí, kde se `@Body` zobrazí:</span><span class="sxs-lookup"><span data-stu-id="dbce0-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="dbce0-151">Nakonec `MasterLayout` v *MasterLayout. Razor* obsahuje prvky rozložení na nejvyšší úrovni, jako je záhlaví, hlavní nabídka a zápatí.</span><span class="sxs-lookup"><span data-stu-id="dbce0-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="dbce0-152">`MasterListLayout` `EpisodesComponent` se vykreslí tam, kde se zobrazí `@Body`:</span><span class="sxs-lookup"><span data-stu-id="dbce0-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="dbce0-153">Sdílení Razor Pages rozložení s integrovanými komponentami</span><span class="sxs-lookup"><span data-stu-id="dbce0-153">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="dbce0-154">Pokud jsou směrovatelný komponenty integrovány do aplikace Razor Pages, lze s komponentami použít sdílené rozložení aplikace.</span><span class="sxs-lookup"><span data-stu-id="dbce0-154">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="dbce0-155">Další informace najdete v tématu <xref:blazor/hosting-model-configuration#integrate-razor-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="dbce0-155">For more information, see <xref:blazor/hosting-model-configuration#integrate-razor-components-into-razor-pages-and-mvc-apps>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dbce0-156">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="dbce0-156">Additional resources</span></span>

* <xref:mvc/views/layout>
