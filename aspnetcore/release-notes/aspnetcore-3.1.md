---
title: Co je nového v ASP.NET Core 3.1
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 3.1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: f375022ad3ebdea2990f626320ef295926f88c22
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662706"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="fbce4-103">Co je nového v ASP.NET Core 3.1</span><span class="sxs-lookup"><span data-stu-id="fbce4-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="fbce4-104">Tento článek upozorňuje na nejvýznamnější změny v ASP.NET jádrem 3.1 s odkazy na příslušnou dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="fbce4-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="fbce4-105">Podpora částečné třídy pro komponenty Razor</span><span class="sxs-lookup"><span data-stu-id="fbce4-105">Partial class support for Razor components</span></span>

<span data-ttu-id="fbce4-106">Komponenty razor jsou nyní generovány jako částečné třídy.</span><span class="sxs-lookup"><span data-stu-id="fbce4-106">Razor components are now generated as partial classes.</span></span> <span data-ttu-id="fbce4-107">Kód pro komponentu Razor lze zapsat pomocí souboru s kódem na pozadí definovaného jako částečná třída, nikoli definování majedu pro komponentu v jednom souboru.</span><span class="sxs-lookup"><span data-stu-id="fbce4-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="fbce4-108">Další informace naleznete [v tématu Podpora částečné třídy](xref:blazor/components#partial-class-support).</span><span class="sxs-lookup"><span data-stu-id="fbce4-108">For more information, see [Partial class support](xref:blazor/components#partial-class-support).</span></span>

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor<span data-ttu-id="fbce4-109">Pomocník s tagy komponent a předat parametry součástem nejvyšší úrovně</span><span class="sxs-lookup"><span data-stu-id="fbce4-109"> Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="fbce4-110">V Blazor ASP.NET Core 3.0, komponenty byly vykresleny do stránek`Html.RenderComponentAsync`a zobrazení pomocí HTML Helper ( ).</span><span class="sxs-lookup"><span data-stu-id="fbce4-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="fbce4-111">V ASP.NET jádrem 3.1 vykreslte komponentu ze stránky nebo zobrazení pomocí nového pomocníka pro označení komponent:</span><span class="sxs-lookup"><span data-stu-id="fbce4-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="fbce4-112">Pomocník HTML zůstává podporován v ASP.NET jádrem 3.1, ale doporučuje se pomocník pro označení komponent.</span><span class="sxs-lookup"><span data-stu-id="fbce4-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

Blazor<span data-ttu-id="fbce4-113">Serverové aplikace teď můžou během počátečního vykreslení předávat parametry součástem nejvyšší úrovně.</span><span class="sxs-lookup"><span data-stu-id="fbce4-113"> Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="fbce4-114">Dříve bylo možné předat parametry pouze součásti nejvyšší úrovně pomocí [rendermode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span><span class="sxs-lookup"><span data-stu-id="fbce4-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="fbce4-115">V této verzi jsou podporovány [rendermode.server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) i [RenderModel.ServerPrerendered.](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered)</span><span class="sxs-lookup"><span data-stu-id="fbce4-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="fbce4-116">Všechny zadané hodnoty parametrů jsou serializovány jako JSON a zahrnuty do počáteční odpovědi.</span><span class="sxs-lookup"><span data-stu-id="fbce4-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="fbce4-117">Například přednastavení `Counter` komponenty s přírůstkovou`IncrementAmount`částkou ( ):</span><span class="sxs-lookup"><span data-stu-id="fbce4-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="fbce4-118">Další informace najdete [v tématu Integrace komponent do stránek Razor pages a aplikací MVC](xref:blazor/integrate-components).</span><span class="sxs-lookup"><span data-stu-id="fbce4-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/integrate-components).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="fbce4-119">Podpora sdílených front v souboru HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="fbce4-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="fbce4-120">[Http.sys](xref:fundamentals/servers/httpsys) podporuje vytváření anonymních front požadavků.</span><span class="sxs-lookup"><span data-stu-id="fbce4-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="fbce4-121">V ASP.NET jádrem 3.1 jsme přidali možnost vytvořit nebo připojit k existující frontě požadavků http.sys.</span><span class="sxs-lookup"><span data-stu-id="fbce4-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="fbce4-122">Vytvoření nebo připojení k existující fronty požadavků s názvem HTTP.sys umožňuje scénáře, kde proces řadiče HTTP.sys, který vlastní frontu, je nezávislý na procesu naslouchací proces.</span><span class="sxs-lookup"><span data-stu-id="fbce4-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="fbce4-123">Tato nezávislost umožňuje zachovat existující připojení a požadavky zařazené do fronty mezi restartováním procesu naslouchací proces:</span><span class="sxs-lookup"><span data-stu-id="fbce4-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="fbce4-124">Prolomení změn pro soubory cookie SameSite</span><span class="sxs-lookup"><span data-stu-id="fbce4-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="fbce4-125">Chování cookies SameSite se změnilo tak, aby odráželo nadcházející změny prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="fbce4-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="fbce4-126">To může ovlivnit scénáře ověřování, jako je AzureAd, OpenIdConnect nebo WsFederation.</span><span class="sxs-lookup"><span data-stu-id="fbce4-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="fbce4-127">Další informace naleznete v tématu <xref:security/samesite>.</span><span class="sxs-lookup"><span data-stu-id="fbce4-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a><span data-ttu-id="fbce4-128">Zabránit výchozím akcím Blazor událostí v aplikacích</span><span class="sxs-lookup"><span data-stu-id="fbce4-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="fbce4-129">Pomocí `@on{EVENT}:preventDefault` atributu direktivy zabraňte výchozí akci události.</span><span class="sxs-lookup"><span data-stu-id="fbce4-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="fbce4-130">V následujícím příkladu je zabráněno výchozí akci zobrazení znaku klíče v textovém poli:</span><span class="sxs-lookup"><span data-stu-id="fbce4-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="fbce4-131">Další informace naleznete v tématu [Zabránění výchozím akcím](xref:blazor/event-handling#prevent-default-actions).</span><span class="sxs-lookup"><span data-stu-id="fbce4-131">For more information, see [Prevent default actions](xref:blazor/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a><span data-ttu-id="fbce4-132">Zastavení šíření událostí Blazor v aplikacích</span><span class="sxs-lookup"><span data-stu-id="fbce4-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="fbce4-133">Pomocí `@on{EVENT}:stopPropagation` atributu direktiva zastavte šíření událostí.</span><span class="sxs-lookup"><span data-stu-id="fbce4-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="fbce4-134">V následujícím příkladu zabráníte zaškrtnutí políčka, `<div>` aby se události z `<div>`podřízeného objektu nešířily do nadřazené položky :</span><span class="sxs-lookup"><span data-stu-id="fbce4-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<input @bind="_stopPropagation" type="checkbox" />

<div @onclick="OnSelectParentDiv">
    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        ...
    </div>
</div>

@code {
    private bool _stopPropagation = false;
}
```

<span data-ttu-id="fbce4-135">Další informace naleznete v tématu [Stop šíření událostí](xref:blazor/event-handling#stop-event-propagation).</span><span class="sxs-lookup"><span data-stu-id="fbce4-135">For more information, see [Stop event propagation](xref:blazor/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-opno-locblazor-app-development"></a><span data-ttu-id="fbce4-136">Podrobné chyby při Blazor vývoji aplikací</span><span class="sxs-lookup"><span data-stu-id="fbce4-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="fbce4-137">Když Blazor aplikace během vývoje nefunguje správně, zobrazení podrobných informací o chybách z aplikace pomáhá při řešení potíží a řešení problému.</span><span class="sxs-lookup"><span data-stu-id="fbce4-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="fbce4-138">Když dojde k Blazor chybě, aplikace zobrazí zlatý pruh v dolní části obrazovky:</span><span class="sxs-lookup"><span data-stu-id="fbce4-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="fbce4-139">Během vývoje vás zlatá lišta přesměruje na konzolu prohlížeče, kde můžete vidět výjimku.</span><span class="sxs-lookup"><span data-stu-id="fbce4-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="fbce4-140">Ve výrobě zlatý pruh upozorní uživatele, že došlo k chybě a doporučuje aktualizovat prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="fbce4-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="fbce4-141">Další informace naleznete [v tématu Podrobné chyby během vývoje](xref:blazor/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="fbce4-141">For more information, see [Detailed errors during development](xref:blazor/handle-errors#detailed-errors-during-development).</span></span>
