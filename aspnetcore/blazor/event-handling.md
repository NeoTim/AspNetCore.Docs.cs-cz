---
title: zpracování Blazor událostí ASP.NET Core
author: guardrex
description: Informace Blazoro funkcích zpracování událostí společnosti , včetně typů argumentů událostí, zpětná volání událostí a správy výchozích událostí prohlížeče.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: c144841805e07a136f153c25a78c7f9af7c5801b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511363"
---
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET Zpracování událostí Core Blazor

[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)

Komponenty Razor poskytují funkce zpracování událostí. Pro atribut elementu [`@on{EVENT}`](xref:mvc/views/razor#onevent) HTML s `@onclick`názvem (například) s hodnotou typu delegáta komponenta Razor zachází s hodnotou atributu jako s obslužnou rutinou události.

Následující kód volá `UpdateHeading` metodu, když je tlačítko vybrané v ui:

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

Následující kód volá `CheckChanged` metodu při změně zaškrtávacího políčka v ui:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Obslužné rutiny událostí mohou <xref:System.Threading.Tasks.Task>být také asynchronní a vrátit . Není třeba ručně volat [StateHasChanged](xref:blazor/lifecycle#state-changes). Výjimky jsou zaznamenány, když k nim dojde.

V následujícím příkladu `UpdateHeading` se nazývá asynchronně, když je vybráno tlačítko:

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

U některých událostí jsou povoleny typy argumentů události. Zadání typu události ve volání metody je nezbytné pouze v případě, že typ události je použit v metodě.

Podporované `EventArgs` jsou uvedeny v následující tabulce.

| Událost            | Třída                | Události a poznámky dom |
| ---------------- | -------------------- | -------------------- |
| Schránka        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Přetáhněte             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer`a `DataTransferItem` podržte přetažená data položek. |
| Chyba            | `ErrorEventArgs`     | `onerror` |
| Událost            | `EventArgs`          | *Obecné*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Schránka*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Vstup*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Média*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Zaměření            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Nezahrnuje podporu pro `relatedTarget`. |
| Vstup            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Klávesnice         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Myš            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Ukazatel myši    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Kolečko myši      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| Průběh         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Dotykové ovládání            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint`představuje jediné kontaktní místo na zařízení citlivém na dotyk. |

Další informace najdete v následujících materiálech:

* [EventArgs třídy v ASP.NET základní referenční zdroj (dotnet/aspnetcore release/3.1 větev)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [MDN webové dokumenty: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Obsahuje informace o tom, které prvky HTML podporují každou událost DOM.

## <a name="lambda-expressions"></a>Výrazy lambda

[Lambda výrazy](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) mohou být také použity:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Často je vhodné zavřít přes další hodnoty, například při itaci přes sadu prvků. Následující příklad vytvoří tři tlačítka, `UpdateHeading` z nichž každé`MouseEventArgs`volá předávání argumentu události ( ) a jeho číslo tlačítka (`buttonNumber`) při výběru v ui:

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
> **Nepoužívejte** proměnnou smyčky`i`( `for` ) ve smyčce přímo ve výrazu lambda. V opačném případě je stejná proměnná použita všemi výrazy lambda, které způsobují, `i`že hodnota společnosti je stejná ve všech lambdách. Vždy zachyťte jeho`buttonNumber` hodnotu v místní proměnné (v předchozím příkladu) a pak ji použijte.

## <a name="eventcallback"></a>EventCallback

Běžný scénář s vnořenými součástmi je touha spustit metodu nadřazené součásti, když dojde k události podřízené součásti,&mdash;například když dojde k `onclick` události v podřízeném objektu. Chcete-li vystavit události napříč součástmi, použijte `EventCallback`. Nadřazená komponenta může přiřadit metodu `EventCallback`zpětného volání k podřízené součásti .

V `ChildComponent` ukázkové aplikaci *(Components/ChildComponent.razor)* ukazuje, `onclick` jak je nastavena `EventCallback` obslužná `ParentComponent`rutina tlačítka pro příjem delegáta z ukázky . Je `EventCallback` zadán s `MouseEventArgs`, který je `onclick` vhodný pro událost z periferního zařízení:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

Nastaví `ParentComponent` způsob `EventCallback<T>` dítěte`OnClickCallback`( ). `ShowMessage`

*Stránky/ParentComponent.razor*:

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

Když je tlačítko vybráno v : `ChildComponent`

* Je `ParentComponent`volána metoda 's. `ShowMessage` `_messageText`je aktualizována a `ParentComponent`zobrazena v .
* Volání [StateHasChanged](xref:blazor/lifecycle#state-changes) není vyžadováno v metodě zpětného`ShowMessage`volání ( ). `StateHasChanged`je volána automaticky `ParentComponent`překreslit , stejně jako podřízené události aktivují opětovné vykreslení komponenty v obslužných rutinách událostí, které se spouštějí v rámci podřízeného.

`EventCallback`a `EventCallback<T>` povolit asynchronní delegáty. `EventCallback<T>`je silně zadán a vyžaduje konkrétní typ argumentu. `EventCallback`je slabě zadaný a umožňuje libovolný typ argumentu.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

Vyvolat `EventCallback` nebo `EventCallback<T>` `InvokeAsync` s a <xref:System.Threading.Tasks.Task>čekat na :

```csharp
await callback.InvokeAsync(arg);
```

Použití `EventCallback` `EventCallback<T>` a pro zpracování událostí a parametry součásti vazby.

Preferujte silně `EventCallback<T>` zadaný `EventCallback`přes . `EventCallback<T>`poskytuje uživatelům komponenty lepší zpětnou vazbu k chybám. Podobně jako u jiných obslužných rutin událostí rozhraní je zadání parametru události volitelné. Použijte, `EventCallback` pokud není předána žádná hodnota zpětného volání.

## <a name="prevent-default-actions"></a>Zabránit výchozím akcím

Pomocí [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) atributu direktivy zabraňte výchozí akci události.

Když je na vstupním zařízení vybrána klávesa a fokus prvku je na textovém poli, prohlížeč obvykle zobrazí znak klíče v textovém poli. V následujícím příkladu je výchozí chování zabráněno `@onkeypress:preventDefault` zadáním atributu direktivy. Čítač se zíhá a **+** klíč není `<input>` zachycen do hodnoty prvku:

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

Zadání atributu `@on{EVENT}:preventDefault` bez hodnoty je `@on{EVENT}:preventDefault="true"`ekvivalentní .

Hodnota atributu může být také výraz. V `_shouldPreventDefault` následujícím příkladu `bool` je pole `true` nastaveno na jedno nebo `false`:

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

Obslužná rutina události není nutné zabránit výchozí akci. Obslužnou rutinu události a zabránit výchozí scénáře akce lze použít nezávisle.

## <a name="stop-event-propagation"></a>Zastavit šíření událostí

Pomocí [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) atributu direktiva zastavte šíření událostí.

V následujícím příkladu zabráníte zaškrtnutí políčka, aby `<div>` se události z druhého `<div>`podřízeného objektu nešířily do nadřazeného objektu :

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
