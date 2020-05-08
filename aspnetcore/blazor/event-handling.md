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
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="d9107-103">Zpracování událostí ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d9107-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="d9107-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d9107-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="d9107-105">Komponenty Razor poskytují funkce pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="d9107-105">Razor components provide event handling features.</span></span> <span data-ttu-id="d9107-106">Pro atribut elementu HTML s názvem [`@on{EVENT}`](xref:mvc/views/razor#onevent) (například `@onclick`) s hodnotou typu delegáta, komponenta Razor považuje hodnotu atributu za obslužnou rutinu události.</span><span class="sxs-lookup"><span data-stu-id="d9107-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="d9107-107">Následující kód volá `UpdateHeading` metodu, pokud je vybráno tlačítko v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="d9107-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="d9107-108">Následující kód volá `CheckChanged` metodu, když je zaškrtávací políčko změněno v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="d9107-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="d9107-109">Obslužné rutiny událostí mohou být také asynchronní a <xref:System.Threading.Tasks.Task>vracet.</span><span class="sxs-lookup"><span data-stu-id="d9107-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="d9107-110">Není nutné ručně volat [StateHasChanged](xref:blazor/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="d9107-110">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="d9107-111">Výjimky jsou protokolovány, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="d9107-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="d9107-112">V následujícím příkladu `UpdateHeading` se volá asynchronně po výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="d9107-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="d9107-113">Typy argumentů události</span><span class="sxs-lookup"><span data-stu-id="d9107-113">Event argument types</span></span>

<span data-ttu-id="d9107-114">U některých událostí jsou povoleny typy argumentů události.</span><span class="sxs-lookup"><span data-stu-id="d9107-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="d9107-115">Zadání typu události ve volání metody je nezbytné pouze v případě, že je typ události použit v metodě.</span><span class="sxs-lookup"><span data-stu-id="d9107-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="d9107-116">Podporované `EventArgs` jsou uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="d9107-116">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="d9107-117">Událost</span><span class="sxs-lookup"><span data-stu-id="d9107-117">Event</span></span>            | <span data-ttu-id="d9107-118">Třída</span><span class="sxs-lookup"><span data-stu-id="d9107-118">Class</span></span>                | <span data-ttu-id="d9107-119">Události a poznámky modelu DOM</span><span class="sxs-lookup"><span data-stu-id="d9107-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="d9107-120">Schránka</span><span class="sxs-lookup"><span data-stu-id="d9107-120">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="d9107-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="d9107-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="d9107-122">Myší</span><span class="sxs-lookup"><span data-stu-id="d9107-122">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="d9107-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="d9107-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="d9107-124">`DataTransfer`a `DataTransferItem` uchovávají přetažená data položky.</span><span class="sxs-lookup"><span data-stu-id="d9107-124">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="d9107-125">Chyba</span><span class="sxs-lookup"><span data-stu-id="d9107-125">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="d9107-126">Událost</span><span class="sxs-lookup"><span data-stu-id="d9107-126">Event</span></span>            | `EventArgs`          | <span data-ttu-id="d9107-127">*Obecné*</span><span class="sxs-lookup"><span data-stu-id="d9107-127">*General*</span></span><br><span data-ttu-id="d9107-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="d9107-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="d9107-129">*Schránka*</span><span class="sxs-lookup"><span data-stu-id="d9107-129">*Clipboard*</span></span><br><span data-ttu-id="d9107-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="d9107-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="d9107-131">*Vstup*</span><span class="sxs-lookup"><span data-stu-id="d9107-131">*Input*</span></span><br><span data-ttu-id="d9107-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="d9107-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="d9107-133">*Média*</span><span class="sxs-lookup"><span data-stu-id="d9107-133">*Media*</span></span><br><span data-ttu-id="d9107-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="d9107-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="d9107-135">Vybrána</span><span class="sxs-lookup"><span data-stu-id="d9107-135">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="d9107-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="d9107-136">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="d9107-137">Nezahrnuje podporu pro `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="d9107-137">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="d9107-138">Vstup</span><span class="sxs-lookup"><span data-stu-id="d9107-138">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="d9107-139">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="d9107-139">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="d9107-140">Klávesnice</span><span class="sxs-lookup"><span data-stu-id="d9107-140">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="d9107-141">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="d9107-141">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="d9107-142">Myš</span><span class="sxs-lookup"><span data-stu-id="d9107-142">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="d9107-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="d9107-143">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="d9107-144">Ukazatel myši</span><span class="sxs-lookup"><span data-stu-id="d9107-144">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="d9107-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="d9107-145">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="d9107-146">Kolečko myši</span><span class="sxs-lookup"><span data-stu-id="d9107-146">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="d9107-147">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="d9107-147">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="d9107-148">Průběh</span><span class="sxs-lookup"><span data-stu-id="d9107-148">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="d9107-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="d9107-149">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="d9107-150">Dotykové ovládání</span><span class="sxs-lookup"><span data-stu-id="d9107-150">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="d9107-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="d9107-151">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="d9107-152">`TouchPoint`představuje jeden kontaktní bod na zařízení citlivém na dotykové ovládání.</span><span class="sxs-lookup"><span data-stu-id="d9107-152">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="d9107-153">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="d9107-153">For more information, see the following resources:</span></span>

* <span data-ttu-id="d9107-154">[Třídy EventArgs ve zdroji odkazů ASP.NET Core (větev dotnet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="d9107-154">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="d9107-155">[MDN web Docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; obsahuje informace o tom, které prvky HTML podporují jednotlivé události modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="d9107-155">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="d9107-156">Výrazy lambda</span><span class="sxs-lookup"><span data-stu-id="d9107-156">Lambda expressions</span></span>

<span data-ttu-id="d9107-157">[Lambda výrazy](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) lze také použít:</span><span class="sxs-lookup"><span data-stu-id="d9107-157">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="d9107-158">Je často vhodné uzavřít další hodnoty, jako například při iteraci přes sadu prvků.</span><span class="sxs-lookup"><span data-stu-id="d9107-158">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="d9107-159">Následující příklad vytvoří tři tlačítka, z nichž každé volá `UpdateHeading` předání argumentu události (`MouseEventArgs`) a jeho čísla tlačítka (`buttonNumber`), pokud je vybráno v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="d9107-159">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="d9107-160">Nepoužívejte **proměnnou** smyčky (`i`) ve `for` smyčce přímo ve výrazu lambda.</span><span class="sxs-lookup"><span data-stu-id="d9107-160">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="d9107-161">V opačném případě se stejná proměnná používá ve všech výrazech lambda, což způsobuje `i`, že hodnota je stejná ve všech výrazech lambda.</span><span class="sxs-lookup"><span data-stu-id="d9107-161">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="d9107-162">Vždycky zachytit svou hodnotu v místní proměnné (`buttonNumber` v předchozím příkladu) a pak ji použít.</span><span class="sxs-lookup"><span data-stu-id="d9107-162">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="d9107-163">Vnořenou eventCallback</span><span class="sxs-lookup"><span data-stu-id="d9107-163">EventCallback</span></span>

<span data-ttu-id="d9107-164">Běžný scénář s vnořenými komponentami je přáním spustit metodu nadřazené komponenty, když dojde&mdash;k události podřízené komponenty, například když dojde k události `onclick` v podřízeném objektu.</span><span class="sxs-lookup"><span data-stu-id="d9107-164">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="d9107-165">Chcete-li zobrazit události napříč komponentami `EventCallback`, použijte.</span><span class="sxs-lookup"><span data-stu-id="d9107-165">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="d9107-166">Nadřazená komponenta může přiřadit metodu zpětného volání podřízené součásti `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="d9107-166">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="d9107-167">`ChildComponent` V ukázkové aplikaci (*Components/ChildComponent. Razor*) ukazuje, jak je nastavena `onclick` obslužná rutina tlačítka pro příjem `EventCallback` delegáta z ukázky. `ParentComponent`</span><span class="sxs-lookup"><span data-stu-id="d9107-167">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="d9107-168">`EventCallback` Je zadaný s `MouseEventArgs`, který je vhodný pro `onclick` událost z periferního zařízení:</span><span class="sxs-lookup"><span data-stu-id="d9107-168">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="d9107-169">`ParentComponent` Nastaví `EventCallback<T>` (`OnClickCallback`) dítěte na jeho `ShowMessage` metodu.</span><span class="sxs-lookup"><span data-stu-id="d9107-169">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="d9107-170">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="d9107-170">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="d9107-171">Když je vybráno tlačítko v `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="d9107-171">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="d9107-172">`ParentComponent`Je volána `ShowMessage` metoda.</span><span class="sxs-lookup"><span data-stu-id="d9107-172">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="d9107-173">`messageText`se aktualizuje a zobrazí v `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="d9107-173">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="d9107-174">V metodě zpětného volání (`ShowMessage`) není vyžadováno volání [StateHasChanged](xref:blazor/lifecycle#state-changes) .</span><span class="sxs-lookup"><span data-stu-id="d9107-174">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="d9107-175">`StateHasChanged`je volána automaticky pro revykreslování `ParentComponent`, stejně jako podřízené události, které aktivují revykreslování komponenty v obslužných rutinách události, které jsou spouštěny v rámci podřízeného objektu.</span><span class="sxs-lookup"><span data-stu-id="d9107-175">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="d9107-176">`EventCallback`a `EventCallback<T>` povolují asynchronní delegáty.</span><span class="sxs-lookup"><span data-stu-id="d9107-176">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="d9107-177">`EventCallback<T>`je silného typu a vyžaduje konkrétní typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="d9107-177">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="d9107-178">`EventCallback`je slabě typované a umožňuje jakýkoli typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="d9107-178">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="d9107-179">Vyvolat `EventCallback` nebo `EventCallback<T>` s `InvokeAsync` a očekávat: <xref:System.Threading.Tasks.Task></span><span class="sxs-lookup"><span data-stu-id="d9107-179">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="d9107-180">Použití `EventCallback` a `EventCallback<T>` pro zpracování událostí a parametry komponenty vazby.</span><span class="sxs-lookup"><span data-stu-id="d9107-180">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="d9107-181">Preferovat silného typu `EventCallback<T>` přes `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="d9107-181">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="d9107-182">`EventCallback<T>`poskytuje lepší odezvu na chyby uživatelů součásti.</span><span class="sxs-lookup"><span data-stu-id="d9107-182">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="d9107-183">Podobně jako u jiných obslužných rutin událostí uživatelského rozhraní je zadání parametru události volitelné.</span><span class="sxs-lookup"><span data-stu-id="d9107-183">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="d9107-184">Použijte `EventCallback` v případě, že zpětnému volání není předáno žádné číslo.</span><span class="sxs-lookup"><span data-stu-id="d9107-184">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="d9107-185">Zabránit výchozím akcím</span><span class="sxs-lookup"><span data-stu-id="d9107-185">Prevent default actions</span></span>

<span data-ttu-id="d9107-186">Chcete- [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) li zabránit výchozí akci pro událost, použijte atribut direktiva.</span><span class="sxs-lookup"><span data-stu-id="d9107-186">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="d9107-187">Když je vybraný klíč na vstupním zařízení a fokus prvku je v textovém poli, prohlížeč normálně zobrazuje znak klíče v textovém poli.</span><span class="sxs-lookup"><span data-stu-id="d9107-187">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="d9107-188">V následujícím příkladu je výchozím chováním znemožněno zadáním atributu `@onkeypress:preventDefault` direktiva.</span><span class="sxs-lookup"><span data-stu-id="d9107-188">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="d9107-189">Čítač zvýší a **+** klíč není zachycen do hodnoty `<input>` prvku:</span><span class="sxs-lookup"><span data-stu-id="d9107-189">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="d9107-190">Určení `@on{EVENT}:preventDefault` atributu bez hodnoty je ekvivalentní `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="d9107-190">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="d9107-191">Hodnotou atributu může být také výraz.</span><span class="sxs-lookup"><span data-stu-id="d9107-191">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="d9107-192">`shouldPreventDefault` V následujícím příkladu `bool` je pole nastaveno na buď `true` nebo: `false`</span><span class="sxs-lookup"><span data-stu-id="d9107-192">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="d9107-193">Obslužná rutina události není nutná, aby se zabránilo výchozí akci.</span><span class="sxs-lookup"><span data-stu-id="d9107-193">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="d9107-194">Obslužná rutina události a zabraňuje použití výchozích akcí, lze použít nezávisle.</span><span class="sxs-lookup"><span data-stu-id="d9107-194">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="d9107-195">Zastavit šíření událostí</span><span class="sxs-lookup"><span data-stu-id="d9107-195">Stop event propagation</span></span>

<span data-ttu-id="d9107-196">Pro zastavení [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) šíření události použijte atribut direktiva.</span><span class="sxs-lookup"><span data-stu-id="d9107-196">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="d9107-197">V následujícím příkladu zaškrtnutí políčka zabrání kliknutí na události z druhého podřízeného `<div>` objektu pro rozšíření na nadřazený: `<div>`</span><span class="sxs-lookup"><span data-stu-id="d9107-197">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
