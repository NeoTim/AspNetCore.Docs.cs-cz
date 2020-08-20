---
title: ASP.NET Core Blazor rozložení
author: guardrex
description: Naučte se vytvářet opakovaně použitelné součásti rozložení pro Blazor aplikace.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: f41b41194f597505d775c95f1e65960c2f827e3b
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628011"
---
# <a name="aspnet-core-no-locblazor-layouts"></a><span data-ttu-id="493d5-103">ASP.NET Core Blazor rozložení</span><span class="sxs-lookup"><span data-stu-id="493d5-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="493d5-104">Od [Rainer Stropek](https://www.timecockpit.com) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="493d5-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="493d5-105">Některé prvky aplikace, jako jsou nabídky, zprávy o autorských právech a logo společnosti, jsou obvykle součástí celkového rozložení aplikace a používají se v každé součásti aplikace.</span><span class="sxs-lookup"><span data-stu-id="493d5-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="493d5-106">Kopírování kódu těchto prvků do všech součástí aplikace není účinný přístup.</span><span class="sxs-lookup"><span data-stu-id="493d5-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="493d5-107">Pokaždé, když jeden z elementů vyžaduje aktualizaci, musí být každá součást aktualizována.</span><span class="sxs-lookup"><span data-stu-id="493d5-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="493d5-108">Takové duplikace je obtížné udržovat a může v průběhu času vést k nekonzistentnímu obsahu.</span><span class="sxs-lookup"><span data-stu-id="493d5-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="493d5-109">*Rozložení* řeší tento problém.</span><span class="sxs-lookup"><span data-stu-id="493d5-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="493d5-110">Technicky, rozložení je pouze jiná komponenta.</span><span class="sxs-lookup"><span data-stu-id="493d5-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="493d5-111">Rozložení je definováno v Razor šabloně nebo v kódu jazyka C# a může používat [datové vazby](xref:blazor/components/data-binding), [vkládání závislostí](xref:blazor/fundamentals/dependency-injection)a další scénáře komponent.</span><span class="sxs-lookup"><span data-stu-id="493d5-111">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="493d5-112">Chcete-li změnit *komponentu* na *rozložení*, součást:</span><span class="sxs-lookup"><span data-stu-id="493d5-112">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="493d5-113">Dědí z <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> , který definuje <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> vlastnost pro vykreslený obsah v rozložení.</span><span class="sxs-lookup"><span data-stu-id="493d5-113">Inherits from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>, which defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="493d5-114">Používá Razor syntaxi `@Body` k určení umístění v označení rozložení, kde je obsah vykreslen.</span><span class="sxs-lookup"><span data-stu-id="493d5-114">Uses the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="493d5-115">Následující ukázka kódu ukazuje Razor šablonu komponenty rozložení, `MainLayout.razor` .</span><span class="sxs-lookup"><span data-stu-id="493d5-115">The following code sample shows the Razor template of a layout component, `MainLayout.razor`.</span></span> <span data-ttu-id="493d5-116">Rozložení dědí <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> a nastavuje `@Body` mezi navigačním panelem a zápatím:</span><span class="sxs-lookup"><span data-stu-id="493d5-116">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="mainlayout-component"></a><span data-ttu-id="493d5-117">`MainLayout` (komponenta)</span><span class="sxs-lookup"><span data-stu-id="493d5-117">`MainLayout` component</span></span>

<span data-ttu-id="493d5-118">V aplikaci na základě jedné z Blazor šablon projektu `MainLayout` je součást ( `MainLayout.razor` ) ve `Shared` složce aplikace:</span><span class="sxs-lookup"><span data-stu-id="493d5-118">In an app based on one of the Blazor project templates, the `MainLayout` component (`MainLayout.razor`) is in the app's `Shared` folder:</span></span>

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

## <a name="default-layout"></a><span data-ttu-id="493d5-119">Výchozí rozložení</span><span class="sxs-lookup"><span data-stu-id="493d5-119">Default layout</span></span>

<span data-ttu-id="493d5-120">Zadejte výchozí rozložení aplikace v <xref:Microsoft.AspNetCore.Components.Routing.Router> součásti v `App.razor` souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="493d5-120">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's `App.razor` file.</span></span> <span data-ttu-id="493d5-121">Následující <xref:Microsoft.AspNetCore.Components.Routing.Router> Komponenta, která je poskytována výchozími Blazor šablonami, nastaví výchozí rozložení na `MainLayout` součást:</span><span class="sxs-lookup"><span data-stu-id="493d5-121">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="493d5-122">Chcete-li zadat výchozí rozložení <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> obsahu, zadejte <xref:Microsoft.AspNetCore.Components.LayoutView> pro <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> obsah:</span><span class="sxs-lookup"><span data-stu-id="493d5-122">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="493d5-123">Další informace o <xref:Microsoft.AspNetCore.Components.Routing.Router> komponentě naleznete v tématu <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="493d5-123">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="493d5-124">Určení rozložení jako výchozího rozložení ve směrovači je užitečný postup, protože je možné ho přepsat pro jednotlivé komponenty nebo pro jednotlivé složky.</span><span class="sxs-lookup"><span data-stu-id="493d5-124">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="493d5-125">Upřednostňujete použití směrovače k nastavení výchozího rozložení aplikace, protože se jedná o nejobecnější postup.</span><span class="sxs-lookup"><span data-stu-id="493d5-125">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="493d5-126">Určení rozložení v součásti</span><span class="sxs-lookup"><span data-stu-id="493d5-126">Specify a layout in a component</span></span>

<span data-ttu-id="493d5-127">Použijte Razor direktivu `@layout` pro použití rozložení na komponentu.</span><span class="sxs-lookup"><span data-stu-id="493d5-127">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="493d5-128">Kompilátor převede `@layout` na <xref:Microsoft.AspNetCore.Components.LayoutAttribute> , který je použit pro třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="493d5-128">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="493d5-129">Obsah následující `MasterList` komponenty je vložen do `MasterLayout` pozice v umístění `@Body` :</span><span class="sxs-lookup"><span data-stu-id="493d5-129">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="493d5-130">Určením rozložení přímo v součásti dojde k přepsání *výchozí sady rozložení* ve směrovači nebo v `@layout` direktivě importované z `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="493d5-130">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from `_Imports.razor`.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="493d5-131">Centralizovaný výběr rozložení</span><span class="sxs-lookup"><span data-stu-id="493d5-131">Centralized layout selection</span></span>

<span data-ttu-id="493d5-132">Každá složka aplikace může volitelně obsahovat soubor šablony s názvem `_Imports.razor` .</span><span class="sxs-lookup"><span data-stu-id="493d5-132">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="493d5-133">Kompilátor obsahuje direktivy uvedené v souboru Imports ve všech Razor šablonách ve stejné složce a rekurzivně ve všech jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="493d5-133">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="493d5-134">Proto soubor, `_Imports.razor` který obsahuje `@layout MyCoolLayout` , zajistí, aby všechny součásti ve složce používaly `MyCoolLayout` .</span><span class="sxs-lookup"><span data-stu-id="493d5-134">Therefore, an `_Imports.razor` file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="493d5-135">Nemusíte opakovaně přidávat `@layout MyCoolLayout` do všech `.razor` souborů ve složce a v jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="493d5-135">There's no need to repeatedly add `@layout MyCoolLayout` to all of the `.razor` files within the folder and subfolders.</span></span> <span data-ttu-id="493d5-136">`@using` direktivy jsou také aplikovány na komponenty stejným způsobem.</span><span class="sxs-lookup"><span data-stu-id="493d5-136">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="493d5-137">Následující `_Imports.razor` soubor importuje:</span><span class="sxs-lookup"><span data-stu-id="493d5-137">The following `_Imports.razor` file imports:</span></span>

* <span data-ttu-id="493d5-138">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="493d5-138">`MyCoolLayout`.</span></span>
* <span data-ttu-id="493d5-139">Všechny Razor součásti ve stejné složce a v jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="493d5-139">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="493d5-140">`BlazorApp1.Data`Obor názvů.</span><span class="sxs-lookup"><span data-stu-id="493d5-140">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="493d5-141">`_Imports.razor`Soubor je podobný [souboru _ViewImports. cshtml pro Razor zobrazení a stránky](xref:mvc/views/layout#importing-shared-directives) , ale používá se konkrétně pro Razor soubory komponent.</span><span class="sxs-lookup"><span data-stu-id="493d5-141">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="493d5-142">Zadáním rozložení v `_Imports.razor` přepisujete rozložení zadané jako *výchozí rozložení*směrovače.</span><span class="sxs-lookup"><span data-stu-id="493d5-142">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's *default layout*.</span></span>

> [!WARNING]
> <span data-ttu-id="493d5-143">Nepřidávejte **not** Razor `@layout` do kořenového souboru direktivu `_Imports.razor` , což vede k nekonečné smyčce rozložení v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="493d5-143">Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts in the app.</span></span> <span data-ttu-id="493d5-144">Pro řízení výchozího rozložení aplikace Určete rozložení v `Router` součásti.</span><span class="sxs-lookup"><span data-stu-id="493d5-144">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="493d5-145">Další informace najdete v části [výchozí rozložení](#default-layout) .</span><span class="sxs-lookup"><span data-stu-id="493d5-145">For more information, see the [Default layout](#default-layout) section.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="493d5-146">Vnořená rozložení</span><span class="sxs-lookup"><span data-stu-id="493d5-146">Nested layouts</span></span>

<span data-ttu-id="493d5-147">Aplikace se můžou skládat z vnořených rozložení.</span><span class="sxs-lookup"><span data-stu-id="493d5-147">Apps can consist of nested layouts.</span></span> <span data-ttu-id="493d5-148">Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení.</span><span class="sxs-lookup"><span data-stu-id="493d5-148">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="493d5-149">Například vnořování rozložení slouží k vytvoření struktury nabídky na více úrovních.</span><span class="sxs-lookup"><span data-stu-id="493d5-149">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="493d5-150">Následující příklad ukazuje, jak použít vnořená rozložení.</span><span class="sxs-lookup"><span data-stu-id="493d5-150">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="493d5-151">`EpisodesComponent.razor`Soubor je součástí, která se má zobrazit.</span><span class="sxs-lookup"><span data-stu-id="493d5-151">The `EpisodesComponent.razor` file is the component to display.</span></span> <span data-ttu-id="493d5-152">Komponenta odkazuje na `MasterListLayout` :</span><span class="sxs-lookup"><span data-stu-id="493d5-152">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="493d5-153">`MasterListLayout.razor`Soubor poskytuje `MasterListLayout` .</span><span class="sxs-lookup"><span data-stu-id="493d5-153">The `MasterListLayout.razor` file provides the `MasterListLayout`.</span></span> <span data-ttu-id="493d5-154">Rozložení odkazuje na jiné rozložení, `MasterLayout` , kde je vykresleno.</span><span class="sxs-lookup"><span data-stu-id="493d5-154">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="493d5-155">`EpisodesComponent` je vykreslen, kde `@Body` se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="493d5-155">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="493d5-156">Nakonec `MasterLayout` `MasterLayout.razor` obsahuje prvky rozložení na nejvyšší úrovni, jako je záhlaví, hlavní nabídka a zápatí.</span><span class="sxs-lookup"><span data-stu-id="493d5-156">Finally, `MasterLayout` in `MasterLayout.razor` contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="493d5-157">`MasterListLayout` tam, `EpisodesComponent` kde se zobrazí, se vykreslí `@Body` :</span><span class="sxs-lookup"><span data-stu-id="493d5-157">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a><span data-ttu-id="493d5-158">Sdílení Razor rozložení stránek pomocí integrovaných komponent</span><span class="sxs-lookup"><span data-stu-id="493d5-158">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="493d5-159">Pokud jsou směrovatelný komponenty integrovány do Razor aplikace Pages, lze použít sdílené rozložení aplikace spolu s komponentami.</span><span class="sxs-lookup"><span data-stu-id="493d5-159">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="493d5-160">Další informace naleznete v tématu <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="493d5-160">For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="493d5-161">Další materiály</span><span class="sxs-lookup"><span data-stu-id="493d5-161">Additional resources</span></span>

* <xref:mvc/views/layout>
