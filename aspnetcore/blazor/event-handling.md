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
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453238"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="9b408-103">Zpracování událostí ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="9b408-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="9b408-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9b408-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="9b408-105">Komponenty Razor poskytují funkce pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="9b408-105">Razor components provide event handling features.</span></span> <span data-ttu-id="9b408-106">Pro atribut elementu HTML s názvem `on{EVENT}` (například `onclick` a `onsubmit`) s hodnotou delegovanou typem, komponenty Razor považují hodnotu atributu za obslužnou rutinu události.</span><span class="sxs-lookup"><span data-stu-id="9b408-106">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="9b408-107">Název atributu je vždy formátován [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span><span class="sxs-lookup"><span data-stu-id="9b408-107">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="9b408-108">Následující kód volá metodu `UpdateHeading`, pokud je tlačítko vybráno v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="9b408-108">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="9b408-109">Následující kód volá metodu `CheckChanged`, když je zaškrtávací políčko v uživatelském rozhraní změněno:</span><span class="sxs-lookup"><span data-stu-id="9b408-109">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="9b408-110">Obslužné rutiny událostí mohou být také asynchronní a vracet <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="9b408-110">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="9b408-111">Není nutné ručně volat [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="9b408-111">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="9b408-112">Výjimky jsou protokolovány, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="9b408-112">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="9b408-113">V následujícím příkladu je `UpdateHeading` volána asynchronně, když je vybráno tlačítko:</span><span class="sxs-lookup"><span data-stu-id="9b408-113">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="9b408-114">Typy argumentů události</span><span class="sxs-lookup"><span data-stu-id="9b408-114">Event argument types</span></span>

<span data-ttu-id="9b408-115">U některých událostí jsou povoleny typy argumentů události.</span><span class="sxs-lookup"><span data-stu-id="9b408-115">For some events, event argument types are permitted.</span></span> <span data-ttu-id="9b408-116">Zadání typu události ve volání metody je nezbytné pouze v případě, že je typ události použit v metodě.</span><span class="sxs-lookup"><span data-stu-id="9b408-116">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="9b408-117">Podporované `EventArgs` jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="9b408-117">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="9b408-118">Událost</span><span class="sxs-lookup"><span data-stu-id="9b408-118">Event</span></span>            | <span data-ttu-id="9b408-119">Třída</span><span class="sxs-lookup"><span data-stu-id="9b408-119">Class</span></span>                | <span data-ttu-id="9b408-120">Události a poznámky modelu DOM</span><span class="sxs-lookup"><span data-stu-id="9b408-120">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="9b408-121">Schránka</span><span class="sxs-lookup"><span data-stu-id="9b408-121">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="9b408-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="9b408-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="9b408-123">Přetažení</span><span class="sxs-lookup"><span data-stu-id="9b408-123">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="9b408-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="9b408-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="9b408-125">`DataTransfer` a `DataTransferItem` uchování přetažených dat položky.</span><span class="sxs-lookup"><span data-stu-id="9b408-125">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="9b408-126">Chyba</span><span class="sxs-lookup"><span data-stu-id="9b408-126">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="9b408-127">Událost</span><span class="sxs-lookup"><span data-stu-id="9b408-127">Event</span></span>            | `EventArgs`          | <span data-ttu-id="9b408-128">*Obecné*</span><span class="sxs-lookup"><span data-stu-id="9b408-128">*General*</span></span><br><span data-ttu-id="9b408-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="9b408-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="9b408-130">*Schránka*</span><span class="sxs-lookup"><span data-stu-id="9b408-130">*Clipboard*</span></span><br><span data-ttu-id="9b408-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="9b408-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="9b408-132">*Input* (Vstup)</span><span class="sxs-lookup"><span data-stu-id="9b408-132">*Input*</span></span><br><span data-ttu-id="9b408-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart``onsubmit`</span><span class="sxs-lookup"><span data-stu-id="9b408-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="9b408-134">*Média*</span><span class="sxs-lookup"><span data-stu-id="9b408-134">*Media*</span></span><br><span data-ttu-id="9b408-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="9b408-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="9b408-136">Vybrána</span><span class="sxs-lookup"><span data-stu-id="9b408-136">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="9b408-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="9b408-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="9b408-138">Neobsahuje podporu pro `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="9b408-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="9b408-139">Vstup</span><span class="sxs-lookup"><span data-stu-id="9b408-139">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="9b408-140">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="9b408-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="9b408-141">Klávesnice</span><span class="sxs-lookup"><span data-stu-id="9b408-141">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="9b408-142">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="9b408-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="9b408-143">Stisknut</span><span class="sxs-lookup"><span data-stu-id="9b408-143">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="9b408-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="9b408-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="9b408-145">Ukazatele myši</span><span class="sxs-lookup"><span data-stu-id="9b408-145">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="9b408-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="9b408-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="9b408-147">Kolečko myši</span><span class="sxs-lookup"><span data-stu-id="9b408-147">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="9b408-148">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="9b408-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="9b408-149">Průběh</span><span class="sxs-lookup"><span data-stu-id="9b408-149">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="9b408-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress``ontimeout`</span><span class="sxs-lookup"><span data-stu-id="9b408-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="9b408-151">Dotykové ovládání</span><span class="sxs-lookup"><span data-stu-id="9b408-151">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="9b408-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave``ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="9b408-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="9b408-153">`TouchPoint` představuje jeden kontaktní bod na zařízení citlivém na dotykové ovládání.</span><span class="sxs-lookup"><span data-stu-id="9b408-153">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="9b408-154">Další informace najdete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="9b408-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="9b408-155">[Třídy EventArgs ve zdroji odkazů ASP.NET Core (větev dotnet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="9b408-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="9b408-156">[MDN web Docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; obsahuje informace o tom, které prvky HTML podporují jednotlivé události modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="9b408-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="9b408-157">Výrazy lambda</span><span class="sxs-lookup"><span data-stu-id="9b408-157">Lambda expressions</span></span>

<span data-ttu-id="9b408-158">[Lambda výrazy](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) lze také použít:</span><span class="sxs-lookup"><span data-stu-id="9b408-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="9b408-159">Je často vhodné uzavřít další hodnoty, jako například při iteraci přes sadu prvků.</span><span class="sxs-lookup"><span data-stu-id="9b408-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="9b408-160">Následující příklad vytvoří tři tlačítka, z nichž každá volá `UpdateHeading` předání argumentu události (`MouseEventArgs`) a jeho číslo tlačítka (`buttonNumber`), pokud je vybráno v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="9b408-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="9b408-161">Nepoužívejte **proměnnou** smyčky (`i`) ve `for` smyčce přímo ve výrazu lambda.</span><span class="sxs-lookup"><span data-stu-id="9b408-161">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="9b408-162">V opačném případě se stejná proměnná používá u všech výrazů lambda, které způsobují, že hodnota `i`být stejná ve všech výrazech lambda.</span><span class="sxs-lookup"><span data-stu-id="9b408-162">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="9b408-163">Vždycky zachytit svou hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak ji použít.</span><span class="sxs-lookup"><span data-stu-id="9b408-163">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="9b408-164">Vnořenou eventCallback</span><span class="sxs-lookup"><span data-stu-id="9b408-164">EventCallback</span></span>

<span data-ttu-id="9b408-165">Běžný scénář s vnořenými komponentami je přáním spustit metodu nadřazené komponenty, když dojde k události podřízené součásti&mdash;například při výskytu události `onclick` v podřízeném objektu.</span><span class="sxs-lookup"><span data-stu-id="9b408-165">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="9b408-166">Chcete-li zobrazit události napříč komponentami, použijte `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="9b408-166">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="9b408-167">Nadřazená komponenta může přiřadit metodu zpětného volání podřízené `EventCallback`komponenty.</span><span class="sxs-lookup"><span data-stu-id="9b408-167">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="9b408-168">`ChildComponent` v ukázkové aplikaci (*Components/ChildComponent. Razor*) ukazuje, jak je nastavená obslužná rutina `onclick` tlačítka pro příjem `EventCallback`ho delegáta z `ParentComponent`ukázky.</span><span class="sxs-lookup"><span data-stu-id="9b408-168">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="9b408-169">`EventCallback` se zadává pomocí `MouseEventArgs`, která je vhodná pro událost `onclick` z periferního zařízení:</span><span class="sxs-lookup"><span data-stu-id="9b408-169">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="9b408-170">`ParentComponent` nastaví `EventCallback<T>` (`OnClickCallback`) dítěte na svou `ShowMessage` metodu.</span><span class="sxs-lookup"><span data-stu-id="9b408-170">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="9b408-171">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="9b408-171">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="9b408-172">Když je vybráno tlačítko v `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="9b408-172">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="9b408-173">Je volána metoda `ShowMessage` `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="9b408-173">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="9b408-174">`_messageText` se aktualizuje a zobrazí v `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="9b408-174">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="9b408-175">Volání [StateHasChanged](xref:blazor/lifecycle#state-changes) není vyžadováno v metodě zpětného volání (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="9b408-175">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="9b408-176">`StateHasChanged` se zavolá automaticky, aby se `ParentComponent`znovu vykreslila, stejně jako podřízené události spouštějí revykreslování komponenty v obslužných rutinách události, které se spouštějí v rámci podřízeného objektu</span><span class="sxs-lookup"><span data-stu-id="9b408-176">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="9b408-177">`EventCallback` a `EventCallback<T>` povolují asynchronní delegáty.</span><span class="sxs-lookup"><span data-stu-id="9b408-177">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="9b408-178">`EventCallback<T>` je silného typu a vyžaduje konkrétní typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="9b408-178">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="9b408-179">`EventCallback` je slabě typované a umožňuje jakýkoli typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="9b408-179">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="9b408-180">Vyvolat `EventCallback` nebo `EventCallback<T>` s `InvokeAsync` a očekávat <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="9b408-180">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="9b408-181">Pro zpracování událostí a parametry komponenty vazby použijte `EventCallback` a `EventCallback<T>`.</span><span class="sxs-lookup"><span data-stu-id="9b408-181">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="9b408-182">Preferovat `EventCallback<T>` silného typu přes `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="9b408-182">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="9b408-183">`EventCallback<T>` poskytuje uživatelům součásti lepší zpětnou vazbu k chybám.</span><span class="sxs-lookup"><span data-stu-id="9b408-183">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="9b408-184">Podobně jako u jiných obslužných rutin událostí uživatelského rozhraní je zadání parametru události volitelné.</span><span class="sxs-lookup"><span data-stu-id="9b408-184">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="9b408-185">Použijte `EventCallback`, pokud není předána žádná hodnota zpětnému volání.</span><span class="sxs-lookup"><span data-stu-id="9b408-185">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="9b408-186">Zabránit výchozím akcím</span><span class="sxs-lookup"><span data-stu-id="9b408-186">Prevent default actions</span></span>

<span data-ttu-id="9b408-187">Chcete-li zabránit výchozí akci pro událost, použijte atribut direktiva [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) .</span><span class="sxs-lookup"><span data-stu-id="9b408-187">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="9b408-188">Když je vybraný klíč na vstupním zařízení a fokus prvku je v textovém poli, prohlížeč normálně zobrazuje znak klíče v textovém poli.</span><span class="sxs-lookup"><span data-stu-id="9b408-188">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="9b408-189">V následujícím příkladu je znemožněno výchozí chování zadáním atributu direktiva `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="9b408-189">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="9b408-190">Čítač zvýší a **+** klíč není zachycen do hodnoty `<input>` elementu:</span><span class="sxs-lookup"><span data-stu-id="9b408-190">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="9b408-191">Zadání atributu `@on{EVENT}:preventDefault` bez hodnoty je ekvivalentem `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="9b408-191">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="9b408-192">Hodnotou atributu může být také výraz.</span><span class="sxs-lookup"><span data-stu-id="9b408-192">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="9b408-193">V následujícím příkladu je `_shouldPreventDefault` `bool` pole nastaveno na hodnotu `true` nebo `false`:</span><span class="sxs-lookup"><span data-stu-id="9b408-193">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="9b408-194">Obslužná rutina události není nutná, aby se zabránilo výchozí akci.</span><span class="sxs-lookup"><span data-stu-id="9b408-194">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="9b408-195">Obslužná rutina události a zabraňuje použití výchozích akcí, lze použít nezávisle.</span><span class="sxs-lookup"><span data-stu-id="9b408-195">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="9b408-196">Zastavit šíření událostí</span><span class="sxs-lookup"><span data-stu-id="9b408-196">Stop event propagation</span></span>

<span data-ttu-id="9b408-197">Pro zastavení šíření události použijte atribut direktiva [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) .</span><span class="sxs-lookup"><span data-stu-id="9b408-197">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="9b408-198">V následujícím příkladu zaškrtnutí políčka zabrání kliknutí na události z druhého podřízeného `<div>` z rozšiřování do nadřazené `<div>`:</span><span class="sxs-lookup"><span data-stu-id="9b408-198">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
