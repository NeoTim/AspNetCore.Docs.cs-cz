---
title: BlazorVirtualizace ASP.NET Core komponent
author: guardrex
description: Naučte se používat virtualizaci komponent v Blazor aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2020
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
uid: blazor/components/virtualization
ms.openlocfilehash: 9c3e53bee7535b36bba3474ff50a881568bbd690
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393805"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a>BlazorVirtualizace ASP.NET Core komponent

Od [Daniel Skořepa](https://github.com/danroth27)

Vylepšete vnímaný výkon vykreslování komponent pomocí Blazor integrované podpory virtualizace architektury. Virtualizace je technika, jak omezit vykreslování uživatelského rozhraní pouze na části, které jsou aktuálně viditelné. Například virtualizace je užitečná v případě, že aplikace musí vykreslovat dlouhý seznam položek a v libovolnou dobu je nutné zobrazit pouze podmnožinu položek. Blazor poskytuje `Virtualize` komponentu, která se dá použít k přidání virtualizace do komponent aplikace.

::: moniker range=">= aspnetcore-5.0"

Bez virtualizace může běžný seznam použít [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) smyčku jazyka C# k vykreslení každé položky v seznamu:

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

Pokud seznam obsahuje tisíce položek, pak vykreslování seznamu může trvat dlouhou dobu. Uživatel může vyskytnout znatelné prodlevy uživatelského rozhraní.

Místo vykreslování každé položky v seznamu najednou, nahraďte [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) smyčku `Virtualize` součástí a určete zdroj pevné položky `Items` . Vykresleny jsou pouze položky, které jsou aktuálně viditelné:

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Pokud není zadán kontext pro komponentu s `Context` , použijte `context` hodnotu ( `@context.{PROPERTY}` ) v šabloně obsahu položky:

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

`Virtualize`Komponenta vypočítá počet položek, které se mají vykreslit, na základě výšky kontejneru a velikosti vykreslených položek.

Obsah položky pro `Virtualize` součást může zahrnovat:

* Prostý kód HTML a Razor kód, jak ukazuje předchozí příklad.
* Jedna nebo více Razor součástí.
* Kombinace HTML/ Razor a Razor komponent.

## <a name="item-provider-delegate"></a>Delegát poskytovatele položek

Pokud nechcete načíst všechny položky do paměti, můžete určit metodu delegáta poskytovatele položek pro `ItemsProvider` parametr komponenty, který asynchronně načte požadované položky na vyžádání:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

Poskytovatel položek obdrží `ItemsProviderRequest` , který určuje požadovaný počet položek od určitého počátečního indexu. Poskytovatel položek následně načte požadované položky z databáze nebo jiné služby a vrátí je jako výsledek `ItemsProviderResult<TItem>` s počtem položek celkem. Poskytovatel položek se může rozhodnout načíst položky s každou žádostí nebo je Uložit do mezipaměti, aby byly snadno dostupné. Nepokoušejte se použít poskytovatele položek a přiřadit kolekci ke `Items` stejné `Virtualize` komponentě.

Následující příklad načte zaměstnance z `EmployeeService` :

```csharp
private async ValueTask<ItemsProviderResult<Employee>> LoadEmployees(
    ItemsProviderRequest request)
{
    var numEmployees = Math.Min(request.Count, totalEmployees - request.StartIndex);
    var employees = await EmployeesService.GetEmployeesAsync(request.StartIndex, 
        numEmployees, request.CancellationToken);

    return new ItemsProviderResult<Employee>(employees, totalEmployees);
}
```

## <a name="placeholder"></a>Zástupný symbol

Vzhledem k tomu, že požadavek na položky ze vzdáleného zdroje dat může nějakou dobu trvat, máte možnost vykreslit zástupný symbol ( `<Placeholder>...</Placeholder>` ), dokud nebudou k dispozici data položky:

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <ItemContent>
        <p>
            @employee.FirstName @employee.LastName has the 
            job title of @employee.JobTitle.
        </p>
    </ItemContent>
    <Placeholder>
        <p>
            Loading&hellip;
        </p>
    </Placeholder>
</Virtualize>
```

## <a name="item-size"></a>Velikost položky

Velikost každé položky v pixelech lze nastavit s hodnotou `ItemSize` (výchozí: 50px):

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a>Počet překontrol

`OverscanCount` Určuje, kolik dalších položek je vykresleno před a po viditelné oblasti. Toto nastavení pomáhá snižovat četnost vykreslování během posouvání. Výsledkem ale vyšších hodnot je více prvků vykreslených na stránce (výchozí: 3):

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Například mřížka nebo seznam, který vykresluje stovky řádků obsahujících komponenty, je náročné na vykreslování procesoru. Zvažte možnost Virtualizovat rozložení mřížky nebo seznamu tak, aby se v určitou dobu vykreslila pouze podmnožina komponent. Příklad vykreslování podmnožiny komponent naleznete v následujících součástech v [ `Virtualization` ukázkové aplikaci (ASPNET/Samples úložiště GitHub)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize` Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): komponenta napsaná v jazyce C#, která implementuje <xref:Microsoft.AspNetCore.Components.ComponentBase> pro vykreslení sady datových řádků počasí na základě posouvání uživatele.
* `FetchData` Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): používá `Virtualize` komponentu k zobrazení 25 řádků dat počasí současně.

::: moniker-end

## <a name="state-changes"></a>Změny stavu

Při provádění změn v položkách vygenerovaných `Virtualize` komponentou zavolejte <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> k vynucení opakovaného vyhodnocení a opětovného vykreslování komponenty.
