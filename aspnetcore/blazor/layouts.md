---
title: ASP.NET Core Blazor rozložení
author: guardrex
description: Zjistěte, jak vytvářet rozložení opakovaně použitelné komponenty pro Blazor aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: blazor/layouts
ms.openlocfilehash: c389eb15d6a3e359d2d282e4aba8f5e2a6079bec
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152782"
---
# <a name="aspnet-core-blazor-layouts"></a><span data-ttu-id="557fc-103">ASP.NET Core Blazor rozložení</span><span class="sxs-lookup"><span data-stu-id="557fc-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="557fc-104">Podle [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="557fc-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="557fc-105">Některé prvky aplikace, jako jsou nabídky, zprávy o autorských právech a loga společnosti, jsou obvykle součástí celkového rozložení vaší aplikace a používat všechny komponenty v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="557fc-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="557fc-106">Kopírování kódu z těchto elementů do všech součástí aplikace není efektivní přístup&mdash;pokaždé, když se jeden z elementů vyžaduje aktualizaci, musí se aktualizovat všechny komponenty.</span><span class="sxs-lookup"><span data-stu-id="557fc-106">Copying the code of these elements into all of the components of an app isn't an efficient approach&mdash;every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="557fc-107">Je obtížné udržovat a může vést k nekonzistentním obsah v čase.</span><span class="sxs-lookup"><span data-stu-id="557fc-107">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="557fc-108">*Rozložení* tento problém vyřešit.</span><span class="sxs-lookup"><span data-stu-id="557fc-108">*Layouts* solve this problem.</span></span>

<span data-ttu-id="557fc-109">Technicky vzato rozložení je jenom další komponenty.</span><span class="sxs-lookup"><span data-stu-id="557fc-109">Technically, a layout is just another component.</span></span> <span data-ttu-id="557fc-110">Rozložení je definována šablona Razor nebo v C# kódu a můžete použít [datové vazby](xref:blazor/components#data-binding), [injektáž závislostí](xref:blazor/dependency-injection)a další součásti scénáře.</span><span class="sxs-lookup"><span data-stu-id="557fc-110">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components#data-binding), [dependency injection](xref:blazor/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="557fc-111">Chcete-li *komponenty* do *rozložení*, součást:</span><span class="sxs-lookup"><span data-stu-id="557fc-111">To turn a *component* into a *layout*, the component:</span></span>

* <span data-ttu-id="557fc-112">Dědí z `LayoutComponentBase`, která definuje `Body` vlastnost, která obsahuje obsah, který se vykreslí uvnitř rozložení.</span><span class="sxs-lookup"><span data-stu-id="557fc-112">Inherits from `LayoutComponentBase`, which defines a `Body` property that contains the content to be rendered inside the layout.</span></span>
* <span data-ttu-id="557fc-113">Používá syntaxi Razor `@Body` k určení umístění v kódu, kde má být vykreslen obsah.</span><span class="sxs-lookup"><span data-stu-id="557fc-113">Uses the Razor syntax `@Body` to specify the location in the markup where the content should be rendered.</span></span>

<span data-ttu-id="557fc-114">Následující příklad kódu ukazuje šablona Razor rozložení součásti *MainLayout.razor*.</span><span class="sxs-lookup"><span data-stu-id="557fc-114">The following code sample shows the Razor template of a layout component, *MainLayout.razor*.</span></span> <span data-ttu-id="557fc-115">Rozložení dědí `LayoutComponentBase` a nastaví `@Body` mezi na navigačním panelu a v zápatí je uvedené:</span><span class="sxs-lookup"><span data-stu-id="557fc-115">The layout inherits `LayoutComponentBase` and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="557fc-116">Určete rozložení v komponentě</span><span class="sxs-lookup"><span data-stu-id="557fc-116">Specify a layout in a component</span></span>

<span data-ttu-id="557fc-117">Pomocí direktivy Razor `@layout` do rozložení můžete použít na komponentu.</span><span class="sxs-lookup"><span data-stu-id="557fc-117">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="557fc-118">Kompilátor převede `@layout` do `LayoutAttribute`, které platí pro třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="557fc-118">The compiler converts `@layout` into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="557fc-119">Obsah následující komponenty *MasterList.razor*, je vložen do *MainLayout* v pozici `@Body`.</span><span class="sxs-lookup"><span data-stu-id="557fc-119">The content of the following component, *MasterList.razor*, is inserted into the *MainLayout* at the position of `@Body`.</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

## <a name="centralized-layout-selection"></a><span data-ttu-id="557fc-120">Výběr centralizované rozložení</span><span class="sxs-lookup"><span data-stu-id="557fc-120">Centralized layout selection</span></span>

<span data-ttu-id="557fc-121">Všechny složky, které aplikace může volitelně obsahovat soubor šablony s názvem *_Imports.razor*.</span><span class="sxs-lookup"><span data-stu-id="557fc-121">Every folder of an app can optionally contain a template file named *_Imports.razor*.</span></span> <span data-ttu-id="557fc-122">Kompilátor obsahuje direktivy zadané v souboru importu ve všech šablon Razor ve stejné složce a rekurzivně ve všech jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="557fc-122">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="557fc-123">Proto *_Imports.razor* soubor obsahující `@layout MainLayout` zajišťuje, že všechny součásti ve složce pomocí *MainLayout*.</span><span class="sxs-lookup"><span data-stu-id="557fc-123">Therefore, a *_Imports.razor* file containing `@layout MainLayout` ensures that all of the components in a folder use *MainLayout*.</span></span> <span data-ttu-id="557fc-124">Není nutné opakovaně přidat `@layout MainLayout` ke všem *.razor* soubory ve složce a jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="557fc-124">There's no need to repeatedly add `@layout MainLayout` to all of the *.razor* files within the folder and subfolders.</span></span> <span data-ttu-id="557fc-125">`@using` direktivy jsou použita také na komponenty stejným způsobem.</span><span class="sxs-lookup"><span data-stu-id="557fc-125">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="557fc-126">Následující *_Imports.razor* souboru importu:</span><span class="sxs-lookup"><span data-stu-id="557fc-126">The following *_Imports.razor* file imports:</span></span>

* <span data-ttu-id="557fc-127">`MainLayout`.</span><span class="sxs-lookup"><span data-stu-id="557fc-127">`MainLayout`.</span></span>
* <span data-ttu-id="557fc-128">Všechny součásti Razor ve stejné složky a všechny její podsložky.</span><span class="sxs-lookup"><span data-stu-id="557fc-128">All Razor components in a the same folder and any subfolders.</span></span>
* <span data-ttu-id="557fc-129">`BlazorApp1.Data` Oboru názvů.</span><span class="sxs-lookup"><span data-stu-id="557fc-129">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="557fc-130">*_Imports.razor* soubor je podobný [_ViewImports.cshtml souboru pro zobrazení syntaxe Razor a stránky](xref:mvc/views/layout#importing-shared-directives) ale platí konkrétně pro soubory součástí syntaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="557fc-130">The *_Imports.razor* file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="557fc-131">Použití šablon Blazor *_Imports.razor* souborů pro výběr rozložení.</span><span class="sxs-lookup"><span data-stu-id="557fc-131">The Blazor templates use *_Imports.razor* files for layout selection.</span></span> <span data-ttu-id="557fc-132">Obsahuje aplikaci vytvořené ze šablony Blazor *_Imports.razor* v kořenovém adresáři projektu a v souboru *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="557fc-132">An app created from a Blazor template contains the *_Imports.razor* file in the root of the project and in the *Pages* folder.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="557fc-133">Vnořené rozložení</span><span class="sxs-lookup"><span data-stu-id="557fc-133">Nested layouts</span></span>

<span data-ttu-id="557fc-134">Aplikace můžou zahrnovat vnořené rozložení.</span><span class="sxs-lookup"><span data-stu-id="557fc-134">Apps can consist of nested layouts.</span></span> <span data-ttu-id="557fc-135">Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení.</span><span class="sxs-lookup"><span data-stu-id="557fc-135">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="557fc-136">Například vnoření rozložení slouží k vytvoření víceúrovňových nabídky struktury.</span><span class="sxs-lookup"><span data-stu-id="557fc-136">For example, nesting layouts can be used to create a multi-level menu structure.</span></span>

<span data-ttu-id="557fc-137">Následující příklad ukazuje, jak používat vnořené rozložení.</span><span class="sxs-lookup"><span data-stu-id="557fc-137">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="557fc-138">*EpisodesComponent.razor* souboru je komponenta zobrazit.</span><span class="sxs-lookup"><span data-stu-id="557fc-138">The *EpisodesComponent.razor* file is the component to display.</span></span> <span data-ttu-id="557fc-139">Odkazy na součásti `MasterListLayout`:</span><span class="sxs-lookup"><span data-stu-id="557fc-139">The component references the `MasterListLayout`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="557fc-140">*MasterListLayout.razor* poskytuje soubor `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="557fc-140">The *MasterListLayout.razor* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="557fc-141">Rozložení odkazuje na jiné rozložení, `MasterLayout`, kde se vykreslí.</span><span class="sxs-lookup"><span data-stu-id="557fc-141">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="557fc-142">`EpisodesComponent` kde je vykreslen `@Body` se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="557fc-142">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="557fc-143">Nakonec `MasterLayout` v *MasterLayout.razor* obsahuje prvky rozložení nejvyšší úrovně, jako je například záhlaví, hlavní nabídky a zápatí.</span><span class="sxs-lookup"><span data-stu-id="557fc-143">Finally, `MasterLayout` in *MasterLayout.razor* contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="557fc-144">*MasterListLayout* s *EpisodesComponent* vykresleny kde `@Body` se zobrazí:</span><span class="sxs-lookup"><span data-stu-id="557fc-144">*MasterListLayout* with *EpisodesComponent* are rendered where `@Body` appears:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a><span data-ttu-id="557fc-145">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="557fc-145">Additional resources</span></span>

* <xref:mvc/views/layout>
