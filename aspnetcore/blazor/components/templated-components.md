---
title: BlazorKomponenty ASP.NET Core šablonou
author: guardrex
description: Přečtěte si, jak mohou šablony součásti přijmout jednu nebo více šablon uživatelského rozhraní jako parametry, které lze poté použít jako součást logiky vykreslování komponenty.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/templated-components
ms.openlocfilehash: ae591fb8280b706d568dd530e2e60a2f7955841c
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103707"
---
# <a name="aspnet-core-blazor-templated-components"></a><span data-ttu-id="55e38-103">BlazorKomponenty ASP.NET Core šablonou</span><span class="sxs-lookup"><span data-stu-id="55e38-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="55e38-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="55e38-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="55e38-105">Komponenty se šablonami jsou komponenty, které přijímají jednu nebo více šablon uživatelského rozhraní jako parametry, které lze poté použít jako součást logiky vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="55e38-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="55e38-106">Komponenty založené na šablonách umožňují vytvářet komponenty vyšší úrovně, které jsou opakovaně použitelné než běžné součásti.</span><span class="sxs-lookup"><span data-stu-id="55e38-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="55e38-107">Mezi několik příkladů patří:</span><span class="sxs-lookup"><span data-stu-id="55e38-107">A couple of examples include:</span></span>

* <span data-ttu-id="55e38-108">Tabulková komponenta, která uživateli umožňuje zadat šablony pro záhlaví, řádky a zápatí tabulky.</span><span class="sxs-lookup"><span data-stu-id="55e38-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="55e38-109">Komponenta seznamu, která umožňuje uživateli určit šablonu pro vykreslování položek v seznamu.</span><span class="sxs-lookup"><span data-stu-id="55e38-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="55e38-110">Parametry šablony</span><span class="sxs-lookup"><span data-stu-id="55e38-110">Template parameters</span></span>

<span data-ttu-id="55e38-111">Komponenta se šablonou je definována zadáním jednoho nebo více parametrů součásti typu <xref:Microsoft.AspNetCore.Components.RenderFragment> nebo <xref:Microsoft.AspNetCore.Components.RenderFragment%601> .</span><span class="sxs-lookup"><span data-stu-id="55e38-111">A templated component is defined by specifying one or more component parameters of type <xref:Microsoft.AspNetCore.Components.RenderFragment> or <xref:Microsoft.AspNetCore.Components.RenderFragment%601>.</span></span> <span data-ttu-id="55e38-112">Fragment vykreslování představuje segment uživatelského rozhraní, které se má vykreslit.</span><span class="sxs-lookup"><span data-stu-id="55e38-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="55e38-113"><xref:Microsoft.AspNetCore.Components.RenderFragment%601>převezme parametr typu, který lze zadat při vyvolání fragmentu vykreslování.</span><span class="sxs-lookup"><span data-stu-id="55e38-113"><xref:Microsoft.AspNetCore.Components.RenderFragment%601> takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="55e38-114">`TableTemplate`část</span><span class="sxs-lookup"><span data-stu-id="55e38-114">`TableTemplate` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="55e38-115">Při použití šablonované komponenty lze parametry šablony zadat pomocí podřízených prvků, které odpovídají názvům parametrů ( `TableHeader` a `RowTemplate` v následujícím příkladu):</span><span class="sxs-lookup"><span data-stu-id="55e38-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

> [!NOTE]
> <span data-ttu-id="55e38-116">Omezení obecného typu budou podporována v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="55e38-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="55e38-117">Další informace najdete v tématu [Povolení omezení obecného typu (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="55e38-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="55e38-118">Kontextové parametry šablony</span><span class="sxs-lookup"><span data-stu-id="55e38-118">Template context parameters</span></span>

<span data-ttu-id="55e38-119">Argumenty součásti typu <xref:Microsoft.AspNetCore.Components.RenderFragment%601> předané jako elementy mají implicitní parametr s názvem `context` (například z předchozí ukázky kódu `@context.PetId` ), ale můžete změnit název parametru pomocí `Context` atributu u podřízeného elementu.</span><span class="sxs-lookup"><span data-stu-id="55e38-119">Component arguments of type <xref:Microsoft.AspNetCore.Components.RenderFragment%601> passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="55e38-120">V následujícím příkladu `RowTemplate` `Context` atribut prvku Určuje `pet` parametr:</span><span class="sxs-lookup"><span data-stu-id="55e38-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="55e38-121">Alternativně lze zadat `Context` atribut prvku komponenty.</span><span class="sxs-lookup"><span data-stu-id="55e38-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="55e38-122">Zadaný `Context` atribut se vztahuje na všechny zadané parametry šablony.</span><span class="sxs-lookup"><span data-stu-id="55e38-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="55e38-123">To může být užitečné, pokud chcete zadat název parametru obsahu pro implicitní podřízený obsah (bez nutnosti zalamování podřízeného elementu).</span><span class="sxs-lookup"><span data-stu-id="55e38-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="55e38-124">V následujícím příkladu se `Context` atribut zobrazí na `TableTemplate` elementu a vztahuje se na všechny parametry šablony:</span><span class="sxs-lookup"><span data-stu-id="55e38-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

## <a name="generic-typed-components"></a><span data-ttu-id="55e38-125">Komponenty s obecným typem</span><span class="sxs-lookup"><span data-stu-id="55e38-125">Generic-typed components</span></span>

<span data-ttu-id="55e38-126">Komponenty se šablonami jsou často typu obecně typované.</span><span class="sxs-lookup"><span data-stu-id="55e38-126">Templated components are often generically typed.</span></span> <span data-ttu-id="55e38-127">Například obecná `ListViewTemplate` Komponenta může být použita k vykreslování `IEnumerable<T>` hodnot.</span><span class="sxs-lookup"><span data-stu-id="55e38-127">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="55e38-128">Chcete-li definovat obecné komponenty, použijte [`@typeparam`](xref:mvc/views/razor#typeparam) direktivu k určení parametrů typu:</span><span class="sxs-lookup"><span data-stu-id="55e38-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="55e38-129">Pokud používáte komponenty s obecným typem, je parametr typu odvozený, pokud je to možné:</span><span class="sxs-lookup"><span data-stu-id="55e38-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="55e38-130">V opačném případě musí být parametr typu explicitně zadán pomocí atributu, který odpovídá názvu parametru typu.</span><span class="sxs-lookup"><span data-stu-id="55e38-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="55e38-131">V následujícím příkladu `TItem="Pet"` Určuje typ:</span><span class="sxs-lookup"><span data-stu-id="55e38-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
