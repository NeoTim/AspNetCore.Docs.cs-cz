---
title: ASP.NET Core Blazor kaskádové hodnoty a parametry
author: guardrex
description: Naučte se přesměrovat data z komponenty předchůdce na podřízené součásti.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/16/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/cascading-values-and-parameters
ms.openlocfilehash: c72bf5584c7e0afdeadbb4103b5e58d91ae8c1e9
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399215"
---
# <a name="aspnet-core-blazor-cascading-values-and-parameters"></a>ASP.NET Core Blazor kaskádové hodnoty a parametry

## <a name="cascading-values-and-parameters"></a>Kaskádové hodnoty a parametry

V některých scénářích je nevhodné přesměrovat data z komponenty předchůdce na dceřinou komponentu pomocí [parametrů komponenty](xref:blazor/components/index#component-parameters), zejména pokud je k dispozici několik vrstev komponent. Kaskádové hodnoty a parametry řeší tento problém tím, že poskytují pohodlný způsob, jak komponenta předchůdce poskytne hodnotu všem jejím následným součástem. Kaskádové hodnoty a parametry také poskytují přístup ke koordinaci komponent.

### <a name="theme-example"></a>Příklad motivu

V následujícím příkladu z ukázkové aplikace `ThemeInfo` Třída určuje informace o motivu pro přetečení hierarchie komponent tak, aby všechna tlačítka v dané části aplikace sdílela stejný styl.

`UIThemeClasses/ThemeInfo.cs`:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Komponenta předchůdce může poskytnout kaskádovou hodnotu pomocí komponenty kaskádová hodnota. <xref:Microsoft.AspNetCore.Components.CascadingValue%601>Komponenta zabalí podstrom hierarchie komponent a poskytne jednu hodnotu všem součástem v rámci daného podstromu.

Například ukázková aplikace určuje informace o motivu ( `ThemeInfo` ) v jednom z rozložení aplikace jako kaskádový parametr pro všechny komponenty, které tvoří tělo `@Body` Vlastnosti. `ButtonClass`má přiřazenou hodnotu `btn-success` v součásti rozložení. Každá odvozená komponenta může tuto vlastnost spotřebovat prostřednictvím `ThemeInfo` kaskádového objektu.

`CascadingValuesParametersLayout`část

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

Chcete-li použít kaskádové hodnoty, komponenty deklaruje kaskádové parametry pomocí [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributu. Kaskádové hodnoty jsou vázány na kaskádové parametry podle typu.

V ukázkové aplikaci `CascadingValuesParametersTheme` váže komponenta `ThemeInfo` kaskádovou hodnotu k kaskádovým parametrům. Parametr slouží k nastavení třídy CSS pro jedno z tlačítek zobrazených komponentou.

`CascadingValuesParametersTheme`část

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

Pro kaskádování více hodnot stejného typu v rámci stejného podstromu zadejte jedinečný <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A> řetězec pro každou <xref:Microsoft.AspNetCore.Components.CascadingValue%601> komponentu a odpovídající [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atribut. V následujícím příkladu jsou dvě <xref:Microsoft.AspNetCore.Components.CascadingValue%601> komponenty kaskádovitě různé instance `MyCascadingType` podle názvu:

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
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

V komponentě následníka tyto parametry přebírají své hodnoty z odpovídajících kaskádových hodnot v komponentě předchůdce podle názvu:

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>Příklad TabSet

Kaskádové parametry také umožňují komponentám spolupracovat napříč hierarchií součástí. Podívejte se například na následující `TabSet` příklad v ukázkové aplikaci.

Ukázková aplikace má `ITab` rozhraní, které implementuje karty:

[!code-csharp[](../common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

`CascadingValuesParametersTabSet`Komponenta používá `TabSet` komponentu, která obsahuje několik `Tab` komponent:

```razor
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
```

Podřízené `Tab` součásti nejsou explicitně předány jako parametry do `TabSet` . Místo toho jsou podřízené `Tab` součásti součástí podřízeného obsahu `TabSet` . `TabSet`Pořád ale potřebuje znát každou `Tab` komponentu, aby mohla vykreslovat hlavičky a aktivní kartu. Chcete-li povolit tuto koordinaci bez nutnosti dalšího kódu, `TabSet` komponenta *může poskytnout sebe sama jako kaskádovou hodnotu* , která je následně vyzvednuta podřízenými `Tab` součástmi.

`TabSet`část

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Podřízené `Tab` komponenty zachytí objekt obsahující `TabSet` jako kaskádový parametr, takže `Tab` komponenty přidávají sebe sama do `TabSet` souřadnice a, na které karty jsou aktivní.

`Tab`část

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]