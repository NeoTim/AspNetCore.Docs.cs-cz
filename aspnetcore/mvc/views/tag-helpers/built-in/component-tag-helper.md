---
title: Pomocná značka komponenty v ASP.NET Core
author: guardrex
ms.author: riande
description: Naučte se, jak pomocí pomocníka značek komponent ASP.NET Core vykreslovat komponenty Razor na stránkách a v zobrazeních.
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 801ceb73de5bb4ef7500624e1fbddbf96d1ab89c
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80226394"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="0b0df-103">Pomocná značka komponenty v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0b0df-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="0b0df-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0b0df-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0b0df-105">Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte [pomocníka značek komponenty](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="0b0df-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

<span data-ttu-id="0b0df-106">Následující pomocník značek komponenty vykreslí `Counter` komponentu na stránce nebo zobrazení:</span><span class="sxs-lookup"><span data-stu-id="0b0df-106">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="0b0df-107">Pomocný objekt tag komponenty může také předat parametry komponentám.</span><span class="sxs-lookup"><span data-stu-id="0b0df-107">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="0b0df-108">Vezměte v úvahu následující `ColorfulCheckbox` komponenty, která nastaví barvu a velikost popisku zaškrtávacího políčka:</span><span class="sxs-lookup"><span data-stu-id="0b0df-108">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="0b0df-109">[Parametry komponenty](xref:blazor/components#component-parameters) `Size` (`int`) a `Color` (`string`) lze nastavit pomocí pomocníka značky komponenty:</span><span class="sxs-lookup"><span data-stu-id="0b0df-109">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="0b0df-110">Na stránce nebo zobrazení se vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="0b0df-110">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="0b0df-111">Předání řetězce v uvozovkách vyžaduje [explicitní výraz Razor](xref:mvc/views/razor#explicit-razor-expressions), jak je znázorněno v předchozím příkladu `param-Color`.</span><span class="sxs-lookup"><span data-stu-id="0b0df-111">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="0b0df-112">Chování analýzy Razor pro hodnotu typu `string` se nevztahuje na atribut `param-*`, protože atribut je `object` typ.</span><span class="sxs-lookup"><span data-stu-id="0b0df-112">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="0b0df-113">Typ parametru musí být serializovatelný jako JSON, což obvykle znamená, že typ musí mít výchozí konstruktor a nastavitelné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="0b0df-113">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="0b0df-114">Například můžete zadat hodnotu pro `Size` a `Color` v předchozím příkladu, protože typy `Size` a `Color` jsou primitivní typy (`int` a `string`), které jsou podporovány serializátorem JSON.</span><span class="sxs-lookup"><span data-stu-id="0b0df-114">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="0b0df-115"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="0b0df-115"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="0b0df-116">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="0b0df-116">Is prerendered into the page.</span></span>
* <span data-ttu-id="0b0df-117">Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="0b0df-117">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="0b0df-118">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="0b0df-118">Render Mode</span></span> | <span data-ttu-id="0b0df-119">Popis</span><span class="sxs-lookup"><span data-stu-id="0b0df-119">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="0b0df-120">Vykreslí komponentu do statického HTML a obsahuje značku pro aplikaci Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="0b0df-120">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="0b0df-121">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="0b0df-121">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="0b0df-122">Vykreslí značku pro aplikaci Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="0b0df-122">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="0b0df-123">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="0b0df-123">Output from the component isn't included.</span></span> <span data-ttu-id="0b0df-124">Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="0b0df-124">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="0b0df-125">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="0b0df-125">Renders the component into static HTML.</span></span> |

<span data-ttu-id="0b0df-126">I když stránky a zobrazení mohou používat komponenty, není tato konverzace pravdivá.</span><span class="sxs-lookup"><span data-stu-id="0b0df-126">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="0b0df-127">Komponenty nemůžou používat funkce pro zobrazení a stránky, jako jsou například částečná zobrazení a oddíly.</span><span class="sxs-lookup"><span data-stu-id="0b0df-127">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="0b0df-128">Chcete-li použít logiku ze částečného zobrazení v komponentě, rozložte logiku částečného zobrazení do komponenty.</span><span class="sxs-lookup"><span data-stu-id="0b0df-128">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="0b0df-129">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="0b0df-129">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0b0df-130">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0b0df-130">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
