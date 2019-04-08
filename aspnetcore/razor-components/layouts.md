---
title: Rozložení Razor komponenty
author: guardrex
description: Zjistěte, jak vytvářet rozložení opakovaně použitelné komponenty pro Razor součásti aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/layouts
ms.openlocfilehash: 31ed940ce40e3ae6e3744418cf241d396308f4fe
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068110"
---
# <a name="razor-components-layouts"></a><span data-ttu-id="f40cc-103">Rozložení Razor komponenty</span><span class="sxs-lookup"><span data-stu-id="f40cc-103">Razor Components layouts</span></span>

<span data-ttu-id="f40cc-104">Podle [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="f40cc-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="f40cc-105">Aplikace obvykle obsahují více než jednu součást.</span><span class="sxs-lookup"><span data-stu-id="f40cc-105">Apps typically contain more than one component.</span></span> <span data-ttu-id="f40cc-106">Prvky, rozložení, jako jsou nabídky, zprávy o autorských právech a loga, musí být k dispozici pro všechny komponenty.</span><span class="sxs-lookup"><span data-stu-id="f40cc-106">Layout elements, such as menus, copyright messages, and logos, must be present on all components.</span></span> <span data-ttu-id="f40cc-107">Kopírování kódu z těchto elementů rozložení do všech součástí aplikace není efektivní přístup.</span><span class="sxs-lookup"><span data-stu-id="f40cc-107">Copying the code of these layout elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="f40cc-108">Tato duplikace je obtížné udržovat a pravděpodobně povede k nekonzistentní obsah v čase.</span><span class="sxs-lookup"><span data-stu-id="f40cc-108">Such duplication is hard to maintain and probably leads to inconsistent content over time.</span></span> <span data-ttu-id="f40cc-109">*Rozložení* tento problém vyřešit.</span><span class="sxs-lookup"><span data-stu-id="f40cc-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="f40cc-110">Technicky vzato rozložení je jenom další komponenty.</span><span class="sxs-lookup"><span data-stu-id="f40cc-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="f40cc-111">Rozložení je definována šablona Razor nebo v C# kódu a může obsahovat [datové vazby](xref:razor-components/components#data-binding), [injektáž závislostí](xref:razor-components/dependency-injection)a další běžné funkce součástí.</span><span class="sxs-lookup"><span data-stu-id="f40cc-111">A layout is defined in a Razor template or in C# code and can contain [data binding](xref:razor-components/components#data-binding), [dependency injection](xref:razor-components/dependency-injection), and other ordinary features of components.</span></span>

<span data-ttu-id="f40cc-112">Zapnout dva další aspekty *komponenty* do *rozložení*</span><span class="sxs-lookup"><span data-stu-id="f40cc-112">Two additional aspects turn a *component* into a *layout*</span></span>

* <span data-ttu-id="f40cc-113">Komponenta rozložení musí dědit z `LayoutComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="f40cc-113">The layout component must inherit from `LayoutComponentBase`.</span></span> `LayoutComponentBase` <span data-ttu-id="f40cc-114">definuje `Body` vlastnost, která obsahuje obsah, který se vykreslí uvnitř rozložení.</span><span class="sxs-lookup"><span data-stu-id="f40cc-114">defines a `Body` property that contains the content to be rendered inside the layout.</span></span>
* <span data-ttu-id="f40cc-115">Používá součásti rozložení `Body` vlastnosti a určit, kde má být obsah textu vykreslen pomocí syntaxe Razor `@Body`.</span><span class="sxs-lookup"><span data-stu-id="f40cc-115">The layout component uses the `Body` property to specify where the body content should be rendered using the Razor syntax `@Body`.</span></span> <span data-ttu-id="f40cc-116">Při vykreslování, `@Body` nahrazuje obsah rozložení.</span><span class="sxs-lookup"><span data-stu-id="f40cc-116">During rendering, `@Body` is replaced by the content of the layout.</span></span>

<span data-ttu-id="f40cc-117">Následující příklad kódu ukazuje šablona Razor součásti rozložení.</span><span class="sxs-lookup"><span data-stu-id="f40cc-117">The following code sample shows the Razor template of a layout component.</span></span> <span data-ttu-id="f40cc-118">Všimněte si použití `LayoutComponentBase` a `@Body`:</span><span class="sxs-lookup"><span data-stu-id="f40cc-118">Note the use of `LayoutComponentBase` and `@Body`:</span></span>

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.cshtml)]

## <a name="use-a-layout-in-a-component"></a><span data-ttu-id="f40cc-119">Použít rozložení v komponentě</span><span class="sxs-lookup"><span data-stu-id="f40cc-119">Use a layout in a component</span></span>

<span data-ttu-id="f40cc-120">Pomocí direktivy Razor `@layout` do rozložení můžete použít na komponentu.</span><span class="sxs-lookup"><span data-stu-id="f40cc-120">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="f40cc-121">Kompilátor převede tuto direktivu do `LayoutAttribute`, které platí pro třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="f40cc-121">The compiler converts this directive into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="f40cc-122">Následující příklad kódu ukazuje koncept.</span><span class="sxs-lookup"><span data-stu-id="f40cc-122">The following code sample demonstrates the concept.</span></span> <span data-ttu-id="f40cc-123">Obsah této součásti je vložen do *MasterLayout* v pozici `@Body`:</span><span class="sxs-lookup"><span data-stu-id="f40cc-123">The content of this component is inserted into the *MasterLayout* at the position of `@Body`:</span></span>

```cshtml
@layout MasterLayout
@page "/master-list"

<h2>Master Episode List</h2>
```

## <a name="centralized-layout-selection"></a><span data-ttu-id="f40cc-124">Výběr centralizované rozložení</span><span class="sxs-lookup"><span data-stu-id="f40cc-124">Centralized layout selection</span></span>

<span data-ttu-id="f40cc-125">Všechny složky, které aplikace aplikace může volitelně obsahovat soubor šablony s názvem *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f40cc-125">Every folder of a an app can optionally contain a template file named *_ViewImports.cshtml*.</span></span> <span data-ttu-id="f40cc-126">Kompilátor obsahuje direktivy zadané v souboru importy zobrazení všech šablon Razor ve stejné složce a rekurzivně ve všech jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="f40cc-126">The compiler includes the directives specified in the view imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="f40cc-127">Proto *_ViewImports.cshtml* soubor obsahující `@layout MainLayout` zajišťuje, že všechny součásti ve složce použití *MainLayout* rozložení.</span><span class="sxs-lookup"><span data-stu-id="f40cc-127">Therefore, a *_ViewImports.cshtml* file containing `@layout MainLayout` ensures that all of the components in a folder use the *MainLayout* layout.</span></span> <span data-ttu-id="f40cc-128">Není nutné opakovaně přidat `@layout` ke všem *.razor* soubory.</span><span class="sxs-lookup"><span data-stu-id="f40cc-128">There's no need to repeatedly add `@layout` to all of the *.razor* files.</span></span>

<span data-ttu-id="f40cc-129">Všimněte si, že používá výchozí šablonu *_ViewImports.cshtml* mechanismus pro výběr rozložení.</span><span class="sxs-lookup"><span data-stu-id="f40cc-129">Note that the default template uses the *_ViewImports.cshtml* mechanism for layout selection.</span></span> <span data-ttu-id="f40cc-130">Nově vytvořenou aplikaci, která obsahuje *_ViewImports.cshtml* soubor *součásti/stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="f40cc-130">A newly created app contains the *_ViewImports.cshtml* file in the *Components/Pages* folder.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="f40cc-131">Vnořené rozložení</span><span class="sxs-lookup"><span data-stu-id="f40cc-131">Nested layouts</span></span>

<span data-ttu-id="f40cc-132">Aplikace můžou zahrnovat vnořené rozložení.</span><span class="sxs-lookup"><span data-stu-id="f40cc-132">Apps can consist of nested layouts.</span></span> <span data-ttu-id="f40cc-133">Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení.</span><span class="sxs-lookup"><span data-stu-id="f40cc-133">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="f40cc-134">Například je možné vnoření rozložení tak, aby odrážely struktura víceúrovňových nabídky.</span><span class="sxs-lookup"><span data-stu-id="f40cc-134">For example, nesting layouts can be used to reflect a multi-level menu structure.</span></span>

<span data-ttu-id="f40cc-135">Následující ukázky kódu ukazují, jak používat vnořené rozložení.</span><span class="sxs-lookup"><span data-stu-id="f40cc-135">The following code samples show how to use nested layouts.</span></span> <span data-ttu-id="f40cc-136">*EpisodesComponent.cshtml* souboru je komponenta zobrazit.</span><span class="sxs-lookup"><span data-stu-id="f40cc-136">The *EpisodesComponent.cshtml* file is the component to display.</span></span> <span data-ttu-id="f40cc-137">Všimněte si, že součást odkazuje rozložení `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="f40cc-137">Note that the component references the layout `MasterListLayout`.</span></span>

<span data-ttu-id="f40cc-138">*EpisodesComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f40cc-138">*EpisodesComponent.cshtml*:</span></span>

```cshtml
@layout MasterListLayout
@page "/master-list/episodes"

<h1>Episodes</h1>
```

<span data-ttu-id="f40cc-139">*MasterListLayout.cshtml* poskytuje soubor `MasterListLayout`.</span><span class="sxs-lookup"><span data-stu-id="f40cc-139">The *MasterListLayout.cshtml* file provides the `MasterListLayout`.</span></span> <span data-ttu-id="f40cc-140">Rozložení odkazuje na jiné rozložení, `MasterLayout`, ve kterém se bude vložen.</span><span class="sxs-lookup"><span data-stu-id="f40cc-140">The layout references another layout, `MasterLayout`, where it's going to be embedded.</span></span>

<span data-ttu-id="f40cc-141">*MasterListLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f40cc-141">*MasterListLayout.cshtml*:</span></span>

```cshtml
@layout MasterLayout
@inherits LayoutComponentBase

<nav>
    <!-- Menu structure of master list -->
    ...
</nav>

@Body
```

<span data-ttu-id="f40cc-142">Nakonec `MasterLayout` obsahuje prvky rozložení nejvyšší úrovně, jako je například záhlaví, zápatí a hlavní nabídky.</span><span class="sxs-lookup"><span data-stu-id="f40cc-142">Finally, `MasterLayout` contains the top-level layout elements, such as the header, footer, and main menu.</span></span>

<span data-ttu-id="f40cc-143">*MasterLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f40cc-143">*MasterLayout.cshtml*:</span></span>

```cshtml
@inherits LayoutComponentBase

<header>...</header>
<nav>...</nav>

@Body
```
