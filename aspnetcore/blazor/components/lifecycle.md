---
title: BlazorŽivotní cyklus ASP.NET Core
author: guardrex
description: Naučte se používat Razor metody životního cyklu komponent v Blazor aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/components/lifecycle
ms.openlocfilehash: 100007a5757c1a54333c57adf573bcb8cc4fdd85
ms.sourcegitcommit: 652aefa1e0d570df42e8bca15ca43b2d250009f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91764048"
---
# <a name="aspnet-core-no-locblazor-lifecycle"></a><span data-ttu-id="91890-103">BlazorŽivotní cyklus ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="91890-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="91890-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="91890-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="91890-105">BlazorRozhraní zahrnuje synchronní a asynchronní metody životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="91890-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="91890-106">Přepište metody životního cyklu pro provádění dalších operací na součástech během inicializace a vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="91890-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

<span data-ttu-id="91890-107">Následující diagramy znázorňují Blazor životní cyklus.</span><span class="sxs-lookup"><span data-stu-id="91890-107">The following diagrams illustrate the Blazor lifecycle.</span></span> <span data-ttu-id="91890-108">Metody životního cyklu jsou definovány s příklady v následujících částech tohoto článku.</span><span class="sxs-lookup"><span data-stu-id="91890-108">Lifecycle methods are defined with examples in the following sections of this article.</span></span>

<span data-ttu-id="91890-109">Události životního cyklu komponenty:</span><span class="sxs-lookup"><span data-stu-id="91890-109">Component lifecycle events:</span></span>

1. <span data-ttu-id="91890-110">Pokud se komponenta poprvé vykresluje na žádost:</span><span class="sxs-lookup"><span data-stu-id="91890-110">If the component is rendering for the first time on a request:</span></span>
   * <span data-ttu-id="91890-111">Vytvořte instanci komponenty.</span><span class="sxs-lookup"><span data-stu-id="91890-111">Create the component's instance.</span></span>
   * <span data-ttu-id="91890-112">Provede vkládání vlastností.</span><span class="sxs-lookup"><span data-stu-id="91890-112">Perform property injection.</span></span> <span data-ttu-id="91890-113">Spusťte příkaz [`SetParametersAsync`](#before-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="91890-113">Run [`SetParametersAsync`](#before-parameters-are-set).</span></span>
   * <span data-ttu-id="91890-114">Volání [`OnInitialized{Async}`](#component-initialization-methods) .</span><span class="sxs-lookup"><span data-stu-id="91890-114">Call [`OnInitialized{Async}`](#component-initialization-methods).</span></span> <span data-ttu-id="91890-115">Pokud <xref:System.Threading.Tasks.Task> je vrácen, <xref:System.Threading.Tasks.Task> je očekáváno a pak je vykreslena komponenta.</span><span class="sxs-lookup"><span data-stu-id="91890-115">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="91890-116">Pokud se <xref:System.Threading.Tasks.Task> nevrátí, vykreslete komponentu.</span><span class="sxs-lookup"><span data-stu-id="91890-116">If a <xref:System.Threading.Tasks.Task> isn't returned, render the component.</span></span>
1. <span data-ttu-id="91890-117">Volání [`OnParametersSet{Async}`](#after-parameters-are-set) .</span><span class="sxs-lookup"><span data-stu-id="91890-117">Call [`OnParametersSet{Async}`](#after-parameters-are-set).</span></span> <span data-ttu-id="91890-118">Pokud <xref:System.Threading.Tasks.Task> je vrácen, <xref:System.Threading.Tasks.Task> je očekáváno a pak je vykreslena komponenta.</span><span class="sxs-lookup"><span data-stu-id="91890-118">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="91890-119">Pokud se <xref:System.Threading.Tasks.Task> nevrátí, vykreslete komponentu.</span><span class="sxs-lookup"><span data-stu-id="91890-119">If a <xref:System.Threading.Tasks.Task> isn't returned, render the component.</span></span>

<img src="lifecycle/_static/lifecycle1.png" alt="Component lifecycle events of a Razor component in Blazor" data-linktype="relative-path" style="max-width:50%;display:block;margin:0 auto">

<span data-ttu-id="91890-120">Zpracování událostí model DOM (Document Object Model) (DOM):</span><span class="sxs-lookup"><span data-stu-id="91890-120">Document Object Model (DOM) event processing:</span></span>

1. <span data-ttu-id="91890-121">Obslužná rutina události je spuštěna.</span><span class="sxs-lookup"><span data-stu-id="91890-121">The event handler is run.</span></span>
1. <span data-ttu-id="91890-122">Pokud <xref:System.Threading.Tasks.Task> je vrácen, <xref:System.Threading.Tasks.Task> je očekáváno a pak je vykreslena komponenta.</span><span class="sxs-lookup"><span data-stu-id="91890-122">If a <xref:System.Threading.Tasks.Task> is returned, the <xref:System.Threading.Tasks.Task> is awaited and then the component is rendered.</span></span> <span data-ttu-id="91890-123">Pokud se <xref:System.Threading.Tasks.Task> nevrátí, komponenta se vykreslí.</span><span class="sxs-lookup"><span data-stu-id="91890-123">If a <xref:System.Threading.Tasks.Task> isn't returned, the component is rendered.</span></span>

<img src="lifecycle/_static/lifecycle2.png" alt="Document Object Model (DOM) event processing" data-linktype="relative-path" style="max-width:50%;display:block;margin:0 auto">

<span data-ttu-id="91890-124">`Render`Životní cyklus:</span><span class="sxs-lookup"><span data-stu-id="91890-124">The `Render` lifecycle:</span></span>

1. <span data-ttu-id="91890-125">Pokud to není první vykreslení komponenty nebo [`ShouldRender`](#suppress-ui-refreshing) je vyhodnoceno jako `false` , neprovádějte pro komponentu další operace.</span><span class="sxs-lookup"><span data-stu-id="91890-125">If this isn't the component's first render or [`ShouldRender`](#suppress-ui-refreshing) is evaluated as `false`, don't perform further operations on the component.</span></span>
1. <span data-ttu-id="91890-126">Sestavte rozdílové stromu vykreslování (rozdíl) a vykreslete komponentu.</span><span class="sxs-lookup"><span data-stu-id="91890-126">Build the render tree diff (difference) and render the component.</span></span>
1. <span data-ttu-id="91890-127">Čeká se na aktualizaci DOM.</span><span class="sxs-lookup"><span data-stu-id="91890-127">Await the DOM to update.</span></span>
1. <span data-ttu-id="91890-128">Volání [`OnAfterRender{Async}`](#after-component-render) .</span><span class="sxs-lookup"><span data-stu-id="91890-128">Call [`OnAfterRender{Async}`](#after-component-render).</span></span>

<img src="lifecycle/_static/lifecycle3.png" alt="Render lifecycle" data-linktype="relative-path" style="max-width:50%;display:block;margin:0 auto">

<span data-ttu-id="91890-129">Volání vývojáře pro [`StateHasChanged`](#state-changes) výsledek vykreslování.</span><span class="sxs-lookup"><span data-stu-id="91890-129">Developer calls to [`StateHasChanged`](#state-changes) result in a render.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="91890-130">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="91890-130">Lifecycle methods</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="91890-131">Před nastavením parametrů</span><span class="sxs-lookup"><span data-stu-id="91890-131">Before parameters are set</span></span>

<span data-ttu-id="91890-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> nastaví parametry zadané nadřazeným prvkem komponenty ve stromu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="91890-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="91890-133"><xref:Microsoft.AspNetCore.Components.ParameterView> obsahuje sadu hodnot parametrů pro komponentu pokaždé, když <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> je volána.</span><span class="sxs-lookup"><span data-stu-id="91890-133"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the set of parameter values for the component each time <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> is called.</span></span>

<span data-ttu-id="91890-134">Výchozí implementace <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sady nastaví hodnotu každé vlastnosti s [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) atributem nebo, který má odpovídající hodnotu v <xref:Microsoft.AspNetCore.Components.ParameterView> .</span><span class="sxs-lookup"><span data-stu-id="91890-134">The default implementation of <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets the value of each property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) or [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute) attribute that has a corresponding value in the <xref:Microsoft.AspNetCore.Components.ParameterView>.</span></span> <span data-ttu-id="91890-135">Parametry, které nemají odpovídající hodnotu v, <xref:Microsoft.AspNetCore.Components.ParameterView> jsou ponechány beze změny.</span><span class="sxs-lookup"><span data-stu-id="91890-135">Parameters that don't have a corresponding value in <xref:Microsoft.AspNetCore.Components.ParameterView> are left unchanged.</span></span>

<span data-ttu-id="91890-136">Pokud [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) není vyvolána, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="91890-136">If [`base.SetParametersAync`](xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A) isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="91890-137">Například neexistuje žádný požadavek na přiřazení příchozích parametrů k vlastnostem třídy.</span><span class="sxs-lookup"><span data-stu-id="91890-137">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="91890-138">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="91890-138">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="91890-139">Další informace najdete v části věnované [odstraňování `IDisposable` komponent](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="91890-139">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="91890-140">Inicializační metody komponenty</span><span class="sxs-lookup"><span data-stu-id="91890-140">Component initialization methods</span></span>

<span data-ttu-id="91890-141"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> jsou vyvolány při inicializaci komponenty po přijetí počátečních parametrů z své nadřazené komponenty v <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="91890-141"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component in <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>.</span></span> 

<span data-ttu-id="91890-142">Použijte <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> , když komponenta provede asynchronní operaci a měla by se aktualizovat po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="91890-142">Use <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="91890-143">Pro synchronní operaci popište <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> :</span><span class="sxs-lookup"><span data-stu-id="91890-143">For a synchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="91890-144">Chcete-li provést asynchronní operaci, přepište <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> a použijte [`await`](/dotnet/csharp/language-reference/operators/await) operátor na operaci:</span><span class="sxs-lookup"><span data-stu-id="91890-144">To perform an asynchronous operation, override <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and use the [`await`](/dotnet/csharp/language-reference/operators/await) operator on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="91890-145">Blazor Server aplikace, které [proprerender volání jejich obsahu](xref:blazor/fundamentals/additional-scenarios#render-mode) <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_dvakrát_**:</span><span class="sxs-lookup"><span data-stu-id="91890-145">Blazor Server apps that [prerender their content](xref:blazor/fundamentals/additional-scenarios#render-mode) call <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> **_twice_**:</span></span>

* <span data-ttu-id="91890-146">Jednou, když je komponenta zpočátku vykreslena jako součást stránky.</span><span class="sxs-lookup"><span data-stu-id="91890-146">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="91890-147">Podruhé, když prohlížeč vytvoří připojení zpátky k serveru.</span><span class="sxs-lookup"><span data-stu-id="91890-147">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="91890-148">Chcete-li zabránit tomu, aby se kód vývojáře v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> Nespuštěn dvakrát, přečtěte si část [stav opětovného připojení po předvykreslování](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="91890-148">To prevent developer code in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="91890-149">I když Blazor Server je aplikace předem vykreslovat, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno.</span><span class="sxs-lookup"><span data-stu-id="91890-149">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="91890-150">Komponenty mohou být při předvykreslování nutné pro vykreslení odlišně.</span><span class="sxs-lookup"><span data-stu-id="91890-150">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="91890-151">Další informace najdete v části [detekce při předvykreslování aplikace](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="91890-151">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="91890-152">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="91890-152">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="91890-153">Další informace najdete v části věnované [odstraňování `IDisposable` komponent](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="91890-153">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="91890-154">Po nastavení parametrů</span><span class="sxs-lookup"><span data-stu-id="91890-154">After parameters are set</span></span>

<span data-ttu-id="91890-155"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> nebo <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> se nazývají:</span><span class="sxs-lookup"><span data-stu-id="91890-155"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="91890-156">Po inicializaci komponenty v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> nebo <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="91890-156">After the component is initialized in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> or <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
* <span data-ttu-id="91890-157">Po opětovném vykreslení nadřazené komponenty a dodání:</span><span class="sxs-lookup"><span data-stu-id="91890-157">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="91890-158">Pouze známé primitivní neměnné typy, u kterých se změnil alespoň jeden parametr.</span><span class="sxs-lookup"><span data-stu-id="91890-158">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="91890-159">Jakékoli parametry komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="91890-159">Any complex-typed parameters.</span></span> <span data-ttu-id="91890-160">Architektura nemůže zjistit, zda hodnoty parametru složitého typu jsou interně provedeny, takže se sada parametrů považuje za změněnou.</span><span class="sxs-lookup"><span data-stu-id="91890-160">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="91890-161">Asynchronní práce při aplikování parametrů a hodnot vlastností musí probíhat během <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> události životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="91890-161">Asynchronous work when applying parameters and property values must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="91890-162">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="91890-162">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="91890-163">Další informace najdete v části věnované [odstraňování `IDisposable` komponent](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="91890-163">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="91890-164">Po vykreslení komponenty</span><span class="sxs-lookup"><span data-stu-id="91890-164">After component render</span></span>

<span data-ttu-id="91890-165"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> jsou volány po dokončení vykreslování součásti.</span><span class="sxs-lookup"><span data-stu-id="91890-165"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="91890-166">V tuto chvíli se naplní odkazy na element a komponentu.</span><span class="sxs-lookup"><span data-stu-id="91890-166">Element and component references are populated at this point.</span></span> <span data-ttu-id="91890-167">Tuto fázi použijte k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace knihoven JavaScript třetích stran, které pracují s vykreslenými prvky modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="91890-167">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="91890-168">`firstRender`Parametr pro <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> :</span><span class="sxs-lookup"><span data-stu-id="91890-168">The `firstRender` parameter for <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A>:</span></span>

* <span data-ttu-id="91890-169">Je nastaven na `true` první, kdy je instance komponenty vykreslena.</span><span class="sxs-lookup"><span data-stu-id="91890-169">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="91890-170">Dá se použít k zajištění toho, aby se inicializace prováděla jenom jednou.</span><span class="sxs-lookup"><span data-stu-id="91890-170">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="91890-171">Asynchronní práce ihned po vykreslení musí nastat během <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> události životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="91890-171">Asynchronous work immediately after rendering must occur during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> lifecycle event.</span></span>
>
> <span data-ttu-id="91890-172">I když vrátíte <xref:System.Threading.Tasks.Task> z <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> , rozhraní neplánuje další cyklus vykreslování pro komponentu po dokončení této úlohy.</span><span class="sxs-lookup"><span data-stu-id="91890-172">Even if you return a <xref:System.Threading.Tasks.Task> from <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="91890-173">To se vyhnete nekonečné smyčce vykreslování.</span><span class="sxs-lookup"><span data-stu-id="91890-173">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="91890-174">Liší se od ostatních metod životního cyklu, které naplánují další cyklus vykreslování po dokončení vrácené úlohy.</span><span class="sxs-lookup"><span data-stu-id="91890-174">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="91890-175"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *nejsou volány během procesu předběžného vykreslování na serveru*.</span><span class="sxs-lookup"><span data-stu-id="91890-175"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> *aren't called during the prerendering process on the server*.</span></span> <span data-ttu-id="91890-176">Metody jsou volány při interaktivním vykreslení komponenty po dokončení předzpracování.</span><span class="sxs-lookup"><span data-stu-id="91890-176">The methods are called when the component is rendered interactively after prerendering is finished.</span></span> <span data-ttu-id="91890-177">Když se aplikace předem vykreslí:</span><span class="sxs-lookup"><span data-stu-id="91890-177">When the app prerenders:</span></span>

1. <span data-ttu-id="91890-178">Komponenta se spustí na serveru, aby vytvořila nějaký statický kód HTML v odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="91890-178">The component executes on the server to produce some static HTML markup in the HTTP response.</span></span> <span data-ttu-id="91890-179">V průběhu této fáze <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> nejsou volány.</span><span class="sxs-lookup"><span data-stu-id="91890-179">During this phase, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> aren't called.</span></span>
1. <span data-ttu-id="91890-180">Když `blazor.server.js` nebo `blazor.webassembly.js` spustíte v prohlížeči, komponenta se restartuje v režimu interaktivního vykreslování.</span><span class="sxs-lookup"><span data-stu-id="91890-180">When `blazor.server.js` or `blazor.webassembly.js` start up in the browser, the component is restarted in an interactive rendering mode.</span></span> <span data-ttu-id="91890-181">Po restartování komponenty se <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** zavolá, protože aplikace už není uvnitř fáze předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="91890-181">After a component is restarted, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> **are** called because the app isn't inside the prerendering phase any longer.</span></span>

<span data-ttu-id="91890-182">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="91890-182">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="91890-183">Další informace najdete v části věnované [odstraňování `IDisposable` komponent](#component-disposal-with-idisposable) .</span><span class="sxs-lookup"><span data-stu-id="91890-183">For more information, see the [Component disposal with `IDisposable`](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="91890-184">Potlačit aktualizaci uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="91890-184">Suppress UI refreshing</span></span>

<span data-ttu-id="91890-185">Přepsáním <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> potlačíte aktualizaci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="91890-185">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="91890-186">Pokud se implementace vrátí `true` , uživatelské rozhraní se obnoví:</span><span class="sxs-lookup"><span data-stu-id="91890-186">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="91890-187"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> je volána při každém vygenerování součásti.</span><span class="sxs-lookup"><span data-stu-id="91890-187"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is called each time the component is rendered.</span></span>

<span data-ttu-id="91890-188">I když <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> je přepsat, komponenta je vždy zpočátku vykreslena.</span><span class="sxs-lookup"><span data-stu-id="91890-188">Even if <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden, the component is always initially rendered.</span></span>

<span data-ttu-id="91890-189">Další informace naleznete v tématu <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span><span class="sxs-lookup"><span data-stu-id="91890-189">For more information, see <xref:blazor/webassembly-performance-best-practices#avoid-unnecessary-component-renders>.</span></span>

## <a name="state-changes"></a><span data-ttu-id="91890-190">Změny stavu</span><span class="sxs-lookup"><span data-stu-id="91890-190">State changes</span></span>

<span data-ttu-id="91890-191"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> upozorní komponentu, že její stav se změnil.</span><span class="sxs-lookup"><span data-stu-id="91890-191"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="91890-192">V případě potřeby volání <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> způsobí, že se komponenta znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="91890-192">When applicable, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes the component to be rerendered.</span></span>

<span data-ttu-id="91890-193"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> se nazývá automaticky pro <xref:Microsoft.AspNetCore.Components.EventCallback> metody.</span><span class="sxs-lookup"><span data-stu-id="91890-193"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called automatically for <xref:Microsoft.AspNetCore.Components.EventCallback> methods.</span></span> <span data-ttu-id="91890-194">Další informace naleznete v tématu <xref:blazor/components/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="91890-194">For more information, see <xref:blazor/components/event-handling#eventcallback>.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="91890-195">Zpracovat nedokončené asynchronní akce při vykreslení</span><span class="sxs-lookup"><span data-stu-id="91890-195">Handle incomplete async actions at render</span></span>

<span data-ttu-id="91890-196">Asynchronní akce provedené v událostech životního cyklu nemusí být před vykreslením komponenty dokončeny.</span><span class="sxs-lookup"><span data-stu-id="91890-196">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="91890-197">Objekty mohou být `null` při provádění metody životního cyklu nebo neúplná data naplněna daty.</span><span class="sxs-lookup"><span data-stu-id="91890-197">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="91890-198">Poskytněte logiku vykreslování pro potvrzení, že jsou objekty inicializovány.</span><span class="sxs-lookup"><span data-stu-id="91890-198">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="91890-199">Vykreslí zástupné prvky uživatelského rozhraní (například zprávu o načítání), zatímco objekty jsou `null` .</span><span class="sxs-lookup"><span data-stu-id="91890-199">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="91890-200">V `FetchData` komponentě Blazor šablony <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> je přepsáno na asynchronně příjem dat předpovědi ( `forecasts` ).</span><span class="sxs-lookup"><span data-stu-id="91890-200">In the `FetchData` component of the Blazor templates, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="91890-201">V takovém případě `forecasts` `null` se uživateli zobrazí zpráva o načítání.</span><span class="sxs-lookup"><span data-stu-id="91890-201">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="91890-202">Po `Task` <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> úspěšném dokončení se komponenta znovu vykreslí s aktualizovaným stavem.</span><span class="sxs-lookup"><span data-stu-id="91890-202">After the `Task` returned by <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="91890-203">`Pages/FetchData.razor` v Blazor Server šabloně:</span><span class="sxs-lookup"><span data-stu-id="91890-203">`Pages/FetchData.razor` in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/FetchData.razor?highlight=9,21,25)]

## <a name="handle-errors"></a><span data-ttu-id="91890-204">Ošetření chyb</span><span class="sxs-lookup"><span data-stu-id="91890-204">Handle errors</span></span>

<span data-ttu-id="91890-205">Informace o zpracování chyb během provádění metod životního cyklu naleznete v tématu <xref:blazor/fundamentals/handle-errors#lifecycle-methods> .</span><span class="sxs-lookup"><span data-stu-id="91890-205">For information on handling errors during lifecycle method execution, see <xref:blazor/fundamentals/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="91890-206">Stav opětovného připojení po předvykreslování</span><span class="sxs-lookup"><span data-stu-id="91890-206">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="91890-207">V Blazor Server aplikaci, když <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> je <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> , komponenta je zpočátku vykreslena jako součást stránky.</span><span class="sxs-lookup"><span data-stu-id="91890-207">In a Blazor Server app when <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> is <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered>, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="91890-208">Jakmile prohlížeč vytvoří připojení zpátky k serveru, komponenta se *znovu*vykreslí a komponenta je teď interaktivní.</span><span class="sxs-lookup"><span data-stu-id="91890-208">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="91890-209">Pokud [`OnInitialized{Async}`](#component-initialization-methods) je k dispozici metoda životního cyklu pro inicializaci komponenty, je metoda provedena *dvakrát*:</span><span class="sxs-lookup"><span data-stu-id="91890-209">If the [`OnInitialized{Async}`](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="91890-210">Když se komponenta předem vykreslí.</span><span class="sxs-lookup"><span data-stu-id="91890-210">When the component is prerendered statically.</span></span>
* <span data-ttu-id="91890-211">Po navázání připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="91890-211">After the server connection has been established.</span></span>

<span data-ttu-id="91890-212">Výsledkem může být znatelné změny v datech zobrazených v uživatelském rozhraní, když je komponenta nakonec vykreslena.</span><span class="sxs-lookup"><span data-stu-id="91890-212">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="91890-213">Abyste se vyhnuli scénáři dvojího vykreslování v Blazor Server aplikaci:</span><span class="sxs-lookup"><span data-stu-id="91890-213">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="91890-214">Předejte identifikátor, který se dá použít k ukládání stavu do mezipaměti během předgenerování a načtení stavu po restartování aplikace.</span><span class="sxs-lookup"><span data-stu-id="91890-214">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="91890-215">Použijte identifikátor při předvykreslování k uložení stavu součásti.</span><span class="sxs-lookup"><span data-stu-id="91890-215">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="91890-216">Pro načtení stavu uloženého v mezipaměti použijte identifikátor po předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="91890-216">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="91890-217">Následující kód demonstruje aktualizaci `WeatherForecastService` v aplikaci založené na šablonách Blazor Server , která vylučuje dvojité vykreslování:</span><span class="sxs-lookup"><span data-stu-id="91890-217">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="91890-218">Další informace o naleznete v <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> tématu <xref:blazor/fundamentals/additional-scenarios#render-mode> .</span><span class="sxs-lookup"><span data-stu-id="91890-218">For more information on the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode>, see <xref:blazor/fundamentals/additional-scenarios#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="91890-219">Rozpoznat, kdy se aplikace předvykresluje</span><span class="sxs-lookup"><span data-stu-id="91890-219">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="91890-220">Vyřazení komponent pomocí IDisposable</span><span class="sxs-lookup"><span data-stu-id="91890-220">Component disposal with IDisposable</span></span>

<span data-ttu-id="91890-221">Pokud komponenta implementuje <xref:System.IDisposable> , je [ `Dispose` Metoda](/dotnet/standard/garbage-collection/implementing-dispose) volána, když je komponenta odebrána z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="91890-221">If a component implements <xref:System.IDisposable>, the [`Dispose` method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="91890-222">K vyřazení může dojít kdykoli, včetně při [inicializaci komponenty](#component-initialization-methods).</span><span class="sxs-lookup"><span data-stu-id="91890-222">Disposal can occur at any time, including during [component initialization](#component-initialization-methods).</span></span> <span data-ttu-id="91890-223">Následující komponenta používá `@implements IDisposable` a `Dispose` metodu:</span><span class="sxs-lookup"><span data-stu-id="91890-223">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="91890-224">Volání <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> v `Dispose` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="91890-224">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="91890-225"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> může být vyvolána jako součást odtrhnout zobrazovací jednotky, takže v tomto okamžiku není podporována aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="91890-225"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="91890-226">Zruší odběr obslužných rutin událostí z událostí .NET.</span><span class="sxs-lookup"><span data-stu-id="91890-226">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="91890-227">Následující příklady [ Blazor formulářů](xref:blazor/forms-validation) ukazují, jak odpojovat obslužnou rutinu události v `Dispose` metodě:</span><span class="sxs-lookup"><span data-stu-id="91890-227">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="91890-228">Přístup k privátnímu poli a lambda</span><span class="sxs-lookup"><span data-stu-id="91890-228">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="91890-229">Přístup k privátní metodě</span><span class="sxs-lookup"><span data-stu-id="91890-229">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="cancelable-background-work"></a><span data-ttu-id="91890-230">Zrušit práci na pozadí</span><span class="sxs-lookup"><span data-stu-id="91890-230">Cancelable background work</span></span>

<span data-ttu-id="91890-231">Komponenty často provádějí dlouhotrvající práci na pozadí, jako je například vytváření síťových volání ( <xref:System.Net.Http.HttpClient> ) a interakce s databázemi.</span><span class="sxs-lookup"><span data-stu-id="91890-231">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="91890-232">Je žádoucí zastavit práci na pozadí, aby se v různých situacích šetřily systémové prostředky.</span><span class="sxs-lookup"><span data-stu-id="91890-232">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="91890-233">Například asynchronní operace na pozadí se nezastaví automaticky, když uživatel přejde mimo komponentu.</span><span class="sxs-lookup"><span data-stu-id="91890-233">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="91890-234">Mezi další důvody, proč mohou pracovní položky na pozadí vyžadovat zrušení patří:</span><span class="sxs-lookup"><span data-stu-id="91890-234">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="91890-235">Spuštění úlohy na pozadí bylo zahájeno s chybnými vstupními daty nebo parametry zpracování.</span><span class="sxs-lookup"><span data-stu-id="91890-235">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="91890-236">Aktuální sada spuštěných pracovních položek na pozadí musí být nahrazena novou sadou pracovních položek.</span><span class="sxs-lookup"><span data-stu-id="91890-236">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="91890-237">Priorita aktuálně spuštěných úloh musí být změněna.</span><span class="sxs-lookup"><span data-stu-id="91890-237">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="91890-238">Aby bylo možné aplikaci znovu nasadit na server, je nutné ji vypnout.</span><span class="sxs-lookup"><span data-stu-id="91890-238">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="91890-239">Prostředky serveru jsou omezené a vyžadují přeplánování pracovních položek na pozadí.</span><span class="sxs-lookup"><span data-stu-id="91890-239">Server resources become limited, necessitating the rescheduling of background work items.</span></span>

<span data-ttu-id="91890-240">Implementace vzorové práce na pozadí v součásti:</span><span class="sxs-lookup"><span data-stu-id="91890-240">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="91890-241">Použijte <xref:System.Threading.CancellationTokenSource> a <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="91890-241">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="91890-242">Při [vyřazení součásti](#component-disposal-with-idisposable) a v jakémkoli bodě zrušení je požadováno ručním zrušením tokenu, voláním [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) signalizace, že by měla být zrušena činnost na pozadí.</span><span class="sxs-lookup"><span data-stu-id="91890-242">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [`CancellationTokenSource.Cancel`](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="91890-243">Po návratu asynchronního volání volejte <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> na token.</span><span class="sxs-lookup"><span data-stu-id="91890-243">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="91890-244">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="91890-244">In the following example:</span></span>

* <span data-ttu-id="91890-245">`await Task.Delay(5000, cts.Token);` představuje dlouhodobě běžící asynchronní práci na pozadí.</span><span class="sxs-lookup"><span data-stu-id="91890-245">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="91890-246">`BackgroundResourceMethod` představuje dlouhotrvající metodu na pozadí, která by neměla být spuštěna, pokud `Resource` je uvolněna před voláním metody.</span><span class="sxs-lookup"><span data-stu-id="91890-246">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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
