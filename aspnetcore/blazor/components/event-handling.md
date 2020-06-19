---
title: BlazorZpracování událostí ASP.NET Core
author: guardrex
description: Přečtěte si o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných voláních událostí a správě výchozích událostí prohlížeče.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/event-handling
ms.openlocfilehash: 32f7595cffc2c31116c8d876c9f9526b84c52f14
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103724"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="6c85a-103">BlazorZpracování událostí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6c85a-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="6c85a-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6c85a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="6c85a-105">komponenty poskytují funkce pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="6c85a-105"> components provide event handling features.</span></span> <span data-ttu-id="6c85a-106">Pro atribut elementu HTML s názvem [`@on{EVENT}`](xref:mvc/views/razor#onevent) (například `@onclick` ) s hodnotou delegovaný typ Razor Komponenta považuje hodnotu atributu za obslužnou rutinu události.</span><span class="sxs-lookup"><span data-stu-id="6c85a-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="6c85a-107">Následující kód volá metodu, `UpdateHeading` Pokud je vybráno tlačítko v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="6c85a-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="6c85a-108">Následující kód volá metodu, `CheckChanged` když je zaškrtávací políčko změněno v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="6c85a-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="6c85a-109">Obslužné rutiny událostí mohou být také asynchronní a vracet <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="6c85a-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="6c85a-110">Není nutné ručně volat [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="6c85a-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="6c85a-111">Výjimky jsou protokolovány, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="6c85a-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="6c85a-112">V následujícím příkladu `UpdateHeading` se volá asynchronně po výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="6c85a-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="6c85a-113">Typy argumentů události</span><span class="sxs-lookup"><span data-stu-id="6c85a-113">Event argument types</span></span>

<span data-ttu-id="6c85a-114">U některých událostí jsou povoleny typy argumentů události.</span><span class="sxs-lookup"><span data-stu-id="6c85a-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="6c85a-115">Zadání typu události ve volání metody je nezbytné pouze v případě, že je typ události použit v metodě.</span><span class="sxs-lookup"><span data-stu-id="6c85a-115">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="6c85a-116">Podporované <xref:System.EventArgs> jsou uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="6c85a-116">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

| <span data-ttu-id="6c85a-117">Událost</span><span class="sxs-lookup"><span data-stu-id="6c85a-117">Event</span></span>            | <span data-ttu-id="6c85a-118">Třída</span><span class="sxs-lookup"><span data-stu-id="6c85a-118">Class</span></span>                | <span data-ttu-id="6c85a-119">Události a poznámky modelu DOM</span><span class="sxs-lookup"><span data-stu-id="6c85a-119">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="6c85a-120">Schránka</span><span class="sxs-lookup"><span data-stu-id="6c85a-120">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="6c85a-121">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="6c85a-121">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="6c85a-122">Myší</span><span class="sxs-lookup"><span data-stu-id="6c85a-122">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="6c85a-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="6c85a-123">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="6c85a-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer>a <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> uchovávají přetažená data položky.</span><span class="sxs-lookup"><span data-stu-id="6c85a-124"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span> |
| <span data-ttu-id="6c85a-125">Chyba</span><span class="sxs-lookup"><span data-stu-id="6c85a-125">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="6c85a-126">Událost</span><span class="sxs-lookup"><span data-stu-id="6c85a-126">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="6c85a-127">*Obecné*</span><span class="sxs-lookup"><span data-stu-id="6c85a-127">*General*</span></span><br><span data-ttu-id="6c85a-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="6c85a-128">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="6c85a-129">*Schránka*</span><span class="sxs-lookup"><span data-stu-id="6c85a-129">*Clipboard*</span></span><br><span data-ttu-id="6c85a-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="6c85a-130">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="6c85a-131">*Vstup*</span><span class="sxs-lookup"><span data-stu-id="6c85a-131">*Input*</span></span><br><span data-ttu-id="6c85a-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span><span class="sxs-lookup"><span data-stu-id="6c85a-132">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit></span></span><br><br><span data-ttu-id="6c85a-133">*Média*</span><span class="sxs-lookup"><span data-stu-id="6c85a-133">*Media*</span></span><br><span data-ttu-id="6c85a-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="6c85a-134">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="6c85a-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers>obsahuje atributy pro konfiguraci mapování mezi názvy událostí a typy argumentů události.</span><span class="sxs-lookup"><span data-stu-id="6c85a-135"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="6c85a-136">Vybrána</span><span class="sxs-lookup"><span data-stu-id="6c85a-136">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="6c85a-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="6c85a-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="6c85a-138">Nezahrnuje podporu pro `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="6c85a-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="6c85a-139">Vstup</span><span class="sxs-lookup"><span data-stu-id="6c85a-139">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="6c85a-140">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="6c85a-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="6c85a-141">Klávesnice</span><span class="sxs-lookup"><span data-stu-id="6c85a-141">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="6c85a-142">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="6c85a-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="6c85a-143">Myš</span><span class="sxs-lookup"><span data-stu-id="6c85a-143">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="6c85a-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="6c85a-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="6c85a-145">Ukazatel myši</span><span class="sxs-lookup"><span data-stu-id="6c85a-145">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="6c85a-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="6c85a-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="6c85a-147">Kolečko myši</span><span class="sxs-lookup"><span data-stu-id="6c85a-147">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="6c85a-148">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="6c85a-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="6c85a-149">Průběh</span><span class="sxs-lookup"><span data-stu-id="6c85a-149">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="6c85a-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="6c85a-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="6c85a-151">Dotykové ovládání</span><span class="sxs-lookup"><span data-stu-id="6c85a-151">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="6c85a-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="6c85a-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="6c85a-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint>představuje jeden kontaktní bod na zařízení citlivém na dotykové ovládání.</span><span class="sxs-lookup"><span data-stu-id="6c85a-153"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="6c85a-154">Další informace najdete v následujících materiálech:</span><span class="sxs-lookup"><span data-stu-id="6c85a-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="6c85a-155">[Třídy EventArgs ve zdroji odkazů ASP.NET Core (větev dotnet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="6c85a-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="6c85a-156">[Webové dokumenty MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): obsahuje informace o tom, které prvky HTML podporují jednotlivé události modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="6c85a-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="6c85a-157">Výrazy lambda</span><span class="sxs-lookup"><span data-stu-id="6c85a-157">Lambda expressions</span></span>

<span data-ttu-id="6c85a-158">[Lambda výrazy](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) lze také použít:</span><span class="sxs-lookup"><span data-stu-id="6c85a-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="6c85a-159">Je často vhodné uzavřít další hodnoty, jako například při iteraci přes sadu prvků.</span><span class="sxs-lookup"><span data-stu-id="6c85a-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="6c85a-160">Následující příklad vytvoří tři tlačítka, z nichž každé volá `UpdateHeading` předání argumentu události ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) a jeho čísla tlačítka ( `buttonNumber` ), pokud je VYBRÁNO v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="6c85a-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="6c85a-161">Nepoužívejte **proměnnou** smyčky přímo ve výrazu lambda, například `i` v předchozím `for` příkladu smyčky nebo v referenční proměnné ve `foreach` smyčce.</span><span class="sxs-lookup"><span data-stu-id="6c85a-161">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example or a reference variable in a `foreach` loop.</span></span> <span data-ttu-id="6c85a-162">V opačném případě se stejná proměnná používá ve všech výrazech lambda, což má za následek použití stejné hodnoty ve všech výrazech lambda.</span><span class="sxs-lookup"><span data-stu-id="6c85a-162">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="6c85a-163">Vždy Zachyťte hodnotu proměnné v místní proměnné a pak ji použijte.</span><span class="sxs-lookup"><span data-stu-id="6c85a-163">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="6c85a-164">V předchozím příkladu `i` je přiřazena proměnná smyčky `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="6c85a-164">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="6c85a-165">Vnořenou eventCallback</span><span class="sxs-lookup"><span data-stu-id="6c85a-165">EventCallback</span></span>

<span data-ttu-id="6c85a-166">Běžný scénář s vnořenými komponentami je přáním spustit metodu nadřazené komponenty, když dojde k události podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="6c85a-166">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="6c85a-167">`onclick`Událost, ke které dochází v podřízené součásti, je běžným případem použití.</span><span class="sxs-lookup"><span data-stu-id="6c85a-167">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="6c85a-168">Chcete-li zobrazit události napříč komponentami, použijte <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="6c85a-168">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="6c85a-169">Nadřazená komponenta může přiřadit metodu zpětného volání podřízené součásti <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="6c85a-169">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="6c85a-170">`ChildComponent`V ukázkové aplikaci (*Components/ChildComponent. Razor*) ukazuje, jak `onclick` je nastavena obslužná rutina tlačítka pro příjem <xref:Microsoft.AspNetCore.Components.EventCallback> delegáta z ukázky `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="6c85a-170">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="6c85a-171"><xref:Microsoft.AspNetCore.Components.EventCallback>Je zadaný s `MouseEventArgs` , který je vhodný pro `onclick` událost z periferního zařízení:</span><span class="sxs-lookup"><span data-stu-id="6c85a-171">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="6c85a-172">`ParentComponent`Nastaví <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) dítěte na jeho `ShowMessage` metodu.</span><span class="sxs-lookup"><span data-stu-id="6c85a-172">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="6c85a-173">*Stránky/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="6c85a-173">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="6c85a-174">Když je vybráno tlačítko v `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="6c85a-174">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="6c85a-175">`ParentComponent` `ShowMessage` Je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="6c85a-175">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="6c85a-176">`messageText`se aktualizuje a zobrazí v `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="6c85a-176">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="6c85a-177">V metodě zpětného volání () není vyžadováno volání [StateHasChanged](xref:blazor/components/lifecycle#state-changes) `ShowMessage` .</span><span class="sxs-lookup"><span data-stu-id="6c85a-177">A call to [StateHasChanged](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="6c85a-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>je volána automaticky pro revykreslování `ParentComponent` , stejně jako podřízené události, které aktivují revykreslování komponenty v obslužných rutinách události, které jsou spouštěny v rámci podřízeného objektu.</span><span class="sxs-lookup"><span data-stu-id="6c85a-178"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="6c85a-179"><xref:Microsoft.AspNetCore.Components.EventCallback>a <xref:Microsoft.AspNetCore.Components.EventCallback%601> povolují asynchronní delegáty.</span><span class="sxs-lookup"><span data-stu-id="6c85a-179"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="6c85a-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601>je silného typu a vyžaduje konkrétní typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="6c85a-180"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="6c85a-181"><xref:Microsoft.AspNetCore.Components.EventCallback>je slabě typované a umožňuje jakýkoli typ argumentu.</span><span class="sxs-lookup"><span data-stu-id="6c85a-181"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="6c85a-182">Vyvolat <xref:Microsoft.AspNetCore.Components.EventCallback> nebo <xref:Microsoft.AspNetCore.Components.EventCallback%601> s <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> a očekávat <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="6c85a-182">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="6c85a-183">Použití <xref:Microsoft.AspNetCore.Components.EventCallback> a <xref:Microsoft.AspNetCore.Components.EventCallback%601> pro zpracování událostí a parametry komponenty vazby.</span><span class="sxs-lookup"><span data-stu-id="6c85a-183">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="6c85a-184">Preferovat silného typu <xref:Microsoft.AspNetCore.Components.EventCallback%601> přes <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="6c85a-184">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="6c85a-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601>poskytuje lepší odezvu na chyby uživatelů součásti.</span><span class="sxs-lookup"><span data-stu-id="6c85a-185"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="6c85a-186">Podobně jako u jiných obslužných rutin událostí uživatelského rozhraní je zadání parametru události volitelné.</span><span class="sxs-lookup"><span data-stu-id="6c85a-186">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="6c85a-187">Použijte <xref:Microsoft.AspNetCore.Components.EventCallback> v případě, že zpětnému volání není předáno žádné číslo.</span><span class="sxs-lookup"><span data-stu-id="6c85a-187">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="6c85a-188">Zabránit výchozím akcím</span><span class="sxs-lookup"><span data-stu-id="6c85a-188">Prevent default actions</span></span>

<span data-ttu-id="6c85a-189">[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault)Chcete-li zabránit výchozí akci pro událost, použijte atribut direktiva.</span><span class="sxs-lookup"><span data-stu-id="6c85a-189">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="6c85a-190">Když je vybraný klíč na vstupním zařízení a fokus prvku je v textovém poli, prohlížeč normálně zobrazuje znak klíče v textovém poli.</span><span class="sxs-lookup"><span data-stu-id="6c85a-190">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="6c85a-191">V následujícím příkladu je výchozím chováním znemožněno zadáním `@onkeypress:preventDefault` atributu direktiva.</span><span class="sxs-lookup"><span data-stu-id="6c85a-191">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="6c85a-192">Čítač zvýší a **+** klíč není zachycen do `<input>` hodnoty prvku:</span><span class="sxs-lookup"><span data-stu-id="6c85a-192">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="6c85a-193">Určení `@on{EVENT}:preventDefault` atributu bez hodnoty je ekvivalentní `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="6c85a-193">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="6c85a-194">Hodnotou atributu může být také výraz.</span><span class="sxs-lookup"><span data-stu-id="6c85a-194">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="6c85a-195">V následujícím příkladu `shouldPreventDefault` je `bool` pole nastaveno na buď `true` nebo `false` :</span><span class="sxs-lookup"><span data-stu-id="6c85a-195">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

<span data-ttu-id="6c85a-196">Obslužná rutina události není nutná, aby se zabránilo výchozí akci.</span><span class="sxs-lookup"><span data-stu-id="6c85a-196">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="6c85a-197">Obslužná rutina události a zabraňuje použití výchozích akcí, lze použít nezávisle.</span><span class="sxs-lookup"><span data-stu-id="6c85a-197">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="6c85a-198">Zastavit šíření událostí</span><span class="sxs-lookup"><span data-stu-id="6c85a-198">Stop event propagation</span></span>

<span data-ttu-id="6c85a-199">[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation)Pro zastavení šíření události použijte atribut direktiva.</span><span class="sxs-lookup"><span data-stu-id="6c85a-199">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="6c85a-200">V následujícím příkladu zaškrtnutí políčka zabrání kliknutí na události z druhého podřízeného `<div>` objektu pro rozšíření na nadřazený `<div>` :</span><span class="sxs-lookup"><span data-stu-id="6c85a-200">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
