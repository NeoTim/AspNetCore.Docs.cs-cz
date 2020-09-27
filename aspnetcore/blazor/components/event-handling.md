---
title: BlazorZpracování událostí ASP.NET Core
author: guardrex
description: Přečtěte si o Blazor funkcích zpracování událostí, včetně typů argumentů události, zpětných voláních událostí a správě výchozích událostí prohlížeče.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/event-handling
ms.openlocfilehash: 0d832d98ac9d1364b5db2bf65f31cbc5442db7f6
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393779"
---
# <a name="aspnet-core-no-locblazor-event-handling"></a><span data-ttu-id="79ba7-103">BlazorZpracování událostí ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="79ba7-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="79ba7-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="79ba7-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="79ba7-105">Razor komponenty poskytují funkce pro zpracování událostí.</span><span class="sxs-lookup"><span data-stu-id="79ba7-105">Razor components provide event handling features.</span></span> <span data-ttu-id="79ba7-106">Pro atribut elementu HTML s názvem [`@on{EVENT}`](xref:mvc/views/razor#onevent) (například `@onclick` ) s hodnotou delegovaný typ Razor Komponenta považuje hodnotu atributu za obslužnou rutinu události.</span><span class="sxs-lookup"><span data-stu-id="79ba7-106">For an HTML element attribute named [`@on{EVENT}`](xref:mvc/views/razor#onevent) (for example, `@onclick`) with a delegate-typed value, a Razor component treats the attribute's value as an event handler.</span></span>

<span data-ttu-id="79ba7-107">Následující kód volá metodu, `UpdateHeading` Pokud je vybráno tlačítko v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="79ba7-107">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="79ba7-108">Následující kód volá metodu, `CheckChanged` když je zaškrtávací políčko změněno v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="79ba7-108">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="79ba7-109">Obslužné rutiny událostí mohou být také asynchronní a vracet <xref:System.Threading.Tasks.Task> .</span><span class="sxs-lookup"><span data-stu-id="79ba7-109">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="79ba7-110">Není nutné ručně volat [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span><span class="sxs-lookup"><span data-stu-id="79ba7-110">There's no need to manually call [StateHasChanged](xref:blazor/components/lifecycle#state-changes).</span></span> <span data-ttu-id="79ba7-111">Výjimky jsou protokolovány, když k nim dojde.</span><span class="sxs-lookup"><span data-stu-id="79ba7-111">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="79ba7-112">V následujícím příkladu `UpdateHeading` se volá asynchronně po výběru tlačítka:</span><span class="sxs-lookup"><span data-stu-id="79ba7-112">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="79ba7-113">Typy argumentů události</span><span class="sxs-lookup"><span data-stu-id="79ba7-113">Event argument types</span></span>

<span data-ttu-id="79ba7-114">U některých událostí jsou povoleny typy argumentů události.</span><span class="sxs-lookup"><span data-stu-id="79ba7-114">For some events, event argument types are permitted.</span></span> <span data-ttu-id="79ba7-115">Zadání parametru události v definici metody události je volitelné a je nezbytné pouze v případě, že je typ události použit v metodě.</span><span class="sxs-lookup"><span data-stu-id="79ba7-115">Specifying an event parameter in an event method definition is optional and only necessary if the event type is used in the method.</span></span> <span data-ttu-id="79ba7-116">V následujícím příkladu `MouseEventArgs` je argument události použit v `ShowMessage` metodě pro nastavení textu zprávy:</span><span class="sxs-lookup"><span data-stu-id="79ba7-116">In the following example, the `MouseEventArgs` event argument is used in the `ShowMessage` method to set message text:</span></span>

```csharp
private void ShowMessage(MouseEventArgs e)
{
    messageText = $"The mouse is at coordinates: {e.ScreenX}:{e.ScreenY}";
}
```

<span data-ttu-id="79ba7-117">Podporované <xref:System.EventArgs> jsou uvedené v následující tabulce.</span><span class="sxs-lookup"><span data-stu-id="79ba7-117">Supported <xref:System.EventArgs> are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="79ba7-118">Událost</span><span class="sxs-lookup"><span data-stu-id="79ba7-118">Event</span></span>            | <span data-ttu-id="79ba7-119">Třída</span><span class="sxs-lookup"><span data-stu-id="79ba7-119">Class</span></span>  | <span data-ttu-id="79ba7-120">Události a poznámky modelu DOM</span><span class="sxs-lookup"><span data-stu-id="79ba7-120">DOM events and notes</span></span> |
| ---------------- | ------ | -------------------- |
| <span data-ttu-id="79ba7-121">Schránka</span><span class="sxs-lookup"><span data-stu-id="79ba7-121">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="79ba7-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="79ba7-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="79ba7-123">Myší</span><span class="sxs-lookup"><span data-stu-id="79ba7-123">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="79ba7-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="79ba7-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="79ba7-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> a <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> uchovávají přetažená data položky.</span><span class="sxs-lookup"><span data-stu-id="79ba7-125"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="79ba7-126">Implementujte přetahování Blazor aplikací pomocí [zprostředkovatele komunikace js](xref:blazor/call-javascript-from-dotnet) pomocí [rozhraní API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)pro přetahování HTML.</span><span class="sxs-lookup"><span data-stu-id="79ba7-126">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="79ba7-127">Chyba</span><span class="sxs-lookup"><span data-stu-id="79ba7-127">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="79ba7-128">Událost</span><span class="sxs-lookup"><span data-stu-id="79ba7-128">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="79ba7-129">*Obecné*</span><span class="sxs-lookup"><span data-stu-id="79ba7-129">*General*</span></span><br><span data-ttu-id="79ba7-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="79ba7-130">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="79ba7-131">*Schránka*</span><span class="sxs-lookup"><span data-stu-id="79ba7-131">*Clipboard*</span></span><br><span data-ttu-id="79ba7-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="79ba7-132">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="79ba7-133">*Vstup*</span><span class="sxs-lookup"><span data-stu-id="79ba7-133">*Input*</span></span><br><span data-ttu-id="79ba7-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="79ba7-134">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="79ba7-135">*Média*</span><span class="sxs-lookup"><span data-stu-id="79ba7-135">*Media*</span></span><br><span data-ttu-id="79ba7-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="79ba7-136">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `ontoggle`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="79ba7-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> obsahuje atributy pro konfiguraci mapování mezi názvy událostí a typy argumentů události.</span><span class="sxs-lookup"><span data-stu-id="79ba7-137"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="79ba7-138">Zaměření</span><span class="sxs-lookup"><span data-stu-id="79ba7-138">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="79ba7-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="79ba7-139">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="79ba7-140">Nezahrnuje podporu pro `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="79ba7-140">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="79ba7-141">Vstup</span><span class="sxs-lookup"><span data-stu-id="79ba7-141">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="79ba7-142">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="79ba7-142">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="79ba7-143">Klávesnice</span><span class="sxs-lookup"><span data-stu-id="79ba7-143">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="79ba7-144">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="79ba7-144">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="79ba7-145">Myš</span><span class="sxs-lookup"><span data-stu-id="79ba7-145">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="79ba7-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="79ba7-146">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="79ba7-147">Ukazatel myši</span><span class="sxs-lookup"><span data-stu-id="79ba7-147">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="79ba7-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="79ba7-148">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="79ba7-149">Kolečko myši</span><span class="sxs-lookup"><span data-stu-id="79ba7-149">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="79ba7-150">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="79ba7-150">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="79ba7-151">Průběh</span><span class="sxs-lookup"><span data-stu-id="79ba7-151">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="79ba7-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="79ba7-152">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="79ba7-153">Dotykové ovládání</span><span class="sxs-lookup"><span data-stu-id="79ba7-153">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="79ba7-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="79ba7-154">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="79ba7-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> představuje jeden kontaktní bod na zařízení citlivém na dotykové ovládání.</span><span class="sxs-lookup"><span data-stu-id="79ba7-155"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="79ba7-156">Událost</span><span class="sxs-lookup"><span data-stu-id="79ba7-156">Event</span></span>            | <span data-ttu-id="79ba7-157">Třída</span><span class="sxs-lookup"><span data-stu-id="79ba7-157">Class</span></span> | <span data-ttu-id="79ba7-158">Události a poznámky modelu DOM</span><span class="sxs-lookup"><span data-stu-id="79ba7-158">DOM events and notes</span></span> |
| ---------------- | ----- | -------------------- |
| <span data-ttu-id="79ba7-159">Schránka</span><span class="sxs-lookup"><span data-stu-id="79ba7-159">Clipboard</span></span>        | <xref:Microsoft.AspNetCore.Components.Web.ClipboardEventArgs> | <span data-ttu-id="79ba7-160">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="79ba7-160">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="79ba7-161">Myší</span><span class="sxs-lookup"><span data-stu-id="79ba7-161">Drag</span></span>             | <xref:Microsoft.AspNetCore.Components.Web.DragEventArgs> | <span data-ttu-id="79ba7-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="79ba7-162">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="79ba7-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> a <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> uchovávají přetažená data položky.</span><span class="sxs-lookup"><span data-stu-id="79ba7-163"><xref:Microsoft.AspNetCore.Components.Web.DataTransfer> and <xref:Microsoft.AspNetCore.Components.Web.DataTransferItem> hold dragged item data.</span></span><br><br><span data-ttu-id="79ba7-164">Implementujte přetahování Blazor aplikací pomocí [zprostředkovatele komunikace js](xref:blazor/call-javascript-from-dotnet) pomocí [rozhraní API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API)pro přetahování HTML.</span><span class="sxs-lookup"><span data-stu-id="79ba7-164">Implement drag and drop in Blazor apps using [JS interop](xref:blazor/call-javascript-from-dotnet) with [HTML Drag and Drop API](https://developer.mozilla.org/docs/Web/API/HTML_Drag_and_Drop_API).</span></span> |
| <span data-ttu-id="79ba7-165">Chyba</span><span class="sxs-lookup"><span data-stu-id="79ba7-165">Error</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.ErrorEventArgs> | `onerror` |
| <span data-ttu-id="79ba7-166">Událost</span><span class="sxs-lookup"><span data-stu-id="79ba7-166">Event</span></span>            | <xref:System.EventArgs> | <span data-ttu-id="79ba7-167">*Obecné*</span><span class="sxs-lookup"><span data-stu-id="79ba7-167">*General*</span></span><br><span data-ttu-id="79ba7-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="79ba7-168">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="79ba7-169">*Schránka*</span><span class="sxs-lookup"><span data-stu-id="79ba7-169">*Clipboard*</span></span><br><span data-ttu-id="79ba7-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="79ba7-170">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="79ba7-171">*Vstup*</span><span class="sxs-lookup"><span data-stu-id="79ba7-171">*Input*</span></span><br><span data-ttu-id="79ba7-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="79ba7-172">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="79ba7-173">*Média*</span><span class="sxs-lookup"><span data-stu-id="79ba7-173">*Media*</span></span><br><span data-ttu-id="79ba7-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="79ba7-174">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onended`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span><br><br><span data-ttu-id="79ba7-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> obsahuje atributy pro konfiguraci mapování mezi názvy událostí a typy argumentů události.</span><span class="sxs-lookup"><span data-stu-id="79ba7-175"><xref:Microsoft.AspNetCore.Components.Web.EventHandlers> holds attributes to configure the mappings between event names and event argument types.</span></span> |
| <span data-ttu-id="79ba7-176">Zaměření</span><span class="sxs-lookup"><span data-stu-id="79ba7-176">Focus</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.FocusEventArgs> | <span data-ttu-id="79ba7-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="79ba7-177">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="79ba7-178">Nezahrnuje podporu pro `relatedTarget` .</span><span class="sxs-lookup"><span data-stu-id="79ba7-178">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="79ba7-179">Vstup</span><span class="sxs-lookup"><span data-stu-id="79ba7-179">Input</span></span>            | <xref:Microsoft.AspNetCore.Components.ChangeEventArgs> | <span data-ttu-id="79ba7-180">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="79ba7-180">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="79ba7-181">Klávesnice</span><span class="sxs-lookup"><span data-stu-id="79ba7-181">Keyboard</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.KeyboardEventArgs> | <span data-ttu-id="79ba7-182">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="79ba7-182">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="79ba7-183">Myš</span><span class="sxs-lookup"><span data-stu-id="79ba7-183">Mouse</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> | <span data-ttu-id="79ba7-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="79ba7-184">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="79ba7-185">Ukazatel myši</span><span class="sxs-lookup"><span data-stu-id="79ba7-185">Mouse pointer</span></span>    | <xref:Microsoft.AspNetCore.Components.Web.PointerEventArgs> | <span data-ttu-id="79ba7-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="79ba7-186">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="79ba7-187">Kolečko myši</span><span class="sxs-lookup"><span data-stu-id="79ba7-187">Mouse wheel</span></span>      | <xref:Microsoft.AspNetCore.Components.Web.WheelEventArgs> | <span data-ttu-id="79ba7-188">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="79ba7-188">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="79ba7-189">Průběh</span><span class="sxs-lookup"><span data-stu-id="79ba7-189">Progress</span></span>         | <xref:Microsoft.AspNetCore.Components.Web.ProgressEventArgs> | <span data-ttu-id="79ba7-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="79ba7-190">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="79ba7-191">Dotykové ovládání</span><span class="sxs-lookup"><span data-stu-id="79ba7-191">Touch</span></span>            | <xref:Microsoft.AspNetCore.Components.Web.TouchEventArgs> | <span data-ttu-id="79ba7-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="79ba7-192">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="79ba7-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> představuje jeden kontaktní bod na zařízení citlivém na dotykové ovládání.</span><span class="sxs-lookup"><span data-stu-id="79ba7-193"><xref:Microsoft.AspNetCore.Components.Web.TouchPoint> represents a single contact point on a touch-sensitive device.</span></span> |

::: moniker-end

<span data-ttu-id="79ba7-194">Další informace naleznete v následujících zdrojích:</span><span class="sxs-lookup"><span data-stu-id="79ba7-194">For more information, see the following resources:</span></span>

* <span data-ttu-id="79ba7-195">[ `EventArgs` třídy v referenčním zdroji ASP.NET Core (dotnet/aspnetcore `master` větev)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="79ba7-195">[`EventArgs` classes in the ASP.NET Core reference source (dotnet/aspnetcore `master` branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Components/Web/src/Web).</span></span> <span data-ttu-id="79ba7-196">`master`Větev představuje rozhraní API ve vývoji pro *Další* ASP.NET Core vydání.</span><span class="sxs-lookup"><span data-stu-id="79ba7-196">The `master` branch represents API under development for the *next* ASP.NET Core release.</span></span> <span data-ttu-id="79ba7-197">Pro aktuální vydání vyberte příslušnou větev úložiště GitHub (například `release/3.1` ).</span><span class="sxs-lookup"><span data-stu-id="79ba7-197">For the current release, select the appropriate GitHub repository branch (for example, `release/3.1`).</span></span>
* <span data-ttu-id="79ba7-198">[Webové dokumenty MDN: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): obsahuje informace o tom, které prvky HTML podporují jednotlivé události modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="79ba7-198">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers): Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="79ba7-199">Výrazy lambda</span><span class="sxs-lookup"><span data-stu-id="79ba7-199">Lambda expressions</span></span>

<span data-ttu-id="79ba7-200">[Lambda výrazy](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) lze také použít:</span><span class="sxs-lookup"><span data-stu-id="79ba7-200">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="79ba7-201">Je často vhodné uzavřít další hodnoty, jako například při iteraci přes sadu prvků.</span><span class="sxs-lookup"><span data-stu-id="79ba7-201">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="79ba7-202">Následující příklad vytvoří tři tlačítka, z nichž každé volá `UpdateHeading` předání argumentu události ( <xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs> ) a jeho čísla tlačítka ( `buttonNumber` ), pokud je VYBRÁNO v uživatelském rozhraní:</span><span class="sxs-lookup"><span data-stu-id="79ba7-202">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (<xref:Microsoft.AspNetCore.Components.Web.MouseEventArgs>) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="79ba7-203">Nepoužívejte **proměnnou** smyčky přímo ve výrazu lambda, například `i` v předchozím `for` příkladu smyčky.</span><span class="sxs-lookup"><span data-stu-id="79ba7-203">Do **not** use a loop variable directly in a lambda expression, such as `i` in the preceding `for` loop example.</span></span> <span data-ttu-id="79ba7-204">V opačném případě se stejná proměnná používá ve všech výrazech lambda, což má za následek použití stejné hodnoty ve všech výrazech lambda.</span><span class="sxs-lookup"><span data-stu-id="79ba7-204">Otherwise, the same variable is used by all lambda expressions, which results in use of the same value in all lambdas.</span></span> <span data-ttu-id="79ba7-205">Vždy Zachyťte hodnotu proměnné v místní proměnné a pak ji použijte.</span><span class="sxs-lookup"><span data-stu-id="79ba7-205">Always capture the variable's value in a local variable and then use it.</span></span> <span data-ttu-id="79ba7-206">V předchozím příkladu `i` je přiřazena proměnná smyčky `buttonNumber` .</span><span class="sxs-lookup"><span data-stu-id="79ba7-206">In the preceding example, the loop variable `i` is assigned to `buttonNumber`.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="79ba7-207">Vnořenou eventCallback</span><span class="sxs-lookup"><span data-stu-id="79ba7-207">EventCallback</span></span>

<span data-ttu-id="79ba7-208">Běžný scénář s vnořenými komponentami je přáním spustit metodu nadřazené komponenty, když dojde k události podřízené součásti.</span><span class="sxs-lookup"><span data-stu-id="79ba7-208">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs.</span></span> <span data-ttu-id="79ba7-209">`onclick`Událost, ke které dochází v podřízené součásti, je běžným případem použití.</span><span class="sxs-lookup"><span data-stu-id="79ba7-209">An `onclick` event occurring in the child component is a common use case.</span></span> <span data-ttu-id="79ba7-210">Chcete-li zobrazit události napříč komponentami, použijte <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="79ba7-210">To expose events across components, use an <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="79ba7-211">Nadřazená komponenta může přiřadit metodu zpětného volání podřízené součásti <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="79ba7-211">A parent component can assign a callback method to a child component's <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span>

<span data-ttu-id="79ba7-212">`ChildComponent`V ukázkové aplikaci ( `Components/ChildComponent.razor` ) ukazuje, jak `onclick` je nastavena obslužná rutina tlačítka pro příjem <xref:Microsoft.AspNetCore.Components.EventCallback> delegáta z ukázky `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="79ba7-212">The `ChildComponent` in the sample app (`Components/ChildComponent.razor`) demonstrates how a button's `onclick` handler is set up to receive an <xref:Microsoft.AspNetCore.Components.EventCallback> delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="79ba7-213"><xref:Microsoft.AspNetCore.Components.EventCallback>Je zadaný s `MouseEventArgs` , který je vhodný pro `onclick` událost z periferního zařízení:</span><span class="sxs-lookup"><span data-stu-id="79ba7-213">The <xref:Microsoft.AspNetCore.Components.EventCallback> is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="79ba7-214">`ParentComponent`Nastaví <xref:Microsoft.AspNetCore.Components.EventCallback%601> ( `OnClickCallback` ) dítěte na jeho `ShowMessage` metodu.</span><span class="sxs-lookup"><span data-stu-id="79ba7-214">The `ParentComponent` sets the child's <xref:Microsoft.AspNetCore.Components.EventCallback%601> (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="79ba7-215">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="79ba7-215">`Pages/ParentComponent.razor`:</span></span>

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

<span data-ttu-id="79ba7-216">Když je vybráno tlačítko v `ChildComponent` :</span><span class="sxs-lookup"><span data-stu-id="79ba7-216">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="79ba7-217">`ParentComponent` `ShowMessage` Je volána metoda.</span><span class="sxs-lookup"><span data-stu-id="79ba7-217">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="79ba7-218">`messageText` se aktualizuje a zobrazí v `ParentComponent` .</span><span class="sxs-lookup"><span data-stu-id="79ba7-218">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="79ba7-219">Volání [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) není vyžadováno v metodě zpětného volání ( `ShowMessage` ).</span><span class="sxs-lookup"><span data-stu-id="79ba7-219">A call to [`StateHasChanged`](xref:blazor/components/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="79ba7-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> je volána automaticky pro revykreslování `ParentComponent` , stejně jako podřízené události, které aktivují revykreslování komponenty v obslužných rutinách události, které jsou spouštěny v rámci podřízeného objektu.</span><span class="sxs-lookup"><span data-stu-id="79ba7-220"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="79ba7-221"><xref:Microsoft.AspNetCore.Components.EventCallback> a <xref:Microsoft.AspNetCore.Components.EventCallback%601> povolují asynchronní delegáty.</span><span class="sxs-lookup"><span data-stu-id="79ba7-221"><xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> permit asynchronous delegates.</span></span> <span data-ttu-id="79ba7-222"><xref:Microsoft.AspNetCore.Components.EventCallback> je slabě typované a umožňuje předat libovolný argument typu v `InvokeAsync(Object)` .</span><span class="sxs-lookup"><span data-stu-id="79ba7-222"><xref:Microsoft.AspNetCore.Components.EventCallback> is weakly typed and allows passing any type argument in `InvokeAsync(Object)`.</span></span> <span data-ttu-id="79ba7-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> je silného typu a vyžaduje předání `T` argumentu v `InvokeAsync(T)` , který je možné přiřadit `TValue` .</span><span class="sxs-lookup"><span data-stu-id="79ba7-223"><xref:Microsoft.AspNetCore.Components.EventCallback%601> is strongly typed and requires passing a `T` argument in `InvokeAsync(T)` that's assignable to `TValue`.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />
```

<span data-ttu-id="79ba7-224">Vyvolat <xref:Microsoft.AspNetCore.Components.EventCallback> nebo <xref:Microsoft.AspNetCore.Components.EventCallback%601> s <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> a očekávat <xref:System.Threading.Tasks.Task> :</span><span class="sxs-lookup"><span data-stu-id="79ba7-224">Invoke an <xref:Microsoft.AspNetCore.Components.EventCallback> or <xref:Microsoft.AspNetCore.Components.EventCallback%601> with <xref:Microsoft.AspNetCore.Components.EventCallback.InvokeAsync%2A> and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await OnClickCallback.InvokeAsync(arg);
```

<span data-ttu-id="79ba7-225">Použití <xref:Microsoft.AspNetCore.Components.EventCallback> a <xref:Microsoft.AspNetCore.Components.EventCallback%601> pro zpracování událostí a parametry komponenty vazby.</span><span class="sxs-lookup"><span data-stu-id="79ba7-225">Use <xref:Microsoft.AspNetCore.Components.EventCallback> and <xref:Microsoft.AspNetCore.Components.EventCallback%601> for event handling and binding component parameters.</span></span>

<span data-ttu-id="79ba7-226">Preferovat silného typu <xref:Microsoft.AspNetCore.Components.EventCallback%601> přes <xref:Microsoft.AspNetCore.Components.EventCallback> .</span><span class="sxs-lookup"><span data-stu-id="79ba7-226">Prefer the strongly typed <xref:Microsoft.AspNetCore.Components.EventCallback%601> over <xref:Microsoft.AspNetCore.Components.EventCallback>.</span></span> <span data-ttu-id="79ba7-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> poskytuje lepší odezvu na chyby uživatelů součásti.</span><span class="sxs-lookup"><span data-stu-id="79ba7-227"><xref:Microsoft.AspNetCore.Components.EventCallback%601> provides better error feedback to users of the component.</span></span> <span data-ttu-id="79ba7-228">Podobně jako u jiných obslužných rutin událostí uživatelského rozhraní je zadání parametru události volitelné.</span><span class="sxs-lookup"><span data-stu-id="79ba7-228">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="79ba7-229">Použijte <xref:Microsoft.AspNetCore.Components.EventCallback> v případě, že zpětnému volání není předáno žádné číslo.</span><span class="sxs-lookup"><span data-stu-id="79ba7-229">Use <xref:Microsoft.AspNetCore.Components.EventCallback> when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="79ba7-230">Zabránit výchozím akcím</span><span class="sxs-lookup"><span data-stu-id="79ba7-230">Prevent default actions</span></span>

<span data-ttu-id="79ba7-231">[`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault)Chcete-li zabránit výchozí akci pro událost, použijte atribut direktiva.</span><span class="sxs-lookup"><span data-stu-id="79ba7-231">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="79ba7-232">Když je vybraný klíč na vstupním zařízení a fokus prvku je v textovém poli, prohlížeč normálně zobrazuje znak klíče v textovém poli.</span><span class="sxs-lookup"><span data-stu-id="79ba7-232">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="79ba7-233">V následujícím příkladu je výchozím chováním znemožněno zadáním `@onkeypress:preventDefault` atributu direktiva.</span><span class="sxs-lookup"><span data-stu-id="79ba7-233">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="79ba7-234">Čítač zvýší a **+** klíč není zachycen do `<input>` hodnoty prvku:</span><span class="sxs-lookup"><span data-stu-id="79ba7-234">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="79ba7-235">Určení `@on{EVENT}:preventDefault` atributu bez hodnoty je ekvivalentní `@on{EVENT}:preventDefault="true"` .</span><span class="sxs-lookup"><span data-stu-id="79ba7-235">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="79ba7-236">Hodnotou atributu může být také výraz.</span><span class="sxs-lookup"><span data-stu-id="79ba7-236">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="79ba7-237">V následujícím příkladu `shouldPreventDefault` je `bool` pole nastaveno na buď `true` nebo `false` :</span><span class="sxs-lookup"><span data-stu-id="79ba7-237">In the following example, `shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="shouldPreventDefault" />
```

## <a name="stop-event-propagation"></a><span data-ttu-id="79ba7-238">Zastavit šíření událostí</span><span class="sxs-lookup"><span data-stu-id="79ba7-238">Stop event propagation</span></span>

<span data-ttu-id="79ba7-239">[`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation)Pro zastavení šíření události použijte atribut direktiva.</span><span class="sxs-lookup"><span data-stu-id="79ba7-239">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="79ba7-240">V následujícím příkladu zaškrtnutí políčka zabrání kliknutí na události z druhého podřízeného `<div>` objektu pro rozšíření na nadřazený `<div>` :</span><span class="sxs-lookup"><span data-stu-id="79ba7-240">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
