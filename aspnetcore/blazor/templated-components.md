---
title: ASP.NET Blazor komponenty šablony Core
author: guardrex
description: Zjistěte, jak mohou šablony komponenty přijímat jednu nebo více šablon uživatelského rozhraní jako parametry, které pak lze použít jako součást logiky vykreslování komponenty.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: blazor/templated-components
ms.openlocfilehash: b57e3fe186402723607e90b1628062f602c77632
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79989502"
---
# <a name="aspnet-core-opno-locblazor-templated-components"></a><span data-ttu-id="7d024-103">ASP.NET Blazor komponenty šablony Core</span><span class="sxs-lookup"><span data-stu-id="7d024-103">ASP.NET Core Blazor templated components</span></span>

<span data-ttu-id="7d024-104">[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="7d024-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="7d024-105">Šablony komponenty jsou součásti, které přijímají jednu nebo více šablon uživatelského rozhraní jako parametry, které pak lze použít jako součást logiky vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="7d024-105">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="7d024-106">Šablony komponent umožňují vytvářet součásti vyšší úrovně, které jsou opakovaněji než běžné součásti.</span><span class="sxs-lookup"><span data-stu-id="7d024-106">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="7d024-107">Několik příkladů patří:</span><span class="sxs-lookup"><span data-stu-id="7d024-107">A couple of examples include:</span></span>

* <span data-ttu-id="7d024-108">Součást tabulky, která umožňuje uživateli zadat šablony pro záhlaví, řádky a zápatí tabulky.</span><span class="sxs-lookup"><span data-stu-id="7d024-108">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="7d024-109">Součást seznamu, která umožňuje uživateli zadat šablonu pro vykreslování položek v seznamu.</span><span class="sxs-lookup"><span data-stu-id="7d024-109">A list component that allows a user to specify a template for rendering items in a list.</span></span>

## <a name="template-parameters"></a><span data-ttu-id="7d024-110">Parametry šablony</span><span class="sxs-lookup"><span data-stu-id="7d024-110">Template parameters</span></span>

<span data-ttu-id="7d024-111">Šablonovaná komponenta je definována zadáním jednoho nebo `RenderFragment` `RenderFragment<T>`více parametrů komponenttypu nebo .</span><span class="sxs-lookup"><span data-stu-id="7d024-111">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="7d024-112">Fragment vykreslení představuje segment ui k vykreslení.</span><span class="sxs-lookup"><span data-stu-id="7d024-112">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="7d024-113">`RenderFragment<T>`přebírá parametr typu, který lze zadat při vyvolání fragmentu vykreslení.</span><span class="sxs-lookup"><span data-stu-id="7d024-113">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="7d024-114">`TableTemplate`Komponenty:</span><span class="sxs-lookup"><span data-stu-id="7d024-114">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="7d024-115">Při použití komponenty šablony lze parametry šablony zadat pomocí podřízených prvků,`TableHeader` `RowTemplate` které odpovídají názvům parametrů ( a v následujícím příkladu):</span><span class="sxs-lookup"><span data-stu-id="7d024-115">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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
> <span data-ttu-id="7d024-116">Omezení obecného typu budou podporována v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="7d024-116">Generic type constraints will be supported in a future release.</span></span> <span data-ttu-id="7d024-117">Další informace naleznete v tématu [Povolit omezení obecného typu (dotnet/aspnetcore #8433).](https://github.com/dotnet/aspnetcore/issues/8433)</span><span class="sxs-lookup"><span data-stu-id="7d024-117">For more information, see [Allow generic type constraints (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).</span></span>

## <a name="template-context-parameters"></a><span data-ttu-id="7d024-118">Parametry kontextu šablony</span><span class="sxs-lookup"><span data-stu-id="7d024-118">Template context parameters</span></span>

<span data-ttu-id="7d024-119">Komponenty argumenty `RenderFragment<T>` typu předané jako `context` prvky mají implicitní parametr `@context.PetId`s názvem (například z `Context` předchozí ukázky kódu), ale můžete změnit název parametru pomocí atributu na podřízený prvek.</span><span class="sxs-lookup"><span data-stu-id="7d024-119">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="7d024-120">V následujícím příkladu `RowTemplate` `Context` atribut prvku určuje `pet` parametr:</span><span class="sxs-lookup"><span data-stu-id="7d024-120">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="7d024-121">Případně můžete zadat `Context` atribut prvku komponenty.</span><span class="sxs-lookup"><span data-stu-id="7d024-121">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="7d024-122">Zadaný `Context` atribut se vztahuje na všechny zadané parametry šablony.</span><span class="sxs-lookup"><span data-stu-id="7d024-122">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="7d024-123">To může být užitečné, pokud chcete zadat název parametru obsahu pro implicitní podřízený obsah (bez obtékání podřízeného prvku).</span><span class="sxs-lookup"><span data-stu-id="7d024-123">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="7d024-124">V následujícím příkladu `Context` se atribut `TableTemplate` zobrazí na elementu a vztahuje se na všechny parametry šablony:</span><span class="sxs-lookup"><span data-stu-id="7d024-124">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

## <a name="generic-typed-components"></a><span data-ttu-id="7d024-125">Komponenty obecného typu</span><span class="sxs-lookup"><span data-stu-id="7d024-125">Generic-typed components</span></span>

<span data-ttu-id="7d024-126">Šablony komponenty jsou často typově zadali.</span><span class="sxs-lookup"><span data-stu-id="7d024-126">Templated components are often generically typed.</span></span> <span data-ttu-id="7d024-127">Například obecnou `ListViewTemplate` komponentu lze použít `IEnumerable<T>` k vykreslení hodnot.</span><span class="sxs-lookup"><span data-stu-id="7d024-127">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="7d024-128">Chcete-li definovat obecnou [`@typeparam`](xref:mvc/views/razor#typeparam) komponentu, použijte direktivu k určení parametrů typu:</span><span class="sxs-lookup"><span data-stu-id="7d024-128">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="7d024-129">Při použití komponent typu obecného typu je parametr typu pokud možno odvozen:</span><span class="sxs-lookup"><span data-stu-id="7d024-129">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="7d024-130">V opačném případě musí být parametr typu explicitně určen pomocí atributu, který odpovídá názvu parametru typu.</span><span class="sxs-lookup"><span data-stu-id="7d024-130">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="7d024-131">V následujícím příkladu `TItem="Pet"` určuje typ:</span><span class="sxs-lookup"><span data-stu-id="7d024-131">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
