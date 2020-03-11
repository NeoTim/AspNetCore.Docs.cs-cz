---
title: Zpracování událostí ASP.NET Core Blazor
author: guardrex
description: Přečtěte si o scénářích zpracování událostí Blazor, včetně typů argumentů události, zpětných volání událostí a správě výchozích událostí prohlížeče.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: 25844ef39aee849072d16f3d73eda0a1c20ee788
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661873"
---
# <a name="aspnet-core-blazor-event-handling"></a>Zpracování událostí ASP.NET Core Blazor

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

Komponenty Razor poskytují funkce pro zpracování událostí. Pro atribut elementu HTML s názvem `on{EVENT}` (například `onclick` a `onsubmit`) s hodnotou delegovanou typem, komponenty Razor považují hodnotu atributu za obslužnou rutinu události. Název atributu je vždy formátován [`@on{EVENT}`](xref:mvc/views/razor#onevent).

Následující kód volá metodu `UpdateHeading`, pokud je tlačítko vybráno v uživatelském rozhraní:

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

Následující kód volá metodu `CheckChanged`, když je zaškrtávací políčko v uživatelském rozhraní změněno:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Obslužné rutiny událostí mohou být také asynchronní a vracet <xref:System.Threading.Tasks.Task>. Není nutné ručně volat [StateHasChanged](xref:blazor/lifecycle#state-changes). Výjimky jsou protokolovány, když k nim dojde.

V následujícím příkladu je `UpdateHeading` volána asynchronně, když je vybráno tlačítko:

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

Podporované `EventArgs` jsou uvedeny v následující tabulce.

| Událost            | Třída                | Události a poznámky modelu DOM |
| ---------------- | -------------------- | -------------------- |
| Schránka        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Přetažení             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer` a `DataTransferItem` uchování přetažených dat položky. |
| Chyba            | `ErrorEventArgs`     | `onerror` |
| Událost            | `EventArgs`          | *Obecné*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Schránka*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Input* (Vstup)<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart``onsubmit`<br><br>*Média*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Vybrána            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Neobsahuje podporu pro `relatedTarget`. |
| Vstup            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Klávesnice         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Stisknut            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Ukazatele myši    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Kolečko myši      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| Průběh         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress``ontimeout` |
| Dotykové ovládání            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave``ontouchcancel`<br><br>`TouchPoint` představuje jeden kontaktní bod na zařízení citlivém na dotykové ovládání. |

Další informace najdete v následujících zdrojích:

* [Třídy EventArgs ve zdroji odkazů ASP.NET Core (větev dotnet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [MDN web Docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; obsahuje informace o tom, které prvky HTML podporují jednotlivé události modelu DOM.

## <a name="lambda-expressions"></a>Výrazy lambda

[Lambda výrazy](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) lze také použít:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Je často vhodné uzavřít další hodnoty, jako například při iteraci přes sadu prvků. Následující příklad vytvoří tři tlačítka, z nichž každá volá `UpdateHeading` předání argumentu události (`MouseEventArgs`) a jeho číslo tlačítka (`buttonNumber`), pokud je vybráno v uživatelském rozhraní:

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> Nepoužívejte **proměnnou** smyčky (`i`) ve `for` smyčce přímo ve výrazu lambda. V opačném případě se stejná proměnná používá u všech výrazů lambda, které způsobují, že hodnota `i`být stejná ve všech výrazech lambda. Vždycky zachytit svou hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak ji použít.

## <a name="eventcallback"></a>Vnořenou eventCallback

Běžný scénář s vnořenými komponentami je přáním spustit metodu nadřazené komponenty, když dojde k události podřízené součásti&mdash;například při výskytu události `onclick` v podřízeném objektu. Chcete-li zobrazit události napříč komponentami, použijte `EventCallback`. Nadřazená komponenta může přiřadit metodu zpětného volání podřízené `EventCallback`komponenty.

`ChildComponent` v ukázkové aplikaci (*Components/ChildComponent. Razor*) ukazuje, jak je nastavená obslužná rutina `onclick` tlačítka pro příjem `EventCallback`ho delegáta z `ParentComponent`ukázky. `EventCallback` se zadává pomocí `MouseEventArgs`, která je vhodná pro událost `onclick` z periferního zařízení:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent` nastaví `EventCallback<T>` (`OnClickCallback`) dítěte na svou `ShowMessage` metodu.

*Stránky/ParentComponent. Razor*:

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

Když je vybráno tlačítko v `ChildComponent`:

* Je volána metoda `ShowMessage` `ParentComponent`. `_messageText` se aktualizuje a zobrazí v `ParentComponent`.
* Volání [StateHasChanged](xref:blazor/lifecycle#state-changes) není vyžadováno v metodě zpětného volání (`ShowMessage`). `StateHasChanged` se zavolá automaticky, aby se `ParentComponent`znovu vykreslila, stejně jako podřízené události spouštějí revykreslování komponenty v obslužných rutinách události, které se spouštějí v rámci podřízeného objektu

`EventCallback` a `EventCallback<T>` povolují asynchronní delegáty. `EventCallback<T>` je silného typu a vyžaduje konkrétní typ argumentu. `EventCallback` je slabě typované a umožňuje jakýkoli typ argumentu.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

Vyvolat `EventCallback` nebo `EventCallback<T>` s `InvokeAsync` a očekávat <xref:System.Threading.Tasks.Task>:

```csharp
await callback.InvokeAsync(arg);
```

Pro zpracování událostí a parametry komponenty vazby použijte `EventCallback` a `EventCallback<T>`.

Preferovat `EventCallback<T>` silného typu přes `EventCallback`. `EventCallback<T>` poskytuje uživatelům součásti lepší zpětnou vazbu k chybám. Podobně jako u jiných obslužných rutin událostí uživatelského rozhraní je zadání parametru události volitelné. Použijte `EventCallback`, pokud není předána žádná hodnota zpětnému volání.

## <a name="prevent-default-actions"></a>Zabránit výchozím akcím

Chcete-li zabránit výchozí akci pro událost, použijte atribut direktiva [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) .

Když je vybraný klíč na vstupním zařízení a fokus prvku je v textovém poli, prohlížeč normálně zobrazuje znak klíče v textovém poli. V následujícím příkladu je znemožněno výchozí chování zadáním atributu direktiva `@onkeypress:preventDefault`. Čítač zvýší a **+** klíč není zachycen do hodnoty `<input>` elementu:

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

Zadání atributu `@on{EVENT}:preventDefault` bez hodnoty je ekvivalentem `@on{EVENT}:preventDefault="true"`.

Hodnotou atributu může být také výraz. V následujícím příkladu je `_shouldPreventDefault` `bool` pole nastaveno na hodnotu `true` nebo `false`:

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

Obslužná rutina události není nutná, aby se zabránilo výchozí akci. Obslužná rutina události a zabraňuje použití výchozích akcí, lze použít nezávisle.

## <a name="stop-event-propagation"></a>Zastavit šíření událostí

Pro zastavení šíření události použijte atribut direktiva [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) .

V následujícím příkladu zaškrtnutí políčka zabrání kliknutí na události z druhého podřízeného `<div>` z rozšiřování do nadřazené `<div>`:

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```
