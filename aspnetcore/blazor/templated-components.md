---
title: Součásti ASP.NET Core Blazor šablonou
author: guardrex
description: Přečtěte si, jak mohou šablony součásti přijmout jednu nebo více šablon uživatelského rozhraní jako parametry, které lze poté použít jako součást logiky vykreslování komponenty.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b57e3fe186402723607e90b1628062f602c77632
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989502"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a><span data-ttu-id="9930c-103">Součásti ASP.NET Core Blazor šablonou</span><span class="sxs-lookup"><span data-stu-id="9930c-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="9930c-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9930c-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9930c-105">Komponenty se šablonami jsou komponenty, které přijímají jednu nebo více šablon uživatelského rozhraní jako parametry, které lze poté použít jako součást logiky vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="9930c-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="9930c-106">Komponenty založené na šablonách umožňují vytvářet komponenty vyšší úrovně, které jsou opakovaně použitelné než běžné součásti.</span><span class="sxs-lookup"><span data-stu-id="9930c-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="9930c-107">Mezi několik příkladů patří:</span><span class="sxs-lookup"><span data-stu-id="9930c-107">A couple of examples include:</span></span>

* <span data-ttu-id="9930c-108">Tabulková komponenta, která uživateli umožňuje zadat šablony pro záhlaví, řádky a zápatí tabulky.</span><span class="sxs-lookup"><span data-stu-id="9930c-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="9930c-109">Komponenta seznamu, která umožňuje uživateli určit šablonu pro vykreslování položek v seznamu.</span><span class="sxs-lookup"><span data-stu-id="9930c-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="9930c-110">Parametry šablony</span><span class="sxs-lookup"><span data-stu-id="9930c-110">Template parameters</span></span>

<span data-ttu-id="9930c-111">Komponenta se šablonou je definována zadáním jednoho nebo více parametrů součásti typu `RenderFragment` nebo `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="9930c-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="9930c-112">Fragment vykreslování představuje segment uživatelského rozhraní, které se má vykreslit.</span><span class="sxs-lookup"><span data-stu-id="9930c-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="9930c-113">`RenderFragment<T>` převezme parametr typu, který lze zadat při vyvolání fragmentu vykreslování.</span><span class="sxs-lookup"><span data-stu-id="9930c-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="9930c-114">součást `TableTemplate`:</span><span class="sxs-lookup"><span data-stu-id="9930c-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="9930c-115">Při použití komponenty se šablonou lze parametry šablony zadat pomocí podřízených prvků, které odpovídají názvům parametrů (`TableHeader` a `RowTemplate` v následujícím příkladu):</span><span class="sxs-lookup"><span data-stu-id="9930c-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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
> <span data-ttu-id="9930c-116">Omezení obecného typu budou podporována v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="9930c-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="9930c-117">Další informace najdete v tématu [Povolení omezení obecného typu (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span><span class="sxs-lookup"><span data-stu-id="9930c-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="9930c-118">Kontextové parametry šablony</span><span class="sxs-lookup"><span data-stu-id="9930c-118">Template context parameters</span></span>

<span data-ttu-id="9930c-119">Argumenty součásti typu `RenderFragment<T>` předány jako elementy mají implicitní parametr s názvem `context` (například z předchozí ukázky kódu `@context.PetId`), ale můžete změnit název parametru pomocí atributu `Context` u podřízeného elementu.</span><span class="sxs-lookup"><span data-stu-id="9930c-119">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="9930c-120">V následujícím příkladu atribut `Context` elementu `RowTemplate` určuje parametr `pet`:</span><span class="sxs-lookup"><span data-stu-id="9930c-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="9930c-121">Alternativně lze zadat atribut `Context` prvku komponenty.</span><span class="sxs-lookup"><span data-stu-id="9930c-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="9930c-122">Zadaný atribut `Context` se vztahuje na všechny zadané parametry šablony.</span><span class="sxs-lookup"><span data-stu-id="9930c-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="9930c-123">To může být užitečné, pokud chcete zadat název parametru obsahu pro implicitní podřízený obsah (bez nutnosti zalamování podřízeného elementu).</span><span class="sxs-lookup"><span data-stu-id="9930c-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="9930c-124">V následujícím příkladu se atribut `Context` zobrazí u elementu `TableTemplate` a vztahuje se na všechny parametry šablony:</span><span class="sxs-lookup"><span data-stu-id="9930c-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

## <a name="generic-typed-components"></a><span data-ttu-id="9930c-125">Komponenty s obecným typem</span><span class="sxs-lookup"><span data-stu-id="9930c-125">Generic-typed components</span></span>

<span data-ttu-id="9930c-126">Komponenty se šablonami jsou často typu obecně typované.</span><span class="sxs-lookup"><span data-stu-id="9930c-126">Templated components are often generically typed.</span></span> <span data-ttu-id="9930c-127">Například obecná `ListViewTemplate` komponenta může být použita pro vykreslení `IEnumerable<T>`ch hodnot.</span><span class="sxs-lookup"><span data-stu-id="9930c-127">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="9930c-128">Chcete-li definovat obecné komponenty, použijte direktivu [`@typeparam`](xref:mvc/views/razor#typeparam) pro určení parametrů typu:</span><span class="sxs-lookup"><span data-stu-id="9930c-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="9930c-129">Pokud používáte komponenty s obecným typem, je parametr typu odvozený, pokud je to možné:</span><span class="sxs-lookup"><span data-stu-id="9930c-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="9930c-130">V opačném případě musí být parametr typu explicitně zadán pomocí atributu, který odpovídá názvu parametru typu.</span><span class="sxs-lookup"><span data-stu-id="9930c-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="9930c-131">V následujícím příkladu `TItem="Pet"` určuje typ:</span><span class="sxs-lookup"><span data-stu-id="9930c-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
