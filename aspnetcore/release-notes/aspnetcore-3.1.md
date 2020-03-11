---
title: Co je nového v ASP.NET Core 3,1
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 3,1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: f375022ad3ebdea2990f626320ef295926f88c22
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78662706"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="4ceef-103">Co je nového v ASP.NET Core 3,1</span><span class="sxs-lookup"><span data-stu-id="4ceef-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="4ceef-104">Tento článek popisuje nejvýznamnější změny v ASP.NET Core 3,1 s odkazy na příslušnou dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="4ceef-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="4ceef-105">Podpora částečné třídy pro součásti Razor</span><span class="sxs-lookup"><span data-stu-id="4ceef-105">Partial class support for Razor components</span></span>

<span data-ttu-id="4ceef-106">Komponenty Razor se nyní generují jako částečné třídy.</span><span class="sxs-lookup"><span data-stu-id="4ceef-106">Razor components are now generated as partial classes.</span></span> <span data-ttu-id="4ceef-107">Kód pro komponentu Razor lze zapsat pomocí souboru kódu na pozadí definovaného jako dílčí třídy namísto definice veškerého kódu pro komponentu v jednom souboru.</span><span class="sxs-lookup"><span data-stu-id="4ceef-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="4ceef-108">Další informace naleznete v tématu [Podpora částečné třídy](xref:blazor/components#partial-class-support).</span><span class="sxs-lookup"><span data-stu-id="4ceef-108">For more information, see [Partial class support](xref:blazor/components#partial-class-support).</span></span>

## <a name="opno-locblazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor<span data-ttu-id="4ceef-109"> pomocníka značek komponenty a předání parametrů do komponent nejvyšší úrovně</span><span class="sxs-lookup"><span data-stu-id="4ceef-109"> Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="4ceef-110">V Blazor s ASP.NET Core 3,0 byly komponenty vykresleny do stránek a zobrazení pomocí pomocníka jazyka HTML (`Html.RenderComponentAsync`).</span><span class="sxs-lookup"><span data-stu-id="4ceef-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="4ceef-111">V ASP.NET Core 3,1 vykreslete komponentu ze stránky nebo zobrazení pomocí nového pomocníka značky komponenty:</span><span class="sxs-lookup"><span data-stu-id="4ceef-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="4ceef-112">Pomocník HTML zůstane v ASP.NET Core 3,1 podporován, ale doporučuje se pomocník značek komponent.</span><span class="sxs-lookup"><span data-stu-id="4ceef-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

<span data-ttu-id="4ceef-113">aplikace Blazor serveru teď můžou předat parametry na nejvyšší úrovni během počátečního vykreslování.</span><span class="sxs-lookup"><span data-stu-id="4ceef-113">Blazor Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="4ceef-114">Dříve jste mohli předat parametry do komponenty nejvyšší úrovně pomocí [RenderMode. static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span><span class="sxs-lookup"><span data-stu-id="4ceef-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="4ceef-115">V této verzi jsou podporovány obě [RenderMode. Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) i [RenderModel. ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) .</span><span class="sxs-lookup"><span data-stu-id="4ceef-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="4ceef-116">Všechny zadané hodnoty parametrů jsou serializovány jako JSON a zahrnuty do počáteční odezvy.</span><span class="sxs-lookup"><span data-stu-id="4ceef-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="4ceef-117">Například fáze PreRender `Counter` komponentu s přírůstnou výší (`IncrementAmount`):</span><span class="sxs-lookup"><span data-stu-id="4ceef-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="4ceef-118">Další informace najdete v tématu [integrace součástí do aplikací Razor Pages a MVC](xref:blazor/integrate-components).</span><span class="sxs-lookup"><span data-stu-id="4ceef-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/integrate-components).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="4ceef-119">Podpora sdílených front v HTTP. sys</span><span class="sxs-lookup"><span data-stu-id="4ceef-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="4ceef-120">[Http. sys](xref:fundamentals/servers/httpsys) podporuje vytváření anonymních front požadavků.</span><span class="sxs-lookup"><span data-stu-id="4ceef-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="4ceef-121">V ASP.NET Core 3,1 jsme přidali možnost vytvořit nebo připojit k existující pojmenované frontě požadavků HTTP. sys.</span><span class="sxs-lookup"><span data-stu-id="4ceef-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="4ceef-122">Vytvoření nebo připojení k existující pojmenované frontě požadavků HTTP. sys umožňuje scénáře, kdy proces řadiče HTTP. sys, který vlastní frontu, je nezávislý na procesu naslouchacího procesu.</span><span class="sxs-lookup"><span data-stu-id="4ceef-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="4ceef-123">Díky této nezávislosti je možné zachovat stávající připojení a zařadit do fronty požadavky mezi restarty procesu naslouchacího procesu:</span><span class="sxs-lookup"><span data-stu-id="4ceef-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="4ceef-124">Průlomové změny pro soubory cookie SameSite</span><span class="sxs-lookup"><span data-stu-id="4ceef-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="4ceef-125">Chování souborů cookie SameSite bylo změněno tak, aby odráželo nadcházející změny prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4ceef-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="4ceef-126">To může mít vliv na scénáře ověřování, jako je AzureAd, OpenIdConnect nebo WsFederation.</span><span class="sxs-lookup"><span data-stu-id="4ceef-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="4ceef-127">Další informace naleznete v tématu <xref:security/samesite>.</span><span class="sxs-lookup"><span data-stu-id="4ceef-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-opno-locblazor-apps"></a><span data-ttu-id="4ceef-128">Zakázat výchozí akce pro události v aplikacích Blazor</span><span class="sxs-lookup"><span data-stu-id="4ceef-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="4ceef-129">Chcete-li zabránit výchozí akci pro událost, použijte atribut direktiva `@on{EVENT}:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="4ceef-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="4ceef-130">V následujícím příkladu je znemožněna výchozí akce zobrazení znaku klíče v textovém poli:</span><span class="sxs-lookup"><span data-stu-id="4ceef-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="4ceef-131">Další informace najdete v tématu [prevence výchozích akcí](xref:blazor/event-handling#prevent-default-actions).</span><span class="sxs-lookup"><span data-stu-id="4ceef-131">For more information, see [Prevent default actions](xref:blazor/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-opno-locblazor-apps"></a><span data-ttu-id="4ceef-132">Zastavení šíření událostí v Blazorch aplikacích</span><span class="sxs-lookup"><span data-stu-id="4ceef-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="4ceef-133">Pro zastavení šíření události použijte atribut direktiva `@on{EVENT}:stopPropagation`.</span><span class="sxs-lookup"><span data-stu-id="4ceef-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="4ceef-134">V následujícím příkladu zabrání zaškrtnutí políčka události kliknutí z podřízeného `<div>` z rozšiřování do nadřazené `<div>`:</span><span class="sxs-lookup"><span data-stu-id="4ceef-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

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

<span data-ttu-id="4ceef-135">Další informace najdete v tématu [zastavení šíření událostí](xref:blazor/event-handling#stop-event-propagation).</span><span class="sxs-lookup"><span data-stu-id="4ceef-135">For more information, see [Stop event propagation](xref:blazor/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-opno-locblazor-app-development"></a><span data-ttu-id="4ceef-136">Podrobné chyby během Blazor vývoje aplikací</span><span class="sxs-lookup"><span data-stu-id="4ceef-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="4ceef-137">Když aplikace Blazor během vývoje nefunguje správně, při řešení potíží a řešení těchto potíží získá podrobné informace o chybě z aplikace.</span><span class="sxs-lookup"><span data-stu-id="4ceef-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="4ceef-138">Když dojde k chybě, Blazor aplikace zobrazí v dolní části obrazovky zlatý pruh:</span><span class="sxs-lookup"><span data-stu-id="4ceef-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="4ceef-139">Během vývoje se zlatý panel vás přesměruje na konzolu prohlížeče, kde vidíte výjimku.</span><span class="sxs-lookup"><span data-stu-id="4ceef-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="4ceef-140">V produkčním okně upozorňuje uživatel, že došlo k chybě, a doporučuje aktualizovat prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="4ceef-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="4ceef-141">Další informace najdete v tématu [podrobné chyby při vývoji](xref:blazor/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="4ceef-141">For more information, see [Detailed errors during development](xref:blazor/handle-errors#detailed-errors-during-development).</span></span>
