---
title: ASP.NET Core Blazor kaskádové hodnoty a parametry
author: guardrex
description: Naučte se přesměrovat data z komponenty předchůdce na podřízené součásti.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
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
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: 8cb2bd5f19a4cc21671a0b077d4ef0fda2e30455
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628583"
---
# <a name="aspnet-core-no-locblazor-cascading-values-and-parameters"></a><span data-ttu-id="e74a6-103">ASP.NET Core Blazor kaskádové hodnoty a parametry</span><span class="sxs-lookup"><span data-stu-id="e74a6-103">ASP.NET Core Blazor cascading values and parameters</span></span>

<span data-ttu-id="e74a6-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e74a6-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="e74a6-105">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e74a6-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e74a6-106">V některých scénářích je nevhodné přesměrovat data z komponenty předchůdce na dceřinou komponentu pomocí [parametrů komponenty](xref:blazor/components/index#component-parameters), zejména pokud je k dispozici několik vrstev komponent.</span><span class="sxs-lookup"><span data-stu-id="e74a6-106">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](xref:blazor/components/index#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="e74a6-107">Kaskádové hodnoty a parametry řeší tento problém tím, že poskytují pohodlný způsob, jak komponenta předchůdce poskytne hodnotu všem jejím následným součástem.</span><span class="sxs-lookup"><span data-stu-id="e74a6-107">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="e74a6-108">Kaskádové hodnoty a parametry také poskytují přístup ke koordinaci komponent.</span><span class="sxs-lookup"><span data-stu-id="e74a6-108">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="e74a6-109">Příklad motivu</span><span class="sxs-lookup"><span data-stu-id="e74a6-109">Theme example</span></span>

<span data-ttu-id="e74a6-110">V následujícím příkladu z ukázkové aplikace `ThemeInfo` Třída určuje informace o motivu pro přetečení hierarchie komponent tak, aby všechna tlačítka v dané části aplikace sdílela stejný styl.</span><span class="sxs-lookup"><span data-stu-id="e74a6-110">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="e74a6-111">`UIThemeClasses/ThemeInfo.cs`:</span><span class="sxs-lookup"><span data-stu-id="e74a6-111">`UIThemeClasses/ThemeInfo.cs`:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="e74a6-112">Komponenta předchůdce může poskytnout kaskádovou hodnotu pomocí komponenty kaskádová hodnota.</span><span class="sxs-lookup"><span data-stu-id="e74a6-112">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="e74a6-113"><xref:Microsoft.AspNetCore.Components.CascadingValue%601>Komponenta zabalí podstrom hierarchie komponent a poskytne jednu hodnotu všem součástem v rámci daného podstromu.</span><span class="sxs-lookup"><span data-stu-id="e74a6-113">The <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="e74a6-114">Například ukázková aplikace určuje informace o motivu ( `ThemeInfo` ) v jednom z rozložení aplikace jako kaskádový parametr pro všechny komponenty, které tvoří tělo `@Body` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="e74a6-114">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="e74a6-115">`ButtonClass` má přiřazenou hodnotu `btn-success` v součásti rozložení.</span><span class="sxs-lookup"><span data-stu-id="e74a6-115">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="e74a6-116">Každá odvozená komponenta může tuto vlastnost spotřebovat prostřednictvím `ThemeInfo` kaskádového objektu.</span><span class="sxs-lookup"><span data-stu-id="e74a6-116">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="e74a6-117">`CascadingValuesParametersLayout` část</span><span class="sxs-lookup"><span data-stu-id="e74a6-117">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="e74a6-118">Chcete-li použít kaskádové hodnoty, komponenty deklaruje kaskádové parametry pomocí [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributu.</span><span class="sxs-lookup"><span data-stu-id="e74a6-118">To make use of cascading values, components declare cascading parameters using the [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="e74a6-119">Kaskádové hodnoty jsou vázány na kaskádové parametry podle typu.</span><span class="sxs-lookup"><span data-stu-id="e74a6-119">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="e74a6-120">V ukázkové aplikaci `CascadingValuesParametersTheme` váže komponenta `ThemeInfo` kaskádovou hodnotu k kaskádovým parametrům.</span><span class="sxs-lookup"><span data-stu-id="e74a6-120">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="e74a6-121">Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.</span><span class="sxs-lookup"><span data-stu-id="e74a6-121">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="e74a6-122">`CascadingValuesParametersTheme` část</span><span class="sxs-lookup"><span data-stu-id="e74a6-122">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="e74a6-123">Pro kaskádování více hodnot stejného typu v rámci stejného podstromu zadejte jedinečný <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> řetězec pro každou <xref:Microsoft.AspNetCore.Components.CascadingValue%601> komponentu a odpovídající [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="e74a6-123">To cascade multiple values of the same type within the same subtree, provide a unique <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> string to each <xref:Microsoft.AspNetCore.Components.CascadingValue%601> component and its corresponding [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute.</span></span> <span data-ttu-id="e74a6-124">V následujícím příkladu jsou dvě <xref:Microsoft.AspNetCore.Components.CascadingValue%601> komponenty kaskádovitě různé instance `MyCascadingType` podle názvu:</span><span class="sxs-lookup"><span data-stu-id="e74a6-124">In the following example, two <xref:Microsoft.AspNetCore.Components.CascadingValue%601> components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value="@parentCascadeParameter1" Name="CascadeParam1">
    <CascadingValue Value="@ParentCascadeParameter2" Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="e74a6-125">V komponentě následníka tyto parametry přebírají své hodnoty z odpovídajících kaskádových hodnot v komponentě předchůdce podle názvu:</span><span class="sxs-lookup"><span data-stu-id="e74a6-125">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="e74a6-126">Příklad TabSet</span><span class="sxs-lookup"><span data-stu-id="e74a6-126">TabSet example</span></span>

<span data-ttu-id="e74a6-127">Kaskádové parametry také umožňují komponentám spolupracovat napříč hierarchií součástí.</span><span class="sxs-lookup"><span data-stu-id="e74a6-127">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="e74a6-128">Podívejte se například na následující `TabSet` příklad v ukázkové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="e74a6-128">For example, consider the following `TabSet` example in the sample app.</span></span>

<span data-ttu-id="e74a6-129">Ukázková aplikace má `ITab` rozhraní, které implementuje karty:</span><span class="sxs-lookup"><span data-stu-id="e74a6-129">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](../common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="e74a6-130">`CascadingValuesParametersTabSet`Komponenta používá `TabSet` komponentu, která obsahuje několik `Tab` komponent:</span><span class="sxs-lookup"><span data-stu-id="e74a6-130">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
@page "/CascadingValuesParametersTabSet"

<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>

@code {
    private bool showThirdTab;
}
```

<span data-ttu-id="e74a6-131">Podřízené `Tab` součásti nejsou explicitně předány jako parametry do `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="e74a6-131">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="e74a6-132">Místo toho jsou podřízené `Tab` součásti součástí podřízeného obsahu `TabSet` .</span><span class="sxs-lookup"><span data-stu-id="e74a6-132">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="e74a6-133">`TabSet`Pořád ale potřebuje znát každou `Tab` komponentu, aby mohla vykreslovat hlavičky a aktivní kartu. Chcete-li povolit tuto koordinaci bez nutnosti dalšího kódu, `TabSet` komponenta *může poskytnout sebe sama jako kaskádovou hodnotu* , která je následně vyzvednuta podřízenými `Tab` součástmi.</span><span class="sxs-lookup"><span data-stu-id="e74a6-133">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="e74a6-134">`TabSet` část</span><span class="sxs-lookup"><span data-stu-id="e74a6-134">`TabSet` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="e74a6-135">Podřízené `Tab` komponenty zachytí objekt obsahující `TabSet` jako kaskádový parametr, takže `Tab` komponenty přidávají sebe sama do `TabSet` souřadnice a, na které karty jsou aktivní.</span><span class="sxs-lookup"><span data-stu-id="e74a6-135">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="e74a6-136">`Tab` část</span><span class="sxs-lookup"><span data-stu-id="e74a6-136">`Tab` component:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]
