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
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="3c231-103">BlazorVirtualizace ASP.NET Core komponent</span><span class="sxs-lookup"><span data-stu-id="3c231-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="3c231-104">Od [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3c231-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="3c231-105">Vylepšete vnímaný výkon vykreslování komponent pomocí Blazor integrované podpory virtualizace architektury.</span><span class="sxs-lookup"><span data-stu-id="3c231-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="3c231-106">Virtualizace je technika, jak omezit vykreslování uživatelského rozhraní pouze na části, které jsou aktuálně viditelné.</span><span class="sxs-lookup"><span data-stu-id="3c231-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="3c231-107">Například virtualizace je užitečná v případě, že aplikace musí vykreslovat dlouhý seznam položek a v libovolnou dobu je nutné zobrazit pouze podmnožinu položek.</span><span class="sxs-lookup"><span data-stu-id="3c231-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="3c231-108">Blazor poskytuje `Virtualize` komponentu, která se dá použít k přidání virtualizace do komponent aplikace.</span><span class="sxs-lookup"><span data-stu-id="3c231-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3c231-109">Bez virtualizace může běžný seznam použít [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) smyčku jazyka C# k vykreslení každé položky v seznamu:</span><span class="sxs-lookup"><span data-stu-id="3c231-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="3c231-110">Pokud seznam obsahuje tisíce položek, pak vykreslování seznamu může trvat dlouhou dobu.</span><span class="sxs-lookup"><span data-stu-id="3c231-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="3c231-111">Uživatel může vyskytnout znatelné prodlevy uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="3c231-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="3c231-112">Místo vykreslování každé položky v seznamu najednou, nahraďte [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) smyčku `Virtualize` součástí a určete zdroj pevné položky `Items` .</span><span class="sxs-lookup"><span data-stu-id="3c231-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="3c231-113">Vykresleny jsou pouze položky, které jsou aktuálně viditelné:</span><span class="sxs-lookup"><span data-stu-id="3c231-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="3c231-114">Pokud není zadán kontext pro komponentu s `Context` , použijte `context` hodnotu ( `@context.{PROPERTY}` ) v šabloně obsahu položky:</span><span class="sxs-lookup"><span data-stu-id="3c231-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="3c231-115">`Virtualize`Komponenta vypočítá počet položek, které se mají vykreslit, na základě výšky kontejneru a velikosti vykreslených položek.</span><span class="sxs-lookup"><span data-stu-id="3c231-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="3c231-116">Obsah položky pro `Virtualize` součást může zahrnovat:</span><span class="sxs-lookup"><span data-stu-id="3c231-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="3c231-117">Prostý kód HTML a Razor kód, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="3c231-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="3c231-118">Jedna nebo více Razor součástí.</span><span class="sxs-lookup"><span data-stu-id="3c231-118">One or more Razor components.</span></span>
* <span data-ttu-id="3c231-119">Kombinace HTML/ Razor a Razor komponent.</span><span class="sxs-lookup"><span data-stu-id="3c231-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="3c231-120">Delegát poskytovatele položek</span><span class="sxs-lookup"><span data-stu-id="3c231-120">Item provider delegate</span></span>

<span data-ttu-id="3c231-121">Pokud nechcete načíst všechny položky do paměti, můžete určit metodu delegáta poskytovatele položek pro `ItemsProvider` parametr komponenty, který asynchronně načte požadované položky na vyžádání:</span><span class="sxs-lookup"><span data-stu-id="3c231-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="3c231-122">Poskytovatel položek obdrží `ItemsProviderRequest` , který určuje požadovaný počet položek od určitého počátečního indexu.</span><span class="sxs-lookup"><span data-stu-id="3c231-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="3c231-123">Poskytovatel položek následně načte požadované položky z databáze nebo jiné služby a vrátí je jako výsledek `ItemsProviderResult<TItem>` s počtem položek celkem.</span><span class="sxs-lookup"><span data-stu-id="3c231-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="3c231-124">Poskytovatel položek se může rozhodnout načíst položky s každou žádostí nebo je Uložit do mezipaměti, aby byly snadno dostupné.</span><span class="sxs-lookup"><span data-stu-id="3c231-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="3c231-125">Nepokoušejte se použít poskytovatele položek a přiřadit kolekci ke `Items` stejné `Virtualize` komponentě.</span><span class="sxs-lookup"><span data-stu-id="3c231-125">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="3c231-126">Následující příklad načte zaměstnance z `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="3c231-126">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="3c231-127">Zástupný symbol</span><span class="sxs-lookup"><span data-stu-id="3c231-127">Placeholder</span></span>

<span data-ttu-id="3c231-128">Vzhledem k tomu, že požadavek na položky ze vzdáleného zdroje dat může nějakou dobu trvat, máte možnost vykreslit zástupný symbol ( `<Placeholder>...</Placeholder>` ), dokud nebudou k dispozici data položky:</span><span class="sxs-lookup"><span data-stu-id="3c231-128">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

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

## <a name="item-size"></a><span data-ttu-id="3c231-129">Velikost položky</span><span class="sxs-lookup"><span data-stu-id="3c231-129">Item size</span></span>

<span data-ttu-id="3c231-130">Velikost každé položky v pixelech lze nastavit s hodnotou `ItemSize` (výchozí: 50px):</span><span class="sxs-lookup"><span data-stu-id="3c231-130">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="3c231-131">Počet překontrol</span><span class="sxs-lookup"><span data-stu-id="3c231-131">Overscan count</span></span>

<span data-ttu-id="3c231-132">`OverscanCount` Určuje, kolik dalších položek je vykresleno před a po viditelné oblasti.</span><span class="sxs-lookup"><span data-stu-id="3c231-132">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="3c231-133">Toto nastavení pomáhá snižovat četnost vykreslování během posouvání.</span><span class="sxs-lookup"><span data-stu-id="3c231-133">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="3c231-134">Výsledkem ale vyšších hodnot je více prvků vykreslených na stránce (výchozí: 3):</span><span class="sxs-lookup"><span data-stu-id="3c231-134">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="3c231-135">Například mřížka nebo seznam, který vykresluje stovky řádků obsahujících komponenty, je náročné na vykreslování procesoru.</span><span class="sxs-lookup"><span data-stu-id="3c231-135">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="3c231-136">Zvažte možnost Virtualizovat rozložení mřížky nebo seznamu tak, aby se v určitou dobu vykreslila pouze podmnožina komponent.</span><span class="sxs-lookup"><span data-stu-id="3c231-136">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="3c231-137">Příklad vykreslování podmnožiny komponent naleznete v následujících součástech v [ `Virtualization` ukázkové aplikaci (ASPNET/Samples úložiště GitHub)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="3c231-137">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="3c231-138">`Virtualize` Component ( [`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs) ): komponenta napsaná v jazyce C#, která implementuje <xref:Microsoft.AspNetCore.Components.ComponentBase> pro vykreslení sady datových řádků počasí na základě posouvání uživatele.</span><span class="sxs-lookup"><span data-stu-id="3c231-138">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="3c231-139">`FetchData` Component ( [`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor) ): používá `Virtualize` komponentu k zobrazení 25 řádků dat počasí současně.</span><span class="sxs-lookup"><span data-stu-id="3c231-139">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

::: moniker-end

## <a name="state-changes"></a><span data-ttu-id="3c231-140">Změny stavu</span><span class="sxs-lookup"><span data-stu-id="3c231-140">State changes</span></span>

<span data-ttu-id="3c231-141">Při provádění změn v položkách vygenerovaných `Virtualize` komponentou zavolejte <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> k vynucení opakovaného vyhodnocení a opětovného vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="3c231-141">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
