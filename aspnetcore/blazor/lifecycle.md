---
title: Životní Blazor cyklus ASP.NET Core
author: guardrex
description: Naučte se používat Razor metody životního cyklu komponent Blazor v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: 81699158a161d0e9c9621235840979ebcd634a7e
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976698"
---
# <a name="aspnet-core-blazor-lifecycle"></a><span data-ttu-id="14f21-103">Životní Blazor cyklus ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="14f21-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="14f21-104">Od [Luke Latham](https://github.com/guardrex) a [Daniel Skořepa](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="14f21-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="14f21-105">Blazor Rozhraní zahrnuje synchronní a asynchronní metody životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="14f21-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="14f21-106">Přepište metody životního cyklu pro provádění dalších operací na součástech během inicializace a vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="14f21-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="14f21-107">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="14f21-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="14f21-108">Inicializační metody komponenty</span><span class="sxs-lookup"><span data-stu-id="14f21-108">Component initialization methods</span></span>

<span data-ttu-id="14f21-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> jsou vyvolány při inicializaci komponenty po přijetí počátečních parametrů ze své nadřazené komponenty.</span><span class="sxs-lookup"><span data-stu-id="14f21-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="14f21-110">Použijte `OnInitializedAsync` , když komponenta provede asynchronní operaci a měla by se aktualizovat po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="14f21-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="14f21-111">Pro synchronní operaci popište `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="14f21-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="14f21-112">Chcete-li provést asynchronní operaci, `OnInitializedAsync` přepište a `await` použijte klíčové slovo pro operaci:</span><span class="sxs-lookup"><span data-stu-id="14f21-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="14f21-113">Serverové aplikace, které [proprerender](xref:blazor/hosting-model-configuration#render-mode) volání `OnInitializedAsync` jejich obsahu **_dvakrát_**:</span><span class="sxs-lookup"><span data-stu-id="14f21-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="14f21-114">Jednou, když je komponenta zpočátku vykreslena jako součást stránky.</span><span class="sxs-lookup"><span data-stu-id="14f21-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="14f21-115">Podruhé, když prohlížeč vytvoří připojení zpátky k serveru.</span><span class="sxs-lookup"><span data-stu-id="14f21-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="14f21-116">Chcete-li zabránit tomu `OnInitializedAsync` , aby se kód vývojáře v Nespuštěn dvakrát, přečtěte si část [stav opětovného připojení po předvykreslování](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="14f21-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="14f21-117">I když Blazor je serverová aplikace předem vykreslovat, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno.</span><span class="sxs-lookup"><span data-stu-id="14f21-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="14f21-118">Komponenty mohou být při předvykreslování nutné pro vykreslení odlišně.</span><span class="sxs-lookup"><span data-stu-id="14f21-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="14f21-119">Další informace najdete v části [detekce při předvykreslování aplikace](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="14f21-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="14f21-120">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="14f21-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="14f21-121">Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.</span><span class="sxs-lookup"><span data-stu-id="14f21-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="14f21-122">Před nastavením parametrů</span><span class="sxs-lookup"><span data-stu-id="14f21-122">Before parameters are set</span></span>

<span data-ttu-id="14f21-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A>nastaví parametry zadané nadřazeným prvkem komponenty ve stromu vykreslování:</span><span class="sxs-lookup"><span data-stu-id="14f21-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="14f21-124"><xref:Microsoft.AspNetCore.Components.ParameterView>obsahuje celou sadu hodnot parametrů pokaždé, když `SetParametersAsync` je volána.</span><span class="sxs-lookup"><span data-stu-id="14f21-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="14f21-125">Výchozí implementace `SetParametersAsync` sady nastaví hodnotu každé vlastnosti s atributem `[Parameter]` nebo `[CascadingParameter]` , který má odpovídající hodnotu v. `ParameterView`</span><span class="sxs-lookup"><span data-stu-id="14f21-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="14f21-126">Parametry, které nemají odpovídající hodnotu v `ParameterView` , jsou ponechány beze změny.</span><span class="sxs-lookup"><span data-stu-id="14f21-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="14f21-127">Pokud `base.SetParametersAync` není vyvolána, vlastní kód může interpretovat hodnotu příchozích parametrů jakýmkoli způsobem, který je vyžadován.</span><span class="sxs-lookup"><span data-stu-id="14f21-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="14f21-128">Například neexistuje žádný požadavek na přiřazení příchozích parametrů k vlastnostem třídy.</span><span class="sxs-lookup"><span data-stu-id="14f21-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="14f21-129">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="14f21-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="14f21-130">Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.</span><span class="sxs-lookup"><span data-stu-id="14f21-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="14f21-131">Po nastavení parametrů</span><span class="sxs-lookup"><span data-stu-id="14f21-131">After parameters are set</span></span>

<span data-ttu-id="14f21-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> jsou volány:</span><span class="sxs-lookup"><span data-stu-id="14f21-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> are called:</span></span>

* <span data-ttu-id="14f21-133">Při inicializaci komponenty a přijetí první sady parametrů ze své nadřazené komponenty.</span><span class="sxs-lookup"><span data-stu-id="14f21-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="14f21-134">Po opětovném vykreslení nadřazené komponenty a dodání:</span><span class="sxs-lookup"><span data-stu-id="14f21-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="14f21-135">Pouze známé primitivní neměnné typy, u kterých se změnil alespoň jeden parametr.</span><span class="sxs-lookup"><span data-stu-id="14f21-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="14f21-136">Jakékoli parametry komplexního typu.</span><span class="sxs-lookup"><span data-stu-id="14f21-136">Any complex-typed parameters.</span></span> <span data-ttu-id="14f21-137">Architektura nemůže zjistit, zda hodnoty parametru složitého typu jsou interně provedeny, takže se sada parametrů považuje za změněnou.</span><span class="sxs-lookup"><span data-stu-id="14f21-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="14f21-138">Asynchronní práce při aplikování parametrů a hodnot vlastností musí probíhat `OnParametersSetAsync` během události životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="14f21-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="14f21-139">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="14f21-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="14f21-140">Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.</span><span class="sxs-lookup"><span data-stu-id="14f21-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="14f21-141">Po vykreslení komponenty</span><span class="sxs-lookup"><span data-stu-id="14f21-141">After component render</span></span>

<span data-ttu-id="14f21-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> jsou volány po dokončení vykreslování součásti.</span><span class="sxs-lookup"><span data-stu-id="14f21-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> are called after a component has finished rendering.</span></span> <span data-ttu-id="14f21-143">V tuto chvíli se naplní odkazy na element a komponentu.</span><span class="sxs-lookup"><span data-stu-id="14f21-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="14f21-144">Tuto fázi použijte k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace knihoven JavaScript třetích stran, které pracují s vykreslenými prvky modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="14f21-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="14f21-145">`firstRender` Parametr pro `OnAfterRenderAsync` a `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="14f21-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="14f21-146">Je nastaven na `true` první, kdy je instance komponenty vykreslena.</span><span class="sxs-lookup"><span data-stu-id="14f21-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="14f21-147">Dá se použít k zajištění toho, aby se inicializace prováděla jenom jednou.</span><span class="sxs-lookup"><span data-stu-id="14f21-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="14f21-148">Asynchronní práce ihned po vykreslení musí nastat během události `OnAfterRenderAsync` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="14f21-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="14f21-149">I když vrátíte <xref:System.Threading.Tasks.Task> z `OnAfterRenderAsync`, rozhraní neplánuje další cyklus vykreslování pro komponentu po dokončení této úlohy.</span><span class="sxs-lookup"><span data-stu-id="14f21-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="14f21-150">To se vyhnete nekonečné smyčce vykreslování.</span><span class="sxs-lookup"><span data-stu-id="14f21-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="14f21-151">Liší se od ostatních metod životního cyklu, které naplánují další cyklus vykreslování po dokončení vrácené úlohy.</span><span class="sxs-lookup"><span data-stu-id="14f21-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="14f21-152">`OnAfterRender`a `OnAfterRenderAsync` *nejsou volány při předvykreslování na serveru.*</span><span class="sxs-lookup"><span data-stu-id="14f21-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="14f21-153">Pokud jsou nastaveny jakékoli obslužné rutiny událostí, odpojte je při vyřazení.</span><span class="sxs-lookup"><span data-stu-id="14f21-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="14f21-154">Další informace naleznete v části [Odstranění součásti s](#component-disposal-with-idisposable) rozhraním IDisposable.</span><span class="sxs-lookup"><span data-stu-id="14f21-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="14f21-155">Potlačit aktualizaci uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="14f21-155">Suppress UI refreshing</span></span>

<span data-ttu-id="14f21-156">Přepsáním <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> potlačíte aktualizaci uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="14f21-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to suppress UI refreshing.</span></span> <span data-ttu-id="14f21-157">Pokud se implementace vrátí `true`, uživatelské rozhraní se obnoví:</span><span class="sxs-lookup"><span data-stu-id="14f21-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="14f21-158">`ShouldRender`je volána při každém vygenerování součásti.</span><span class="sxs-lookup"><span data-stu-id="14f21-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="14f21-159">I když `ShouldRender` je přepsat, komponenta je vždy zpočátku vykreslena.</span><span class="sxs-lookup"><span data-stu-id="14f21-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="14f21-160">Změny stavu</span><span class="sxs-lookup"><span data-stu-id="14f21-160">State changes</span></span>

<span data-ttu-id="14f21-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>upozorní komponentu, že její stav se změnil.</span><span class="sxs-lookup"><span data-stu-id="14f21-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> notifies the component that its state has changed.</span></span> <span data-ttu-id="14f21-162">V případě potřeby volání `StateHasChanged` způsobí, že se komponenta znovu vykreslí.</span><span class="sxs-lookup"><span data-stu-id="14f21-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="14f21-163">Zpracovat nedokončené asynchronní akce při vykreslení</span><span class="sxs-lookup"><span data-stu-id="14f21-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="14f21-164">Asynchronní akce provedené v událostech životního cyklu nemusí být před vykreslením komponenty dokončeny.</span><span class="sxs-lookup"><span data-stu-id="14f21-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="14f21-165">Objekty mohou být `null` při provádění metody životního cyklu nebo neúplná data naplněna daty.</span><span class="sxs-lookup"><span data-stu-id="14f21-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="14f21-166">Poskytněte logiku vykreslování pro potvrzení, že jsou objekty inicializovány.</span><span class="sxs-lookup"><span data-stu-id="14f21-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="14f21-167">Vykreslí zástupné prvky uživatelského rozhraní (například zprávu o načítání), `null`zatímco objekty jsou.</span><span class="sxs-lookup"><span data-stu-id="14f21-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="14f21-168">V `FetchData` Blazor komponentě šablony `OnInitializedAsync` je přepsáno na asynchronně příjem dat předpovědi (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="14f21-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="14f21-169">`null`V takovém případě `forecasts` se uživateli zobrazí zpráva o načítání.</span><span class="sxs-lookup"><span data-stu-id="14f21-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="14f21-170">`Task` Po `OnInitializedAsync` úspěšném dokončení se komponenta znovu vykreslí s aktualizovaným stavem.</span><span class="sxs-lookup"><span data-stu-id="14f21-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="14f21-171">*Pages/FetchData. Razor* v Blazor šabloně serveru:</span><span class="sxs-lookup"><span data-stu-id="14f21-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="14f21-172">Vyřazení komponent pomocí IDisposable</span><span class="sxs-lookup"><span data-stu-id="14f21-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="14f21-173">Pokud komponenta implementuje <xref:System.IDisposable>, je volána [Metoda Dispose](/dotnet/standard/garbage-collection/implementing-dispose) při odebrání komponenty z uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="14f21-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="14f21-174">Následující komponenta používá `@implements IDisposable` a `Dispose` metodu:</span><span class="sxs-lookup"><span data-stu-id="14f21-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="14f21-175">Volání <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> v `Dispose` není podporováno.</span><span class="sxs-lookup"><span data-stu-id="14f21-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in `Dispose` isn't supported.</span></span> <span data-ttu-id="14f21-176">`StateHasChanged`může být vyvolána jako součást odtrhnout zobrazovací jednotky, takže v tomto okamžiku není podporována aktualizace uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="14f21-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="14f21-177">Zruší odběr obslužných rutin událostí z událostí .NET.</span><span class="sxs-lookup"><span data-stu-id="14f21-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="14f21-178">Následující [ Blazor příklady formulářů](xref:blazor/forms-validation) ukazují, jak odpojovat obslužnou rutinu události v `Dispose` metodě:</span><span class="sxs-lookup"><span data-stu-id="14f21-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="14f21-179">Přístup k privátnímu poli a lambda</span><span class="sxs-lookup"><span data-stu-id="14f21-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="14f21-180">Přístup k privátní metodě</span><span class="sxs-lookup"><span data-stu-id="14f21-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="14f21-181">Ošetření chyb</span><span class="sxs-lookup"><span data-stu-id="14f21-181">Handle errors</span></span>

<span data-ttu-id="14f21-182">Informace o zpracování chyb během provádění metod životního cyklu naleznete <xref:blazor/handle-errors#lifecycle-methods>v tématu.</span><span class="sxs-lookup"><span data-stu-id="14f21-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="14f21-183">Stav opětovného připojení po předvykreslování</span><span class="sxs-lookup"><span data-stu-id="14f21-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="14f21-184">V Blazor serverové aplikaci, kde `RenderMode` je `ServerPrerendered`, se komponenta zpočátku generuje jako součást stránky staticky.</span><span class="sxs-lookup"><span data-stu-id="14f21-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="14f21-185">Jakmile prohlížeč vytvoří připojení zpátky k serveru, komponenta se *znovu*vykreslí a komponenta je teď interaktivní.</span><span class="sxs-lookup"><span data-stu-id="14f21-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="14f21-186">Pokud je k dispozici metoda inicializace životního cyklu "inicializujd [{Async}](#component-initialization-methods) " pro inicializaci komponenty, je metoda provedena *dvakrát*:</span><span class="sxs-lookup"><span data-stu-id="14f21-186">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="14f21-187">Když se komponenta předem vykreslí.</span><span class="sxs-lookup"><span data-stu-id="14f21-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="14f21-188">Po navázání připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="14f21-188">After the server connection has been established.</span></span>

<span data-ttu-id="14f21-189">Výsledkem může být znatelné změny v datech zobrazených v uživatelském rozhraní, když je komponenta nakonec vykreslena.</span><span class="sxs-lookup"><span data-stu-id="14f21-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="14f21-190">Abyste se vyhnuli scénáři dvojího vykreslování Blazor v serverové aplikaci:</span><span class="sxs-lookup"><span data-stu-id="14f21-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="14f21-191">Předejte identifikátor, který se dá použít k ukládání stavu do mezipaměti během předgenerování a načtení stavu po restartování aplikace.</span><span class="sxs-lookup"><span data-stu-id="14f21-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="14f21-192">Použijte identifikátor při předvykreslování k uložení stavu součásti.</span><span class="sxs-lookup"><span data-stu-id="14f21-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="14f21-193">Pro načtení stavu uloženého v mezipaměti použijte identifikátor po předvykreslování.</span><span class="sxs-lookup"><span data-stu-id="14f21-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="14f21-194">Následující kód demonstruje aktualizaci `WeatherForecastService` v serverové aplikaci založené na Blazor šablonách, která vylučuje dvojité vykreslování:</span><span class="sxs-lookup"><span data-stu-id="14f21-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="14f21-195">Další informace o `RenderMode`naleznete v tématu <xref:blazor/hosting-model-configuration#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="14f21-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="14f21-196">Rozpoznat, kdy se aplikace předvykresluje</span><span class="sxs-lookup"><span data-stu-id="14f21-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="cancelable-background-work"></a><span data-ttu-id="14f21-197">Zrušit práci na pozadí</span><span class="sxs-lookup"><span data-stu-id="14f21-197">Cancelable background work</span></span>

<span data-ttu-id="14f21-198">Komponenty často provádějí dlouhotrvající práci na pozadí, jako je například vytváření síťových volání (<xref:System.Net.Http.HttpClient>) a interakce s databázemi.</span><span class="sxs-lookup"><span data-stu-id="14f21-198">Components often perform long-running background work, such as making network calls (<xref:System.Net.Http.HttpClient>) and interacting with databases.</span></span> <span data-ttu-id="14f21-199">Je žádoucí zastavit práci na pozadí, aby se v různých situacích šetřily systémové prostředky.</span><span class="sxs-lookup"><span data-stu-id="14f21-199">It's desirable to stop the background work to conserve system resources in several situations.</span></span> <span data-ttu-id="14f21-200">Například asynchronní operace na pozadí se nezastaví automaticky, když uživatel přejde mimo komponentu.</span><span class="sxs-lookup"><span data-stu-id="14f21-200">For example, background asynchronous operations don't automatically stop when a user navigates away from a component.</span></span>

<span data-ttu-id="14f21-201">Mezi další důvody, proč mohou pracovní položky na pozadí vyžadovat zrušení patří:</span><span class="sxs-lookup"><span data-stu-id="14f21-201">Other reasons why background work items might require cancellation include:</span></span>

* <span data-ttu-id="14f21-202">Spuštění úlohy na pozadí bylo zahájeno s chybnými vstupními daty nebo parametry zpracování.</span><span class="sxs-lookup"><span data-stu-id="14f21-202">An executing background task was started with faulty input data or processing parameters.</span></span>
* <span data-ttu-id="14f21-203">Aktuální sada spuštěných pracovních položek na pozadí musí být nahrazena novou sadou pracovních položek.</span><span class="sxs-lookup"><span data-stu-id="14f21-203">The current set of executing background work items must be replaced with a new set of work items.</span></span>
* <span data-ttu-id="14f21-204">Priorita aktuálně spuštěných úloh musí být změněna.</span><span class="sxs-lookup"><span data-stu-id="14f21-204">The priority of currently executing tasks must be changed.</span></span>
* <span data-ttu-id="14f21-205">Aby bylo možné aplikaci znovu nasadit na server, je nutné ji vypnout.</span><span class="sxs-lookup"><span data-stu-id="14f21-205">The app has to be shut down in order to redeploy it to the server.</span></span>
* <span data-ttu-id="14f21-206">Prostředky serveru jsou omezené a vyžadují přeplánování pracovních položek backgound.</span><span class="sxs-lookup"><span data-stu-id="14f21-206">Server resources become limited, necessitating the rescheduling of backgound work items.</span></span>

<span data-ttu-id="14f21-207">Implementace vzorové práce na pozadí v součásti:</span><span class="sxs-lookup"><span data-stu-id="14f21-207">To implement a cancelable background work pattern in a component:</span></span>

* <span data-ttu-id="14f21-208">Použijte <xref:System.Threading.CancellationTokenSource> a <xref:System.Threading.CancellationToken>.</span><span class="sxs-lookup"><span data-stu-id="14f21-208">Use a <xref:System.Threading.CancellationTokenSource> and <xref:System.Threading.CancellationToken>.</span></span>
* <span data-ttu-id="14f21-209">Při [vyřazení součásti](#component-disposal-with-idisposable) a v jakémkoli bodě zrušení je požadováno ručním zrušením tokenu, volání [CancellationTokenSource. Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) k signalizaci, že by měla být zrušena činnost na pozadí.</span><span class="sxs-lookup"><span data-stu-id="14f21-209">On [disposal of the component](#component-disposal-with-idisposable) and at any point cancellation is desired by manually cancelling the token, call [CancellationTokenSource.Cancel](xref:System.Threading.CancellationTokenSource.Cancel%2A) to signal that the background work should be cancelled.</span></span>
* <span data-ttu-id="14f21-210">Po návratu asynchronního volání volejte <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> na token.</span><span class="sxs-lookup"><span data-stu-id="14f21-210">After the asynchronous call returns, call <xref:System.Threading.CancellationToken.ThrowIfCancellationRequested%2A> on the token.</span></span>

<span data-ttu-id="14f21-211">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="14f21-211">In the following example:</span></span>

* <span data-ttu-id="14f21-212">`await Task.Delay(5000, cts.Token);`představuje dlouhodobě běžící asynchronní práci na pozadí.</span><span class="sxs-lookup"><span data-stu-id="14f21-212">`await Task.Delay(5000, cts.Token);` represents long-running asynchronous background work.</span></span>
* <span data-ttu-id="14f21-213">`BackgroundResourceMethod`představuje dlouhotrvající metodu na pozadí, která by neměla být spuštěna, `Resource` Pokud je uvolněna před voláním metody.</span><span class="sxs-lookup"><span data-stu-id="14f21-213">`BackgroundResourceMethod` represents a long-running background method that shouldn't start if the `Resource` is disposed before the method is called.</span></span>

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
