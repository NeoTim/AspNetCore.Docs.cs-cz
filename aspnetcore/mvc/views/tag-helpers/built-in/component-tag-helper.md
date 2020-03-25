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
# <a name="component-tag-helper-in-aspnet-core"></a>Pomocná značka komponenty v ASP.NET Core

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

Chcete-li vykreslit komponentu ze stránky nebo zobrazení, použijte [pomocníka značek komponenty](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).

Následující pomocník značek komponenty vykreslí `Counter` komponentu na stránce nebo zobrazení:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

Pomocný objekt tag komponenty může také předat parametry komponentám. Vezměte v úvahu následující `ColorfulCheckbox` komponenty, která nastaví barvu a velikost popisku zaškrtávacího políčka:

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

[Parametry komponenty](xref:blazor/components#component-parameters) `Size` (`int`) a `Color` (`string`) lze nastavit pomocí pomocníka značky komponenty:

```cshtml
<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

Na stránce nebo zobrazení se vykreslí následující kód HTML:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

Předání řetězce v uvozovkách vyžaduje [explicitní výraz Razor](xref:mvc/views/razor#explicit-razor-expressions), jak je znázorněno v předchozím příkladu `param-Color`. Chování analýzy Razor pro hodnotu typu `string` se nevztahuje na atribut `param-*`, protože atribut je `object` typ.

Typ parametru musí být serializovatelný jako JSON, což obvykle znamená, že typ musí mít výchozí konstruktor a nastavitelné vlastnosti. Například můžete zadat hodnotu pro `Size` a `Color` v předchozím příkladu, protože typy `Size` a `Color` jsou primitivní typy (`int` a `string`), které jsou podporovány serializátorem JSON.

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> nakonfiguruje, jestli součást:

* Je předem vykreslen na stránku.
* Je vykreslen jako statický kód HTML na stránce nebo pokud obsahuje nezbytné informace pro spuštění aplikace Blazor z uživatelského agenta.

| Režim vykreslování | Popis |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Vykreslí komponentu do statického HTML a obsahuje značku pro aplikaci Blazor Server. Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Vykreslí značku pro aplikaci Blazor serveru. Výstup komponenty není zahrnutý. Když se spustí uživatelský agent, tato značka se použije k zavedení Blazor aplikace. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Vykreslí komponentu do statického HTML. |

I když stránky a zobrazení mohou používat komponenty, není tato konverzace pravdivá. Komponenty nemůžou používat funkce pro zobrazení a stránky, jako jsou například částečná zobrazení a oddíly. Chcete-li použít logiku ze částečného zobrazení v komponentě, rozložte logiku částečného zobrazení do komponenty.

Vykreslování součástí serveru ze statické stránky HTML není podporováno.

## <a name="additional-resources"></a>Další zdroje

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
