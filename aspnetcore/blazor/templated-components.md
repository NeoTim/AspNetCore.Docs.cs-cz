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
# <a name="aspnet-core-opno-locblazor-templated-components"></a>ASP.NET Blazor komponenty šablony Core

[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)

Šablony komponenty jsou součásti, které přijímají jednu nebo více šablon uživatelského rozhraní jako parametry, které pak lze použít jako součást logiky vykreslování komponenty. Šablony komponent umožňují vytvářet součásti vyšší úrovně, které jsou opakovaněji než běžné součásti. Několik příkladů patří:

* Součást tabulky, která umožňuje uživateli zadat šablony pro záhlaví, řádky a zápatí tabulky.
* Součást seznamu, která umožňuje uživateli zadat šablonu pro vykreslování položek v seznamu.

## <a name="template-parameters"></a>Parametry šablony

Šablonovaná komponenta je definována zadáním jednoho nebo `RenderFragment` `RenderFragment<T>`více parametrů komponenttypu nebo . Fragment vykreslení představuje segment ui k vykreslení. `RenderFragment<T>`přebírá parametr typu, který lze zadat při vyvolání fragmentu vykreslení.

`TableTemplate`Komponenty:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

Při použití komponenty šablony lze parametry šablony zadat pomocí podřízených prvků,`TableHeader` `RowTemplate` které odpovídají názvům parametrů ( a v následujícím příkladu):

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
> Omezení obecného typu budou podporována v budoucí verzi. Další informace naleznete v tématu [Povolit omezení obecného typu (dotnet/aspnetcore #8433).](https://github.com/dotnet/aspnetcore/issues/8433)

## <a name="template-context-parameters"></a>Parametry kontextu šablony

Komponenty argumenty `RenderFragment<T>` typu předané jako `context` prvky mají implicitní parametr `@context.PetId`s názvem (například z `Context` předchozí ukázky kódu), ale můžete změnit název parametru pomocí atributu na podřízený prvek. V následujícím příkladu `RowTemplate` `Context` atribut prvku určuje `pet` parametr:

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

Případně můžete zadat `Context` atribut prvku komponenty. Zadaný `Context` atribut se vztahuje na všechny zadané parametry šablony. To může být užitečné, pokud chcete zadat název parametru obsahu pro implicitní podřízený obsah (bez obtékání podřízeného prvku). V následujícím příkladu `Context` se atribut `TableTemplate` zobrazí na elementu a vztahuje se na všechny parametry šablony:

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

## <a name="generic-typed-components"></a>Komponenty obecného typu

Šablony komponenty jsou často typově zadali. Například obecnou `ListViewTemplate` komponentu lze použít `IEnumerable<T>` k vykreslení hodnot. Chcete-li definovat obecnou [`@typeparam`](xref:mvc/views/razor#typeparam) komponentu, použijte direktivu k určení parametrů typu:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

Při použití komponent typu obecného typu je parametr typu pokud možno odvozen:

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

V opačném případě musí být parametr typu explicitně určen pomocí atributu, který odpovídá názvu parametru typu. V následujícím příkladu `TItem="Pet"` určuje typ:

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```
