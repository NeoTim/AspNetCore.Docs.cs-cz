<span data-ttu-id="0b9a5-101">I když je aplikace Blazor na straně serveru předem vykreslovat, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno.</span><span class="sxs-lookup"><span data-stu-id="0b9a5-101">While a Blazor server-side app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="0b9a5-102">Komponenty mohou být při předvykreslování nutné pro vykreslení odlišně.</span><span class="sxs-lookup"><span data-stu-id="0b9a5-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="0b9a5-103">Chcete-li spojit volání interoperability JavaScriptu až po navázání spojení s prohlížečem, můžete `OnAfterRenderAsync` použít událost životního cyklu součásti.</span><span class="sxs-lookup"><span data-stu-id="0b9a5-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the `OnAfterRenderAsync` component lifecycle event.</span></span> <span data-ttu-id="0b9a5-104">Tato událost se volá jenom v případě, že se aplikace úplně vykreslí a naváže se připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="0b9a5-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<input @ref="myInput" value="Value set during render" />

@code {
    private ElementReference myInput;

    protected override void OnAfterRender()
    {
        JSRuntime.InvokeAsync<object>(
            "setElementValue", myInput, "Value set after render");
    }
}
```

<span data-ttu-id="0b9a5-105">Následující komponenta ukazuje, jak použít zprostředkovatele komunikace s JavaScriptem jako součást logiky inicializace komponenty způsobem, který je kompatibilní s předvykreslováním.</span><span class="sxs-lookup"><span data-stu-id="0b9a5-105">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="0b9a5-106">Komponenta ukazuje, že je možné aktivovat aktualizaci vykreslování zevnitř `OnAfterRenderAsync`.</span><span class="sxs-lookup"><span data-stu-id="0b9a5-106">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="0b9a5-107">Vývojář se musí v tomto scénáři vyhnout vytvoření nekonečné smyčky.</span><span class="sxs-lookup"><span data-stu-id="0b9a5-107">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="0b9a5-108">Kde `JSRuntime.InvokeAsync` je volána, `ElementRef` je použita pouze v `OnAfterRenderAsync` a nikoli v předchozí metodě životního cyklu, protože není k dispozici žádný element jazyka JavaScript, dokud není komponenta vykreslena.</span><span class="sxs-lookup"><span data-stu-id="0b9a5-108">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="0b9a5-109">`StateHasChanged`se volá, aby se komponenta znovu vykreslila s novým stavem získaným z volání interoperability JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0b9a5-109">`StateHasChanged` is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="0b9a5-110">Kód nevytváří nekonečnou smyčku `StateHasChanged` , protože je volána `infoFromJs` pouze `null`v případě, že je.</span><span class="sxs-lookup"><span data-stu-id="0b9a5-110">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

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
    <input id="val-set-by-interop" @ref="myElem" />
</p>

@code {
    private string infoFromJs;
    private ElementReference myElem;

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

<span data-ttu-id="0b9a5-111">Chcete-li podmíněně vykreslovat jiný obsah na základě toho, zda aplikace aktuálně předchází obsah, `IsConnected` použijte vlastnost `IComponentContext` ve službě.</span><span class="sxs-lookup"><span data-stu-id="0b9a5-111">To conditionally render different content based on whether the app is currently prerendering content, use the `IsConnected` property on the `IComponentContext` service.</span></span> <span data-ttu-id="0b9a5-112">Při spuštění na straně serveru se `IsConnected` vrátí `true` jenom v případě, že existuje aktivní připojení k klientovi.</span><span class="sxs-lookup"><span data-stu-id="0b9a5-112">When running server-side, `IsConnected` only returns `true` if there's an active connection to the client.</span></span> <span data-ttu-id="0b9a5-113">Vždycky se vrátí `true` při spuštění na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="0b9a5-113">It always returns `true` when running client-side.</span></span>

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
