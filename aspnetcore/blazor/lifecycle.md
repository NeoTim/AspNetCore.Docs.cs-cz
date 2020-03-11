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
ms.openlocfilehash: ecacd0a9728cbefd716e9dc7cd8a8c62f3df6e0d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659927"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="084ab-103">Životní cyklus Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="084ab-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="084ab-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="084ab-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="084ab-105">Rozhraní Blazor zahrnuje synchronní a asynchronní metody životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="084ab-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="084ab-106">Přepište metody životního cyklu pro provádění dalších operací na součástech během inicializace a vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="084ab-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="084ab-107">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="084ab-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="084ab-108">Inicializační metody komponenty</span><span class="sxs-lookup"><span data-stu-id="084ab-108">Component initialization methods</span></span>

<span data-ttu-id="084ab-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> jsou vyvolány při inicializaci komponenty po přijetí počátečních parametrů ze své nadřazené komponenty.</span><span class="sxs-lookup"><span data-stu-id="084ab-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="084ab-110">Použijte `OnInitializedAsync`, když komponenta provede asynchronní operaci a měla by se aktualizovat po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="084ab-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="084ab-111">Pro synchronní operaci popište `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="084ab-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="084ab-112">Chcete-li provést asynchronní operaci, popište `OnInitializedAsync` a použijte klíčové slovo `await` pro operaci:</span><span class="sxs-lookup"><span data-stu-id="084ab-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="084ab-113"> serverových aplikací, které [proprerender volání jejich obsahu](xref:blazor/hosting-model-configuration#render-mode) `OnInitializedAsync` **_dvakrát_** :</span><span class="sxs-lookup"><span data-stu-id="084ab-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="084ab-114">Jednou, když je komponenta zpočátku vykreslena jako součást stránky.</span><span class="sxs-lookup"><span data-stu-id="084ab-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="084ab-115">Podruhé, když prohlížeč vytvoří připojení zpátky k serveru.</span><span class="sxs-lookup"><span data-stu-id="084ab-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="084ab-116">Chcete-li zabránit tomu, aby kód pro vývojáře v `OnInitializedAsync` běžel dvakrát, přečtěte si část [stav opětovného připojení po předvykreslování](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="084ab-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="084ab-117">I když je aplikace Blazor serveru předem vykreslovat, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno.</span><span class="sxs-lookup"><span data-stu-id="084ab-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="084ab-118">Komponenty mohou být při předvykreslování nutné pro vykreslení odlišně.</span><span class="sxs-lookup"><span data-stu-id="084ab-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="084ab-119">Další informace najdete v části [detekce při předvykreslování aplikace](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="084ab-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="084ab-120">Před nastavením parametrů</span><span class="sxs-lookup"><span data-stu-id="084ab-120">Before parameters are set</span></span>

<span data-ttu-id="084ab-121"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> nastaví parametry zadané nadřazeným prvkem komponenty ve stromu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="084ab-121"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="084ab-122"><xref:Microsoft.AspNetCore.Components.ParameterView> obsahuje celou sadu hodnot parametrů pokaždé, když `SetParametersAsync` je volána.</span><span class="sxs-lookup"><span data-stu-id="084ab-122"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="084ab-123">Výchozí implementace `SetParametersAsync` nastaví hodnotu každé vlastnosti s atributem `[Parameter]` nebo `[CascadingParameter]`, který má odpovídající hodnotu v `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="084ab-123">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="084ab-124">Parametry, které nemají odpovídající hodnotu v `ParameterView`, jsou ponechány beze změny.</span><span class="sxs-lookup"><span data-stu-id="084ab-124">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="084ab-125">Pokud `base.SetParametersAync` není vyvolán, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="084ab-125">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="084ab-126">Například neexistuje žádný požadavek na přiřazení příchozích parametrů k vlastnostem třídy.</span><span class="sxs-lookup"><span data-stu-id="084ab-126">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="084ab-127">Po nastavení parametrů</span><span class="sxs-lookup"><span data-stu-id="084ab-127">After parameters are set</span></span>

<span data-ttu-id="084ab-128">jsou volány <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*>:</span><span class="sxs-lookup"><span data-stu-id="084ab-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="084ab-129">Při inicializaci komponenty a přijetí první sady parametrů ze své nadřazené komponenty.</span><span class="sxs-lookup"><span data-stu-id="084ab-129">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="084ab-130">Po opětovném vykreslení nadřazené komponenty a dodání:</span><span class="sxs-lookup"><span data-stu-id="084ab-130">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="084ab-131">Pouze známé primitivní neměnné typy, u kterých se změnil alespoň jeden parametr.</span><span class="sxs-lookup"><span data-stu-id="084ab-131">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="084ab-132">Jakékoli parametry komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="084ab-132">Any complex-typed parameters.</span></span> <span data-ttu-id="084ab-133">Architektura nemůže zjistit, zda hodnoty parametru složitého typu jsou interně provedeny, takže se sada parametrů považuje za změněnou.</span><span class="sxs-lookup"><span data-stu-id="084ab-133">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="084ab-134">Asynchronní práce při aplikování parametrů a hodnot vlastností musí probíhat během události `OnParametersSetAsync` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="084ab-134">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="084ab-135">Po vykreslení komponenty</span><span class="sxs-lookup"><span data-stu-id="084ab-135">After component render</span></span>

<span data-ttu-id="084ab-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> jsou volány po dokončení vykreslování součásti.</span><span class="sxs-lookup"><span data-stu-id="084ab-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="084ab-137">V tuto chvíli se naplní odkazy na element a komponentu.</span><span class="sxs-lookup"><span data-stu-id="084ab-137">Element and component references are populated at this point.</span></span> <span data-ttu-id="084ab-138">Tuto fázi použijte k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace knihoven JavaScript třetích stran, které pracují s vykreslenými prvky modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="084ab-138">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="084ab-139">Parametr `firstRender` pro `OnAfterRenderAsync` a `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="084ab-139">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="084ab-140">Je nastaven na `true` při prvním vykreslení instance komponenty.</span><span class="sxs-lookup"><span data-stu-id="084ab-140">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="084ab-141">Dá se použít k zajištění toho, aby se inicializace prováděla jenom jednou.</span><span class="sxs-lookup"><span data-stu-id="084ab-141">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="084ab-142">Asynchronní práce ihned po vykreslení musí nastat během `OnAfterRenderAsync` události životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="084ab-142">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="084ab-143">I když vrátíte <xref:System.Threading.Tasks.Task> z `OnAfterRenderAsync`, rozhraní neplánuje další cyklus vykreslování pro komponentu po dokončení této úlohy.</span><span class="sxs-lookup"><span data-stu-id="084ab-143">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="084ab-144">To se vyhnete nekonečné smyčce vykreslování.</span><span class="sxs-lookup"><span data-stu-id="084ab-144">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="084ab-145">Liší se od ostatních metod životního cyklu, které naplánují další cyklus vykreslování po dokončení vrácené úlohy.</span><span class="sxs-lookup"><span data-stu-id="084ab-145">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="084ab-146">`OnAfterRender` a `OnAfterRenderAsync` *nejsou volány při předvykreslování na serveru.*</span><span class="sxs-lookup"><span data-stu-id="084ab-146">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="084ab-147">Potlačit aktualizaci uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="084ab-147">Suppress UI refreshing</span></span>

<span data-ttu-id="084ab-148">Přepište <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> pro potlačení aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="084ab-148">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="084ab-149">Pokud implementace vrátí `true`, uživatelské rozhraní se obnoví:</span><span class="sxs-lookup"><span data-stu-id="084ab-149">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="084ab-150">`ShouldRender` se volá pokaždé, když se komponenta vykreslí.</span><span class="sxs-lookup"><span data-stu-id="084ab-150">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="084ab-151">I v případě, že je `ShouldRender` přepsat, komponenta je vždy zpočátku vykreslena.</span><span class="sxs-lookup"><span data-stu-id="084ab-151">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="084ab-152">Změny stavu</span><span class="sxs-lookup"><span data-stu-id="084ab-152">State changes</span></span>

<span data-ttu-id="084ab-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> upozorní komponentu, jejíž stav byl změněn.</span><span class="sxs-lookup"><span data-stu-id="084ab-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="084ab-154">Pokud je to možné, volání `StateHasChanged` způsobí, že se komponenta znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="084ab-154">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="084ab-155">Zpracovat nedokončené asynchronní akce při vykreslení</span><span class="sxs-lookup"><span data-stu-id="084ab-155">Handle incomplete async actions at render</span></span>

<span data-ttu-id="084ab-156">Asynchronní akce provedené v událostech životního cyklu nemusí být před vykreslením komponenty dokončeny.</span><span class="sxs-lookup"><span data-stu-id="084ab-156">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="084ab-157">Objekty mohou být při provádění metody životního cyklu `null` nebo nedokončené s daty.</span><span class="sxs-lookup"><span data-stu-id="084ab-157">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="084ab-158">Poskytněte logiku vykreslování pro potvrzení, že jsou objekty inicializovány.</span><span class="sxs-lookup"><span data-stu-id="084ab-158">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="084ab-159">Vykreslí prvky uživatelského rozhraní zástupného textu (například zprávu o načítání), zatímco objekty jsou `null`.</span><span class="sxs-lookup"><span data-stu-id="084ab-159">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="084ab-160">V `FetchData` součásti šablon Blazor je `OnInitializedAsync` přepsáno na asynchronně příjem dat předpovědi (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="084ab-160">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="084ab-161">Pokud je `forecasts` `null`, uživateli se zobrazí zpráva o načítání.</span><span class="sxs-lookup"><span data-stu-id="084ab-161">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="084ab-162">Jakmile se `Task` vrátí `OnInitializedAsync` dokončí, komponenta se znovu vykreslí s aktualizovaným stavem.</span><span class="sxs-lookup"><span data-stu-id="084ab-162">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="084ab-163">*Pages/FetchData. Razor* v šabloně serveru Blazor:</span><span class="sxs-lookup"><span data-stu-id="084ab-163">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="084ab-164">Vyřazení komponent pomocí IDisposable</span><span class="sxs-lookup"><span data-stu-id="084ab-164">Component disposal with IDisposable</span></span>

<span data-ttu-id="084ab-165">Pokud komponenta implementuje <xref:System.IDisposable>, je volána [Metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) při odebrání komponenty z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="084ab-165">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="084ab-166">Následující komponenta používá `@implements IDisposable` a metodu `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="084ab-166">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="084ab-167">Volání <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> v `Dispose` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="084ab-167">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="084ab-168">`StateHasChanged` může být vyvolána jako součást odtrhnout zobrazovací jednotky, takže v tomto okamžiku není podporována aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="084ab-168">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="084ab-169">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="084ab-169">Handle errors</span></span>

<span data-ttu-id="084ab-170">Informace o zpracování chyb během provádění metod životního cyklu naleznete v tématu <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="084ab-170">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="084ab-171">Stav opětovného připojení po předvykreslování</span><span class="sxs-lookup"><span data-stu-id="084ab-171">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="084ab-172">V aplikaci Blazor serveru, když je `RenderMode` `ServerPrerendered`, je součást zpočátku vykreslena jako součást stránky.</span><span class="sxs-lookup"><span data-stu-id="084ab-172">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="084ab-173">Jakmile prohlížeč vytvoří připojení zpátky k serveru, komponenta se *znovu*vykreslí a komponenta je teď interaktivní.</span><span class="sxs-lookup"><span data-stu-id="084ab-173">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="084ab-174">Pokud je k dispozici metoda inicializace životního cyklu "inicializujd [{Async}](xref:blazor/lifecycle#component-initialization-methods) " pro inicializaci komponenty, je metoda provedena *dvakrát*:</span><span class="sxs-lookup"><span data-stu-id="084ab-174">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="084ab-175">Když se komponenta předem vykreslí.</span><span class="sxs-lookup"><span data-stu-id="084ab-175">When the component is prerendered statically.</span></span>
* <span data-ttu-id="084ab-176">Po navázání připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="084ab-176">After the server connection has been established.</span></span>

<span data-ttu-id="084ab-177">Výsledkem může být znatelné změny v datech zobrazených v uživatelském rozhraní, když je komponenta nakonec vykreslena.</span><span class="sxs-lookup"><span data-stu-id="084ab-177">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="084ab-178">Abyste se vyhnuli scénáři dvojího vykreslování v aplikaci Blazor serveru:</span><span class="sxs-lookup"><span data-stu-id="084ab-178">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="084ab-179">Předejte identifikátor, který se dá použít k ukládání stavu do mezipaměti během předgenerování a načtení stavu po restartování aplikace.</span><span class="sxs-lookup"><span data-stu-id="084ab-179">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="084ab-180">Použijte identifikátor při předvykreslování k uložení stavu součásti.</span><span class="sxs-lookup"><span data-stu-id="084ab-180">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="084ab-181">Pro načtení stavu uloženého v mezipaměti použijte identifikátor po předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="084ab-181">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="084ab-182">Následující kód demonstruje aktualizované `WeatherForecastService` v aplikaci Blazor serveru založeném na šablonách, která vylučuje dvojité vykreslování:</span><span class="sxs-lookup"><span data-stu-id="084ab-182">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="084ab-183">Další informace o `RenderMode`najdete v tématu <xref:blazor/hosting-model-configuration#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="084ab-183">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="084ab-184">Rozpoznat, kdy se aplikace předvykresluje</span><span class="sxs-lookup"><span data-stu-id="084ab-184">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
