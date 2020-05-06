---
title: Komponenty Blazor ASP.NET Core šablonou
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
uid: blazor/templated-components
ms.openlocfilehash: de603d3520c124b278312e5167a2f8bad14cf6e9
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771064"
---
# <a name="aspnet-core-blazor-templated-components"></a>Komponenty Blazor ASP.NET Core šablonou

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

Komponenty se šablonami jsou komponenty, které přijímají jednu nebo více šablon uživatelského rozhraní jako parametry, které lze poté použít jako součást logiky vykreslování komponenty. Komponenty založené na šablonách umožňují vytvářet komponenty vyšší úrovně, které jsou opakovaně použitelné než běžné součásti. Mezi několik příkladů patří:

* Tabulková komponenta, která uživateli umožňuje zadat šablony pro záhlaví, řádky a zápatí tabulky.
* Komponenta seznamu, která umožňuje uživateli určit šablonu pro vykreslování položek v seznamu.

## <a name="template-parameters"></a>Parametry šablony

Komponenta se šablonou je definována zadáním jednoho nebo více parametrů součásti typu `RenderFragment` nebo. `RenderFragment<T>` Fragment vykreslování představuje segment uživatelského rozhraní, které se má vykreslit. `RenderFragment<T>`převezme parametr typu, který lze zadat při vyvolání fragmentu vykreslování.

`TableTemplate`část

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Při použití šablonované komponenty lze parametry šablony zadat pomocí podřízených prvků, které odpovídají názvům parametrů (`TableHeader` a `RowTemplate` v následujícím příkladu):

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
> Omezení obecného typu budou podporována v budoucí verzi. Další informace najdete v tématu [Povolení omezení obecného typu (dotnet/aspnetcore #8433)](https://github.com/dotnet/aspnetcore/issues/8433).

## <a name="template-context-parameters"></a>Kontextové parametry šablony

Argumenty součásti typu `RenderFragment<T>` předané jako elementy mají implicitní parametr s názvem `context` (například z předchozí ukázky kódu `@context.PetId`), ale můžete změnit název parametru pomocí `Context` atributu u podřízeného elementu. V následujícím příkladu `RowTemplate` `Context` atribut prvku Určuje `pet` parametr:

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

Alternativně lze zadat `Context` atribut prvku komponenty. Zadaný `Context` atribut se vztahuje na všechny zadané parametry šablony. To může být užitečné, pokud chcete zadat název parametru obsahu pro implicitní podřízený obsah (bez nutnosti zalamování podřízeného elementu). V následujícím příkladu se `Context` atribut zobrazí na `TableTemplate` elementu a vztahuje se na všechny parametry šablony:

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

## <a name="generic-typed-components"></a>Komponenty s obecným typem

Komponenty se šablonami jsou často typu obecně typované. Například obecná `ListViewTemplate` komponenta může být použita k vykreslování `IEnumerable<T>` hodnot. Chcete-li definovat obecné komponenty, použijte [`@typeparam`](xref:mvc/views/razor#typeparam) direktivu k určení parametrů typu:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Pokud používáte komponenty s obecným typem, je parametr typu odvozený, pokud je to možné:

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

V opačném případě musí být parametr typu explicitně zadán pomocí atributu, který odpovídá názvu parametru typu. V následujícím příkladu `TItem="Pet"` určuje typ:

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
