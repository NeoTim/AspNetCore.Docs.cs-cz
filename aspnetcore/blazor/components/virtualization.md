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

Například mřížka nebo seznam, který vykresluje stovky řádků obsahujících komponenty, je náročné na vykreslování procesoru. Zvažte možnost Virtualizovat rozložení mřížky nebo seznamu tak, aby se v určitou dobu vykreslila pouze podmnožina komponent.

Následující `Virtualize` Komponenta ( `Virtualize.cs` ) implementuje <xref:Microsoft.AspNetCore.Components.ComponentBase> k vykreslení podřízeného obsahu na základě posouvání uživatele:

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

Následující `FetchData` součást ( `FetchData.razor` ) používá předchozí `Virtualize` komponentu k zobrazení 25 řádků dat o počasí v čase:

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

V předchozím příkladu je přístup k zamezení absolutního umístění pro každou jednotlivou položku. Absolutní umístění by mělo mít nějaké výhody (můžeme mít jistotu, že položky zabírají zadané množství prostoru Y), ale má chybnou nevýhodu, že ztratíte normální šířky a nemůžete při určování velikosti obsahu získat sloupce tabulky, které by mohly být v řádcích nebo v hlavičce.

Koncept za návrhem `Virtualize` součásti je v tom, že komponenta nemění způsob, jakým jsou položky rozloženy v modelu DOM. Neexistují žádné přidané prvky obálky, kromě jednoho, jehož `TagName` zadání zadáte.

Nejlepším řešením je vyhnout se tomu i `TagName` Obálkový prvek. `Virtualize`Komponenta negeneruje žádné vlastní prvky. Všechny komponenty jsou vykresleny, ale mnoho instancí šablon je nutných k vyplnění zbývajícího viditelného místa v nejbližším rolovacím předchůdci, a navíc přidejte následující oddělovací element, který umožňuje, aby posuvný předchůdce měl správný rozsah posouvání. Vzhledem k tomu, že se jedná o příslušné rozložení, je stejný jako v případě, že plný rozsah podřízených objektů byl fyzicky v modelu DOM. Vyžaduje, abyste zadali přesné, `ItemHeight` ale. Pokud se to nepovede (například proto, že zaznamenáte, že jste si zanechali možnost zadat `style.height` na a `<tr>` ), ukončí součást vykreslování špatného počtu instancí šablon a buď neplní uživatelské rozhraní, nebo neefektivně vykreslí příliš mnoho. Navíc se rozsah posunutí na nadřazeném prvku nezobrazí správně.

::: moniker-end

## <a name="state-changes"></a>Změny stavu

Při provádění změn v položkách vygenerovaných `Virtualize` komponentou zavolejte <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> k vynucení opakovaného vyhodnocení a opětovného vykreslování komponenty.
