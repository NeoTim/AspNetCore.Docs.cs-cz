---
title: Zpracování Blazor událostí ASP.NET Core
author: guardrex
description: Přečtěte Blazorsi o funkcích zpracování událostí, včetně typů argumentů události, zpětných voláních událostí a správě výchozích událostí prohlížeče.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: aa338bbe61eec14bc1e1b3606e11e26bfb0e6a09
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967464"
---
# <a name="aspnet-core-blazor-event-handling"></a>Zpracování událostí ASP.NET Core Blazor

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

Komponenty Razor poskytují funkce pro zpracování událostí. Pro atribut elementu HTML s názvem [`@on{EVENT}`](xref:mvc/views/razor#onevent) (například `@onclick`) s hodnotou typu delegáta, komponenta Razor považuje hodnotu atributu za obslužnou rutinu události.

Následující kód volá `UpdateHeading` metodu, pokud je vybráno tlačítko v uživatelském rozhraní:

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

Následující kód volá `CheckChanged` metodu, když je zaškrtávací políčko změněno v uživatelském rozhraní:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Obslužné rutiny událostí mohou být také asynchronní a <xref:System.Threading.Tasks.Task>vracet. Není nutné ručně volat [StateHasChanged](xref:blazor/lifecycle#state-changes). Výjimky jsou protokolovány, když k nim dojde.

V následujícím příkladu `UpdateHeading` se volá asynchronně po výběru tlačítka:

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

## <a name="event-argument-types"></a>Typy argumentů události

U některých událostí jsou povoleny typy argumentů události. Zadání typu události ve volání metody je nezbytné pouze v případě, že je typ události použit v metodě.

Podporované `EventArgs` jsou uvedené v následující tabulce.

| Událost            | Třída                | Události a poznámky modelu DOM |
| ---------------- | -------------------- | -------------------- |
| Schránka        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Myší             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer`a `DataTransferItem` uchovávají přetažená data položky. |
| Chyba            | `ErrorEventArgs`     | `onerror` |
| Událost            | `EventArgs`          | *Obecné*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Schránka*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Vstup*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Média*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Vybrána            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Nezahrnuje podporu pro `relatedTarget`. |
| Vstup            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Klávesnice         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Myš            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Ukazatel myši    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Kolečko myši      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| Průběh         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Dotykové ovládání            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint`představuje jeden kontaktní bod na zařízení citlivém na dotykové ovládání. |

Další informace najdete v následujících materiálech:

* [Třídy EventArgs ve zdroji odkazů ASP.NET Core (větev dotnet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [MDN web Docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; obsahuje informace o tom, které prvky HTML podporují jednotlivé události modelu DOM.

## <a name="lambda-expressions"></a>Výrazy lambda

[Lambda výrazy](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) lze také použít:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Je často vhodné uzavřít další hodnoty, jako například při iteraci přes sadu prvků. Následující příklad vytvoří tři tlačítka, z nichž každé volá `UpdateHeading` předání argumentu události (`MouseEventArgs`) a jeho čísla tlačítka (`buttonNumber`), pokud je vybráno v uživatelském rozhraní:

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> Nepoužívejte **proměnnou** smyčky (`i`) ve `for` smyčce přímo ve výrazu lambda. V opačném případě se stejná proměnná používá ve všech výrazech lambda, což způsobuje `i`, že hodnota je stejná ve všech výrazech lambda. Vždycky zachytit svou hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak ji použít.

## <a name="eventcallback"></a>Vnořenou eventCallback

Běžný scénář s vnořenými komponentami je přáním spustit metodu nadřazené komponenty, když dojde&mdash;k události podřízené komponenty, například když dojde k události `onclick` v podřízeném objektu. Chcete-li zobrazit události napříč komponentami `EventCallback`, použijte. Nadřazená komponenta může přiřadit metodu zpětného volání podřízené součásti `EventCallback`.

`ChildComponent` V ukázkové aplikaci (*Components/ChildComponent. Razor*) ukazuje, jak je nastavena `onclick` obslužná rutina tlačítka pro příjem `EventCallback` delegáta z ukázky. `ParentComponent` `EventCallback` Je zadaný s `MouseEventArgs`, který je vhodný pro `onclick` událost z periferního zařízení:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent` Nastaví `EventCallback<T>` (`OnClickCallback`) dítěte na jeho `ShowMessage` metodu.

*Stránky/ParentComponent. Razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

Když je vybráno tlačítko v `ChildComponent`:

* `ParentComponent`Je volána `ShowMessage` metoda. `messageText`se aktualizuje a zobrazí v `ParentComponent`.
* V metodě zpětného volání (`ShowMessage`) není vyžadováno volání [StateHasChanged](xref:blazor/lifecycle#state-changes) . `StateHasChanged`je volána automaticky pro revykreslování `ParentComponent`, stejně jako podřízené události, které aktivují revykreslování komponenty v obslužných rutinách události, které jsou spouštěny v rámci podřízeného objektu.

`EventCallback`a `EventCallback<T>` povolují asynchronní delegáty. `EventCallback<T>`je silného typu a vyžaduje konkrétní typ argumentu. `EventCallback`je slabě typované a umožňuje jakýkoli typ argumentu.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

Vyvolat `EventCallback` nebo `EventCallback<T>` s `InvokeAsync` a očekávat: <xref:System.Threading.Tasks.Task>

```csharp
await callback.InvokeAsync(arg);
```

Použití `EventCallback` a `EventCallback<T>` pro zpracování událostí a parametry komponenty vazby.

Preferovat silného typu `EventCallback<T>` přes `EventCallback`. `EventCallback<T>`poskytuje lepší odezvu na chyby uživatelů součásti. Podobně jako u jiných obslužných rutin událostí uživatelského rozhraní je zadání parametru události volitelné. Použijte `EventCallback` v případě, že zpětnému volání není předáno žádné číslo.

## <a name="prevent-default-actions"></a>Zabránit výchozím akcím

Chcete- [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) li zabránit výchozí akci pro událost, použijte atribut direktiva.

Když je vybraný klíč na vstupním zařízení a fokus prvku je v textovém poli, prohlížeč normálně zobrazuje znak klíče v textovém poli. V následujícím příkladu je výchozím chováním znemožněno zadáním atributu `@onkeypress:preventDefault` direktiva. Čítač zvýší a **+** klíč není zachycen do hodnoty `<input>` prvku:

```razor
<input value="@count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            count++;
        }
    }
}
```

Určení `@on{EVENT}:preventDefault` atributu bez hodnoty je ekvivalentní `@on{EVENT}:preventDefault="true"`.

Hodnotou atributu může být také výraz. `shouldPreventDefault` V následujícím příkladu `bool` je pole nastaveno na buď `true` nebo: `false`

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

Obslužná rutina události není nutná, aby se zabránilo výchozí akci. Obslužná rutina události a zabraňuje použití výchozích akcí, lze použít nezávisle.

## <a name="stop-event-propagation"></a>Zastavit šíření událostí

Pro zastavení [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) šíření události použijte atribut direktiva.

V následujícím příkladu zaškrtnutí políčka zabrání kliknutí na události z druhého podřízeného `<div>` objektu pro rozšíření na nadřazený: `<div>`

```razor
<label>
    <input @bind="stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
