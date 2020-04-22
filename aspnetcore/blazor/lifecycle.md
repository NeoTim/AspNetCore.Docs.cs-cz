---
title: ASP.NET Blazor životní cyklus jádra
author: guardrex
description: Přečtěte si, jak používat metody Blazor životního cyklu komponent Razor v aplikacích ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: e7450ad57acc87500bb977aa8349c6ee009e3bf4
ms.sourcegitcommit: c9d1208e86160615b2d914cce74a839ae41297a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81791455"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="76195-103">ASP.NET Blazor životní cyklus jádra</span><span class="sxs-lookup"><span data-stu-id="76195-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="76195-104">[Luke Latham](https://github.com/guardrex) a [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="76195-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="76195-105">Rámec Blazor zahrnuje synchronní a asynchronní metody životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="76195-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="76195-106">Přepište metody životního cyklu k provádění dalších operací s komponentami během inicializace a vykreslování součástí.</span><span class="sxs-lookup"><span data-stu-id="76195-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="76195-107">Metody životního cyklu</span><span class="sxs-lookup"><span data-stu-id="76195-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="76195-108">Metody inicializace komponent</span><span class="sxs-lookup"><span data-stu-id="76195-108">Component initialization methods</span></span>

<span data-ttu-id="76195-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*>a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> jsou vyvolány při inicializování komponenty poté, co obdržela své počáteční parametry z nadřazené součásti.</span><span class="sxs-lookup"><span data-stu-id="76195-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="76195-110">Použijte, `OnInitializedAsync` když komponenta provádí asynchronní operaci a měla by se aktualizovat po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="76195-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="76195-111">Pro synchronní operaci přepište `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="76195-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="76195-112">Chcete-li provést asynchronní operaci, přepište `OnInitializedAsync` a použijte `await` klíčové slovo v operaci:</span><span class="sxs-lookup"><span data-stu-id="76195-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="76195-113">Serverové aplikace, které [předkonejte svůj](xref:blazor/hosting-model-configuration#render-mode) `OnInitializedAsync` **_obsah, volají dvakrát_**:</span><span class="sxs-lookup"><span data-stu-id="76195-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="76195-114">Jednou, když je komponenta zpočátku vykreslena staticky jako součást stránky.</span><span class="sxs-lookup"><span data-stu-id="76195-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="76195-115">Podruhé, když prohlížeč naváže připojení zpět k serveru.</span><span class="sxs-lookup"><span data-stu-id="76195-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="76195-116">Chcete-li zabránit `OnInitializedAsync` spuštění kódu vývojáře v režimu dvakrát, přečtěte si [část Stavové opětovné připojení po předběžném vykreslování.](#stateful-reconnection-after-prerendering)</span><span class="sxs-lookup"><span data-stu-id="76195-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="76195-117">Zatímco Blazor aplikace Server je předběžné vykreslování, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno.</span><span class="sxs-lookup"><span data-stu-id="76195-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="76195-118">Součásti může být nutné vykreslit odlišně, když jsou předem vykresleny.</span><span class="sxs-lookup"><span data-stu-id="76195-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="76195-119">Další informace najdete v části [Zjistit, kdy je aplikace předvykačování.](#detect-when-the-app-is-prerendering)</span><span class="sxs-lookup"><span data-stu-id="76195-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

<span data-ttu-id="76195-120">Pokud jsou nastaveny obslužné rutiny událostí, odpojit je na likvidaci.</span><span class="sxs-lookup"><span data-stu-id="76195-120">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="76195-121">Další informace naleznete v [části Likvidace komponent s iJednorázovým.](#component-disposal-with-idisposable)</span><span class="sxs-lookup"><span data-stu-id="76195-121">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="76195-122">Před nastavením parametrů</span><span class="sxs-lookup"><span data-stu-id="76195-122">Before parameters are set</span></span>

<span data-ttu-id="76195-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*>nastaví parametry dodané nadřazenou komponentou ve stromu vykreslení:</span><span class="sxs-lookup"><span data-stu-id="76195-123"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="76195-124"><xref:Microsoft.AspNetCore.Components.ParameterView>obsahuje celou sadu hodnot parametrů `SetParametersAsync` pokaždé, když je volána.</span><span class="sxs-lookup"><span data-stu-id="76195-124"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="76195-125">Výchozí implementace `SetParametersAsync` nastaví hodnotu každé `[Parameter]` vlastnosti s atributem nebo, `[CascadingParameter]` který má odpovídající hodnotu v `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="76195-125">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="76195-126">Parametry, které nemají odpovídající hodnotu v `ParameterView` jsou ponechány beze změny.</span><span class="sxs-lookup"><span data-stu-id="76195-126">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="76195-127">Pokud `base.SetParametersAync` není vyvolána, vlastní kód můžete interpretovat hodnotu příchozí parametry v libovolném způsobem požadované.</span><span class="sxs-lookup"><span data-stu-id="76195-127">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="76195-128">Například neexistuje žádný požadavek přiřadit příchozí parametry vlastnosti na třídu.</span><span class="sxs-lookup"><span data-stu-id="76195-128">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

<span data-ttu-id="76195-129">Pokud jsou nastaveny obslužné rutiny událostí, odpojit je na likvidaci.</span><span class="sxs-lookup"><span data-stu-id="76195-129">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="76195-130">Další informace naleznete v [části Likvidace komponent s iJednorázovým.](#component-disposal-with-idisposable)</span><span class="sxs-lookup"><span data-stu-id="76195-130">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="76195-131">Po nastavení parametrů</span><span class="sxs-lookup"><span data-stu-id="76195-131">After parameters are set</span></span>

<span data-ttu-id="76195-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*>a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> nazývají se:</span><span class="sxs-lookup"><span data-stu-id="76195-132"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="76195-133">Když je komponenta inicializována a získala první sadu parametrů z nadřazené součásti.</span><span class="sxs-lookup"><span data-stu-id="76195-133">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="76195-134">Když se nadřazená součást znovu vykresluje a dodává:</span><span class="sxs-lookup"><span data-stu-id="76195-134">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="76195-135">Pouze známé primitivní neměnné typy, které alespoň jeden parametr změnil.</span><span class="sxs-lookup"><span data-stu-id="76195-135">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="76195-136">Všechny parametry se složitým typem.</span><span class="sxs-lookup"><span data-stu-id="76195-136">Any complex-typed parameters.</span></span> <span data-ttu-id="76195-137">Rozhraní framework nemůže vědět, zda hodnoty parametru s komplexním typem zmutovaly interně, takže považuje sadu parametrů za změněnou.</span><span class="sxs-lookup"><span data-stu-id="76195-137">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="76195-138">Asynchronní práce při použití parametrů a hodnot `OnParametersSetAsync` vlastností musí dojít během události životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="76195-138">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="76195-139">Pokud jsou nastaveny obslužné rutiny událostí, odpojit je na likvidaci.</span><span class="sxs-lookup"><span data-stu-id="76195-139">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="76195-140">Další informace naleznete v [části Likvidace komponent s iJednorázovým.](#component-disposal-with-idisposable)</span><span class="sxs-lookup"><span data-stu-id="76195-140">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="after-component-render"></a><span data-ttu-id="76195-141">Po vykreslení komponenty</span><span class="sxs-lookup"><span data-stu-id="76195-141">After component render</span></span>

<span data-ttu-id="76195-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*>a <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> jsou volány po dokončení vykreslování komponenty.</span><span class="sxs-lookup"><span data-stu-id="76195-142"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="76195-143">Odkazy na element y a komponenty jsou v tomto okamžiku naplněny.</span><span class="sxs-lookup"><span data-stu-id="76195-143">Element and component references are populated at this point.</span></span> <span data-ttu-id="76195-144">Tato fáze slouží k provedení dalších kroků inicializace pomocí vykresleného obsahu, jako je například aktivace javascriptových knihoven třetích stran, které pracují s vykreslené prvky DOM.</span><span class="sxs-lookup"><span data-stu-id="76195-144">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="76195-145">Parametr `firstRender` pro `OnAfterRenderAsync` `OnAfterRender`a:</span><span class="sxs-lookup"><span data-stu-id="76195-145">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="76195-146">Je nastavena na `true` první, kdy je instance komponenty vykreslena.</span><span class="sxs-lookup"><span data-stu-id="76195-146">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="76195-147">Lze zajistit, že inicializační práce se provádí pouze jednou.</span><span class="sxs-lookup"><span data-stu-id="76195-147">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="76195-148">Asynchronní práce ihned po vykreslování musí dojít během události `OnAfterRenderAsync` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="76195-148">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="76195-149">I v případě, že vrátíte <xref:System.Threading.Tasks.Task> z `OnAfterRenderAsync`, rozhraní neplánuje další cyklus vykreslení pro komponentu po dokončení tohoto úkolu.</span><span class="sxs-lookup"><span data-stu-id="76195-149">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="76195-150">To to je, aby se zabránilo nekonečné vykreslení smyčky.</span><span class="sxs-lookup"><span data-stu-id="76195-150">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="76195-151">Liší se od jiných metod životního cyklu, které plánují další cyklus vykreslení po dokončení vrácené úlohy.</span><span class="sxs-lookup"><span data-stu-id="76195-151">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="76195-152">`OnAfterRender`a `OnAfterRenderAsync` *nejsou volány při předběžném vykreslování na serveru.*</span><span class="sxs-lookup"><span data-stu-id="76195-152">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

<span data-ttu-id="76195-153">Pokud jsou nastaveny obslužné rutiny událostí, odpojit je na likvidaci.</span><span class="sxs-lookup"><span data-stu-id="76195-153">If any event handlers are set up, unhook them on disposal.</span></span> <span data-ttu-id="76195-154">Další informace naleznete v [části Likvidace komponent s iJednorázovým.](#component-disposal-with-idisposable)</span><span class="sxs-lookup"><span data-stu-id="76195-154">For more information, see the [Component disposal with IDisposable](#component-disposal-with-idisposable) section.</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="76195-155">Potlačit aktualizaci uI</span><span class="sxs-lookup"><span data-stu-id="76195-155">Suppress UI refreshing</span></span>

<span data-ttu-id="76195-156">Přepsáním <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> potlačíte, abyste potlačili aktualizaci uI.</span><span class="sxs-lookup"><span data-stu-id="76195-156">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="76195-157">Pokud se `true`vrátí implementace , ui se aktualizuje:</span><span class="sxs-lookup"><span data-stu-id="76195-157">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="76195-158">`ShouldRender`při každém vykreslení komponenty.</span><span class="sxs-lookup"><span data-stu-id="76195-158">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="76195-159">I `ShouldRender` když je přepsána, komponenta je vždy zpočátku vykreslena.</span><span class="sxs-lookup"><span data-stu-id="76195-159">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="76195-160">Změny stavu</span><span class="sxs-lookup"><span data-stu-id="76195-160">State changes</span></span>

<span data-ttu-id="76195-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*>upozorní komponentu, že se její stav změnil.</span><span class="sxs-lookup"><span data-stu-id="76195-161"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="76195-162">Pokud je `StateHasChanged` to možné, volání způsobí, že součást má být rerenderována.</span><span class="sxs-lookup"><span data-stu-id="76195-162">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="76195-163">Zpracování neúplných asynchronních akcí při vykreslení</span><span class="sxs-lookup"><span data-stu-id="76195-163">Handle incomplete async actions at render</span></span>

<span data-ttu-id="76195-164">Asynchronní akce prováděné v událostech životního cyklu pravděpodobně nebyly dokončeny před vykreslením součásti.</span><span class="sxs-lookup"><span data-stu-id="76195-164">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="76195-165">Objekty `null` mohou být nebo nezcela naplněny daty při provádění metody životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="76195-165">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="76195-166">Zadejte logiku vykreslování a potvrďte, že objekty jsou inicializovány.</span><span class="sxs-lookup"><span data-stu-id="76195-166">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="76195-167">Vykreslujte zástupné prvky uživatelského `null`rozhraní (například načítanou zprávu), zatímco objekty jsou .</span><span class="sxs-lookup"><span data-stu-id="76195-167">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="76195-168">V `FetchData` součásti Blazor šablon je `OnInitializedAsync` přepsána tak, aby asychronně přijínala data prognózy (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="76195-168">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="76195-169">Pokud `forecasts` `null`je , zobrazí se uživateli načítaná zpráva.</span><span class="sxs-lookup"><span data-stu-id="76195-169">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="76195-170">Po `Task` vrácena `OnInitializedAsync` dokončena je komponenta překreslovat s aktualizovaným stavem.</span><span class="sxs-lookup"><span data-stu-id="76195-170">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="76195-171">*Stránky/FetchData.razor* v Blazor šabloně Server:</span><span class="sxs-lookup"><span data-stu-id="76195-171">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="76195-172">Likvidace součástí s IDisposable</span><span class="sxs-lookup"><span data-stu-id="76195-172">Component disposal with IDisposable</span></span>

<span data-ttu-id="76195-173">Pokud součást implementuje <xref:System.IDisposable>, Dispose [metoda](/dotnet/standard/garbage-collection/implementing-dispose) je volána při odebrání komponenty z ui.</span><span class="sxs-lookup"><span data-stu-id="76195-173">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="76195-174">Používá následující `@implements IDisposable` komponentu `Dispose` a metodu:</span><span class="sxs-lookup"><span data-stu-id="76195-174">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="76195-175">Ozvát <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> `Dispose` se není podporováno.</span><span class="sxs-lookup"><span data-stu-id="76195-175">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="76195-176">`StateHasChanged`může být vyvolána jako součást stržení vykreslovacího modulu, takže požadování aktualizací ui v tomto okamžiku není podporováno.</span><span class="sxs-lookup"><span data-stu-id="76195-176">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

<span data-ttu-id="76195-177">Odhlášení obslužných rutin událostí z událostí rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="76195-177">Unsubscribe event handlers from .NET events.</span></span> <span data-ttu-id="76195-178">Následující [ Blazor příklady formuláře](xref:blazor/forms-validation) ukazují, jak odpojit obslužnou rutinu události v metodě: `Dispose`</span><span class="sxs-lookup"><span data-stu-id="76195-178">The following [Blazor form](xref:blazor/forms-validation) examples show how to unhook an event handler in the `Dispose` method:</span></span>

* <span data-ttu-id="76195-179">Přístup založený na soukromém poli a lambdě</span><span class="sxs-lookup"><span data-stu-id="76195-179">Private field and lambda approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-1.razor?highlight=23,28)]

* <span data-ttu-id="76195-180">Přístup soukromé metody</span><span class="sxs-lookup"><span data-stu-id="76195-180">Private method approach</span></span>

  [!code-razor[](lifecycle/samples_snapshot/3.x/event-handler-disposal-2.razor?highlight=16,26)]

## <a name="handle-errors"></a><span data-ttu-id="76195-181">Ošetření chyb</span><span class="sxs-lookup"><span data-stu-id="76195-181">Handle errors</span></span>

<span data-ttu-id="76195-182">Informace o zpracování chyb během provádění <xref:blazor/handle-errors#lifecycle-methods>metody životního cyklu naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="76195-182">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="76195-183">Stavové opětovné připojení po předběžném vykreslování</span><span class="sxs-lookup"><span data-stu-id="76195-183">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="76195-184">V Blazor aplikaci `RenderMode` Server, když je `ServerPrerendered`, je komponenta zpočátku vykreslena staticky jako součást stránky.</span><span class="sxs-lookup"><span data-stu-id="76195-184">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="76195-185">Jakmile prohlížeč naváže připojení zpět k serveru, komponenta je *vykreslena znovu*a komponenta je nyní interaktivní.</span><span class="sxs-lookup"><span data-stu-id="76195-185">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="76195-186">Pokud je k dispozici metoda životního cyklu [OnInitialized{Async}](#component-initialization-methods) pro inicializaci komponenty, je metoda provedena *dvakrát*:</span><span class="sxs-lookup"><span data-stu-id="76195-186">If the [OnInitialized{Async}](#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="76195-187">Když je komponenta předvykrestoustaticky.</span><span class="sxs-lookup"><span data-stu-id="76195-187">When the component is prerendered statically.</span></span>
* <span data-ttu-id="76195-188">Po navázání připojení k serveru.</span><span class="sxs-lookup"><span data-stu-id="76195-188">After the server connection has been established.</span></span>

<span data-ttu-id="76195-189">To může mít za následek znatelnou změnu dat zobrazených v unovém unovém řízení při nakonec vykreslení komponenty.</span><span class="sxs-lookup"><span data-stu-id="76195-189">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="76195-190">Chcete-li se vyhnout scénáři dvojitého vykreslování Blazor v aplikaci Server:</span><span class="sxs-lookup"><span data-stu-id="76195-190">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="76195-191">Předat identifikátor, který lze použít k ukládání do mezipaměti stavu během předběžného vykreslování a načíst stav po restartování aplikace.</span><span class="sxs-lookup"><span data-stu-id="76195-191">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="76195-192">Pomocí identifikátoru při předběžném vykreslování uložte stav komponenty.</span><span class="sxs-lookup"><span data-stu-id="76195-192">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="76195-193">Použijte identifikátor po předběžném vykreslování k načtení stavu uloženého v mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="76195-193">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="76195-194">Následující kód ukazuje aktualizovanou `WeatherForecastService` aplikaci Server založenou na Blazor šablonách, která zabraňuje dvojitému vykreslování:</span><span class="sxs-lookup"><span data-stu-id="76195-194">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

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

<span data-ttu-id="76195-195">Další informace naleznete `RenderMode`v <xref:blazor/hosting-model-configuration#render-mode>tématu .</span><span class="sxs-lookup"><span data-stu-id="76195-195">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="76195-196">Zjištění, kdy je aplikace předvykreslování</span><span class="sxs-lookup"><span data-stu-id="76195-196">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
