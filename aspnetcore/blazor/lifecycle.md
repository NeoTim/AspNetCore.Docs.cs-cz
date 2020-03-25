---
title: Životní cyklus Blazor ASP.NET Core
author: guardrex
description: Naučte se používat metody životního cyklu komponenty Razor v aplikacích ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 831f575afa6ce11d06c016d43ecd1bb59d09eab6
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218905"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="2673c-103">Životní cyklus Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2673c-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="2673c-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2673c-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="2673c-105">Rozhraní Blazor zahrnuje synchronní a asynchronní metody životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="2673c-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="2673c-106">Přepište metody životního cyklu pro provádění dalších operací na součástech během inicializace a vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="2673c-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="2673c-107">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="2673c-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="2673c-108">Inicializační metody komponenty</span><span class="sxs-lookup"><span data-stu-id="2673c-108">Component initialization methods</span></span>

<span data-ttu-id="2673c-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> jsou vyvolány při inicializaci komponenty po přijetí počátečních parametrů ze své nadřazené komponenty.</span><span class="sxs-lookup"><span data-stu-id="2673c-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="2673c-110">Použijte `OnInitializedAsync`, když komponenta provede asynchronní operaci a měla by se aktualizovat po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="2673c-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="2673c-111">Pro synchronní operaci popište `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="2673c-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="2673c-112">Chcete-li provést asynchronní operaci, popište `OnInitializedAsync` a použijte klíčové slovo `await` pro operaci:</span><span class="sxs-lookup"><span data-stu-id="2673c-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="2673c-113"> serverových aplikací, které [proprerender volání jejich obsahu](xref:blazor/hosting-model-configuration#render-mode) `OnInitializedAsync` **_dvakrát_** :</span><span class="sxs-lookup"><span data-stu-id="2673c-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="2673c-114">Jednou, když je komponenta zpočátku vykreslena jako součást stránky.</span><span class="sxs-lookup"><span data-stu-id="2673c-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="2673c-115">Podruhé, když prohlížeč vytvoří připojení zpátky k serveru.</span><span class="sxs-lookup"><span data-stu-id="2673c-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="2673c-116">Chcete-li zabránit tomu, aby kód pro vývojáře v `OnInitializedAsync` běžel dvakrát, přečtěte si část [stav opětovného připojení po předvykreslování](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="2673c-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="2673c-117">I když je aplikace Blazor serveru předem vykreslovat, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno.</span><span class="sxs-lookup"><span data-stu-id="2673c-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="2673c-118">Komponenty mohou být při předvykreslování nutné pro vykreslení odlišně.</span><span class="sxs-lookup"><span data-stu-id="2673c-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="2673c-119">Další informace najdete v části [detekce při předvykreslování aplikace](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="2673c-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="2673c-120">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="2673c-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="2673c-121">Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.</span><span class="sxs-lookup"><span data-stu-id="2673c-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="2673c-122">Před nastavením parametrů</span><span class="sxs-lookup"><span data-stu-id="2673c-122">Before parameters are set</span></span>

<span data-ttu-id="2673c-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> nastaví parametry zadané nadřazeným prvkem komponenty ve stromu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="2673c-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="2673c-124"><xref:Microsoft.AspNetCore.Components.ParameterView> obsahuje celou sadu hodnot parametrů pokaždé, když `SetParametersAsync` je volána.</span><span class="sxs-lookup"><span data-stu-id="2673c-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="2673c-125">Výchozí implementace `SetParametersAsync` nastaví hodnotu každé vlastnosti s atributem `[Parameter]` nebo `[CascadingParameter]`, který má odpovídající hodnotu v `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="2673c-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="2673c-126">Parametry, které nemají odpovídající hodnotu v `ParameterView`, jsou ponechány beze změny.</span><span class="sxs-lookup"><span data-stu-id="2673c-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="2673c-127">Pokud `base.SetParametersAync` není vyvolán, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="2673c-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="2673c-128">Například neexistuje žádný požadavek na přiřazení příchozích parametrů k vlastnostem třídy.</span><span class="sxs-lookup"><span data-stu-id="2673c-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="2673c-129">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="2673c-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="2673c-130">Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.</span><span class="sxs-lookup"><span data-stu-id="2673c-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="2673c-131">Po nastavení parametrů</span><span class="sxs-lookup"><span data-stu-id="2673c-131">After parameters are set</span></span>

<span data-ttu-id="2673c-132">jsou volány <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*>:</span><span class="sxs-lookup"><span data-stu-id="2673c-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="2673c-133">Při inicializaci komponenty a přijetí první sady parametrů ze své nadřazené komponenty.</span><span class="sxs-lookup"><span data-stu-id="2673c-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="2673c-134">Po opětovném vykreslení nadřazené komponenty a dodání:</span><span class="sxs-lookup"><span data-stu-id="2673c-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="2673c-135">Pouze známé primitivní neměnné typy, u kterých se změnil alespoň jeden parametr.</span><span class="sxs-lookup"><span data-stu-id="2673c-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="2673c-136">Jakékoli parametry komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="2673c-136">Any complex-typed parameters.</span></span> <span data-ttu-id="2673c-137">Architektura nemůže zjistit, zda hodnoty parametru složitého typu jsou interně provedeny, takže se sada parametrů považuje za změněnou.</span><span class="sxs-lookup"><span data-stu-id="2673c-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="2673c-138">Asynchronní práce při aplikování parametrů a hodnot vlastností musí probíhat během události `OnParametersSetAsync` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="2673c-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="2673c-139">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="2673c-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="2673c-140">Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.</span><span class="sxs-lookup"><span data-stu-id="2673c-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="2673c-141">Po vykreslení komponenty</span><span class="sxs-lookup"><span data-stu-id="2673c-141">After component render</span></span>

<span data-ttu-id="2673c-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> jsou volány po dokončení vykreslování součásti.</span><span class="sxs-lookup"><span data-stu-id="2673c-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="2673c-143">V tuto chvíli se naplní odkazy na element a komponentu.</span><span class="sxs-lookup"><span data-stu-id="2673c-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="2673c-144">Tuto fázi použijte k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace knihoven JavaScript třetích stran, které pracují s vykreslenými prvky modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="2673c-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="2673c-145">Parametr `firstRender` pro `OnAfterRenderAsync` a `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="2673c-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="2673c-146">Je nastaven na `true` při prvním vykreslení instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="2673c-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="2673c-147">Dá se použít k zajištění toho, aby se inicializace prováděla jenom jednou.</span><span class="sxs-lookup"><span data-stu-id="2673c-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="2673c-148">Asynchronní práce ihned po vykreslení musí nastat během `OnAfterRenderAsync` události životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="2673c-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="2673c-149">I když vrátíte <xref:System.Threading.Tasks.Task> z `OnAfterRenderAsync`, rozhraní neplánuje další cyklus vykreslování pro komponentu po dokončení této úlohy.</span><span class="sxs-lookup"><span data-stu-id="2673c-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="2673c-150">To se vyhnete nekonečné smyčce vykreslování.</span><span class="sxs-lookup"><span data-stu-id="2673c-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="2673c-151">Liší se od ostatních metod životního cyklu, které naplánují další cyklus vykreslování po dokončení vrácené úlohy.</span><span class="sxs-lookup"><span data-stu-id="2673c-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="2673c-152">`OnAfterRender` a `OnAfterRenderAsync` *nejsou volány při předvykreslování na serveru.*</span><span class="sxs-lookup"><span data-stu-id="2673c-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="2673c-153">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="2673c-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="2673c-154">Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.</span><span class="sxs-lookup"><span data-stu-id="2673c-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="2673c-155">Potlačit aktualizaci uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="2673c-155">Suppress UI refreshing</span></span>

<span data-ttu-id="2673c-156">Přepište <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> pro potlačení aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2673c-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="2673c-157">Pokud implementace vrátí `true`, uživatelské rozhraní se obnoví:</span><span class="sxs-lookup"><span data-stu-id="2673c-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="2673c-158">`ShouldRender` se volá pokaždé, když se komponenta vykreslí.</span><span class="sxs-lookup"><span data-stu-id="2673c-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="2673c-159">I v případě, že je `ShouldRender` přepsat, komponenta je vždy zpočátku vykreslena.</span><span class="sxs-lookup"><span data-stu-id="2673c-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="2673c-160">Změny stavu</span><span class="sxs-lookup"><span data-stu-id="2673c-160">State changes</span></span>

<span data-ttu-id="2673c-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> upozorní komponentu, jejíž stav byl změněn.</span><span class="sxs-lookup"><span data-stu-id="2673c-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="2673c-162">Pokud je to možné, volání `StateHasChanged` způsobí, že se komponenta znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="2673c-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="2673c-163">Zpracovat nedokončené asynchronní akce při vykreslení</span><span class="sxs-lookup"><span data-stu-id="2673c-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="2673c-164">Asynchronní akce provedené v událostech životního cyklu nemusí být před vykreslením komponenty dokončeny.</span><span class="sxs-lookup"><span data-stu-id="2673c-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="2673c-165">Objekty mohou být při provádění metody životního cyklu `null` nebo nedokončené s daty.</span><span class="sxs-lookup"><span data-stu-id="2673c-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="2673c-166">Poskytněte logiku vykreslování pro potvrzení, že jsou objekty inicializovány.</span><span class="sxs-lookup"><span data-stu-id="2673c-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="2673c-167">Vykreslí prvky uživatelského rozhraní zástupného textu (například zprávu o načítání), zatímco objekty jsou `null`.</span><span class="sxs-lookup"><span data-stu-id="2673c-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="2673c-168">V `FetchData` součásti šablon Blazor je `OnInitializedAsync` přepsáno na asynchronně příjem dat předpovědi (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="2673c-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="2673c-169">Pokud je `forecasts` `null`, uživateli se zobrazí zpráva o načítání.</span><span class="sxs-lookup"><span data-stu-id="2673c-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="2673c-170">Jakmile se `Task` vrátí `OnInitializedAsync` dokončí, komponenta se znovu vykreslí s aktualizovaným stavem.</span><span class="sxs-lookup"><span data-stu-id="2673c-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="2673c-171">*Pages/FetchData. Razor* v šabloně serveru Blazor:</span><span class="sxs-lookup"><span data-stu-id="2673c-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="2673c-172">Vyřazení komponent pomocí IDisposable</span><span class="sxs-lookup"><span data-stu-id="2673c-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="2673c-173">Pokud komponenta implementuje <xref:System.IDisposable>, je volána [Metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) při odebrání komponenty z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2673c-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="2673c-174">Následující komponenta používá `@implements IDisposable` a metodu `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="2673c-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="2673c-175">Volání <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> v `Dispose` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="2673c-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="2673c-176">`StateHasChanged` může být vyvolána jako součást odtrhnout zobrazovací jednotky, takže v tomto okamžiku není podporována aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2673c-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="2673c-177">Zruší odběr obslužných rutin událostí z událostí .NET.</span><span class="sxs-lookup"><span data-stu-id="2673c-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="2673c-178">Následující příklady [Blazor formuláře](xref:blazor/forms-validation) ukazují, jak odpojovat obslužnou rutinu události v metodě `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="2673c-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="2673c-179">Přístup k privátnímu poli a lambda</span><span class="sxs-lookup"><span data-stu-id="2673c-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="2673c-180">Přístup k privátní metodě</span><span class="sxs-lookup"><span data-stu-id="2673c-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="2673c-181">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="2673c-181">Handle errors</span></span>

<span data-ttu-id="2673c-182">Informace o zpracování chyb během provádění metod životního cyklu naleznete v tématu <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="2673c-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="2673c-183">Stav opětovného připojení po předvykreslování</span><span class="sxs-lookup"><span data-stu-id="2673c-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="2673c-184">V aplikaci Blazor serveru, když je `RenderMode` `ServerPrerendered`, je součást zpočátku vykreslena jako součást stránky.</span><span class="sxs-lookup"><span data-stu-id="2673c-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="2673c-185">Jakmile prohlížeč vytvoří připojení zpátky k serveru, komponenta se *znovu*vykreslí a komponenta je teď interaktivní.</span><span class="sxs-lookup"><span data-stu-id="2673c-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="2673c-186">Pokud je k dispozici metoda inicializace životního cyklu "inicializujd [{Async}](xref:blazor/lifecycle#component-initialization-methods) " pro inicializaci komponenty, je metoda provedena *dvakrát*:</span><span class="sxs-lookup"><span data-stu-id="2673c-186">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="2673c-187">Když se komponenta předem vykreslí.</span><span class="sxs-lookup"><span data-stu-id="2673c-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="2673c-188">Po navázání připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="2673c-188">After the server connection has been established.</span></span>

<span data-ttu-id="2673c-189">Výsledkem může být znatelné změny v datech zobrazených v uživatelském rozhraní, když je komponenta nakonec vykreslena.</span><span class="sxs-lookup"><span data-stu-id="2673c-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="2673c-190">Abyste se vyhnuli scénáři dvojího vykreslování v aplikaci Blazor serveru:</span><span class="sxs-lookup"><span data-stu-id="2673c-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="2673c-191">Předejte identifikátor, který se dá použít k ukládání stavu do mezipaměti během předgenerování a načtení stavu po restartování aplikace.</span><span class="sxs-lookup"><span data-stu-id="2673c-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="2673c-192">Použijte identifikátor při předvykreslování k uložení stavu součásti.</span><span class="sxs-lookup"><span data-stu-id="2673c-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="2673c-193">Pro načtení stavu uloženého v mezipaměti použijte identifikátor po předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="2673c-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="2673c-194">Následující kód demonstruje aktualizované `WeatherForecastService` v aplikaci Blazor serveru založeném na šablonách, která vylučuje dvojité vykreslování:</span><span class="sxs-lookup"><span data-stu-id="2673c-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
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
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="2673c-195">Další informace o `RenderMode`najdete v tématu <xref:blazor/hosting-model-configuration#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="2673c-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="2673c-196">Rozpoznat, kdy se aplikace předvykresluje</span><span class="sxs-lookup"><span data-stu-id="2673c-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
