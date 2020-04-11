---
title: Pomocník s tagy komponenty v ASP.NET jádru
author: guardrex
ms.author: riande
description: Přečtěte si, jak pomocí pomocníka ASP.NET základní hořce a označení komponent k vykreslení komponent Razor na stránkách a v zobrazeních.
ms.custom: mvc
ms.date: 04/01/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 4a6b21229ce086099fcddfeb51c3a959ef639f24
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123432"
---
# <a name="component-tag-helper-in-aspnet-core"></a>Pomocník s tagy komponenty v ASP.NET jádru

[Daniel Roth](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

Chcete-li vykreslit komponentu ze stránky nebo ze zobrazení, použijte [pomocníka pro označení součásti](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).

## <a name="prerequisites"></a>Požadavky

Postupujte podle pokynů v *připravte aplikaci na použití komponent na stránkách a zobrazení* části <xref:blazor/integrate-components#prepare-the-app-to-use-components-in-pages-and-views> článku.

## <a name="component-tag-helper"></a>Pomocník pro označení komponenty

Následující pomocník s značkami `Counter` komponent vykreslí komponentu na stránce nebo v zobrazení:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Předchozí příklad předpokládá, že `Counter` komponenta je ve složce *Stránky* aplikace.

Pomocník s tagy komponenty může také předat parametry součástem. Vezměte v `ColorfulCheckbox` úvahu následující součást, která nastavuje barvu a velikost popisku zaškrtávacího políčka:

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

`Size` `int` [Parametry komponenty](xref:blazor/components#component-parameters) ( ) a `Color` (`string`) lze nastavit pomocí pomocníka pro značku součásti:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

Předchozí příklad předpokládá, že `ColorfulCheckbox` komponenta je ve složce *Shared* aplikace.

Na stránce nebo v zobrazení se vykresluje následující kód HTML:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Předání řetězce v uvozovkách vyžaduje explicitní `param-Color` výraz [Razor](xref:mvc/views/razor#explicit-razor-expressions), jak je znázorněno v předchozím příkladu. Chování analýzy holicího `string` strojku pro hodnotu `param-*` typu se nevztahuje na atribut, protože atribut je `object` typ.

Typ parametru musí být JSON serializovatelný, což obvykle znamená, že typ musí mít výchozí konstruktor a nastavitelné vlastnosti. Můžete například zadat hodnotu `Size` `Color` pro a v předchozím příkladu, protože`int` typy `string` `Size` a `Color` jsou primitivní typy ( a ), které jsou podporovány serializátorem JSON.

V následujícím příkladu je objekt třídy předán komponentě:

*MyClass.cs*:

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

**Třída musí mít veřejný konstruktor bez parametrů.**

*Shared/MyComponent.razor*:

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

*Stránky/MyPage.cshtml*:

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

Předchozí příklad předpokládá, že `MyComponent` komponenta je ve složce *Shared* aplikace. `MyClass`je v oboru názvů aplikace`{APP ASSEMBLY}`( ).

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>konfiguruje, zda součást:

* Je prerendered do stránky.
* Je vykreslen jako statické HTML na stránce, nebo pokud obsahuje potřebné informace k bootstrap aplikace Blazor od uživatelského agenta.

| Režim vykreslení | Popis |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Vykreslí komponentu do statického KÓDU Blazor HTML a obsahuje značku pro serverovou aplikaci. Při spuštění uživatelského agenta se tato značka Blazor používá k zavádění aplikace. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Vykreslí značku Blazor pro serverovou aplikaci. Výstup z komponenty není zahrnut. Při spuštění uživatelského agenta se tato značka Blazor používá k zavádění aplikace. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Vykreslí komponentu do statického HTML. |

Zatímco stránky a zobrazení mohou používat komponenty, konverzace není pravdivá. Součásti nemohou používat funkce specifické pro zobrazení a stránku, jako jsou částečná zobrazení a oddíly. Chcete-li použít logiku z částečného pohledu v komponentě, faktor out částečné zobrazení logiky do komponenty.

Vykreslování součástí serveru ze statické stránky HTML není podporováno.

## <a name="additional-resources"></a>Další zdroje

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
