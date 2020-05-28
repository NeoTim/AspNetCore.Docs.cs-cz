---
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

---
# <a name="aspnet-core-blazor-event-handling"></a>BlazorZpracování událostí ASP.NET Core

Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)

Razorkomponenty poskytují funkce pro zpracování událostí. Pro atribut elementu HTML s názvem [`@on{EVENT}`](xref:mvc/views/razor#onevent) (například `@onclick` ) s hodnotou delegovaný typ Razor Komponenta považuje hodnotu atributu za obslužnou rutinu události.

Následující kód volá metodu, `UpdateHeading` Pokud je vybráno tlačítko v uživatelském rozhraní:

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

Následující kód volá metodu, `CheckChanged` když je zaškrtávací políčko změněno v uživatelském rozhraní:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Obslužné rutiny událostí mohou být také asynchronní a vracet <xref:System.Threading.Tasks.Task> . Není nutné ručně volat [StateHasChanged](xref:blazor/lifecycle#state-changes). Výjimky jsou protokolovány, když k nim dojde.

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

Podporované <xref:System.EventArgs> jsou uvedené v následující tabulce.

| Událost            | Třída                | Události a poznámky modelu DOM |
| ---
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-------- | ---Název: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

---------- | ---Název: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

-
title: ' ASP.NET Core Blazor zpracování událostí ' Autor: Popis: ' informace o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných volání událostí a Správa výchozích událostí prohlížeče. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

---------- | | Schránka | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | `oncut`, `oncopy`, `onpaste` | | Přetáhněte | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`,`ondragend`<br><br><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>a <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> uchovávají přetažená data položky. | | Chyba | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` | | Událost | <xref:System.EventArgs>  |  *Obecné*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Schránka*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Vstup*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit><br><br>*Média*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`<br><br><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>obsahuje atributy pro konfiguraci mapování mezi názvy událostí a typy argumentů události. | | Zaměření | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | `onfocus`, `onblur`, `onfocusin`,`onfocusout`<br><br>Nezahrnuje podporu pro `relatedTarget` . | | Vstup | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | `onchange`, `oninput` | | Klávesnice | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | `onkeydown`, `onkeypress`, `onkeyup` | | Myš | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` | | Ukazatel myši | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` | | Kolečko myši | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | `onwheel`, `onmousewheel` | | Průběh | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` | | Dotykové ovládání | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`,`ontouchcancel`<br><br><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>představuje jeden kontaktní bod na zařízení citlivém na dotykové ovládání. |

Další informace najdete v následujících materiálech:

* [Třídy EventArgs ve zdroji odkazů ASP.NET Core (větev dotnet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [Webové dokumenty MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): obsahuje informace o tom, které prvky HTML podporují jednotlivé události modelu DOM.

## <a name="lambda-expressions"></a>Výrazy lambda

[Lambda výrazy](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) lze také použít:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Je často vhodné uzavřít další hodnoty, jako například při iteraci přes sadu prvků. Následující příklad vytvoří tři tlačítka, z nichž každé volá `UpdateHeading` předání argumentu události ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) a jeho čísla tlačítka ( `buttonNumber` ), pokud je VYBRÁNO v uživatelském rozhraní:

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
> Nepoužívejte **proměnnou** smyčky ( `i` ) ve `for` smyčce přímo ve výrazu lambda. V opačném případě se stejná proměnná používá ve všech výrazech lambda, což způsobuje `i` , že hodnota je stejná ve všech výrazech lambda. Vždycky zachytit svou hodnotu v místní proměnné ( `buttonNumber` v předchozím příkladu) a pak ji použít.

## <a name="eventcallback"></a>Vnořenou eventCallback

Běžný scénář s vnořenými komponentami je přáním spustit metodu nadřazené komponenty, když dojde k události podřízené součásti. `onclick`Událost, ke které dochází v podřízené součásti, je běžným případem použití. Chcete-li zobrazit události napříč komponentami, použijte <xref:Microsoft.AspNetCore.Components.EventCallback> . Nadřazená komponenta může přiřadit metodu zpětného volání podřízené součásti <xref:Microsoft.AspNetCore.Components.EventCallback> .

`ChildComponent`V ukázkové aplikaci (*Components/ChildComponent. Razor*) ukazuje, jak `onclick` je nastavena obslužná rutina tlačítka pro příjem <xref:Microsoft.AspNetCore.Components.EventCallback> delegáta z ukázky `ParentComponent` . <xref:Microsoft.AspNetCore.Components.EventCallback>Je zadaný s `MouseEventArgs` , který je vhodný pro `onclick` událost z periferního zařízení:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent`Nastaví <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) dítěte na jeho `ShowMessage` metodu.

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

Když je vybráno tlačítko v `ChildComponent` :

* `ParentComponent` `ShowMessage` Je volána metoda. `messageText`se aktualizuje a zobrazí v `ParentComponent` .
* V metodě zpětného volání () není vyžadováno volání [StateHasChanged](xref:blazor/lifecycle#state-changes) `ShowMessage` . <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>je volána automaticky pro revykreslování `ParentComponent` , stejně jako podřízené události, které aktivují revykreslování komponenty v obslužných rutinách události, které jsou spouštěny v rámci podřízeného objektu.

<xref:Microsoft.AspNetCore.Components.EventCallback>a <xref:Microsoft.AspNetCore.Components.EventCallback%601> povolují asynchronní delegáty. <xref:Microsoft.AspNetCore.Components.EventCallback%601>je silného typu a vyžaduje konkrétní typ argumentu. <xref:Microsoft.AspNetCore.Components.EventCallback>je slabě typované a umožňuje jakýkoli typ argumentu.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

Vyvolat <xref:Microsoft.AspNetCore.Components.EventCallback> nebo <xref:Microsoft.AspNetCore.Components.EventCallback%601> s <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> a očekávat <xref:System.Threading.Tasks.Task> :

```csharp
await callback.InvokeAsync(arg);
```

Použití <xref:Microsoft.AspNetCore.Components.EventCallback> a <xref:Microsoft.AspNetCore.Components.EventCallback%601> pro zpracování událostí a parametry komponenty vazby.

Preferovat silného typu <xref:Microsoft.AspNetCore.Components.EventCallback%601> přes <xref:Microsoft.AspNetCore.Components.EventCallback> . <xref:Microsoft.AspNetCore.Components.EventCallback%601>poskytuje lepší odezvu na chyby uživatelů součásti. Podobně jako u jiných obslužných rutin událostí uživatelského rozhraní je zadání parametru události volitelné. Použijte <xref:Microsoft.AspNetCore.Components.EventCallback> v případě, že zpětnému volání není předáno žádné číslo.

## <a name="prevent-default-actions"></a>Zabránit výchozím akcím

[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault)Chcete-li zabránit výchozí akci pro událost, použijte atribut direktiva.

Když je vybraný klíč na vstupním zařízení a fokus prvku je v textovém poli, prohlížeč normálně zobrazuje znak klíče v textovém poli. V následujícím příkladu je výchozím chováním znemožněno zadáním `@onkeypress:preventDefault` atributu direktiva. Čítač zvýší a **+** klíč není zachycen do `<input>` hodnoty prvku:

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

Určení `@on{EVENT}:preventDefault` atributu bez hodnoty je ekvivalentní `@on{EVENT}:preventDefault="true"` .

Hodnotou atributu může být také výraz. V následujícím příkladu `shouldPreventDefault` je `bool` pole nastaveno na buď `true` nebo `false` :

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

Obslužná rutina události není nutná, aby se zabránilo výchozí akci. Obslužná rutina události a zabraňuje použití výchozích akcí, lze použít nezávisle.

## <a name="stop-event-propagation"></a>Zastavit šíření událostí

[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation)Pro zastavení šíření události použijte atribut direktiva.

V následujícím příkladu zaškrtnutí políčka zabrání kliknutí na události z druhého podřízeného `<div>` objektu pro rozšíření na nadřazený `<div>` :

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
