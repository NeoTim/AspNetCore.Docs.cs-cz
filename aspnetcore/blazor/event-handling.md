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
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="16a6b-103">ASP.NET Zpracování událostí Core Blazor</span><span class="sxs-lookup"><span data-stu-id="16a6b-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="16a6b-104">[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="16a6b-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="16a6b-105">Komponenty Razor poskytují funkce zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="16a6b-105">Razor components provide event handling features.</span></span> <span data-ttu-id="16a6b-106">Pro atribut elementu [`@on{EVENT}`](xref:mvc/views/razor#onevent) HTML s `@onclick`názvem (například) s hodnotou typu delegáta komponenta Razor zachází s hodnotou atributu jako s obslužnou rutinou události.</span><span class="sxs-lookup"><span data-stu-id="16a6b-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="16a6b-107">Následující kód volá `UpdateHeading` metodu, když je tlačítko vybrané v ui:</span><span class="sxs-lookup"><span data-stu-id="16a6b-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="16a6b-108">Následující kód volá `CheckChanged` metodu při změně zaškrtávacího políčka v ui:</span><span class="sxs-lookup"><span data-stu-id="16a6b-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="16a6b-109">Obslužné rutiny událostí mohou <xref:System.Threading.Tasks.Task>být také asynchronní a vrátit .</span><span class="sxs-lookup"><span data-stu-id="16a6b-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="16a6b-110">Není třeba ručně volat [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="16a6b-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="16a6b-111">Výjimky jsou zaznamenány, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="16a6b-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="16a6b-112">V následujícím příkladu `UpdateHeading` se nazývá asynchronně, když je vybráno tlačítko:</span><span class="sxs-lookup"><span data-stu-id="16a6b-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="16a6b-113">Typy argumentů události</span><span class="sxs-lookup"><span data-stu-id="16a6b-113">Event argument types</span></span>

<span data-ttu-id="16a6b-114">U některých událostí jsou povoleny typy argumentů události.</span><span class="sxs-lookup"><span data-stu-id="16a6b-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="16a6b-115">Zadání typu události ve volání metody je nezbytné pouze v případě, že typ události je použit v metodě.</span><span class="sxs-lookup"><span data-stu-id="16a6b-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="16a6b-116">Podporované `EventArgs` jsou uvedeny v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="16a6b-116">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="16a6b-117">Událost</span><span class="sxs-lookup"><span data-stu-id="16a6b-117">Event</span></span>            | <span data-ttu-id="16a6b-118">Třída</span><span class="sxs-lookup"><span data-stu-id="16a6b-118">Class</span></span>                | <span data-ttu-id="16a6b-119">Události a poznámky dom</span><span class="sxs-lookup"><span data-stu-id="16a6b-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="16a6b-120">Schránka</span><span class="sxs-lookup"><span data-stu-id="16a6b-120">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="16a6b-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="16a6b-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="16a6b-122">Přetáhněte</span><span class="sxs-lookup"><span data-stu-id="16a6b-122">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="16a6b-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="16a6b-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="16a6b-124">`DataTransfer`a `DataTransferItem` podržte přetažená data položek.</span><span class="sxs-lookup"><span data-stu-id="16a6b-124">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="16a6b-125">Chyba</span><span class="sxs-lookup"><span data-stu-id="16a6b-125">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="16a6b-126">Událost</span><span class="sxs-lookup"><span data-stu-id="16a6b-126">Event</span></span>            | `EventArgs`          | <span data-ttu-id="16a6b-127">*Obecné*</span><span class="sxs-lookup"><span data-stu-id="16a6b-127">*General*</span></span><br><span data-ttu-id="16a6b-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="16a6b-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="16a6b-129">*Schránka*</span><span class="sxs-lookup"><span data-stu-id="16a6b-129">*Clipboard*</span></span><br><span data-ttu-id="16a6b-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="16a6b-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="16a6b-131">*Vstup*</span><span class="sxs-lookup"><span data-stu-id="16a6b-131">*Input*</span></span><br><span data-ttu-id="16a6b-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="16a6b-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="16a6b-133">*Média*</span><span class="sxs-lookup"><span data-stu-id="16a6b-133">*Media*</span></span><br><span data-ttu-id="16a6b-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="16a6b-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="16a6b-135">Zaměření</span><span class="sxs-lookup"><span data-stu-id="16a6b-135">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="16a6b-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="16a6b-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="16a6b-137">Nezahrnuje podporu pro `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="16a6b-137">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="16a6b-138">Vstup</span><span class="sxs-lookup"><span data-stu-id="16a6b-138">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="16a6b-139">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="16a6b-139">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="16a6b-140">Klávesnice</span><span class="sxs-lookup"><span data-stu-id="16a6b-140">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="16a6b-141">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="16a6b-141">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="16a6b-142">Myš</span><span class="sxs-lookup"><span data-stu-id="16a6b-142">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="16a6b-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="16a6b-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="16a6b-144">Ukazatel myši</span><span class="sxs-lookup"><span data-stu-id="16a6b-144">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="16a6b-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="16a6b-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="16a6b-146">Kolečko myši</span><span class="sxs-lookup"><span data-stu-id="16a6b-146">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="16a6b-147">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="16a6b-147">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="16a6b-148">Průběh</span><span class="sxs-lookup"><span data-stu-id="16a6b-148">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="16a6b-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="16a6b-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="16a6b-150">Dotykové ovládání</span><span class="sxs-lookup"><span data-stu-id="16a6b-150">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="16a6b-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="16a6b-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="16a6b-152">`TouchPoint`představuje jediné kontaktní místo na zařízení citlivém na dotyk.</span><span class="sxs-lookup"><span data-stu-id="16a6b-152">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="16a6b-153">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="16a6b-153">For more information, see the following resources:</span></span>

* <span data-ttu-id="16a6b-154">[EventArgs třídy v ASP.NET základní referenční zdroj (dotnet/aspnetcore release/3.1 větev)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="16a6b-154">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="16a6b-155">[MDN webové dokumenty: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Obsahuje informace o tom, které prvky HTML podporují každou událost DOM.</span><span class="sxs-lookup"><span data-stu-id="16a6b-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="16a6b-156">Výrazy lambda</span><span class="sxs-lookup"><span data-stu-id="16a6b-156">Lambda expressions</span></span>

<span data-ttu-id="16a6b-157">[Lambda výrazy](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) mohou být také použity:</span><span class="sxs-lookup"><span data-stu-id="16a6b-157">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="16a6b-158">Často je vhodné zavřít přes další hodnoty, například při itaci přes sadu prvků.</span><span class="sxs-lookup"><span data-stu-id="16a6b-158">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="16a6b-159">Následující příklad vytvoří tři tlačítka, `UpdateHeading` z nichž každé`MouseEventArgs`volá předávání argumentu události ( ) a jeho číslo tlačítka (`buttonNumber`) při výběru v ui:</span><span class="sxs-lookup"><span data-stu-id="16a6b-159">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="16a6b-160">**Nepoužívejte** proměnnou smyčky`i`( `for` ) ve smyčce přímo ve výrazu lambda.</span><span class="sxs-lookup"><span data-stu-id="16a6b-160">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="16a6b-161">V opačném případě je stejná proměnná použita všemi výrazy lambda, které způsobují, `i`že hodnota společnosti je stejná ve všech lambdách.</span><span class="sxs-lookup"><span data-stu-id="16a6b-161">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="16a6b-162">Vždy zachyťte jeho`buttonNumber` hodnotu v místní proměnné (v předchozím příkladu) a pak ji použijte.</span><span class="sxs-lookup"><span data-stu-id="16a6b-162">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="16a6b-163">EventCallback</span><span class="sxs-lookup"><span data-stu-id="16a6b-163">EventCallback</span></span>

<span data-ttu-id="16a6b-164">Běžný scénář s vnořenými součástmi je touha spustit metodu nadřazené součásti, když dojde k události podřízené součásti,&mdash;například když dojde k `onclick` události v podřízeném objektu.</span><span class="sxs-lookup"><span data-stu-id="16a6b-164">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="16a6b-165">Chcete-li vystavit události napříč součástmi, použijte `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="16a6b-165">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="16a6b-166">Nadřazená komponenta může přiřadit metodu `EventCallback`zpětného volání k podřízené součásti .</span><span class="sxs-lookup"><span data-stu-id="16a6b-166">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="16a6b-167">V `ChildComponent` ukázkové aplikaci *(Components/ChildComponent.razor)* ukazuje, `onclick` jak je nastavena `EventCallback` obslužná `ParentComponent`rutina tlačítka pro příjem delegáta z ukázky .</span><span class="sxs-lookup"><span data-stu-id="16a6b-167">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="16a6b-168">Je `EventCallback` zadán s `MouseEventArgs`, který je `onclick` vhodný pro událost z periferního zařízení:</span><span class="sxs-lookup"><span data-stu-id="16a6b-168">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="16a6b-169">Nastaví `ParentComponent` způsob `EventCallback<T>` dítěte`OnClickCallback`( ). `ShowMessage`</span><span class="sxs-lookup"><span data-stu-id="16a6b-169">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="16a6b-170">*Stránky/ParentComponent.razor*:</span><span class="sxs-lookup"><span data-stu-id="16a6b-170">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="16a6b-171">Když je tlačítko vybráno v : `ChildComponent`</span><span class="sxs-lookup"><span data-stu-id="16a6b-171">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="16a6b-172">Je `ParentComponent`volána metoda 's. `ShowMessage`</span><span class="sxs-lookup"><span data-stu-id="16a6b-172">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="16a6b-173">`_messageText`je aktualizována a `ParentComponent`zobrazena v .</span><span class="sxs-lookup"><span data-stu-id="16a6b-173">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="16a6b-174">Volání [StateHasChanged](xref:blazor/lifecycle#state-changes) není vyžadováno v metodě zpětného`ShowMessage`volání ( ).</span><span class="sxs-lookup"><span data-stu-id="16a6b-174">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="16a6b-175">`StateHasChanged`je volána automaticky `ParentComponent`překreslit , stejně jako podřízené události aktivují opětovné vykreslení komponenty v obslužných rutinách událostí, které se spouštějí v rámci podřízeného.</span><span class="sxs-lookup"><span data-stu-id="16a6b-175">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="16a6b-176">`EventCallback`a `EventCallback<T>` povolit asynchronní delegáty.</span><span class="sxs-lookup"><span data-stu-id="16a6b-176">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="16a6b-177">`EventCallback<T>`je silně zadán a vyžaduje konkrétní typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="16a6b-177">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="16a6b-178">`EventCallback`je slabě zadaný a umožňuje libovolný typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="16a6b-178">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="16a6b-179">Vyvolat `EventCallback` nebo `EventCallback<T>` `InvokeAsync` s a <xref:System.Threading.Tasks.Task>čekat na :</span><span class="sxs-lookup"><span data-stu-id="16a6b-179">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="16a6b-180">Použití `EventCallback` `EventCallback<T>` a pro zpracování událostí a parametry součásti vazby.</span><span class="sxs-lookup"><span data-stu-id="16a6b-180">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="16a6b-181">Preferujte silně `EventCallback<T>` zadaný `EventCallback`přes .</span><span class="sxs-lookup"><span data-stu-id="16a6b-181">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="16a6b-182">`EventCallback<T>`poskytuje uživatelům komponenty lepší zpětnou vazbu k chybám.</span><span class="sxs-lookup"><span data-stu-id="16a6b-182">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="16a6b-183">Podobně jako u jiných obslužných rutin událostí rozhraní je zadání parametru události volitelné.</span><span class="sxs-lookup"><span data-stu-id="16a6b-183">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="16a6b-184">Použijte, `EventCallback` pokud není předána žádná hodnota zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="16a6b-184">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="16a6b-185">Zabránit výchozím akcím</span><span class="sxs-lookup"><span data-stu-id="16a6b-185">Prevent default actions</span></span>

<span data-ttu-id="16a6b-186">Pomocí [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) atributu direktivy zabraňte výchozí akci události.</span><span class="sxs-lookup"><span data-stu-id="16a6b-186">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="16a6b-187">Když je na vstupním zařízení vybrána klávesa a fokus prvku je na textovém poli, prohlížeč obvykle zobrazí znak klíče v textovém poli.</span><span class="sxs-lookup"><span data-stu-id="16a6b-187">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="16a6b-188">V následujícím příkladu je výchozí chování zabráněno `@onkeypress:preventDefault` zadáním atributu direktivy.</span><span class="sxs-lookup"><span data-stu-id="16a6b-188">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="16a6b-189">Čítač se zíhá a **+** klíč není `<input>` zachycen do hodnoty prvku:</span><span class="sxs-lookup"><span data-stu-id="16a6b-189">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="16a6b-190">Zadání atributu `@on{EVENT}:preventDefault` bez hodnoty je `@on{EVENT}:preventDefault="true"`ekvivalentní .</span><span class="sxs-lookup"><span data-stu-id="16a6b-190">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="16a6b-191">Hodnota atributu může být také výraz.</span><span class="sxs-lookup"><span data-stu-id="16a6b-191">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="16a6b-192">V `_shouldPreventDefault` následujícím příkladu `bool` je pole `true` nastaveno na jedno nebo `false`:</span><span class="sxs-lookup"><span data-stu-id="16a6b-192">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="16a6b-193">Obslužná rutina události není nutné zabránit výchozí akci.</span><span class="sxs-lookup"><span data-stu-id="16a6b-193">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="16a6b-194">Obslužnou rutinu události a zabránit výchozí scénáře akce lze použít nezávisle.</span><span class="sxs-lookup"><span data-stu-id="16a6b-194">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="16a6b-195">Zastavit šíření událostí</span><span class="sxs-lookup"><span data-stu-id="16a6b-195">Stop event propagation</span></span>

<span data-ttu-id="16a6b-196">Pomocí [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) atributu direktiva zastavte šíření událostí.</span><span class="sxs-lookup"><span data-stu-id="16a6b-196">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="16a6b-197">V následujícím příkladu zabráníte zaškrtnutí políčka, aby `<div>` se události z druhého `<div>`podřízeného objektu nešířily do nadřazeného objektu :</span><span class="sxs-lookup"><span data-stu-id="16a6b-197">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
