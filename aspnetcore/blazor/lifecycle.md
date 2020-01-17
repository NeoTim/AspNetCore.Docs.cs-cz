---
title: Životní cyklus Blazor ASP.NET Core
author: guardrex
description: Naučte se používat metody životního cyklu komponenty Razor v aplikacích ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: df5bb676df59b538179a69978040521c4ee78ed1
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146365"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="ceac3-103">Životní cyklus Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ceac3-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="ceac3-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ceac3-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ceac3-105">Rozhraní Blazor zahrnuje synchronní a asynchronní metody životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="ceac3-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="ceac3-106">Přepište metody životního cyklu pro provádění dalších operací na součástech během inicializace a vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="ceac3-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="ceac3-107">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="ceac3-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="ceac3-108">Inicializační metody komponenty</span><span class="sxs-lookup"><span data-stu-id="ceac3-108">Component initialization methods</span></span>

<span data-ttu-id="ceac3-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> jsou vyvolány při inicializaci komponenty po přijetí počátečních parametrů ze své nadřazené komponenty.</span><span class="sxs-lookup"><span data-stu-id="ceac3-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="ceac3-110">Použijte `OnInitializedAsync`, když komponenta provede asynchronní operaci a měla by se aktualizovat po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="ceac3-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span> <span data-ttu-id="ceac3-111">Tyto metody jsou volány pouze jednou při prvním vytvoření instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="ceac3-111">These methods are only called one time when the component is first instantiated.</span></span>

<span data-ttu-id="ceac3-112">Pro synchronní operaci popište `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="ceac3-112">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="ceac3-113">Chcete-li provést asynchronní operaci, popište `OnInitializedAsync` a použijte klíčové slovo `await` pro operaci:</span><span class="sxs-lookup"><span data-stu-id="ceac3-113">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

### <a name="before-parameters-are-set"></a><span data-ttu-id="ceac3-114">Před nastavením parametrů</span><span class="sxs-lookup"><span data-stu-id="ceac3-114">Before parameters are set</span></span>

<span data-ttu-id="ceac3-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> nastaví parametry zadané nadřazeným prvkem komponenty ve stromu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="ceac3-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="ceac3-116"><xref:Microsoft.AspNetCore.Components.ParameterView> obsahuje celou sadu hodnot parametrů pokaždé, když `SetParametersAsync` je volána.</span><span class="sxs-lookup"><span data-stu-id="ceac3-116"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="ceac3-117">Výchozí implementace `SetParametersAsync` nastaví hodnotu každé vlastnosti s atributem `[Parameter]` nebo `[CascadingParameter]`, který má odpovídající hodnotu v `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="ceac3-117">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="ceac3-118">Parametry, které nemají odpovídající hodnotu v `ParameterView`, jsou ponechány beze změny.</span><span class="sxs-lookup"><span data-stu-id="ceac3-118">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="ceac3-119">Pokud `base.SetParametersAync` není vyvolán, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="ceac3-119">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="ceac3-120">Například neexistuje žádný požadavek na přiřazení příchozích parametrů k vlastnostem třídy.</span><span class="sxs-lookup"><span data-stu-id="ceac3-120">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="ceac3-121">Po nastavení parametrů</span><span class="sxs-lookup"><span data-stu-id="ceac3-121">After parameters are set</span></span>

<span data-ttu-id="ceac3-122">jsou volány <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*>:</span><span class="sxs-lookup"><span data-stu-id="ceac3-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="ceac3-123">Při inicializaci komponenty a přijetí první sady parametrů ze své nadřazené komponenty.</span><span class="sxs-lookup"><span data-stu-id="ceac3-123">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="ceac3-124">Po opětovném vykreslení nadřazené komponenty a dodání:</span><span class="sxs-lookup"><span data-stu-id="ceac3-124">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="ceac3-125">Pouze známé primitivní neměnné typy, u kterých se změnil alespoň jeden parametr.</span><span class="sxs-lookup"><span data-stu-id="ceac3-125">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="ceac3-126">Jakékoli parametry komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="ceac3-126">Any complex-typed parameters.</span></span> <span data-ttu-id="ceac3-127">Architektura nemůže zjistit, zda hodnoty parametru složitého typu jsou interně provedeny, takže se sada parametrů považuje za změněnou.</span><span class="sxs-lookup"><span data-stu-id="ceac3-127">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="ceac3-128">Asynchronní práce při aplikování parametrů a hodnot vlastností musí probíhat během události `OnParametersSetAsync` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="ceac3-128">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="ceac3-129">Po vykreslení komponenty</span><span class="sxs-lookup"><span data-stu-id="ceac3-129">After component render</span></span>

<span data-ttu-id="ceac3-130"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> jsou volány po dokončení vykreslování součásti.</span><span class="sxs-lookup"><span data-stu-id="ceac3-130"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="ceac3-131">V tuto chvíli se naplní odkazy na element a komponentu.</span><span class="sxs-lookup"><span data-stu-id="ceac3-131">Element and component references are populated at this point.</span></span> <span data-ttu-id="ceac3-132">Tuto fázi použijte k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace knihoven JavaScript třetích stran, které pracují s vykreslenými prvky modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="ceac3-132">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="ceac3-133">Parametr `firstRender` pro `OnAfterRenderAsync` a `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="ceac3-133">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="ceac3-134">Je nastaven na `true` při prvním vykreslení instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="ceac3-134">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="ceac3-135">Dá se použít k zajištění toho, aby se inicializace prováděla jenom jednou.</span><span class="sxs-lookup"><span data-stu-id="ceac3-135">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="ceac3-136">Asynchronní práce ihned po vykreslení musí nastat během `OnAfterRenderAsync` události životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="ceac3-136">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="ceac3-137">I když vrátíte <xref:System.Threading.Tasks.Task> z `OnAfterRenderAsync`, rozhraní neplánuje další cyklus vykreslování pro komponentu po dokončení této úlohy.</span><span class="sxs-lookup"><span data-stu-id="ceac3-137">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="ceac3-138">To se vyhnete nekonečné smyčce vykreslování.</span><span class="sxs-lookup"><span data-stu-id="ceac3-138">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="ceac3-139">Liší se od ostatních metod životního cyklu, které naplánují další cyklus vykreslování po dokončení vrácené úlohy.</span><span class="sxs-lookup"><span data-stu-id="ceac3-139">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="ceac3-140">`OnAfterRender` a `OnAfterRenderAsync` *nejsou volány při předvykreslování na serveru.*</span><span class="sxs-lookup"><span data-stu-id="ceac3-140">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="ceac3-141">Potlačit aktualizaci uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="ceac3-141">Suppress UI refreshing</span></span>

<span data-ttu-id="ceac3-142">Přepište <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> pro potlačení aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ceac3-142">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="ceac3-143">Pokud implementace vrátí `true`, uživatelské rozhraní se obnoví:</span><span class="sxs-lookup"><span data-stu-id="ceac3-143">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="ceac3-144">`ShouldRender` se volá pokaždé, když se komponenta vykreslí.</span><span class="sxs-lookup"><span data-stu-id="ceac3-144">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="ceac3-145">I v případě, že je `ShouldRender` přepsat, komponenta je vždy zpočátku vykreslena.</span><span class="sxs-lookup"><span data-stu-id="ceac3-145">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="ceac3-146">Změny stavu</span><span class="sxs-lookup"><span data-stu-id="ceac3-146">State changes</span></span>

<span data-ttu-id="ceac3-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> upozorní komponentu, jejíž stav byl změněn.</span><span class="sxs-lookup"><span data-stu-id="ceac3-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="ceac3-148">Pokud je to možné, volání `StateHasChanged` způsobí, že se komponenta znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="ceac3-148">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="ceac3-149">Zpracovat nedokončené asynchronní akce při vykreslení</span><span class="sxs-lookup"><span data-stu-id="ceac3-149">Handle incomplete async actions at render</span></span>

<span data-ttu-id="ceac3-150">Asynchronní akce provedené v událostech životního cyklu nemusí být před vykreslením komponenty dokončeny.</span><span class="sxs-lookup"><span data-stu-id="ceac3-150">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="ceac3-151">Objekty mohou být při provádění metody životního cyklu `null` nebo nedokončené s daty.</span><span class="sxs-lookup"><span data-stu-id="ceac3-151">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="ceac3-152">Poskytněte logiku vykreslování pro potvrzení, že jsou objekty inicializovány.</span><span class="sxs-lookup"><span data-stu-id="ceac3-152">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="ceac3-153">Vykreslí prvky uživatelského rozhraní zástupného textu (například zprávu o načítání), zatímco objekty jsou `null`.</span><span class="sxs-lookup"><span data-stu-id="ceac3-153">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="ceac3-154">V `FetchData` součásti šablon Blazor je `OnInitializedAsync` přepsáno na asynchronně příjem dat předpovědi (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="ceac3-154">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="ceac3-155">Pokud je `forecasts` `null`, uživateli se zobrazí zpráva o načítání.</span><span class="sxs-lookup"><span data-stu-id="ceac3-155">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="ceac3-156">Jakmile se `Task` vrátí `OnInitializedAsync` dokončí, komponenta se znovu vykreslí s aktualizovaným stavem.</span><span class="sxs-lookup"><span data-stu-id="ceac3-156">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="ceac3-157">*Pages/FetchData. Razor* v šabloně serveru Blazor:</span><span class="sxs-lookup"><span data-stu-id="ceac3-157">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="ceac3-158">Vyřazení komponent pomocí IDisposable</span><span class="sxs-lookup"><span data-stu-id="ceac3-158">Component disposal with IDisposable</span></span>

<span data-ttu-id="ceac3-159">Pokud komponenta implementuje <xref:System.IDisposable>, je volána [Metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) při odebrání komponenty z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ceac3-159">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="ceac3-160">Následující komponenta používá `@implements IDisposable` a metodu `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="ceac3-160">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="ceac3-161">Volání <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> v `Dispose` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="ceac3-161">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="ceac3-162">`StateHasChanged` může být vyvolána jako součást odtrhnout zobrazovací jednotky, takže v tomto okamžiku není podporována aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ceac3-162">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="ceac3-163">Ošetření chyb</span><span class="sxs-lookup"><span data-stu-id="ceac3-163">Handle errors</span></span>

<span data-ttu-id="ceac3-164">Informace o zpracování chyb během provádění metod životního cyklu naleznete v tématu <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="ceac3-164">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>
