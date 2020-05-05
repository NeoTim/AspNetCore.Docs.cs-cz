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
# <a name="component-tag-helper-in-aspnet-core"></a>Pomocná značka komponenty v ASP.NET Core

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte [pomocníka značek komponenty](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).

## <a name="prerequisites"></a>Požadavky

Postupujte podle pokynů v části *Příprava aplikace na používání součástí na stránkách a pohledech v* <xref:blazor/integrate-components#prepare-the-app> článku.

## <a name="component-tag-helper"></a>Pomocník značek komponenty

Následující pomocník značek komponent vykresluje `Counter` komponentu na stránce nebo zobrazení:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Předchozí příklad předpokládá, že je `Counter` komponenta ve složce *stránky* aplikace.

Pomocný objekt tag komponenty může také předat parametry komponentám. Vezměte v úvahu `ColorfulCheckbox` následující komponentu, která nastaví barvu a velikost popisku zaškrtávacího políčka:

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

`Size` [Parametry komponenty](xref:blazor/components#component-parameters) (`int`) `Color` a`string`() lze nastavit pomocí pomocníka značky komponenty:

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

Předchozí příklad předpokládá, že je `ColorfulCheckbox` komponenta ve *sdílené* složce aplikace.

Na stránce nebo zobrazení se vykreslí následující kód HTML:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Předání řetězce v uvozovkách vyžaduje [explicitní výraz Razor](xref:mvc/views/razor#explicit-razor-expressions), jak je znázorněno `param-Color` v předchozím příkladu. Chování analýzy Razor pro hodnotu `string` typu se nevztahuje na `param-*` atribut, protože atribut je `object` typu.

Typ parametru musí být serializovatelný jako JSON, což obvykle znamená, že typ musí mít výchozí konstruktor a nastavitelné vlastnosti. Například můžete `Size` zadat hodnotu pro a `Color` v předchozím příkladu, protože typy `Size` a `Color` jsou primitivní typy (`int` a `string`), které jsou podporovány serializátorem JSON.

V následujícím příkladu je objekt třídy předán do komponenty:

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

*Shared/MyComponent. Razor*:

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

*Pages/MyPage. cshtml*:

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

Předchozí příklad předpokládá, že je `MyComponent` komponenta ve *sdílené* složce aplikace. `MyClass`je v oboru názvů aplikace (`{APP ASSEMBLY}`).

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>nakonfiguruje, jestli součást:

* Je předem vykreslen na stránku.
* Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.

| Režim vykreslování | Popis |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Vykreslí komponentu do statického HTML a obsahuje značku pro Blazor serverovou aplikaci. Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Vykreslí značku pro Blazor serverovou aplikaci. Výstup komponenty není zahrnutý. Když se spustí uživatelský agent, tato značka se použije ke spuštění Blazor aplikace. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Vykreslí komponentu do statického HTML. |

I když stránky a zobrazení mohou používat komponenty, není tato konverzace pravdivá. Komponenty nemůžou používat funkce pro zobrazení a stránky, jako jsou například částečná zobrazení a oddíly. Chcete-li použít logiku ze částečného zobrazení v komponentě, rozložte logiku částečného zobrazení do komponenty.

Vykreslování součástí serveru ze statické stránky HTML není podporováno.

## <a name="additional-resources"></a>Další zdroje

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
