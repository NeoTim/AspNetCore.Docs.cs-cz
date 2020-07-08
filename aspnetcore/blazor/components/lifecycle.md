---
title: BlazorŽivotní cyklus ASP.NET Core
author: guardrex
description: Naučte se používat Razor metody životního cyklu komponent v Blazor aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/lifecycle
ms.openlocfilehash: 6b9653356659700ae8396a01b38c04d59a86625f
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86059887"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="7dd87-103">BlazorŽivotní cyklus ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7dd87-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="7dd87-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="7dd87-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="7dd87-105">BlazorRozhraní zahrnuje synchronní a asynchronní metody životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="7dd87-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="7dd87-106">Přepište metody životního cyklu pro provádění dalších operací na součástech během inicializace a vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="7dd87-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="7dd87-107">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="7dd87-107">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="7dd87-108">Před nastavením parametrů</span><span class="sxs-lookup"><span data-stu-id="7dd87-108">Before parameters are set</span></span>

<span data-ttu-id="7dd87-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>nastaví parametry zadané nadřazeným prvkem komponenty ve stromu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="7dd87-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="7dd87-110"><xref:Microsoft.AspNetCore.Components.ParameterView>obsahuje celou sadu hodnot parametrů pokaždé, když <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> je volána.</span><span class="sxs-lookup"><span data-stu-id="7dd87-110"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="7dd87-111">Výchozí implementace <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sady nastaví hodnotu každé vlastnosti s [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributem nebo, který má odpovídající hodnotu v <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="7dd87-111">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="7dd87-112">Parametry, které nemají odpovídající hodnotu v, <xref:Microsoft.AspNetCore.Components.ParameterView> jsou ponechány beze změny.</span><span class="sxs-lookup"><span data-stu-id="7dd87-112">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="7dd87-113">Pokud [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) není vyvolána, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="7dd87-113">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="7dd87-114">Například neexistuje žádný požadavek na přiřazení příchozích parametrů k vlastnostem třídy.</span><span class="sxs-lookup"><span data-stu-id="7dd87-114">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="7dd87-115">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="7dd87-115">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="7dd87-116">Další informace najdete v části věnované [odstraňování `IDisposable` komponent](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="7dd87-116">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="7dd87-117">Inicializační metody komponenty</span><span class="sxs-lookup"><span data-stu-id="7dd87-117">Component initialization methods</span></span>

<span data-ttu-id="7dd87-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> jsou vyvolány při inicializaci komponenty po přijetí počátečních parametrů z své nadřazené komponenty v <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="7dd87-118"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="7dd87-119">Použijte <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> , když komponenta provede asynchronní operaci a měla by se aktualizovat po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="7dd87-119">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="7dd87-120">Pro synchronní operaci popište <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="7dd87-120">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="7dd87-121">Chcete-li provést asynchronní operaci, přepište <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> a použijte [`await`](/dotnet/csharp/language-reference/operators/await) operátor na operaci:</span><span class="sxs-lookup"><span data-stu-id="7dd87-121">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor Server<span data-ttu-id="7dd87-122">aplikace, které [proprerender volání jejich obsahu](xref:blazor/fundamentals/additional-scenarios#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_dvakrát_**:</span><span class="sxs-lookup"><span data-stu-id="7dd87-122"> apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span></span>

* <span data-ttu-id="7dd87-123">Jednou, když je komponenta zpočátku vykreslena jako součást stránky.</span><span class="sxs-lookup"><span data-stu-id="7dd87-123">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="7dd87-124">Podruhé, když prohlížeč vytvoří připojení zpátky k serveru.</span><span class="sxs-lookup"><span data-stu-id="7dd87-124">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="7dd87-125">Chcete-li zabránit tomu, aby se kód vývojáře v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Nespuštěn dvakrát, přečtěte si část [stav opětovného připojení po předvykreslování](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="7dd87-125">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="7dd87-126">I když Blazor Server je aplikace předem vykreslovat, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno.</span><span class="sxs-lookup"><span data-stu-id="7dd87-126">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="7dd87-127">Komponenty mohou být při předvykreslování nutné pro vykreslení odlišně.</span><span class="sxs-lookup"><span data-stu-id="7dd87-127">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="7dd87-128">Další informace najdete v části [detekce při předvykreslování aplikace](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="7dd87-128">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="7dd87-129">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="7dd87-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="7dd87-130">Další informace najdete v části věnované [odstraňování `IDisposable` komponent](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="7dd87-130">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="7dd87-131">Po nastavení parametrů</span><span class="sxs-lookup"><span data-stu-id="7dd87-131">After parameters are set</span></span>

<span data-ttu-id="7dd87-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>nebo <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> se nazývají:</span><span class="sxs-lookup"><span data-stu-id="7dd87-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="7dd87-133">Po inicializaci komponenty v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> nebo <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> .</span><span class="sxs-lookup"><span data-stu-id="7dd87-133">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>.</span></span>
* <span data-ttu-id="7dd87-134">Po opětovném vykreslení nadřazené komponenty a dodání:</span><span class="sxs-lookup"><span data-stu-id="7dd87-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="7dd87-135">Pouze známé primitivní neměnné typy, u kterých se změnil alespoň jeden parametr.</span><span class="sxs-lookup"><span data-stu-id="7dd87-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="7dd87-136">Jakékoli parametry komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="7dd87-136">Any complex-typed parameters.</span></span> <span data-ttu-id="7dd87-137">Architektura nemůže zjistit, zda hodnoty parametru složitého typu jsou interně provedeny, takže se sada parametrů považuje za změněnou.</span><span class="sxs-lookup"><span data-stu-id="7dd87-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="7dd87-138">Asynchronní práce při aplikování parametrů a hodnot vlastností musí probíhat během <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> události životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="7dd87-138">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="7dd87-139">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="7dd87-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="7dd87-140">Další informace najdete v části věnované [odstraňování `IDisposable` komponent](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="7dd87-140">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="7dd87-141">Po vykreslení komponenty</span><span class="sxs-lookup"><span data-stu-id="7dd87-141">After component render</span></span>

<span data-ttu-id="7dd87-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> jsou volány po dokončení vykreslování součásti.</span><span class="sxs-lookup"><span data-stu-id="7dd87-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="7dd87-143">V tuto chvíli se naplní odkazy na element a komponentu.</span><span class="sxs-lookup"><span data-stu-id="7dd87-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="7dd87-144">Tuto fázi použijte k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace knihoven JavaScript třetích stran, které pracují s vykreslenými prvky modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="7dd87-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="7dd87-145">`firstRender`Parametr pro <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="7dd87-145">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="7dd87-146">Je nastaven na `true` první, kdy je instance komponenty vykreslena.</span><span class="sxs-lookup"><span data-stu-id="7dd87-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="7dd87-147">Dá se použít k zajištění toho, aby se inicializace prováděla jenom jednou.</span><span class="sxs-lookup"><span data-stu-id="7dd87-147">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="7dd87-148">Asynchronní práce ihned po vykreslení musí nastat během <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> události životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="7dd87-148">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="7dd87-149">I když vrátíte <xref:System.Threading.Tasks.Task> z <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , rozhraní neplánuje další cyklus vykreslování pro komponentu po dokončení této úlohy.</span><span class="sxs-lookup"><span data-stu-id="7dd87-149">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="7dd87-150">To se vyhnete nekonečné smyčce vykreslování.</span><span class="sxs-lookup"><span data-stu-id="7dd87-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="7dd87-151">Liší se od ostatních metod životního cyklu, které naplánují další cyklus vykreslování po dokončení vrácené úlohy.</span><span class="sxs-lookup"><span data-stu-id="7dd87-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="7dd87-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *nejsou volány při předvykreslování na serveru.*</span><span class="sxs-lookup"><span data-stu-id="7dd87-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="7dd87-153">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="7dd87-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="7dd87-154">Další informace najdete v části věnované [odstraňování `IDisposable` komponent](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="7dd87-154">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="7dd87-155">Potlačit aktualizaci uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="7dd87-155">Suppress UI refreshing</span></span>

<span data-ttu-id="7dd87-156">Přepsáním <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> potlačíte aktualizaci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7dd87-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="7dd87-157">Pokud se implementace vrátí `true` , uživatelské rozhraní se obnoví:</span><span class="sxs-lookup"><span data-stu-id="7dd87-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="7dd87-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>je volána při každém vygenerování součásti.</span><span class="sxs-lookup"><span data-stu-id="7dd87-158"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="7dd87-159">I když <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> je přepsat, komponenta je vždy zpočátku vykreslena.</span><span class="sxs-lookup"><span data-stu-id="7dd87-159">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="7dd87-160">Další informace naleznete v tématu <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="7dd87-160">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="7dd87-161">Změny stavu</span><span class="sxs-lookup"><span data-stu-id="7dd87-161">State changes</span></span>

<span data-ttu-id="7dd87-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>upozorní komponentu, že její stav se změnil.</span><span class="sxs-lookup"><span data-stu-id="7dd87-162"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="7dd87-163">V případě potřeby volání <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> způsobí, že se komponenta znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="7dd87-163">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="7dd87-164"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>se nazývá automaticky pro <xref:Microsoft.AspNetCore.Components.EventCallback> metody.</span><span class="sxs-lookup"><span data-stu-id="7dd87-164"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="7dd87-165">Další informace naleznete v tématu <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="7dd87-165">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="7dd87-166">Zpracovat nedokončené asynchronní akce při vykreslení</span><span class="sxs-lookup"><span data-stu-id="7dd87-166">Handle incomplete async actions at render</span></span>

<span data-ttu-id="7dd87-167">Asynchronní akce provedené v událostech životního cyklu nemusí být před vykreslením komponenty dokončeny.</span><span class="sxs-lookup"><span data-stu-id="7dd87-167">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="7dd87-168">Objekty mohou být `null` při provádění metody životního cyklu nebo neúplná data naplněna daty.</span><span class="sxs-lookup"><span data-stu-id="7dd87-168">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="7dd87-169">Poskytněte logiku vykreslování pro potvrzení, že jsou objekty inicializovány.</span><span class="sxs-lookup"><span data-stu-id="7dd87-169">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="7dd87-170">Vykreslí zástupné prvky uživatelského rozhraní (například zprávu o načítání), zatímco objekty jsou `null` .</span><span class="sxs-lookup"><span data-stu-id="7dd87-170">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="7dd87-171">V `FetchData` komponentě Blazor šablony <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> je přepsáno na asynchronně příjem dat předpovědi ( `forecasts` ).</span><span class="sxs-lookup"><span data-stu-id="7dd87-171">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="7dd87-172">V takovém případě `forecasts` `null` se uživateli zobrazí zpráva o načítání.</span><span class="sxs-lookup"><span data-stu-id="7dd87-172">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="7dd87-173">Po `Task` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> úspěšném dokončení se komponenta znovu vykreslí s aktualizovaným stavem.</span><span class="sxs-lookup"><span data-stu-id="7dd87-173">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="7dd87-174">`Pages/FetchData.razor`v Blazor Server šabloně:</span><span class="sxs-lookup"><span data-stu-id="7dd87-174">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="7dd87-175">Vyřazení komponent pomocí IDisposable</span><span class="sxs-lookup"><span data-stu-id="7dd87-175">Component disposal with IDisposable</span></span>

<span data-ttu-id="7dd87-176">Pokud komponenta implementuje <xref:System.IDisposable> , je [ `Dispose` Metoda](/dotnet/standard/garbage-collection/implementing-dispose) volána, když je komponenta odebrána z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7dd87-176">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="7dd87-177">Následující komponenta používá `@implements IDisposable` a `Dispose` metodu:</span><span class="sxs-lookup"><span data-stu-id="7dd87-177">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="7dd87-178">Volání <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> v `Dispose` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="7dd87-178">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="7dd87-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>může být vyvolána jako součást odtrhnout zobrazovací jednotky, takže v tomto okamžiku není podporována aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7dd87-179"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="7dd87-180">Zruší odběr obslužných rutin událostí z událostí .NET.</span><span class="sxs-lookup"><span data-stu-id="7dd87-180">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="7dd87-181">Následující příklady [ Blazor formulářů](xref:blazor/forms-validation) ukazují, jak odpojovat obslužnou rutinu události v `Dispose` metodě:</span><span class="sxs-lookup"><span data-stu-id="7dd87-181">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="7dd87-182">Přístup k privátnímu poli a lambda</span><span class="sxs-lookup"><span data-stu-id="7dd87-182">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="7dd87-183">Přístup k privátní metodě</span><span class="sxs-lookup"><span data-stu-id="7dd87-183">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="7dd87-184">Ošetření chyb</span><span class="sxs-lookup"><span data-stu-id="7dd87-184">Handle errors</span></span>

<span data-ttu-id="7dd87-185">Informace o zpracování chyb během provádění metod životního cyklu naleznete v tématu <xref:blazor/fundamentals/handle-errors#lifecycle-methods> .</span><span class="sxs-lookup"><span data-stu-id="7dd87-185">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="7dd87-186">Stav opětovného připojení po předvykreslování</span><span class="sxs-lookup"><span data-stu-id="7dd87-186">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="7dd87-187">V Blazor Server aplikaci, když <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> je <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , komponenta je zpočátku vykreslena jako součást stránky.</span><span class="sxs-lookup"><span data-stu-id="7dd87-187">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="7dd87-188">Jakmile prohlížeč vytvoří připojení zpátky k serveru, komponenta se *znovu*vykreslí a komponenta je teď interaktivní.</span><span class="sxs-lookup"><span data-stu-id="7dd87-188">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="7dd87-189">Pokud [`OnInitialized{Async}`](#component-initialization-methods) je k dispozici metoda životního cyklu pro inicializaci komponenty, je metoda provedena *dvakrát*:</span><span class="sxs-lookup"><span data-stu-id="7dd87-189">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="7dd87-190">Když se komponenta předem vykreslí.</span><span class="sxs-lookup"><span data-stu-id="7dd87-190">When the component is prerendered statically.</span></span>
* <span data-ttu-id="7dd87-191">Po navázání připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="7dd87-191">After the server connection has been established.</span></span>

<span data-ttu-id="7dd87-192">Výsledkem může být znatelné změny v datech zobrazených v uživatelském rozhraní, když je komponenta nakonec vykreslena.</span><span class="sxs-lookup"><span data-stu-id="7dd87-192">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="7dd87-193">Abyste se vyhnuli scénáři dvojího vykreslování v Blazor Server aplikaci:</span><span class="sxs-lookup"><span data-stu-id="7dd87-193">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="7dd87-194">Předejte identifikátor, který se dá použít k ukládání stavu do mezipaměti během předgenerování a načtení stavu po restartování aplikace.</span><span class="sxs-lookup"><span data-stu-id="7dd87-194">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="7dd87-195">Použijte identifikátor při předvykreslování k uložení stavu součásti.</span><span class="sxs-lookup"><span data-stu-id="7dd87-195">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="7dd87-196">Pro načtení stavu uloženého v mezipaměti použijte identifikátor po předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="7dd87-196">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="7dd87-197">Následující kód demonstruje aktualizaci `WeatherForecastService` v aplikaci založené na šablonách Blazor Server , která vylučuje dvojité vykreslování:</span><span class="sxs-lookup"><span data-stu-id="7dd87-197">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = summaries[rng.Next(summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="7dd87-198">Další informace o naleznete v <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> tématu <xref:blazor/fundamentals/additional-scenarios#render-mode> .</span><span class="sxs-lookup"><span data-stu-id="7dd87-198">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="7dd87-199">Rozpoznat, kdy se aplikace předvykresluje</span><span class="sxs-lookup"><span data-stu-id="7dd87-199">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="7dd87-200">Zrušit práci na pozadí</span><span class="sxs-lookup"><span data-stu-id="7dd87-200">Cancelable background work</span></span>

<span data-ttu-id="7dd87-201">Komponenty často provádějí dlouhotrvající práci na pozadí, jako je například vytváření síťových volání ( <xref:System.Net.Http.HttpClient> ) a interakce s databázemi.</span><span class="sxs-lookup"><span data-stu-id="7dd87-201">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="7dd87-202">Je žádoucí zastavit práci na pozadí, aby se v různých situacích šetřily systémové prostředky.</span><span class="sxs-lookup"><span data-stu-id="7dd87-202">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="7dd87-203">Například asynchronní operace na pozadí se nezastaví automaticky, když uživatel přejde mimo komponentu.</span><span class="sxs-lookup"><span data-stu-id="7dd87-203">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="7dd87-204">Mezi další důvody, proč mohou pracovní položky na pozadí vyžadovat zrušení patří:</span><span class="sxs-lookup"><span data-stu-id="7dd87-204">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="7dd87-205">Spuštění úlohy na pozadí bylo zahájeno s chybnými vstupními daty nebo parametry zpracování.</span><span class="sxs-lookup"><span data-stu-id="7dd87-205">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="7dd87-206">Aktuální sada spuštěných pracovních položek na pozadí musí být nahrazena novou sadou pracovních položek.</span><span class="sxs-lookup"><span data-stu-id="7dd87-206">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="7dd87-207">Priorita aktuálně spuštěných úloh musí být změněna.</span><span class="sxs-lookup"><span data-stu-id="7dd87-207">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="7dd87-208">Aby bylo možné aplikaci znovu nasadit na server, je nutné ji vypnout.</span><span class="sxs-lookup"><span data-stu-id="7dd87-208">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="7dd87-209">Prostředky serveru jsou omezené a vyžadují přeplánování pracovních položek na pozadí.</span><span class="sxs-lookup"><span data-stu-id="7dd87-209">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="7dd87-210">Implementace vzorové práce na pozadí v součásti:</span><span class="sxs-lookup"><span data-stu-id="7dd87-210">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="7dd87-211">Použijte <xref:System.Threading.CancellationTokenSource> a <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="7dd87-211">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="7dd87-212">Při [vyřazení součásti](#component-disposal-with-idisposable) a v jakémkoli bodě zrušení je požadováno ručním zrušením tokenu, voláním [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) signalizace, že by měla být zrušena činnost na pozadí.</span><span class="sxs-lookup"><span data-stu-id="7dd87-212">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="7dd87-213">Po návratu asynchronního volání volejte <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> na token.</span><span class="sxs-lookup"><span data-stu-id="7dd87-213">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="7dd87-214">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="7dd87-214">In the following example:</span></span>

* <span data-ttu-id="7dd87-215">`await Task.Delay(5000, cts.Token);`představuje dlouhodobě běžící asynchronní práci na pozadí.</span><span class="sxs-lookup"><span data-stu-id="7dd87-215">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="7dd87-216">`BackgroundResourceMethod`představuje dlouhotrvající metodu na pozadí, která by neměla být spuštěna, pokud `Resource` je uvolněna před voláním metody.</span><span class="sxs-lookup"><span data-stu-id="7dd87-216">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

```razor
@implements IDisposable
@using System.Threading

<button @onclick="LongRunningWork">Trigger long running work</button>

@code {
    private Resource resource = new Resource();
    private CancellationTokenSource cts = new CancellationTokenSource();

    protected async Task LongRunningWork()
    {
        await Task.Delay(5000, cts.Token);

        cts.Token.ThrowIfCancellationRequested();
        resource.BackgroundResourceMethod();
    }

    public void Dispose()
    {
        cts.Cancel();
        cts.Dispose();
        resource.Dispose();
    }

    private class Resource : IDisposable
    {
        private bool disposed;

        public void BackgroundResourceMethod()
        {
            if (disposed)
            {
                throw new ObjectDisposedException(nameof(Resource));
            }
            
            ...
        }
        
        public void Dispose()
        {
            disposed = true;
        }
    }
}
```
