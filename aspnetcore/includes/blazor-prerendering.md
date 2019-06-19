<span data-ttu-id="ae68d-101">Při aplikaci na straně serveru Blazor se před vykreslením, nejsou některé akce, jako je například volání do jazyka JavaScript, je to možné, protože nebyl navázat připojení s prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="ae68d-101">While a Blazor server-side app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="ae68d-102">Součásti nutné k vykreslení odlišně při předkreslených.</span><span class="sxs-lookup"><span data-stu-id="ae68d-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="ae68d-103">Zpoždění JavaScript volání interop až po navázání připojení s prohlížečem můžete použít `OnAfterRenderAsync` událostí součástí životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="ae68d-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the `OnAfterRenderAsync` component lifecycle event.</span></span> <span data-ttu-id="ae68d-104">Tato událost je volána pouze po aplikace plně vykreslením a připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="ae68d-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<input @ref="myInput" value="Value set during render" />

@code {
    ElementRef myInput;

    protected override void OnAfterRender()
    {
        JSRuntime.InvokeAsync<object>(
            "setElementValue", myInput, "Value set after render");
    }
}
```

<span data-ttu-id="ae68d-105">Tato součást následující demonstruje použití zprostředkovatele komunikace s objekty jazyka JavaScript jako součást logiky inicializace komponenty způsobem, který je kompatibilní s dokončení fáze před vykreslením.</span><span class="sxs-lookup"><span data-stu-id="ae68d-105">This following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span>

<span data-ttu-id="ae68d-106">Součást ukazuje, že je možné aktivovat vykreslování aktualizace z uvnitř `OnAfterRenderAsync`.</span><span class="sxs-lookup"><span data-stu-id="ae68d-106">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="ae68d-107">V tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="ae68d-107">For this scenario.</span></span> <span data-ttu-id="ae68d-108">Vývojář musí Vyhněte se vytváření nekonečnou smyčku.</span><span class="sxs-lookup"><span data-stu-id="ae68d-108">the developer must avoid creating an infinite loop.</span></span>

<span data-ttu-id="ae68d-109">Kde `JSRuntime.InvokeAsync` se nazývá `ElementRef` je použít jenom v `OnAfterRenderAsync` a žádná předchozí metoda životního cyklu protože neexistuje žádný element jazyka JavaScript do po vykreslení komponentu.</span><span class="sxs-lookup"><span data-stu-id="ae68d-109">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="ae68d-110">`StateHasChanged` je volána k rerender komponentu s nový stav získaný z volání interop jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ae68d-110">`StateHasChanged` is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="ae68d-111">Kód nevytvoří nekonečnou smyčku, protože `StateHasChanged` se volá jenom v případě `infoFromJs` je `null`.</span><span class="sxs-lookup"><span data-stu-id="ae68d-111">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IComponentContext ComponentContext
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

<p>
    Set value via JS interop call:
    <input id="val-set-by-interop" @ref="@myElem" />
</p>

@code {
    string infoFromJs;
    ElementRef myElem;

    protected override async Task OnAfterRenderAsync()
    {
        // TEMPORARY: Currently we need this guard to avoid making the interop
        // call during prerendering. Soon this will be unnecessary because we
        // will change OnAfterRenderAsync so that it won't run during the
        // prerendering phase.
        if (!ComponentContext.IsConnected)
        {
            return;
        }

        if (infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementValue", myElem, "Hello from interop call");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="ae68d-112">Podmíněně vykreslit rozdílný obsah založen na tom, jestli aplikace je aktuálně před vykreslením obsahu, použijte `IsConnected` vlastnost `IComponentContext` služby.</span><span class="sxs-lookup"><span data-stu-id="ae68d-112">To conditionally render different content based on whether the app is currently prerendering content, use the `IsConnected` property on the `IComponentContext` service.</span></span> <span data-ttu-id="ae68d-113">Při spuštění serveru straně `IsConnected` vrátí jenom `true` Pokud neexistuje aktivní připojení ke klientovi.</span><span class="sxs-lookup"><span data-stu-id="ae68d-113">When running server-side, `IsConnected` only returns `true` if there's an active connection to the client.</span></span> <span data-ttu-id="ae68d-114">Vždy vrátí `true` při spuštění na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="ae68d-114">It always returns `true` when running client-side.</span></span>

```cshtml
@page "/isconnected-example"
@using Microsoft.AspNetCore.Components.Services
@inject IComponentContext ComponentContext

<h1>IsConnected Example</h1>

<p>
    Current state:
    <strong id="connected-state">
        @(ComponentContext.IsConnected ? "connected" : "not connected")
    </strong>
</p>

<p>
    Clicks:
    <strong id="count">@count</strong>
    <button id="increment-count" @onclick="@(() => count++)">Click me</button>
</p>

@code {
    private int count;
}
```
