---
---
<span data-ttu-id="812c7-101">I když je aplikace serveru Blazor předem vykreslovat, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno.</span><span class="sxs-lookup"><span data-stu-id="812c7-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="812c7-102">Komponenty mohou být při předvykreslování nutné pro vykreslení odlišně.</span><span class="sxs-lookup"><span data-stu-id="812c7-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="812c7-103">Chcete-li spojit volání interoperability JavaScriptu až po navázání spojení s prohlížečem, můžete použít [událost životního cyklu součásti OnAfterRenderAsync](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="812c7-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="812c7-104">Tato událost se volá jenom v případě, že se aplikace úplně vykreslí a naváže se připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="812c7-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

<span data-ttu-id="812c7-105">Pro předchozí příklad kódu poskytněte `setElementText` funkci JavaScriptu uvnitř `<head>` elementu `wwwroot/index.html` (Blazor WebAssembly) nebo `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="812c7-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="812c7-106">Funkce je volána s <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> a nevrací hodnotu:</span><span class="sxs-lookup"><span data-stu-id="812c7-106">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="812c7-107">Předchozí příklad upravuje model DOM (Document Object Model) (DOM) přímo pro demonstrační účely.</span><span class="sxs-lookup"><span data-stu-id="812c7-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="812c7-108">Přímá úprava modelu DOM pomocí JavaScriptu se ve většině scénářů nedoporučuje, protože JavaScript může kolidovat se sledováním změn v Blazor.</span><span class="sxs-lookup"><span data-stu-id="812c7-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="812c7-109">Následující komponenta ukazuje, jak použít zprostředkovatele komunikace s JavaScriptem jako součást logiky inicializace komponenty způsobem, který je kompatibilní s předvykreslováním.</span><span class="sxs-lookup"><span data-stu-id="812c7-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="812c7-110">Komponenta ukazuje, že je možné aktivovat aktualizaci vykreslování zevnitř <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="812c7-110">The component shows that it's possible to trigger a rendering update from inside <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>.</span></span> <span data-ttu-id="812c7-111">Vývojář se musí v tomto scénáři vyhnout vytvoření nekonečné smyčky.</span><span class="sxs-lookup"><span data-stu-id="812c7-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="812c7-112">Kde <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> je volána, `ElementRef` je použita pouze v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> a nikoli v předchozí metodě životního cyklu, protože není k dispozici žádný element jazyka JavaScript, dokud není komponenta vykreslena.</span><span class="sxs-lookup"><span data-stu-id="812c7-112">Where <xref:Microsoft.JSInterop.JSRuntime.InvokeAsync%2A?displayProperty=nameWithType> is called, `ElementRef` is only used in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A> and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="812c7-113">[StateHasChanged](xref:blazor/components/lifecycle#state-changes) se volá, aby se komponenta znovu vykreslila s novým stavem získaným z volání interoperability JavaScript.</span><span class="sxs-lookup"><span data-stu-id="812c7-113">[StateHasChanged](xref:blazor/components/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="812c7-114">Kód nevytváří nekonečnou smyčku, protože `StateHasChanged` je volána pouze v případě `infoFromJs` , že je `null` .</span><span class="sxs-lookup"><span data-stu-id="812c7-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="812c7-115">Pro předchozí příklad kódu poskytněte `setElementText` funkci JavaScriptu uvnitř `<head>` elementu `wwwroot/index.html` (Blazor WebAssembly) nebo `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="812c7-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> <span data-ttu-id="812c7-116">Funkce je volána s <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> a vrátí hodnotu:</span><span class="sxs-lookup"><span data-stu-id="812c7-116">The function is called with<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="812c7-117">Předchozí příklad upravuje model DOM (Document Object Model) (DOM) přímo pro demonstrační účely.</span><span class="sxs-lookup"><span data-stu-id="812c7-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="812c7-118">Přímá úprava modelu DOM pomocí JavaScriptu se ve většině scénářů nedoporučuje, protože JavaScript může kolidovat se sledováním změn v Blazor.</span><span class="sxs-lookup"><span data-stu-id="812c7-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
