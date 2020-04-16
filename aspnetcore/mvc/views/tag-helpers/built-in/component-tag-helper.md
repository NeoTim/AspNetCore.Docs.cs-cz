---
title: Pomocník s tagy komponenty v ASP.NET jádru
author: guardrex
ms.author: riande
description: Přečtěte si, jak pomocí pomocníka ASP.NET základní hořce a označení komponent k vykreslení komponent Razor na stránkách a v zobrazeních.
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: aaa4b92a8912b4f52d861ed07432aa7cf3ca5240
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440958"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="3acaa-103">Pomocník s tagy komponenty v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="3acaa-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="3acaa-104">[Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3acaa-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3acaa-105">Chcete-li vykreslit komponentu ze stránky nebo ze zobrazení, použijte [pomocníka pro označení součásti](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="3acaa-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3acaa-106">Požadavky</span><span class="sxs-lookup"><span data-stu-id="3acaa-106">Prerequisites</span></span>

<span data-ttu-id="3acaa-107">Postupujte podle pokynů v *připravte aplikaci na použití komponent na stránkách a zobrazení* části <xref:blazor/integrate-components#prepare-the-app> článku.</span><span class="sxs-lookup"><span data-stu-id="3acaa-107">Follow the guidance in the *Prepare the app to use components in pages and views* section of the <xref:blazor/integrate-components#prepare-the-app> article.</span></span>

## <a name="component-tag-helper"></a><span data-ttu-id="3acaa-108">Pomocník pro označení komponenty</span><span class="sxs-lookup"><span data-stu-id="3acaa-108">Component Tag Helper</span></span>

<span data-ttu-id="3acaa-109">Následující pomocník s značkami `Counter` komponent vykreslí komponentu na stránce nebo v zobrazení:</span><span class="sxs-lookup"><span data-stu-id="3acaa-109">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="3acaa-110">Předchozí příklad předpokládá, že `Counter` komponenta je ve složce *Stránky* aplikace.</span><span class="sxs-lookup"><span data-stu-id="3acaa-110">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span>

<span data-ttu-id="3acaa-111">Pomocník s tagy komponenty může také předat parametry součástem.</span><span class="sxs-lookup"><span data-stu-id="3acaa-111">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="3acaa-112">Vezměte v `ColorfulCheckbox` úvahu následující součást, která nastavuje barvu a velikost popisku zaškrtávacího políčka:</span><span class="sxs-lookup"><span data-stu-id="3acaa-112">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="3acaa-113">`Size` `int` [Parametry komponenty](xref:blazor/components#component-parameters) ( ) a `Color` (`string`) lze nastavit pomocí pomocníka pro značku součásti:</span><span class="sxs-lookup"><span data-stu-id="3acaa-113">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="3acaa-114">Předchozí příklad předpokládá, že `ColorfulCheckbox` komponenta je ve složce *Shared* aplikace.</span><span class="sxs-lookup"><span data-stu-id="3acaa-114">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span>

<span data-ttu-id="3acaa-115">Na stránce nebo v zobrazení se vykresluje následující kód HTML:</span><span class="sxs-lookup"><span data-stu-id="3acaa-115">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="3acaa-116">Předání řetězce v uvozovkách vyžaduje explicitní `param-Color` výraz [Razor](xref:mvc/views/razor#explicit-razor-expressions), jak je znázorněno v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="3acaa-116">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="3acaa-117">Chování analýzy holicího `string` strojku pro hodnotu `param-*` typu se nevztahuje na atribut, protože atribut je `object` typ.</span><span class="sxs-lookup"><span data-stu-id="3acaa-117">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="3acaa-118">Typ parametru musí být JSON serializovatelný, což obvykle znamená, že typ musí mít výchozí konstruktor a nastavitelné vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="3acaa-118">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="3acaa-119">Můžete například zadat hodnotu `Size` `Color` pro a v předchozím příkladu, protože`int` typy `string` `Size` a `Color` jsou primitivní typy ( a ), které jsou podporovány serializátorem JSON.</span><span class="sxs-lookup"><span data-stu-id="3acaa-119">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="3acaa-120">V následujícím příkladu je objekt třídy předán komponentě:</span><span class="sxs-lookup"><span data-stu-id="3acaa-120">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="3acaa-121">*MyClass.cs*:</span><span class="sxs-lookup"><span data-stu-id="3acaa-121">*MyClass.cs*:</span></span>

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

<span data-ttu-id="3acaa-122">**Třída musí mít veřejný konstruktor bez parametrů.**</span><span class="sxs-lookup"><span data-stu-id="3acaa-122">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="3acaa-123">*Shared/MyComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="3acaa-123">*Shared/MyComponent.razor*:</span></span>

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

<span data-ttu-id="3acaa-124">*Stránky/MyPage.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3acaa-124">*Pages/MyPage.cshtml*:</span></span>

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

<span data-ttu-id="3acaa-125">Předchozí příklad předpokládá, že `MyComponent` komponenta je ve složce *Shared* aplikace.</span><span class="sxs-lookup"><span data-stu-id="3acaa-125">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="3acaa-126">`MyClass`je v oboru názvů aplikace`{APP ASSEMBLY}`( ).</span><span class="sxs-lookup"><span data-stu-id="3acaa-126">`MyClass` is in the app's namespace (`{APP ASSEMBLY}`).</span></span>

<span data-ttu-id="3acaa-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>konfiguruje, zda součást:</span><span class="sxs-lookup"><span data-stu-id="3acaa-127"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="3acaa-128">Je prerendered do stránky.</span><span class="sxs-lookup"><span data-stu-id="3acaa-128">Is prerendered into the page.</span></span>
* <span data-ttu-id="3acaa-129">Je vykreslen jako statické HTML na stránce, nebo pokud obsahuje potřebné informace k bootstrap aplikace Blazor od uživatelského agenta.</span><span class="sxs-lookup"><span data-stu-id="3acaa-129">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="3acaa-130">Režim vykreslení</span><span class="sxs-lookup"><span data-stu-id="3acaa-130">Render Mode</span></span> | <span data-ttu-id="3acaa-131">Popis</span><span class="sxs-lookup"><span data-stu-id="3acaa-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="3acaa-132">Vykreslí komponentu do statického KÓDU Blazor HTML a obsahuje značku pro serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3acaa-132">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="3acaa-133">Při spuštění uživatelského agenta se tato značka Blazor používá k zavádění aplikace.</span><span class="sxs-lookup"><span data-stu-id="3acaa-133">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="3acaa-134">Vykreslí značku Blazor pro serverovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3acaa-134">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="3acaa-135">Výstup z komponenty není zahrnut.</span><span class="sxs-lookup"><span data-stu-id="3acaa-135">Output from the component isn't included.</span></span> <span data-ttu-id="3acaa-136">Při spuštění uživatelského agenta se tato značka Blazor používá k zavádění aplikace.</span><span class="sxs-lookup"><span data-stu-id="3acaa-136">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="3acaa-137">Vykreslí komponentu do statického HTML.</span><span class="sxs-lookup"><span data-stu-id="3acaa-137">Renders the component into static HTML.</span></span> |

<span data-ttu-id="3acaa-138">Zatímco stránky a zobrazení mohou používat komponenty, konverzace není pravdivá.</span><span class="sxs-lookup"><span data-stu-id="3acaa-138">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="3acaa-139">Součásti nemohou používat funkce specifické pro zobrazení a stránku, jako jsou částečná zobrazení a oddíly.</span><span class="sxs-lookup"><span data-stu-id="3acaa-139">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="3acaa-140">Chcete-li použít logiku z částečného pohledu v komponentě, faktor out částečné zobrazení logiky do komponenty.</span><span class="sxs-lookup"><span data-stu-id="3acaa-140">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="3acaa-141">Vykreslování součástí serveru ze statické stránky HTML není podporováno.</span><span class="sxs-lookup"><span data-stu-id="3acaa-141">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3acaa-142">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3acaa-142">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
