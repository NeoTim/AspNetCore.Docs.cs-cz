---
title: Rozložení Razor komponenty
author: guardrex
description: Zjistěte, jak vytvářet rozložení opakovaně použitelné komponenty pro Blazor a Razor součásti aplikace.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/layouts
ms.openlocfilehash: c67ef1ae79f7961c571af7db3da0028cb870c7ed
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55668116"
---
# <a name="razor-components-layouts"></a><span data-ttu-id="2d02d-103">Rozložení Razor komponenty</span><span class="sxs-lookup"><span data-stu-id="2d02d-103">Razor Components layouts</span></span>

<span data-ttu-id="2d02d-104">Podle [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="2d02d-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="2d02d-105">Aplikace obvykle obsahují více než jednu stránku.</span><span class="sxs-lookup"><span data-stu-id="2d02d-105">Apps typically contain more than one page.</span></span> <span data-ttu-id="2d02d-106">Prvky, rozložení, jako jsou nabídky, zprávy o autorských právech a loga, musí být k dispozici na všech stránkách.</span><span class="sxs-lookup"><span data-stu-id="2d02d-106">Layout elements, such as menus, copyright messages, and logos, must be present on all pages.</span></span> <span data-ttu-id="2d02d-107">Kopírování kódu z těchto elementů rozložení do všech stránek aplikace není efektivní řešení.</span><span class="sxs-lookup"><span data-stu-id="2d02d-107">Copying the code of these layout elements into all of the pages of an app isn't an efficient solution.</span></span> <span data-ttu-id="2d02d-108">Tato duplikace je obtížné udržovat a pravděpodobně povede k nekonzistentní obsah v čase.</span><span class="sxs-lookup"><span data-stu-id="2d02d-108">Such duplication is hard to maintain and probably leads to inconsistent content over time.</span></span> <span data-ttu-id="2d02d-109">*Rozložení* tento problém vyřešit.</span><span class="sxs-lookup"><span data-stu-id="2d02d-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="2d02d-110">Technicky vzato rozložení je jenom další komponenty.</span><span class="sxs-lookup"><span data-stu-id="2d02d-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="2d02d-111">Rozložení je definována šablona Razor nebo v C# kódu a může obsahovat další běžné funkce komponenty, vkládání závislostí a datové vazby.</span><span class="sxs-lookup"><span data-stu-id="2d02d-111">A layout is defined in a Razor template or in C# code and can contain data binding, dependency injection, and other ordinary features of components.</span></span> <span data-ttu-id="2d02d-112">Zapnout dva další aspekty *komponenty* do *rozložení*:</span><span class="sxs-lookup"><span data-stu-id="2d02d-112">Two additional aspects turn a *component* into a *layout*:</span></span>

* <span data-ttu-id="2d02d-113">Komponenta rozložení musí dědit z `BlazorLayoutComponent`.</span><span class="sxs-lookup"><span data-stu-id="2d02d-113">The layout component must inherit from `BlazorLayoutComponent`.</span></span> <span data-ttu-id="2d02d-114">`BlazorLayoutComponent` definuje `Body` vlastnost, která obsahuje obsah, který se vykreslí uvnitř rozložení.</span><span class="sxs-lookup"><span data-stu-id="2d02d-114">`BlazorLayoutComponent` defines a `Body` property that contains the content to be rendered inside the layout.</span></span>
* <span data-ttu-id="2d02d-115">Používá součásti rozložení `Body` vlastnosti a určit, kde má být obsah textu vykreslen pomocí syntaxe Razor `@Body`.</span><span class="sxs-lookup"><span data-stu-id="2d02d-115">The layout component uses the `Body` property to specify where the body content should be rendered using the Razor syntax `@Body`.</span></span> <span data-ttu-id="2d02d-116">Při vykreslování, `@Body` nahrazuje obsah rozložení.</span><span class="sxs-lookup"><span data-stu-id="2d02d-116">During rendering, `@Body` is replaced by the content of the layout.</span></span>

<span data-ttu-id="2d02d-117">Následující příklad kódu ukazuje šablona Razor součásti rozložení.</span><span class="sxs-lookup"><span data-stu-id="2d02d-117">The following code sample shows the Razor template of a layout component.</span></span> <span data-ttu-id="2d02d-118">Všimněte si použití `BlazorLayoutComponent` a `@Body`:</span><span class="sxs-lookup"><span data-stu-id="2d02d-118">Note the use of `BlazorLayoutComponent` and `@Body`:</span></span>

```csharp
@inherits BlazorLayoutComponent

<header>
    <h1>ERP Master 3000</h1>
</header>

<nav>
    <a href="master-data">Master Data Management</a>
    <a href="invoicing">Invoicing</a>
    <a href="accounting">Accounting</a>
</nav>

@Body

<footer>
    &copy; by @CopyrightMessage
</footer>

@functions {
    public string CopyrightMessage { get; set; }
    ...
}
```

## <a name="use-a-layout-in-a-component"></a><span data-ttu-id="2d02d-119">Použít rozložení v komponentě</span><span class="sxs-lookup"><span data-stu-id="2d02d-119">Use a layout in a component</span></span>

<span data-ttu-id="2d02d-120">Pomocí direktivy Razor `@layout` do rozložení můžete použít na komponentu.</span><span class="sxs-lookup"><span data-stu-id="2d02d-120">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="2d02d-121">Kompilátor převede tuto direktivu do `LayoutAttribute`, které platí pro třídu komponenty.</span><span class="sxs-lookup"><span data-stu-id="2d02d-121">The compiler converts this directive into a `LayoutAttribute`, which is applied to the component class.</span></span>

<span data-ttu-id="2d02d-122">Následující příklad kódu ukazuje koncept.</span><span class="sxs-lookup"><span data-stu-id="2d02d-122">The following code sample demonstrates the concept.</span></span> <span data-ttu-id="2d02d-123">Obsah této součásti je vložen do *MasterLayout* v pozici `@Body`:</span><span class="sxs-lookup"><span data-stu-id="2d02d-123">The content of this component is inserted into the *MasterLayout* at the position of `@Body`:</span></span>

```csharp
@layout MasterLayout

@page "/master-data"

<h2>Master Data Management</h2>
...
```

## <a name="centralized-layout-selection"></a><span data-ttu-id="2d02d-124">Výběr centralizované rozložení</span><span class="sxs-lookup"><span data-stu-id="2d02d-124">Centralized layout selection</span></span>

<span data-ttu-id="2d02d-125">Všechny složky, které aplikace aplikace může volitelně obsahovat soubor šablony s názvem *_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="2d02d-125">Every folder of a an app can optionally contain a template file named *_ViewImports.cshtml*.</span></span> <span data-ttu-id="2d02d-126">Kompilátor obsahuje direktivy zadané v souboru importy zobrazení všech šablon Razor ve stejné složce a rekurzivně ve všech jejích podsložkách.</span><span class="sxs-lookup"><span data-stu-id="2d02d-126">The compiler includes the directives specified in the view imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="2d02d-127">Proto *_ViewImports.cshtml* soubor obsahující `@layout MainLayout` zajišťuje, že všechny součásti ve složce použití *MainLayout* rozložení.</span><span class="sxs-lookup"><span data-stu-id="2d02d-127">Therefore, a *_ViewImports.cshtml* file containing `@layout MainLayout` ensures that all of the components in a folder use the *MainLayout* layout.</span></span> <span data-ttu-id="2d02d-128">Není nutné opakovaně přidat `@layout` ke všem  *\*.cshtml* soubory.</span><span class="sxs-lookup"><span data-stu-id="2d02d-128">There's no need to repeatedly add `@layout` to all of the *\*.cshtml* files.</span></span>

<span data-ttu-id="2d02d-129">Všimněte si, že používá výchozí šablonu *_ViewImports.cshtml* mechanismus pro výběr rozložení.</span><span class="sxs-lookup"><span data-stu-id="2d02d-129">Note that the default template uses the *_ViewImports.cshtml* mechanism for layout selection.</span></span> <span data-ttu-id="2d02d-130">Nově vytvořenou aplikaci, která obsahuje *_ViewImports.cshtml* soubor *stránky* složky.</span><span class="sxs-lookup"><span data-stu-id="2d02d-130">A newly created app contains the *_ViewImports.cshtml* file in the *Pages* folder.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="2d02d-131">Vnořené rozložení</span><span class="sxs-lookup"><span data-stu-id="2d02d-131">Nested layouts</span></span>

<span data-ttu-id="2d02d-132">Aplikace můžou zahrnovat vnořené rozložení.</span><span class="sxs-lookup"><span data-stu-id="2d02d-132">Apps can consist of nested layouts.</span></span> <span data-ttu-id="2d02d-133">Komponenta může odkazovat na rozložení, které zase odkazuje na jiné rozložení.</span><span class="sxs-lookup"><span data-stu-id="2d02d-133">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="2d02d-134">Například je možné vnoření rozložení tak, aby odrážely struktura víceúrovňových nabídky.</span><span class="sxs-lookup"><span data-stu-id="2d02d-134">For example, nesting layouts can be used to reflect a multi-level menu structure.</span></span>

<span data-ttu-id="2d02d-135">Následující ukázky kódu ukazují, jak používat vnořené rozložení.</span><span class="sxs-lookup"><span data-stu-id="2d02d-135">The following code samples show how to use nested layouts.</span></span> <span data-ttu-id="2d02d-136">*CustomersComponent.cshtml* souboru je komponenta zobrazit.</span><span class="sxs-lookup"><span data-stu-id="2d02d-136">The *CustomersComponent.cshtml* file is the component to display.</span></span> <span data-ttu-id="2d02d-137">Všimněte si, že součást odkazuje rozložení `MasterDataLayout`.</span><span class="sxs-lookup"><span data-stu-id="2d02d-137">Note that the component references the layout `MasterDataLayout`.</span></span>

<span data-ttu-id="2d02d-138">*CustomersComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2d02d-138">*CustomersComponent.cshtml*:</span></span>

```csharp
@layout MasterDataLayout

@page "/master-data/customers"

<h1>Customer Maintenance</h1>
...
```

<span data-ttu-id="2d02d-139">*MasterDataLayout.cshtml* poskytuje soubor `MasterDataLayout`.</span><span class="sxs-lookup"><span data-stu-id="2d02d-139">The *MasterDataLayout.cshtml* file provides the `MasterDataLayout`.</span></span> <span data-ttu-id="2d02d-140">Rozložení odkazuje na jiné rozložení, `MainLayout`, ve kterém se bude vložen.</span><span class="sxs-lookup"><span data-stu-id="2d02d-140">The layout references another layout, `MainLayout`, where it's going to be embedded.</span></span>

<span data-ttu-id="2d02d-141">*MasterDataLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2d02d-141">*MasterDataLayout.cshtml*:</span></span>

```csharp
@layout MainLayout
@inherits BlazorLayoutComponent

<nav>
    <!-- Menu structure of master data module -->
    ...
</nav>

@Body
```

<span data-ttu-id="2d02d-142">Nakonec `MainLayout` obsahuje prvky rozložení nejvyšší úrovně, jako je například záhlaví, zápatí a hlavní nabídky.</span><span class="sxs-lookup"><span data-stu-id="2d02d-142">Finally, `MainLayout` contains the top-level layout elements, such as the header, footer, and main menu.</span></span>

<span data-ttu-id="2d02d-143">*MainLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2d02d-143">*MainLayout.cshtml*:</span></span>

```csharp
@inherits BlazorLayoutComponent

<header>...</header>
<nav>...</nav>

@Body
```
