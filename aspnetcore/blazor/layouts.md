---
title: Blazor rozložení
author: guardrex
description: Zjistěte, jak vytvářet rozložení opakovaně použitelné komponenty pro Blazor aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: blazor/layouts
ms.openlocfilehash: 42d5d03c50c525c3d819f6dac39109eff6534800
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614773"
---
# <a name="blazor-layouts"></a><span data-ttu-id="f13c8-103">Blazor rozložení</span><span class="sxs-lookup"><span data-stu-id="f13c8-103">Blazor layouts</span></span>

<span data-ttu-id="f13c8-104">Podle [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="f13c8-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="f13c8-105">Aplikace obvykle obsahují více než jednu součást.</span><span class="sxs-lookup"><span data-stu-id="f13c8-105">Apps typically contain more than one component.</span></span> <span data-ttu-id="f13c8-106">Prvky, rozložení, jako jsou nabídky, zprávy o autorských právech a loga, musí být k dispozici pro všechny komponenty.</span><span class="sxs-lookup"><span data-stu-id="f13c8-106">Layout elements, such as menus, copyright messages, and logos, must be present on all components.</span></span> <span data-ttu-id="f13c8-107">Kopírování kódu z těchto elementů rozložení do všech součástí aplikace není efektivní přístup.</span><span class="sxs-lookup"><span data-stu-id="f13c8-107">Copying the code of these layout elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="f13c8-108">Tato duplikace je obtížné udržovat a pravděpodobně povede k nekonzistentní obsah v čase.</span><span class="sxs-lookup"><span data-stu-id="f13c8-108">Such duplication is hard to maintain and probably leads to inconsistent content over time.</span></span> <span data-ttu-id="f13c8-109">*Rozložení* tento problém vyřešit.</span><span class="sxs-lookup"><span data-stu-id="f13c8-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="f13c8-110">Technicky vzato rozložení je jenom další komponenty.</span><span class="sxs-lookup"><span data-stu-id="f13c8-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="f13c8-111">Rozložení je definována šablona Razor nebo v C# kódu a může obsahovat [datové vazby](xref:blazor/components#data-binding), [injektáž závislostí](xref:blazor/dependency-injection)a další běžné funkce součástí.</span><span class="sxs-lookup"><span data-stu-id="f13c8-111">A layout is defined in a Razor template or in C# code and can contain [data binding](xref:blazor/components#data-binding), [dependency injection](xref:blazor/dependency-injection), and other ordinary features of components.</span></span>

<span data-ttu-id="f13c8-112">Zapnout dva další aspekty *komponenty* do *rozložení*</span><span class="sxs-lookup"><span data-stu-id="f13c8-112">Two additional aspects turn a *component* into a *layout*</span></span>

* <span data-ttu-id="f13c8-113">Komponenta rozložení musí dědit z `LayoutComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="f13c8-113">The layout component must inherit from `LayoutComponentBase`.</span></span> <span data-ttu-id="f13c8-114">`LayoutComponentBase` definuje `Body` vlastnost, která obsahuje obsah, který se vykreslí uvnitř rozložení.</span><span class="sxs-lookup"><span data-stu-id="f13c8-114">`LayoutComponentBase` defines a `Body` property that contains the content to be rendered inside the layout.</span></span>
* <span data-ttu-id="f13c8-115">Používá součásti rozložení `Body` vlastnosti a určit, kde má být obsah textu vykreslen pomocí syntaxe Razor `@Body`.</span><span class="sxs-lookup"><span data-stu-id="f13c8-115">The layout component uses the `Body` property to specify where the body content should be rendered using the Razor syntax `@Body`.</span></span> <span data-ttu-id="f13c8-116">Při vykreslování, `@Body` nahrazuje obsah rozložení.</span><span class="sxs-lookup"><span data-stu-id="f13c8-116">During rendering, `@Body` is replaced by the content of the layout.</span></span>

<span data-ttu-id="f13c8-117">Následující příklad kódu ukazuje šablona Razor součásti rozložení.</span><span class="sxs-lookup"><span data-stu-id="f13c8-117">The following code sample shows the Razor template of a layout component.</span></span> <span data-ttu-id="f13c8-118">Všimněte si použití `LayoutComponentBase` a `@Body`:</span><span class="sxs-lookup"><span data-stu-id="f13c8-118">Note the use of `LayoutComponentBase` and `@Body`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.cshtml)]

## <a name="use-a-layout-in-a-component"></a><span data-ttu-id="f13c8-119">Použít rozložení v komponentě</span><span class="sxs-lookup"><span data-stu-id="f13c8-119">Use a layout in a component</span></span>

<span data-ttu-id="f13c8-120">Pomocí direktivy Razor `@layout` do rozložení můžete použít na komponentu.</span><span class="sxs-lookup"><span data-stu-id="f13c8-120">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="f13c8-121">Kompilátor převede tuto direktivu do `LayoutAttribute`, které platí pro třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="f13c8-121">The compiler converts this directive into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="f13c8-122">Následující příklad kódu ukazuje koncept.</span><span class="sxs-lookup"><span data-stu-id="f13c8-122">The following code sample demonstrates the concept.</span></span> <span data-ttu-id="f13c8-123">Obsah této součásti je vložen do *MasterLayout* v pozici `@Body`:</span><span class="sxs-lookup"><span data-stu-id="f13c8-123">The content of this component is inserted into the *MasterLayout* at the position of `@Body`:</span></span>

```cshtml
@layout MasterLayout
@page "/master-list"

<h2>Master Episode List</h2>
```

## <a name="centralized-layout-selection"></a><span data-ttu-id="f13c8-124">Výběr centralizované rozložení</span><span class="sxs-lookup"><span data-stu-id="f13c8-124">Centralized layout selection</span></span>

<span data-ttu-id="f13c8-125">Všechny složky, které aplikace aplikace může volitelně obsahovat soubor šablony s názvem *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f13c8-125">Every folder of a an app can optionally contain a template file named *_ViewImports.cshtml*.</span></span> <span data-ttu-id="f13c8-126">Kompilátor obsahuje direktivy zadané v souboru importy zobrazení všech šablon Razor ve stejné složce a rekurzivně ve všech jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="f13c8-126">The compiler includes the directives specified in the view imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="f13c8-127">Proto *_ViewImports.cshtml* soubor obsahující `@layout MainLayout` zajišťuje, že všechny součásti ve složce použití *MainLayout* rozložení.</span><span class="sxs-lookup"><span data-stu-id="f13c8-127">Therefore, a *_ViewImports.cshtml* file containing `@layout MainLayout` ensures that all of the components in a folder use the *MainLayout* layout.</span></span> <span data-ttu-id="f13c8-128">Není nutné opakovaně přidat `@layout` ke všem *.razor* soubory.</span><span class="sxs-lookup"><span data-stu-id="f13c8-128">There's no need to repeatedly add `@layout` to all of the *.razor* files.</span></span>

<span data-ttu-id="f13c8-129">Všimněte si, že používá výchozí šablonu *_ViewImports.cshtml* mechanismus pro výběr rozložení.</span><span class="sxs-lookup"><span data-stu-id="f13c8-129">Note that the default template uses the *_ViewImports.cshtml* mechanism for layout selection.</span></span> <span data-ttu-id="f13c8-130">Nově vytvořenou aplikaci, která obsahuje *_ViewImports.cshtml* soubor *součásti/stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="f13c8-130">A newly created app contains the *_ViewImports.cshtml* file in the *Components/Pages* folder.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="f13c8-131">Vnořené rozložení</span><span class="sxs-lookup"><span data-stu-id="f13c8-131">Nested layouts</span></span>

<span data-ttu-id="f13c8-132">Aplikace můžou zahrnovat vnořené rozložení.</span><span class="sxs-lookup"><span data-stu-id="f13c8-132">Apps can consist of nested layouts.</span></span> <span data-ttu-id="f13c8-133">Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení.</span><span class="sxs-lookup"><span data-stu-id="f13c8-133">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="f13c8-134">Například je možné vnoření rozložení tak, aby odrážely struktura víceúrovňových nabídky.</span><span class="sxs-lookup"><span data-stu-id="f13c8-134">For example, nesting layouts can be used to reflect a multi-level menu structure.</span></span>

<span data-ttu-id="f13c8-135">Následující ukázky kódu ukazují, jak používat vnořené rozložení.</span><span class="sxs-lookup"><span data-stu-id="f13c8-135">The following code samples show how to use nested layouts.</span></span> <span data-ttu-id="f13c8-136">*EpisodesComponent.cshtml* souboru je komponenta zobrazit.</span><span class="sxs-lookup"><span data-stu-id="f13c8-136">The *EpisodesComponent.cshtml* file is the component to display.</span></span> <span data-ttu-id="f13c8-137">Všimněte si, že součást odkazuje rozložení `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="f13c8-137">Note that the component references the layout `MasterListLayout`.</span></span>

<span data-ttu-id="f13c8-138">*EpisodesComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f13c8-138">*EpisodesComponent.cshtml*:</span></span>

```cshtml
@layout MasterListLayout
@page "/master-list/episodes"

<h1>Episodes</h1>
```

<span data-ttu-id="f13c8-139">*MasterListLayout.cshtml* poskytuje soubor `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="f13c8-139">The *MasterListLayout.cshtml* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="f13c8-140">Rozložení odkazuje na jiné rozložení, `MasterLayout`, ve kterém se bude vložen.</span><span class="sxs-lookup"><span data-stu-id="f13c8-140">The layout references another layout, `MasterLayout`, where it's going to be embedded.</span></span>

<span data-ttu-id="f13c8-141">*MasterListLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f13c8-141">*MasterListLayout.cshtml*:</span></span>

```cshtml
@layout MasterLayout
@inherits LayoutComponentBase

<nav>
    <!-- Menu structure of master list -->
    ...
</nav>

@Body
```

<span data-ttu-id="f13c8-142">Nakonec `MasterLayout` obsahuje prvky rozložení nejvyšší úrovně, jako je například záhlaví, zápatí a hlavní nabídky.</span><span class="sxs-lookup"><span data-stu-id="f13c8-142">Finally, `MasterLayout` contains the top-level layout elements, such as the header, footer, and main menu.</span></span>

<span data-ttu-id="f13c8-143">*MasterLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f13c8-143">*MasterLayout.cshtml*:</span></span>

```cshtml
@inherits LayoutComponentBase

<header>...</header>
<nav>...</nav>

@Body
```
