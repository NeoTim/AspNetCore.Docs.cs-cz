---
title: Co je nového v ASP.NET Core 3,1
author: rick-anderson
description: Seznamte se s novými funkcemi v ASP.NET Core 3,1.
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-3.1
ms.openlocfilehash: 67fc972676549a02265035c129c513f11d303d51
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774044"
---
# <a name="whats-new-in-aspnet-core-31"></a><span data-ttu-id="f173e-103">Co je nového v ASP.NET Core 3,1</span><span class="sxs-lookup"><span data-stu-id="f173e-103">What's new in ASP.NET Core 3.1</span></span>

<span data-ttu-id="f173e-104">Tento článek popisuje nejvýznamnější změny v ASP.NET Core 3,1 s odkazy na příslušnou dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="f173e-104">This article highlights the most significant changes in ASP.NET Core 3.1 with links to relevant documentation.</span></span>

## <a name="partial-class-support-for-razor-components"></a><span data-ttu-id="f173e-105">Podpora částečné třídy pro Razor součásti</span><span class="sxs-lookup"><span data-stu-id="f173e-105">Partial class support for Razor components</span></span>

Razor<span data-ttu-id="f173e-106">komponenty se nyní generují jako částečné třídy.</span><span class="sxs-lookup"><span data-stu-id="f173e-106"> components are now generated as partial classes.</span></span> <span data-ttu-id="f173e-107">Kód pro Razor komponentu lze zapsat pomocí souboru kódu na pozadí definovaného jako dílčí třídy namísto definice veškerého kódu pro komponentu v jednom souboru.</span><span class="sxs-lookup"><span data-stu-id="f173e-107">Code for a Razor component can be written using a code-behind file defined as a partial class rather than defining all the code for the component in a single file.</span></span> <span data-ttu-id="f173e-108">Další informace naleznete v tématu [Podpora částečné třídy](xref:blazor/components#partial-class-support).</span><span class="sxs-lookup"><span data-stu-id="f173e-108">For more information, see [Partial class support](xref:blazor/components#partial-class-support).</span></span>

## <a name="blazor-component-tag-helper-and-pass-parameters-to-top-level-components"></a>Blazor<span data-ttu-id="f173e-109">Pomocná komponenta značek komponenty a předání parametrů komponentám nejvyšší úrovně</span><span class="sxs-lookup"><span data-stu-id="f173e-109"> Component Tag Helper and pass parameters to top-level components</span></span>

<span data-ttu-id="f173e-110">V Blazor with ASP.NET Core 3,0 byly komponenty vykresleny do stránek a zobrazení pomocí pomocníka jazyka HTML`Html.RenderComponentAsync`().</span><span class="sxs-lookup"><span data-stu-id="f173e-110">In Blazor with ASP.NET Core 3.0, components were rendered into pages and views using an HTML Helper (`Html.RenderComponentAsync`).</span></span> <span data-ttu-id="f173e-111">V ASP.NET Core 3,1 vykreslete komponentu ze stránky nebo zobrazení pomocí nového pomocníka značky komponenty:</span><span class="sxs-lookup"><span data-stu-id="f173e-111">In ASP.NET Core 3.1, render a component from a page or view with the new Component Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="f173e-112">Pomocník HTML zůstane v ASP.NET Core 3,1 podporován, ale doporučuje se pomocník značek komponent.</span><span class="sxs-lookup"><span data-stu-id="f173e-112">The HTML Helper remains supported in ASP.NET Core 3.1, but the Component Tag Helper is recommended.</span></span>

Blazor<span data-ttu-id="f173e-113">Serverové aplikace teď můžou předat parametry na nejvyšší úrovni během počátečního vykreslování.</span><span class="sxs-lookup"><span data-stu-id="f173e-113"> Server apps can now pass parameters to top-level components during the initial render.</span></span> <span data-ttu-id="f173e-114">Dříve jste mohli předat parametry do komponenty nejvyšší úrovně pomocí [RenderMode. static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span><span class="sxs-lookup"><span data-stu-id="f173e-114">Previously you could only pass parameters to a top-level component with [RenderMode.Static](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static).</span></span> <span data-ttu-id="f173e-115">V této verzi jsou podporovány obě [RenderMode. Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) i [RenderModel. ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) .</span><span class="sxs-lookup"><span data-stu-id="f173e-115">With this release, both [RenderMode.Server](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server) and [RenderModel.ServerPrerendered](xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered) are supported.</span></span> <span data-ttu-id="f173e-116">Všechny zadané hodnoty parametrů jsou serializovány jako JSON a zahrnuty do počáteční odezvy.</span><span class="sxs-lookup"><span data-stu-id="f173e-116">Any specified parameter values are serialized as JSON and included in the initial response.</span></span>

<span data-ttu-id="f173e-117">Například fáze PreRender `Counter` komponentu s přírůstnou velikostí (`IncrementAmount`):</span><span class="sxs-lookup"><span data-stu-id="f173e-117">For example, prerender a `Counter` component with an increment amount (`IncrementAmount`):</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="f173e-118">Další informace najdete v tématu [integrace součástí do Razor stránek a aplikací MVC](xref:blazor/integrate-components).</span><span class="sxs-lookup"><span data-stu-id="f173e-118">For more information, see [Integrate components into Razor Pages and MVC apps](xref:blazor/integrate-components).</span></span>

## <a name="support-for-shared-queues-in-httpsys"></a><span data-ttu-id="f173e-119">Podpora sdílených front v HTTP. sys</span><span class="sxs-lookup"><span data-stu-id="f173e-119">Support for shared queues in HTTP.sys</span></span>

<span data-ttu-id="f173e-120">[Http. sys](xref:fundamentals/servers/httpsys) podporuje vytváření anonymních front požadavků.</span><span class="sxs-lookup"><span data-stu-id="f173e-120">[HTTP.sys](xref:fundamentals/servers/httpsys) supports creating anonymous request queues.</span></span> <span data-ttu-id="f173e-121">V ASP.NET Core 3,1 jsme přidali možnost vytvořit nebo připojit k existující pojmenované frontě požadavků HTTP. sys.</span><span class="sxs-lookup"><span data-stu-id="f173e-121">In ASP.NET Core 3.1, we've added to ability to create or attach to an existing named HTTP.sys request queue.</span></span> <span data-ttu-id="f173e-122">Vytvoření nebo připojení k existující pojmenované frontě požadavků HTTP. sys umožňuje scénáře, kdy proces řadiče HTTP. sys, který vlastní frontu, je nezávislý na procesu naslouchacího procesu.</span><span class="sxs-lookup"><span data-stu-id="f173e-122">Creating or attaching to an existing named HTTP.sys request queue enables scenarios where the HTTP.sys controller process that owns the queue is independent of the listener process.</span></span> <span data-ttu-id="f173e-123">Díky této nezávislosti je možné zachovat stávající připojení a zařadit do fronty požadavky mezi restarty procesu naslouchacího procesu:</span><span class="sxs-lookup"><span data-stu-id="f173e-123">This independence makes it possible to preserve existing connections and enqueued requests between listener process restarts:</span></span>

[!code-csharp[](sample/Program.cs?name=snippet)]

## <a name="breaking-changes-for-samesite-cookies"></a><span data-ttu-id="f173e-124">Průlomové změny pro soubory cookie SameSite</span><span class="sxs-lookup"><span data-stu-id="f173e-124">Breaking changes for SameSite cookies</span></span>

<span data-ttu-id="f173e-125">Chování souborů cookie SameSite bylo změněno tak, aby odráželo nadcházející změny prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="f173e-125">The behavior of SameSite cookies has changed to reflect upcoming browser changes.</span></span> <span data-ttu-id="f173e-126">To může mít vliv na scénáře ověřování, jako je AzureAd, OpenIdConnect nebo WsFederation.</span><span class="sxs-lookup"><span data-stu-id="f173e-126">This may affect authentication scenarios like AzureAd, OpenIdConnect, or WsFederation.</span></span> <span data-ttu-id="f173e-127">Další informace naleznete v tématu <xref:security/samesite>.</span><span class="sxs-lookup"><span data-stu-id="f173e-127">For more information, see <xref:security/samesite>.</span></span>

## <a name="prevent-default-actions-for-events-in-blazor-apps"></a><span data-ttu-id="f173e-128">Zakázat výchozí akce pro události v Blazor aplikacích</span><span class="sxs-lookup"><span data-stu-id="f173e-128">Prevent default actions for events in Blazor apps</span></span>

<span data-ttu-id="f173e-129">Chcete- `@on{EVENT}:preventDefault` li zabránit výchozí akci pro událost, použijte atribut direktiva.</span><span class="sxs-lookup"><span data-stu-id="f173e-129">Use the `@on{EVENT}:preventDefault` directive attribute to prevent the default action for an event.</span></span> <span data-ttu-id="f173e-130">V následujícím příkladu je znemožněna výchozí akce zobrazení znaku klíče v textovém poli:</span><span class="sxs-lookup"><span data-stu-id="f173e-130">In the following example, the default action of displaying the key's character in the text box is prevented:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />
```

<span data-ttu-id="f173e-131">Další informace najdete v tématu [prevence výchozích akcí](xref:blazor/event-handling#prevent-default-actions).</span><span class="sxs-lookup"><span data-stu-id="f173e-131">For more information, see [Prevent default actions](xref:blazor/event-handling#prevent-default-actions).</span></span>

## <a name="stop-event-propagation-in-blazor-apps"></a><span data-ttu-id="f173e-132">Zastavit šíření událostí v Blazor aplikacích</span><span class="sxs-lookup"><span data-stu-id="f173e-132">Stop event propagation in Blazor apps</span></span>

<span data-ttu-id="f173e-133">Pro zastavení `@on{EVENT}:stopPropagation` šíření události použijte atribut direktiva.</span><span class="sxs-lookup"><span data-stu-id="f173e-133">Use the `@on{EVENT}:stopPropagation` directive attribute to stop event propagation.</span></span> <span data-ttu-id="f173e-134">V následujícím příkladu zaškrtnutí políčka zabrání kliknutí na události z podřízeného `<div>` objektu z rozšiřování do nadřazené položky: `<div>`</span><span class="sxs-lookup"><span data-stu-id="f173e-134">In the following example, selecting the check box prevents click events from the child `<div>` from propagating to the parent `<div>`:</span></span>

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

<span data-ttu-id="f173e-135">Další informace najdete v tématu [zastavení šíření událostí](xref:blazor/event-handling#stop-event-propagation).</span><span class="sxs-lookup"><span data-stu-id="f173e-135">For more information, see [Stop event propagation](xref:blazor/event-handling#stop-event-propagation).</span></span>

## <a name="detailed-errors-during-blazor-app-development"></a><span data-ttu-id="f173e-136">Podrobné chyby při Blazor vývoji aplikací</span><span class="sxs-lookup"><span data-stu-id="f173e-136">Detailed errors during Blazor app development</span></span>

<span data-ttu-id="f173e-137">Když Blazor aplikace nefunguje správně během vývoje, při řešení potíží a řešení těchto potíží získá podrobné informace o chybě z aplikace.</span><span class="sxs-lookup"><span data-stu-id="f173e-137">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="f173e-138">Když dojde k chybě, Blazor aplikace zobrazí v dolní části obrazovky žlutý pruh:</span><span class="sxs-lookup"><span data-stu-id="f173e-138">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="f173e-139">Během vývoje se zlatý panel vás přesměruje na konzolu prohlížeče, kde vidíte výjimku.</span><span class="sxs-lookup"><span data-stu-id="f173e-139">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="f173e-140">V produkčním okně upozorňuje uživatel, že došlo k chybě, a doporučuje aktualizovat prohlížeč.</span><span class="sxs-lookup"><span data-stu-id="f173e-140">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="f173e-141">Další informace najdete v tématu [podrobné chyby při vývoji](xref:blazor/handle-errors#detailed-errors-during-development).</span><span class="sxs-lookup"><span data-stu-id="f173e-141">For more information, see [Detailed errors during development](xref:blazor/handle-errors#detailed-errors-during-development).</span></span>
