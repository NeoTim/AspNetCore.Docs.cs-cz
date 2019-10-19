<span data-ttu-id="ea862-101">I když je aplikace serveru Blazor předem vykreslovat, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno.</span><span class="sxs-lookup"><span data-stu-id="ea862-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="ea862-102">Komponenty mohou být při předvykreslování nutné pro vykreslení odlišně.</span><span class="sxs-lookup"><span data-stu-id="ea862-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="ea862-103">Chcete-li spojit volání interoperability JavaScriptu až po navázání spojení s prohlížečem, můžete použít událost `OnAfterRenderAsync` životní cyklus komponent.</span><span class="sxs-lookup"><span data-stu-id="ea862-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the `OnAfterRenderAsync` component lifecycle event.</span></span> <span data-ttu-id="ea862-104">Tato událost se volá jenom v případě, že se aplikace úplně vykreslí a naváže se připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="ea862-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<input @ref="myInput" value="Value set during render" />

@code {
    private ElementReference myInput;

    protected override void OnAfterRender(bool firstRender)
    {
        if (firstRender)
        {
            JSRuntime.InvokeVoidAsync(
                "setElementValue", myInput, "Value set after render");
        }
    }
}
```

<span data-ttu-id="ea862-105">Následující komponenta ukazuje, jak použít zprostředkovatele komunikace s JavaScriptem jako součást logiky inicializace komponenty způsobem, který je kompatibilní s předvykreslováním.</span><span class="sxs-lookup"><span data-stu-id="ea862-105">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="ea862-106">Tato součást ukazuje, že je možné aktivovat aktualizaci vykreslování z `OnAfterRenderAsync` zevnitř.</span><span class="sxs-lookup"><span data-stu-id="ea862-106">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="ea862-107">Vývojář se musí v tomto scénáři vyhnout vytvoření nekonečné smyčky.</span><span class="sxs-lookup"><span data-stu-id="ea862-107">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="ea862-108">Tam, kde je volána `JSRuntime.InvokeAsync`, `ElementRef` je použita pouze v `OnAfterRenderAsync` a nikoli v dřívějším způsobu životního cyklu, protože není k dispozici žádný element JavaScriptu, dokud není komponenta vykreslena.</span><span class="sxs-lookup"><span data-stu-id="ea862-108">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="ea862-109">`StateHasChanged` se volá, aby se komponenta znovu vykreslila s novým stavem získaným z volání interoperability JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ea862-109">`StateHasChanged` is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="ea862-110">Kód nevytváří nekonečnou smyčku, protože `StateHasChanged` je volána pouze v případě, že `infoFromJs` je `null`.</span><span class="sxs-lookup"><span data-stu-id="ea862-110">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
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

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementValue", myElem, "Hello from interop call");

            StateHasChanged();
        }
    }
}
```
