---
title: Pomocná značka komponenty v ASP.NET Core
author: guardrex
ms.author: riande
description: Naučte se používat ASP.NET Core pomocníka značek komponenty k vykreslování Razor komponent na stránkách a v zobrazeních.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4e003e5ed5e7863d8a218c0f02bb37e214e31910
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773926"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="3dd29-103">Pomocná značka komponenty v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3dd29-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="3dd29-104">Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3dd29-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3dd29-105">Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte [pomocníka značek komponenty](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="3dd29-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3dd29-106">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3dd29-106">Prerequisites</span></span>

<span data-ttu-id="3dd29-107">Postupujte podle pokynů v části *Příprava aplikace na používání součástí na stránkách a pohledech v* <xref:blazor/integrate-components#prepare-the-app> článku.</span><span class="sxs-lookup"><span data-stu-id="3dd29-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="3dd29-108">Pomocník značek komponenty</span><span class="sxs-lookup"><span data-stu-id="3dd29-108">Component Tag Helper</span></span>

<span data-ttu-id="3dd29-109">Následující pomocník značek komponent vykresluje `Counter` komponentu na stránce nebo zobrazení:</span><span class="sxs-lookup"><span data-stu-id="3dd29-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="3dd29-110">Předchozí příklad předpokládá, že je `Counter` komponenta ve složce *stránky* aplikace.</span><span class="sxs-lookup"><span data-stu-id="3dd29-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="3dd29-111">Pomocný objekt tag komponenty může také předat parametry komponentám.</span><span class="sxs-lookup"><span data-stu-id="3dd29-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="3dd29-112">Vezměte v úvahu `ColorfulCheckbox` následující komponentu, která nastaví barvu a velikost popisku zaškrtávacího políčka:</span><span class="sxs-lookup"><span data-stu-id="3dd29-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="3dd29-113">`Size` [Parametry komponenty](xref:blazor/components#component-parameters) (`int`) `Color` a`string`() lze nastavit pomocí pomocníka značky komponenty:</span><span class="sxs-lookup"><span data-stu-id="3dd29-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="3dd29-114">Předchozí příklad předpokládá, že je `ColorfulCheckbox` komponenta ve *sdílené* složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="3dd29-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="3dd29-115">Na stránce nebo zobrazení se vykreslí následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="3dd29-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="3dd29-116">Předání řetězce v uvozovkách vyžaduje [explicitní výraz Razor](xref:mvc/views/razor#explicit-razor-expressions), jak je znázorněno `param-Color` v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="3dd29-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="3dd29-117">Chování analýzy Razor pro hodnotu `string` typu se nevztahuje na `param-*` atribut, protože atribut je `object` typu.</span><span class="sxs-lookup"><span data-stu-id="3dd29-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="3dd29-118">Typ parametru musí být serializovatelný jako JSON, což obvykle znamená, že typ musí mít výchozí konstruktor a nastavitelné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3dd29-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="3dd29-119">Například můžete `Size` zadat hodnotu pro a `Color` v předchozím příkladu, protože typy `Size` a `Color` jsou primitivní typy (`int` a `string`), které jsou podporovány serializátorem JSON.</span><span class="sxs-lookup"><span data-stu-id="3dd29-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="3dd29-120">V následujícím příkladu je objekt třídy předán do komponenty:</span><span class="sxs-lookup"><span data-stu-id="3dd29-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="3dd29-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="3dd29-121">*MyClass.cs*:</span></span>

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

<span data-ttu-id="3dd29-122">**Třída musí mít veřejný konstruktor bez parametrů.**</span><span class="sxs-lookup"><span data-stu-id="3dd29-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="3dd29-123">*Shared/MyComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="3dd29-123">*Shared/MyComponent.razor*:</span></span>

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

<span data-ttu-id="3dd29-124">*Pages/MyPage. cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3dd29-124">*Pages/MyPage.cshtml*:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

<span data-ttu-id="3dd29-125">Předchozí příklad předpokládá, že je `MyComponent` komponenta ve *sdílené* složce aplikace.</span><span class="sxs-lookup"><span data-stu-id="3dd29-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="3dd29-126">`MyClass`je v oboru názvů aplikace (`{APP ASSEMBLY}`).</span><span class="sxs-lookup"><span data-stu-id="3dd29-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="3dd29-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>nakonfiguruje, jestli součást:</span><span class="sxs-lookup"><span data-stu-id="3dd29-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="3dd29-128">Je předem vykreslen na stránku.</span><span class="sxs-lookup"><span data-stu-id="3dd29-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="3dd29-129">Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="3dd29-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="3dd29-130">Režim vykreslování</span><span class="sxs-lookup"><span data-stu-id="3dd29-130">Render Mode</span></span> | <span data-ttu-id="3dd29-131">Popis</span><span class="sxs-lookup"><span data-stu-id="3dd29-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="3dd29-132">Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3dd29-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="3dd29-133">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="3dd29-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="3dd29-134">Vykreslí značku pro Blazor serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3dd29-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="3dd29-135">Výstup komponenty není zahrnutý.</span><span class="sxs-lookup"><span data-stu-id="3dd29-135">Output from the component isn't included.</span></span> <span data-ttu-id="3dd29-136">Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace.</span><span class="sxs-lookup"><span data-stu-id="3dd29-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="3dd29-137">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="3dd29-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="3dd29-138">I když stránky a zobrazení mohou používat komponenty, není tato konverzace pravdivá.</span><span class="sxs-lookup"><span data-stu-id="3dd29-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="3dd29-139">Komponenty nemůžou používat funkce pro zobrazení a stránky, jako jsou například částečná zobrazení a oddíly.</span><span class="sxs-lookup"><span data-stu-id="3dd29-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="3dd29-140">Chcete-li použít logiku ze částečného zobrazení v komponentě, rozložte logiku částečného zobrazení do komponenty.</span><span class="sxs-lookup"><span data-stu-id="3dd29-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="3dd29-141">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="3dd29-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3dd29-142">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3dd29-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
