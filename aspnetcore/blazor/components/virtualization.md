---
title: BlazorVirtualizace ASP.NET Core komponent
author: guardrex
description: Naučte se používat virtualizaci komponent v Blazor aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/21/2020
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
ms.openlocfilehash: 911eeeb445741aa1519e1464dd4a75e26f6f12ab
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847569"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="15309-103">BlazorVirtualizace ASP.NET Core komponent</span><span class="sxs-lookup"><span data-stu-id="15309-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="15309-104">Od [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="15309-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="15309-105">Vylepšete vnímaný výkon vykreslování komponent pomocí Blazor integrované podpory virtualizace architektury.</span><span class="sxs-lookup"><span data-stu-id="15309-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="15309-106">Virtualizace je technika, jak omezit vykreslování uživatelského rozhraní pouze na části, které jsou aktuálně viditelné.</span><span class="sxs-lookup"><span data-stu-id="15309-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="15309-107">Například virtualizace je užitečná v případě, že aplikace musí vykreslovat dlouhý seznam nebo tabulku s mnoha řádky a v daném okamžiku je nutné zobrazit pouze podmnožinu položek.</span><span class="sxs-lookup"><span data-stu-id="15309-107">For example, virtualization is helpful when the app must render a long list or a table with many rows and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="15309-108">Blazor poskytuje `Virtualize` komponentu, která se dá použít k přidání virtualizace do komponent aplikace.</span><span class="sxs-lookup"><span data-stu-id="15309-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="15309-109">Bez virtualizace může [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) k vykreslení každé položky v seznamu nebo v jednotlivých řádcích v tabulce použít smyčku v jazyce C#.</span><span class="sxs-lookup"><span data-stu-id="15309-109">Without virtualization, a typical list or table-based component might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list or each row in the table:</span></span>

```razor
<table>
    @foreach (var employee in employees)
    {
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    }
</table>
```

<span data-ttu-id="15309-110">Pokud seznam obsahuje tisíce položek, pak vykreslování seznamu může trvat dlouhou dobu.</span><span class="sxs-lookup"><span data-stu-id="15309-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="15309-111">Uživatel může vyskytnout znatelné prodlevy uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="15309-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="15309-112">Místo vykreslování každé položky v seznamu najednou, nahraďte [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) smyčku `Virtualize` součástí a určete zdroj pevné položky `Items` .</span><span class="sxs-lookup"><span data-stu-id="15309-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="15309-113">Vykresleny jsou pouze položky, které jsou aktuálně viditelné:</span><span class="sxs-lookup"><span data-stu-id="15309-113">Only the items that are currently visible are rendered:</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees">
        <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="15309-114">Pokud není zadán kontext pro komponentu s `Context` , použijte `context` hodnotu ( `@context.{PROPERTY}` ) v šabloně obsahu položky:</span><span class="sxs-lookup"><span data-stu-id="15309-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<table>
    <Virtualize Items="@employees">
        <tr>
            <td>@context.FirstName</td>
            <td>@context.LastName</td>
            <td>@context.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="15309-115">`Virtualize`Komponenta vypočítá počet položek, které se mají vykreslit, na základě výšky kontejneru a velikosti vykreslených položek.</span><span class="sxs-lookup"><span data-stu-id="15309-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="15309-116">Delegát poskytovatele položek</span><span class="sxs-lookup"><span data-stu-id="15309-116">Item provider delegate</span></span>

<span data-ttu-id="15309-117">Pokud nechcete načíst všechny položky do paměti, můžete určit metodu delegáta poskytovatele položek pro `ItemsProvider` parametr komponenty, který asynchronně načte požadované položky na vyžádání:</span><span class="sxs-lookup"><span data-stu-id="15309-117">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
         <tr>
            <td>@employee.FirstName</td>
            <td>@employee.LastName</td>
            <td>@employee.JobTitle</td>
        </tr>
    </Virtualize>
</table>
```

<span data-ttu-id="15309-118">Poskytovatel položek obdrží `ItemsProviderRequest` , který určuje požadovaný počet položek od určitého počátečního indexu.</span><span class="sxs-lookup"><span data-stu-id="15309-118">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="15309-119">Poskytovatel položek následně načte požadované položky z databáze nebo jiné služby a vrátí je jako výsledek `ItemsProviderResult<TItem>` s počtem položek celkem.</span><span class="sxs-lookup"><span data-stu-id="15309-119">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="15309-120">Poskytovatel položek se může rozhodnout načíst položky s každou žádostí nebo je Uložit do mezipaměti, aby byly snadno dostupné.</span><span class="sxs-lookup"><span data-stu-id="15309-120">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="15309-121">Nepokoušejte se použít poskytovatele položek a přiřadit kolekci ke `Items` stejné `Virtualize` komponentě.</span><span class="sxs-lookup"><span data-stu-id="15309-121">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="15309-122">Následující příklad načte zaměstnance z `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="15309-122">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="15309-123">Zástupný symbol</span><span class="sxs-lookup"><span data-stu-id="15309-123">Placeholder</span></span>

<span data-ttu-id="15309-124">Vzhledem k tomu, že požadavek na položky ze vzdáleného zdroje dat může nějakou dobu trvat, máte možnost vykreslit zástupný symbol ( `<Placeholder>...</Placeholder>` ), dokud nebudou k dispozici data položky:</span><span class="sxs-lookup"><span data-stu-id="15309-124">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

```razor
<table>
    <Virtualize Context="employee" ItemsProvider="@LoadEmployees">
        <ItemContent>
            <tr>
                <td>@employee.FirstName</td>
                <td>@employee.LastName</td>
                <td>@employee.JobTitle</td>
            </tr>
        </ItemContent>
        <Placeholder>
            <tr>
                <td>Loading...</td>
            </tr>
        </Placeholder>
    </Virtualize>
</table>
```

## <a name="item-size"></a><span data-ttu-id="15309-125">Velikost položky</span><span class="sxs-lookup"><span data-stu-id="15309-125">Item size</span></span>

<span data-ttu-id="15309-126">Velikost každé položky v pixelech lze nastavit s hodnotou `ItemSize` (výchozí: 50px):</span><span class="sxs-lookup"><span data-stu-id="15309-126">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees" ItemSize="25">
        ...
    </Virtualize>
</table>
```

## <a name="overscan-count"></a><span data-ttu-id="15309-127">Počet překontrol</span><span class="sxs-lookup"><span data-stu-id="15309-127">Overscan count</span></span>

<span data-ttu-id="15309-128">`OverscanCount` Určuje, kolik dalších položek je vykresleno před a po viditelné oblasti.</span><span class="sxs-lookup"><span data-stu-id="15309-128">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="15309-129">Toto nastavení pomáhá snižovat četnost vykreslování během posouvání.</span><span class="sxs-lookup"><span data-stu-id="15309-129">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="15309-130">Výsledkem ale vyšších hodnot je více prvků vykreslených na stránce (výchozí: 3):</span><span class="sxs-lookup"><span data-stu-id="15309-130">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<table>
    <Virtualize Context="employee" Items="@employees" OverscanCount="4">
        ...
    </Virtualize>
</table>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="15309-131">Například mřížka nebo seznam, který vykresluje stovky řádků obsahujících komponenty, je náročné na vykreslování procesoru.</span><span class="sxs-lookup"><span data-stu-id="15309-131">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="15309-132">Zvažte možnost Virtualizovat rozložení mřížky nebo seznamu tak, aby se v určitou dobu vykreslila pouze podmnožina komponent.</span><span class="sxs-lookup"><span data-stu-id="15309-132">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="15309-133">Příklad vykreslování podmnožiny komponent naleznete v následujících součástech v [ `Virtualization` ukázkové aplikaci (ASPNET/Samples úložiště GitHub)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="15309-133">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="15309-134">`Virtualize` Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): komponenta napsaná v jazyce C#, která implementuje <xref:Microsoft.AspNetCore.Components.ComponentBase> pro vykreslení sady datových řádků počasí na základě posouvání uživatele.</span><span class="sxs-lookup"><span data-stu-id="15309-134">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="15309-135">`FetchData` Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): používá `Virtualize` komponentu k zobrazení 25 řádků dat počasí současně.</span><span class="sxs-lookup"><span data-stu-id="15309-135">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

::: moniker-end

## <a name="state-changes"></a><span data-ttu-id="15309-136">Změny stavu</span><span class="sxs-lookup"><span data-stu-id="15309-136">State changes</span></span>

<span data-ttu-id="15309-137">Při provádění změn v položkách vygenerovaných `Virtualize` komponentou zavolejte <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> k vynucení opakovaného vyhodnocení a opětovného vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="15309-137">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
