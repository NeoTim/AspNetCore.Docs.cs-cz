---
title: ASP.NET Blazor základní rozložení
author: guardrex
description: Přečtěte si, jak vytvořit opakovaně Blazor použitelné komponenty rozložení pro aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660410"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a><span data-ttu-id="2c094-103">ASP.NET Blazor základní rozložení</span><span class="sxs-lookup"><span data-stu-id="2c094-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="2c094-104">Od [Rainer Stropek](https://www.timecockpit.com) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2c094-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2c094-105">Některé prvky aplikace, jako jsou nabídky, zprávy o autorských právech a loga společnosti, jsou obvykle součástí celkového rozložení aplikace a používají je všechny součásti v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2c094-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="2c094-106">Kopírování kódu těchto prvků do všech součástí aplikace není efektivní přístup&mdash;pokaždé, když jeden z prvků vyžaduje aktualizaci, musí být aktualizovány všechny součásti.</span><span class="sxs-lookup"><span data-stu-id="2c094-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="2c094-107">Tato duplikace je obtížné udržet a může vést k nekonzistentnímu obsahu v průběhu času.</span><span class="sxs-lookup"><span data-stu-id="2c094-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="2c094-108">*Rozložení* vyřešit tento problém.</span><span class="sxs-lookup"><span data-stu-id="2c094-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="2c094-109">Technicky je rozložení jen další součástí.</span><span class="sxs-lookup"><span data-stu-id="2c094-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="2c094-110">Rozložení je definováno v šabloně Razor nebo v kódu Jazyka C# a může používat [datové vazby](xref:blazor/data-binding), [vkládání závislostí](xref:blazor/dependency-injection)a další scénáře komponent.</span><span class="sxs-lookup"><span data-stu-id="2c094-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="2c094-111">Chcete-li *komponentu* přeměnit na *rozvržení*, součást:</span><span class="sxs-lookup"><span data-stu-id="2c094-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="2c094-112">Dědí `LayoutComponentBase`z , který `Body` definuje vlastnost pro vykreslený obsah uvnitř rozložení.</span><span class="sxs-lookup"><span data-stu-id="2c094-112">Inherits from `LayoutComponentBase`, which defines a `Body` property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="2c094-113">Syntaxe `@Body` Razor k určení umístění ve značkách rozložení, kde je obsah vykreslen.</span><span class="sxs-lookup"><span data-stu-id="2c094-113">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="2c094-114">Následující ukázka kódu ukazuje šablonu Razor komponenty rozložení *MainLayout.razor*.</span><span class="sxs-lookup"><span data-stu-id="2c094-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="2c094-115">Rozložení dědí `LayoutComponentBase` a `@Body` nastavuje mezi navigační panel a zápatí:</span><span class="sxs-lookup"><span data-stu-id="2c094-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

<span data-ttu-id="2c094-116">V aplikaci založené na Blazor jedné ze `MainLayout` šablon aplikace je komponenta (*MainLayout.razor*) ve složce *Shared* aplikace.</span><span class="sxs-lookup"><span data-stu-id="2c094-116">In an app based on one of the Blazor app templates, the `MainLayout` component (*MainLayout.razor*) is in the app's *Shared* folder.</span></span>

## <a name="default-layout"></a><span data-ttu-id="2c094-117">Výchozí rozložení</span><span class="sxs-lookup"><span data-stu-id="2c094-117">Default layout</span></span>

<span data-ttu-id="2c094-118">Zadejte výchozí rozložení `Router` aplikace v komponentě v souboru *App.razor* aplikace.</span><span class="sxs-lookup"><span data-stu-id="2c094-118">Specify the default app layout in the `Router` component in the app's *App.razor* file.</span></span> <span data-ttu-id="2c094-119">Následující `Router` komponenta, která je k Blazor dispozici ve výchozích šablonách, nastaví výchozí rozložení na komponentu: `MainLayout`</span><span class="sxs-lookup"><span data-stu-id="2c094-119">The following `Router` component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="2c094-120">Chcete-li zadat `NotFound` výchozí rozložení `LayoutView` obsahu, zadejte pro `NotFound` obsah:</span><span class="sxs-lookup"><span data-stu-id="2c094-120">To supply a default layout for `NotFound` content, specify a `LayoutView` for `NotFound` content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="2c094-121">Další informace o `Router` součásti <xref:blazor/routing>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="2c094-121">For more information on the `Router` component, see <xref:blazor/routing>.</span></span>

<span data-ttu-id="2c094-122">Zadání rozložení jako výchozího rozložení ve směrovači je užitečné, protože může být přepsáno na základě jednotlivých komponent nebo složek.</span><span class="sxs-lookup"><span data-stu-id="2c094-122">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="2c094-123">Raději pomocí směrovače nastavit výchozí rozložení aplikace, protože je to nejobecnější technika.</span><span class="sxs-lookup"><span data-stu-id="2c094-123">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="2c094-124">Určení rozložení v součásti</span><span class="sxs-lookup"><span data-stu-id="2c094-124">Specify a layout in a component</span></span>

<span data-ttu-id="2c094-125">Použití razor `@layout` směrnice použít rozložení komponenty.</span><span class="sxs-lookup"><span data-stu-id="2c094-125">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="2c094-126">Kompilátor převede `@layout` `LayoutAttribute`na , který je použit na třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="2c094-126">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="2c094-127">Obsah následující `MasterList` součásti se vkládá `MasterLayout` do polohy `@Body`:</span><span class="sxs-lookup"><span data-stu-id="2c094-127">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="2c094-128">Zadání rozložení přímo v součásti přepíše *výchozí sadu rozvržení* ve směrovači nebo direktivu `@layout` importovnou z *_Imports.razor*.</span><span class="sxs-lookup"><span data-stu-id="2c094-128">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from *_Imports.razor*.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="2c094-129">Centralizovaný výběr rozvržení</span><span class="sxs-lookup"><span data-stu-id="2c094-129">Centralized layout selection</span></span>

<span data-ttu-id="2c094-130">Každá složka aplikace může volitelně obsahovat soubor šablony s názvem *_Imports.razor*.</span><span class="sxs-lookup"><span data-stu-id="2c094-130">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="2c094-131">Kompilátor obsahuje direktivy zadané v souboru importů ve všech šablonách Razor ve stejné složce a rekurzivně ve všech jeho podsložkách.</span><span class="sxs-lookup"><span data-stu-id="2c094-131">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="2c094-132">Proto *soubor _Imports.razor* obsahující `@layout MyCoolLayout` zajišťuje, že všechny součásti ve `MyCoolLayout`složce použít .</span><span class="sxs-lookup"><span data-stu-id="2c094-132">Therefore, an *_Imports.razor* file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="2c094-133">Není třeba opakovaně přidávat `@layout MyCoolLayout` do všech souborů *.razor* ve složce a podsložkách.</span><span class="sxs-lookup"><span data-stu-id="2c094-133">There's no need to repeatedly add `@layout MyCoolLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="2c094-134">`@using`stejným způsobem se stejným způsobem uplatňují i směrnice.</span><span class="sxs-lookup"><span data-stu-id="2c094-134">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="2c094-135">Následující *_Imports.razor* soubor importuje:</span><span class="sxs-lookup"><span data-stu-id="2c094-135">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="2c094-136">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="2c094-136">`MyCoolLayout`.</span></span>
* <span data-ttu-id="2c094-137">Všechny komponenty Razor ve stejné složce a ve všech podsložkách.</span><span class="sxs-lookup"><span data-stu-id="2c094-137">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="2c094-138">Obor `BlazorApp1.Data` názvů.</span><span class="sxs-lookup"><span data-stu-id="2c094-138">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="2c094-139">Soubor *_Imports.razor* je podobný [souboru _ViewImports.cshtml pro zobrazení a stránky Razor,](xref:mvc/views/layout#importing-shared-directives) ale je aplikován speciálně na soubory komponent Razor.</span><span class="sxs-lookup"><span data-stu-id="2c094-139">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="2c094-140">Určení rozložení v *souboru _Imports.razor* přepíše rozložení určené jako *výchozí rozložení*směrovače .</span><span class="sxs-lookup"><span data-stu-id="2c094-140">Specifying a layout in *_Imports.razor* overrides a layout specified as the router's *default layout*.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="2c094-141">Vnořená rozložení</span><span class="sxs-lookup"><span data-stu-id="2c094-141">Nested layouts</span></span>

<span data-ttu-id="2c094-142">Aplikace se mohou skládat z vnořených rozložení.</span><span class="sxs-lookup"><span data-stu-id="2c094-142">Apps can consist of nested layouts.</span></span> <span data-ttu-id="2c094-143">Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení.</span><span class="sxs-lookup"><span data-stu-id="2c094-143">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="2c094-144">Například vnoření rozložení se používají k vytvoření víceúrovňové struktury nabídky.</span><span class="sxs-lookup"><span data-stu-id="2c094-144">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="2c094-145">Následující příklad ukazuje, jak používat vnořená rozložení.</span><span class="sxs-lookup"><span data-stu-id="2c094-145">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="2c094-146">Soubor *EpisodesComponent.razor* je komponenta, která se má zobrazit.</span><span class="sxs-lookup"><span data-stu-id="2c094-146">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="2c094-147">Komponenta odkazuje `MasterListLayout`na :</span><span class="sxs-lookup"><span data-stu-id="2c094-147">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="2c094-148">Soubor *MasterListLayout.razor* poskytuje `MasterListLayout`soubor .</span><span class="sxs-lookup"><span data-stu-id="2c094-148">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="2c094-149">Rozložení odkazuje na jiné `MasterLayout`rozložení , kde je vykresleno.</span><span class="sxs-lookup"><span data-stu-id="2c094-149">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="2c094-150">`EpisodesComponent`je vykreslena `@Body` tam, kde se objeví:</span><span class="sxs-lookup"><span data-stu-id="2c094-150">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="2c094-151">Nakonec `MasterLayout` v *MasterLayout.razor* obsahuje prvky rozložení nejvyšší úrovně, jako je například záhlaví, hlavní nabídka a zápatí.</span><span class="sxs-lookup"><span data-stu-id="2c094-151">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="2c094-152">`MasterListLayout`s `EpisodesComponent` vykreslením, `@Body` kde se objeví:</span><span class="sxs-lookup"><span data-stu-id="2c094-152">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a><span data-ttu-id="2c094-153">Sdílení rozložení Razor Pages s integrovanými komponentami</span><span class="sxs-lookup"><span data-stu-id="2c094-153">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="2c094-154">Když jsou směrovatelné součásti integrovány do aplikace Razor Pages, sdílené rozložení aplikace lze použít s komponentami.</span><span class="sxs-lookup"><span data-stu-id="2c094-154">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="2c094-155">Další informace naleznete v tématu <xref:blazor/integrate-components>.</span><span class="sxs-lookup"><span data-stu-id="2c094-155">For more information, see <xref:blazor/integrate-components>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2c094-156">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2c094-156">Additional resources</span></span>

* <xref:mvc/views/layout>
