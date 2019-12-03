---
title: Životní cyklus Blazor ASP.NET Core
author: guardrex
description: Naučte se používat metody životního cyklu komponenty Razor v aplikacích ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
no-loc:
- Blazor
uid: blazor/lifecycle
ms.openlocfilehash: 1482f6b2147c74b11836e8029401bb8bcb3cdb2d
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681411"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="5adb6-103">Životní cyklus Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5adb6-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="5adb6-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="5adb6-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="5adb6-105">Rozhraní Blazor zahrnuje synchronní a asynchronní metody životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="5adb6-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="5adb6-106">Přepište metody životního cyklu pro provádění dalších operací na součástech během inicializace a vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="5adb6-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="5adb6-107">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="5adb6-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="5adb6-108">Inicializační metody komponenty</span><span class="sxs-lookup"><span data-stu-id="5adb6-108">Component initialization methods</span></span>

<span data-ttu-id="5adb6-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> spustit kód, který inicializuje komponentu.</span><span class="sxs-lookup"><span data-stu-id="5adb6-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> execute code that initializes a component.</span></span> <span data-ttu-id="5adb6-110">Tyto metody jsou volány pouze jednou při prvním vytvoření instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="5adb6-110">These methods are only called one time when the component is first instantiated.</span></span>

<span data-ttu-id="5adb6-111">K provedení asynchronní operace použijte `OnInitializedAsync` a klíčové slovo `await` na operaci:</span><span class="sxs-lookup"><span data-stu-id="5adb6-111">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="5adb6-112">Asynchronní práce během inicializace komponenty se musí vyskytnout během `OnInitializedAsync` události životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="5adb6-112">Asynchronous work during component initialization must occur during the `OnInitializedAsync` lifecycle event.</span></span>

<span data-ttu-id="5adb6-113">Pro synchronní operaci použijte `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="5adb6-113">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

### <a name="before-parameters-are-set"></a><span data-ttu-id="5adb6-114">Před nastavením parametrů</span><span class="sxs-lookup"><span data-stu-id="5adb6-114">Before parameters are set</span></span>

<span data-ttu-id="5adb6-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> nastaví parametry zadané nadřazeným prvkem komponenty ve stromu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="5adb6-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="5adb6-116"><xref:Microsoft.AspNetCore.Components.ParameterView> obsahuje celou sadu hodnot parametrů pokaždé, když `SetParametersAsync` je volána.</span><span class="sxs-lookup"><span data-stu-id="5adb6-116"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="5adb6-117">Výchozí implementace `SetParametersAsync` nastaví hodnotu každé vlastnosti dekorované pomocí atributu `[Parameter]` nebo `[CascadingParameter]`, který má odpovídající hodnotu v `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="5adb6-117">The default implementation of `SetParametersAsync` sets the value of each property decorated with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="5adb6-118">Parametry, které nemají odpovídající hodnotu v `ParameterView`, jsou ponechány beze změny.</span><span class="sxs-lookup"><span data-stu-id="5adb6-118">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="5adb6-119">Pokud `base.SetParametersAync` není vyvolán, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="5adb6-119">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="5adb6-120">Například neexistuje žádný požadavek na přiřazení příchozích parametrů k vlastnostem třídy.</span><span class="sxs-lookup"><span data-stu-id="5adb6-120">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="5adb6-121">Po nastavení parametrů</span><span class="sxs-lookup"><span data-stu-id="5adb6-121">After parameters are set</span></span>

<span data-ttu-id="5adb6-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> jsou volány, když komponenta přijímá parametry z nadřazené položky a hodnoty jsou přiřazeny vlastnostem.</span><span class="sxs-lookup"><span data-stu-id="5adb6-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="5adb6-123">Tyto metody jsou spouštěny po inicializaci komponenty a pokaždé, když jsou zadány nové hodnoty parametrů:</span><span class="sxs-lookup"><span data-stu-id="5adb6-123">These methods are executed after component initialization and each time new parameter values are specified:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="5adb6-124">Asynchronní práce při aplikování parametrů a hodnot vlastností musí probíhat během události `OnParametersSetAsync` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="5adb6-124">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="5adb6-125">Po vykreslení komponenty</span><span class="sxs-lookup"><span data-stu-id="5adb6-125">After component render</span></span>

<span data-ttu-id="5adb6-126"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> jsou volány po dokončení vykreslování součásti.</span><span class="sxs-lookup"><span data-stu-id="5adb6-126"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="5adb6-127">V tuto chvíli se naplní odkazy na element a komponentu.</span><span class="sxs-lookup"><span data-stu-id="5adb6-127">Element and component references are populated at this point.</span></span> <span data-ttu-id="5adb6-128">Tuto fázi použijte k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace knihoven JavaScript třetích stran, které pracují s vykreslenými prvky modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="5adb6-128">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="5adb6-129">Parametr `firstRender` pro `OnAfterRenderAsync` a `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="5adb6-129">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="5adb6-130">Je nastaven na `true` při prvním vykreslení instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="5adb6-130">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="5adb6-131">Dá se použít k zajištění toho, aby se inicializace prováděla jenom jednou.</span><span class="sxs-lookup"><span data-stu-id="5adb6-131">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="5adb6-132">Asynchronní práce ihned po vykreslení musí nastat během `OnAfterRenderAsync` události životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="5adb6-132">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="5adb6-133">I když vrátíte <xref:System.Threading.Tasks.Task> z `OnAfterRenderAsync`, rozhraní neplánuje další cyklus vykreslování pro komponentu po dokončení této úlohy.</span><span class="sxs-lookup"><span data-stu-id="5adb6-133">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="5adb6-134">To se vyhnete nekonečné smyčce vykreslování.</span><span class="sxs-lookup"><span data-stu-id="5adb6-134">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="5adb6-135">Liší se od ostatních metod životního cyklu, které naplánují další cyklus vykreslování po dokončení vrácené úlohy.</span><span class="sxs-lookup"><span data-stu-id="5adb6-135">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="5adb6-136">`OnAfterRender` a `OnAfterRenderAsync` *nejsou volány při předvykreslování na serveru.*</span><span class="sxs-lookup"><span data-stu-id="5adb6-136">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="5adb6-137">Potlačit aktualizaci uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="5adb6-137">Suppress UI refreshing</span></span>

<span data-ttu-id="5adb6-138">Přepište <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> pro potlačení aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5adb6-138">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="5adb6-139">Pokud implementace vrátí `true`, uživatelské rozhraní se obnoví:</span><span class="sxs-lookup"><span data-stu-id="5adb6-139">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="5adb6-140">`ShouldRender` se volá pokaždé, když se komponenta vykreslí.</span><span class="sxs-lookup"><span data-stu-id="5adb6-140">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="5adb6-141">I v případě, že je `ShouldRender` přepsat, komponenta je vždy zpočátku vykreslena.</span><span class="sxs-lookup"><span data-stu-id="5adb6-141">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="5adb6-142">Změny stavu</span><span class="sxs-lookup"><span data-stu-id="5adb6-142">State changes</span></span>

<span data-ttu-id="5adb6-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> upozorní komponentu, jejíž stav byl změněn.</span><span class="sxs-lookup"><span data-stu-id="5adb6-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="5adb6-144">Pokud je to možné, volání `StateHasChanged` způsobí, že se komponenta znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="5adb6-144">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="5adb6-145">Zpracovat nedokončené asynchronní akce při vykreslení</span><span class="sxs-lookup"><span data-stu-id="5adb6-145">Handle incomplete async actions at render</span></span>

<span data-ttu-id="5adb6-146">Asynchronní akce provedené v událostech životního cyklu nemusí být před vykreslením komponenty dokončeny.</span><span class="sxs-lookup"><span data-stu-id="5adb6-146">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="5adb6-147">Objekty mohou být při provádění metody životního cyklu `null` nebo nedokončené s daty.</span><span class="sxs-lookup"><span data-stu-id="5adb6-147">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="5adb6-148">Poskytněte logiku vykreslování pro potvrzení, že jsou objekty inicializovány.</span><span class="sxs-lookup"><span data-stu-id="5adb6-148">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="5adb6-149">Vykreslí prvky uživatelského rozhraní zástupného textu (například zprávu o načítání), zatímco objekty jsou `null`.</span><span class="sxs-lookup"><span data-stu-id="5adb6-149">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="5adb6-150">V `FetchData` součásti šablon Blazor je `OnInitializedAsync` přepsáno na asynchronně příjem dat předpovědi (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="5adb6-150">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="5adb6-151">Pokud je `forecasts` `null`, uživateli se zobrazí zpráva o načítání.</span><span class="sxs-lookup"><span data-stu-id="5adb6-151">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="5adb6-152">Jakmile se `Task` vrátí `OnInitializedAsync` dokončí, komponenta se znovu vykreslí s aktualizovaným stavem.</span><span class="sxs-lookup"><span data-stu-id="5adb6-152">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="5adb6-153">*Pages/FetchData. Razor* v šabloně serveru Blazor:</span><span class="sxs-lookup"><span data-stu-id="5adb6-153">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-cshtml[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="5adb6-154">Vyřazení komponent pomocí IDisposable</span><span class="sxs-lookup"><span data-stu-id="5adb6-154">Component disposal with IDisposable</span></span>

<span data-ttu-id="5adb6-155">Pokud komponenta implementuje <xref:System.IDisposable>, je volána [Metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) při odebrání komponenty z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5adb6-155">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="5adb6-156">Následující komponenta používá `@implements IDisposable` a metodu `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="5adb6-156">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
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
> <span data-ttu-id="5adb6-157">Volání <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> v `Dispose` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="5adb6-157">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="5adb6-158">`StateHasChanged` může být vyvolána jako součást odtrhnout zobrazovací jednotky, takže v tomto okamžiku není podporována aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5adb6-158">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="5adb6-159">Ošetření chyb</span><span class="sxs-lookup"><span data-stu-id="5adb6-159">Handle errors</span></span>

<span data-ttu-id="5adb6-160">Informace o zpracování chyb během provádění metod životního cyklu naleznete v tématu <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="5adb6-160">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>
