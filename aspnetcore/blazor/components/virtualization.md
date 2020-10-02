---
title: BlazorVirtualizace ASP.NET Core komponent
author: guardrex
description: Naučte se používat virtualizaci komponent v Blazor aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
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
ms.openlocfilehash: 2ba698eaba23fd67617375e5186d40d45d9772fd
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653877"
---
# <a name="aspnet-core-no-locblazor-component-virtualization"></a><span data-ttu-id="b7932-103">BlazorVirtualizace ASP.NET Core komponent</span><span class="sxs-lookup"><span data-stu-id="b7932-103">ASP.NET Core Blazor component virtualization</span></span>

<span data-ttu-id="b7932-104">Od [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b7932-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="b7932-105">Vylepšete vnímaný výkon vykreslování komponent pomocí Blazor integrované podpory virtualizace architektury.</span><span class="sxs-lookup"><span data-stu-id="b7932-105">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="b7932-106">Virtualizace je technika, jak omezit vykreslování uživatelského rozhraní pouze na části, které jsou aktuálně viditelné.</span><span class="sxs-lookup"><span data-stu-id="b7932-106">Virtualization is a technique for limiting UI rendering to just the parts that are currently visible.</span></span> <span data-ttu-id="b7932-107">Například virtualizace je užitečná v případě, že aplikace musí vykreslovat dlouhý seznam položek a v libovolnou dobu je nutné zobrazit pouze podmnožinu položek.</span><span class="sxs-lookup"><span data-stu-id="b7932-107">For example, virtualization is helpful when the app must render a long list of items and only a subset of items is required to be visible at any given time.</span></span> <span data-ttu-id="b7932-108">Blazor poskytuje `Virtualize` komponentu, která se dá použít k přidání virtualizace do komponent aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7932-108">Blazor provides the `Virtualize` component that can be used to add virtualization to an app's components.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b7932-109">Bez virtualizace může běžný seznam použít [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) smyčku jazyka C# k vykreslení každé položky v seznamu:</span><span class="sxs-lookup"><span data-stu-id="b7932-109">Without virtualization, a typical list might use a C# [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop to render each item in the list:</span></span>

```razor
@foreach (var employee in employees)
{
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
}
```

<span data-ttu-id="b7932-110">Pokud seznam obsahuje tisíce položek, pak vykreslování seznamu může trvat dlouhou dobu.</span><span class="sxs-lookup"><span data-stu-id="b7932-110">If the list contains thousands of items, then rendering the list may take a long time.</span></span> <span data-ttu-id="b7932-111">Uživatel může vyskytnout znatelné prodlevy uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b7932-111">The user may experience a noticeable UI lag.</span></span>

<span data-ttu-id="b7932-112">Místo vykreslování každé položky v seznamu najednou, nahraďte [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) smyčku `Virtualize` součástí a určete zdroj pevné položky `Items` .</span><span class="sxs-lookup"><span data-stu-id="b7932-112">Instead of rendering each item in the list all at one time, replace the [`foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop with the `Virtualize` component and specify a fixed item source with `Items`.</span></span> <span data-ttu-id="b7932-113">Vykresleny jsou pouze položky, které jsou aktuálně viditelné:</span><span class="sxs-lookup"><span data-stu-id="b7932-113">Only the items that are currently visible are rendered:</span></span>

```razor
<Virtualize Context="employee" Items="@employees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="b7932-114">Pokud není zadán kontext pro komponentu s `Context` , použijte `context` hodnotu ( `@context.{PROPERTY}` ) v šabloně obsahu položky:</span><span class="sxs-lookup"><span data-stu-id="b7932-114">If not specifying a context to the component with `Context`, use the `context` value (`@context.{PROPERTY}`) in the item content template:</span></span>

```razor
<Virtualize Items="@employees">
    <p>
        @context.FirstName @context.LastName has the 
        job title of @context.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="b7932-115">`Virtualize`Komponenta vypočítá počet položek, které se mají vykreslit, na základě výšky kontejneru a velikosti vykreslených položek.</span><span class="sxs-lookup"><span data-stu-id="b7932-115">The `Virtualize` component calculates how many items to render based on the height of the container and the size of the rendered items.</span></span>

<span data-ttu-id="b7932-116">Obsah položky pro `Virtualize` součást může zahrnovat:</span><span class="sxs-lookup"><span data-stu-id="b7932-116">The item content for the `Virtualize` component can include:</span></span>

* <span data-ttu-id="b7932-117">Prostý kód HTML a Razor kód, jak ukazuje předchozí příklad.</span><span class="sxs-lookup"><span data-stu-id="b7932-117">Plain HTML and Razor code, as the preceding example shows.</span></span>
* <span data-ttu-id="b7932-118">Jedna nebo více Razor součástí.</span><span class="sxs-lookup"><span data-stu-id="b7932-118">One or more Razor components.</span></span>
* <span data-ttu-id="b7932-119">Kombinace HTML/ Razor a Razor komponent.</span><span class="sxs-lookup"><span data-stu-id="b7932-119">A mix of HTML/Razor and Razor components.</span></span>

## <a name="item-provider-delegate"></a><span data-ttu-id="b7932-120">Delegát poskytovatele položek</span><span class="sxs-lookup"><span data-stu-id="b7932-120">Item provider delegate</span></span>

<span data-ttu-id="b7932-121">Pokud nechcete načíst všechny položky do paměti, můžete určit metodu delegáta poskytovatele položek pro `ItemsProvider` parametr komponenty, který asynchronně načte požadované položky na vyžádání:</span><span class="sxs-lookup"><span data-stu-id="b7932-121">If you don't want to load all of the items into memory, you can specify an items provider delegate method to the component's `ItemsProvider` parameter that asynchronously retrieves the requested items on demand:</span></span>

```razor
<Virtualize Context="employee" ItemsProvider="@LoadEmployees">
    <p>
        @employee.FirstName @employee.LastName has the 
        job title of @employee.JobTitle.
    </p>
</Virtualize>
```

<span data-ttu-id="b7932-122">Poskytovatel položek obdrží `ItemsProviderRequest` , který určuje požadovaný počet položek od určitého počátečního indexu.</span><span class="sxs-lookup"><span data-stu-id="b7932-122">The items provider receives an `ItemsProviderRequest`, which specifies the required number of items starting at a specific start index.</span></span> <span data-ttu-id="b7932-123">Poskytovatel položek následně načte požadované položky z databáze nebo jiné služby a vrátí je jako výsledek `ItemsProviderResult<TItem>` s počtem položek celkem.</span><span class="sxs-lookup"><span data-stu-id="b7932-123">The items provider then retrieves the requested items from a database or other service and returns them as an `ItemsProviderResult<TItem>` along with a count of the total items.</span></span> <span data-ttu-id="b7932-124">Poskytovatel položek se může rozhodnout načíst položky s každou žádostí nebo je Uložit do mezipaměti, aby byly snadno dostupné.</span><span class="sxs-lookup"><span data-stu-id="b7932-124">The items provider can choose to retrieve the items with each request or cache them so that they're readily available.</span></span> <span data-ttu-id="b7932-125">Nepokoušejte se použít poskytovatele položek a přiřadit kolekci ke `Items` stejné `Virtualize` komponentě.</span><span class="sxs-lookup"><span data-stu-id="b7932-125">Don't attempt to use an items provider and assign a collection to `Items` for the same `Virtualize` component.</span></span>

<span data-ttu-id="b7932-126">Následující příklad načte zaměstnance z `EmployeeService` :</span><span class="sxs-lookup"><span data-stu-id="b7932-126">The following example loads employees from an `EmployeeService`:</span></span>

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

## <a name="placeholder"></a><span data-ttu-id="b7932-127">Zástupný symbol</span><span class="sxs-lookup"><span data-stu-id="b7932-127">Placeholder</span></span>

<span data-ttu-id="b7932-128">Vzhledem k tomu, že požadavek na položky ze vzdáleného zdroje dat může nějakou dobu trvat, máte možnost vykreslit zástupný symbol ( `<Placeholder>...</Placeholder>` ), dokud nebudou k dispozici data položky:</span><span class="sxs-lookup"><span data-stu-id="b7932-128">Because requesting items from a remote data source might take some time, you have the option to render a placeholder (`<Placeholder>...</Placeholder>`) until the item data is available:</span></span>

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

## <a name="item-size"></a><span data-ttu-id="b7932-129">Velikost položky</span><span class="sxs-lookup"><span data-stu-id="b7932-129">Item size</span></span>

<span data-ttu-id="b7932-130">Velikost každé položky v pixelech lze nastavit s hodnotou `ItemSize` (výchozí: 50px):</span><span class="sxs-lookup"><span data-stu-id="b7932-130">The size of each item in pixels can be set with `ItemSize` (default: 50px):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" ItemSize="25">
    ...
</Virtualize>
```

## <a name="overscan-count"></a><span data-ttu-id="b7932-131">Počet překontrol</span><span class="sxs-lookup"><span data-stu-id="b7932-131">Overscan count</span></span>

<span data-ttu-id="b7932-132">`OverscanCount` Určuje, kolik dalších položek je vykresleno před a po viditelné oblasti.</span><span class="sxs-lookup"><span data-stu-id="b7932-132">`OverscanCount` determines how many additional items are rendered before and after the visible region.</span></span> <span data-ttu-id="b7932-133">Toto nastavení pomáhá snižovat četnost vykreslování během posouvání.</span><span class="sxs-lookup"><span data-stu-id="b7932-133">This setting helps to reduce the frequency of rendering during scrolling.</span></span> <span data-ttu-id="b7932-134">Výsledkem ale vyšších hodnot je více prvků vykreslených na stránce (výchozí: 3):</span><span class="sxs-lookup"><span data-stu-id="b7932-134">However, higher values result in more elements rendered in the page (default: 3):</span></span>

```razor
<Virtualize Context="employee" Items="@employees" OverscanCount="4">
    ...
</Virtualize>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b7932-135">Například mřížka nebo seznam, který vykresluje stovky řádků obsahujících komponenty, je náročné na vykreslování procesoru.</span><span class="sxs-lookup"><span data-stu-id="b7932-135">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="b7932-136">Zvažte možnost Virtualizovat rozložení mřížky nebo seznamu tak, aby se v určitou dobu vykreslila pouze podmnožina komponent.</span><span class="sxs-lookup"><span data-stu-id="b7932-136">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span>

<span data-ttu-id="b7932-137">Následující `Virtualize` Komponenta ( `Virtualize.cs` ) implementuje <xref:Microsoft.AspNetCore.Components.ComponentBase> k vykreslení podřízeného obsahu na základě posouvání uživatele:</span><span class="sxs-lookup"><span data-stu-id="b7932-137">The following `Virtualize` component (`Virtualize.cs`) implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render child content based on user scrolling:</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Rendering;
using Microsoft.JSInterop;

public class Virtualize<TItem> : ComponentBase
{
    [Parameter]
    public string TagName { get; set; } = "div";

    [Parameter]
    public RenderFragment<TItem> ChildContent { get; set; }

    [Parameter]
    public ICollection<TItem> Items { get; set; }

    [Parameter]
    public double ItemHeight { get; set; }

    [Parameter(CaptureUnmatchedValues = true)] 
    public Dictionary<string, object> Attributes { get; set; }

    [Inject]
    IJSRuntime JS { get; set; }

    ElementReference contentElement;
    int numItemsToSkipBefore;
    int numItemsToShow;

    protected override void BuildRenderTree(RenderTreeBuilder builder)
    {
        // Render actual content
        builder.OpenElement(0, TagName);
        builder.AddMultipleAttributes(1, Attributes);

        var translateY = numItemsToSkipBefore * ItemHeight;
        builder.AddAttribute(2, "style", $"transform: translateY({ translateY }px);");
        builder.AddAttribute(2, "data-translateY", translateY);
        builder.AddElementReferenceCapture(3, @ref => { contentElement = @ref; });

        // As an important optimization, *don't* use builder.AddContent(seq, ChildContent, item)
        // because that implicitly wraps a new region around each item, which in turn means that 
        // @key does nothing (because keys are scoped to regions). Instead, create a single 
        // container region and then invoke the fragments directly.

        builder.OpenRegion(4);

        foreach (var item in Items.Skip(numItemsToSkipBefore).Take(numItemsToShow))
        {
            ChildContent(item)(builder);
        }

        builder.CloseRegion();

        builder.CloseElement();

        // Also emit a spacer that causes the total vertical height to add up to 
        // Items.Count()*numItems

        builder.OpenElement(5, "div");
        var numHiddenItems = Items.Count - numItemsToShow;
        builder.AddAttribute(6, "style", 
            $"width: 1px; height: { numHiddenItems * ItemHeight }px;");
        builder.CloseElement();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            var objectRef = DotNetObjectReference.Create(this);
            var initResult = await JS.InvokeAsync<ScrollEventArgs>(
                "VirtualizedComponent._initialize", objectRef, contentElement);
            OnScroll(initResult);
        }
    }

    [JSInvokable]
    public void OnScroll(ScrollEventArgs args)
    {
        var relativeTop = args.ContainerRect.Top - args.ContentRect.Top;
        numItemsToSkipBefore = Math.Max(0, (int)(relativeTop / ItemHeight));

        var visibleHeight = args.ContainerRect.Bottom - 
            (args.ContentRect.Top + numItemsToSkipBefore * ItemHeight);
        numItemsToShow = (int)Math.Ceiling(visibleHeight / ItemHeight) + 3;

        StateHasChanged();
    }

    public class ScrollEventArgs
    {
        public DOMRect ContainerRect { get; set; }
        public DOMRect ContentRect { get; set; }
    }

    public class DOMRect
    {
        public double Top { get; set; }
        public double Bottom { get; set; }
        public double Left { get; set; } 
        public double Right { get; set; }
        public double Width { get; set; }
        public double Height { get; set; }
    }
}
```

<span data-ttu-id="b7932-138">Následující `FetchData` součást ( `FetchData.razor` ) používá předchozí `Virtualize` komponentu k zobrazení 25 řádků dat o počasí v čase:</span><span class="sxs-lookup"><span data-stu-id="b7932-138">The following `FetchData` component (`FetchData.razor`) uses the preceding `Virtualize` component to display 25 rows of weather data at a time:</span></span>

```razor
@page "/"
@page "/fetchdata"
@inject HttpClient Http

<h1>Weather forecast</h1>

<p>This component demonstrates fetching data from a service.</p>

@if (forecasts == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h2>Using <code>table-layout: fixed</code></h2>
    <div style="height:300px; overflow-y: auto;">
        <Virtualize ItemHeight="25" Items="@forecasts">
            <tr @key="@context.GetHashCode()" 
                    style="display: table; table-layout: fixed; width: 100%;">
                <td>@context.Date.ToShortDateString()</td>
                <td>@context.TemperatureC</td>
                <td>@context.TemperatureF</td>
                <td>@context.Summary</td>
            </tr>
        </Virtualize>
    </div>

    <h2>Using <code>position: sticky</code></h2>
    <div style="height: 300px; overflow-y: auto; position: relative;">
        <table>
            <thead class="sticky">
                <tr>
                    <th>Date</th>
                    <th>Temperature (C)</th>
                    <th>Temperature (F)</th>
                    <th>Summary</th>
                </tr>
            </thead>

            <Virtualize TagName="tbody" ItemHeight="25" Items="@forecasts">
                <tr @key="@context.GetHashCode()">
                    <td>@context.Date.ToShortDateString()</td>
                    <td>@context.TemperatureC</td>
                    <td>@context.TemperatureF</td>
                    <td>@context.Summary</td>
                </tr>
            </Virtualize>
        </table>
    </div>

    <style type="text/css">
        thead.sticky th {
            position: sticky;
            top: 0;
        }
        tr td {
            height: 25px;
        }
    </style>
}

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>(
            "sample-data/weather.json");
    }

    public class WeatherForecast
    {
        public DateTime Date { get; set; }

        public int TemperatureC { get; set; }

        public string Summary { get; set; }

        public int TemperatureF => 32 + (int)(TemperatureC / 0.5556);
    }
}
```

<span data-ttu-id="b7932-139">V předchozím příkladu je přístup k zamezení absolutního umístění pro každou jednotlivou položku.</span><span class="sxs-lookup"><span data-stu-id="b7932-139">In the preceding example, the approach is about avoiding absolute positioning for each individual item.</span></span> <span data-ttu-id="b7932-140">Absolutní umístění by mělo mít nějaké výhody (můžeme mít jistotu, že položky zabírají zadané množství prostoru Y), ale má chybnou nevýhodu, že ztratíte normální šířky a nemůžete při určování velikosti obsahu získat sloupce tabulky, které by mohly být v řádcích nebo v hlavičce.</span><span class="sxs-lookup"><span data-stu-id="b7932-140">Absolute positioning would have some advantages (we can be sure the items do take up the specified amount of Y space) but has the bad disadvantage that you lose the normal widths and can't get table columns to line up across rows/header when based on content sizing.</span></span>

<span data-ttu-id="b7932-141">Koncept za návrhem `Virtualize` součásti je v tom, že komponenta nemění způsob, jakým jsou položky rozloženy v modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="b7932-141">The concept behind the design of the `Virtualize` component is that the component doesn't change how the items are laid out within the DOM.</span></span> <span data-ttu-id="b7932-142">Neexistují žádné přidané prvky obálky, kromě jednoho, jehož `TagName` zadání zadáte.</span><span class="sxs-lookup"><span data-stu-id="b7932-142">There are no added wrapper elements, besides the single one whose `TagName` you specify.</span></span>

<span data-ttu-id="b7932-143">Nejlepším řešením je vyhnout se tomu i `TagName` Obálkový prvek.</span><span class="sxs-lookup"><span data-stu-id="b7932-143">The best approach is to avoid even the `TagName` wrapper element.</span></span> <span data-ttu-id="b7932-144">`Virtualize`Komponenta negeneruje žádné vlastní prvky.</span><span class="sxs-lookup"><span data-stu-id="b7932-144">Have the `Virtualize` component emit no elements of its own.</span></span> <span data-ttu-id="b7932-145">Všechny komponenty jsou vykresleny, ale mnoho instancí šablon je nutných k vyplnění zbývajícího viditelného místa v nejbližším rolovacím předchůdci, a navíc přidejte následující oddělovací element, který umožňuje, aby posuvný předchůdce měl správný rozsah posouvání.</span><span class="sxs-lookup"><span data-stu-id="b7932-145">All the component does is render however many of the template instances are required to fill up any remaining visible space in the closest scrollable ancestor, plus add a following spacer element that makes the scrollable ancestor have the right scrolling range.</span></span> <span data-ttu-id="b7932-146">Vzhledem k tomu, že se jedná o příslušné rozložení, je stejný jako v případě, že plný rozsah podřízených objektů byl fyzicky v modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="b7932-146">As far as the layout is concerned, it's the same as if the full range of children were physically in the DOM.</span></span> <span data-ttu-id="b7932-147">Vyžaduje, abyste zadali přesné, `ItemHeight` ale.</span><span class="sxs-lookup"><span data-stu-id="b7932-147">It does require you to specify an accurate `ItemHeight` though.</span></span> <span data-ttu-id="b7932-148">Pokud se to nepovede (například proto, že zaznamenáte, že jste si zanechali možnost zadat `style.height` na a `<tr>` ), ukončí součást vykreslování špatného počtu instancí šablon a buď neplní uživatelské rozhraní, nebo neefektivně vykreslí příliš mnoho.</span><span class="sxs-lookup"><span data-stu-id="b7932-148">If you get it wrong (for example, because you're confused and think it's valid to specify `style.height` on a `<tr>`), then the component ends up rendering the wrong number of template instances and either not fill up the UI or inefficiently render too many.</span></span> <span data-ttu-id="b7932-149">Navíc se rozsah posunutí na nadřazeném prvku nezobrazí správně.</span><span class="sxs-lookup"><span data-stu-id="b7932-149">Additionally, the scroll range on the parent won't be correct.</span></span>

::: moniker-end

## <a name="state-changes"></a><span data-ttu-id="b7932-150">Změny stavu</span><span class="sxs-lookup"><span data-stu-id="b7932-150">State changes</span></span>

<span data-ttu-id="b7932-151">Při provádění změn v položkách vygenerovaných `Virtualize` komponentou zavolejte <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> k vynucení opakovaného vyhodnocení a opětovného vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="b7932-151">When making changes to items rendered by the `Virtualize` component, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to force re-evaluation and rerendering of the component.</span></span>
