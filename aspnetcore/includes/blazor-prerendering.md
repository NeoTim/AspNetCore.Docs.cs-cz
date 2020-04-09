---
no-loc:
- Blazor
- SignalR
ms.openlocfilehash: 5f3e22e04fe18149ec5a8acb42f42a8ef83a7664
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659717"
---
<span data-ttu-id="98f1f-101">Zatímco Blazor aplikace Server je předběžné vykreslování, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno.</span><span class="sxs-lookup"><span data-stu-id="98f1f-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="98f1f-102">Součásti může být nutné vykreslit odlišně, když jsou předem vykresleny.</span><span class="sxs-lookup"><span data-stu-id="98f1f-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="98f1f-103">Chcete-li odložit volání meziop jazyka JavaScript až po navázání připojení k prohlížeči, můžete použít [událost životního cyklu komponenty OnAfterRenderAsync](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="98f1f-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the [OnAfterRenderAsync component lifecycle event](xref:blazor/lifecycle#after-component-render).</span></span> <span data-ttu-id="98f1f-104">Tato událost je volána pouze po úplné vykreslení aplikace a navázání připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="98f1f-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

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

<span data-ttu-id="98f1f-105">Pro předchozí ukázkový kód `setElementText` zadejte funkci `<head>` JavaScript uvnitřBlazor elementu *wwwroot/index.html* (WebAssembly)Blazor nebo *Pages/_Host.cshtml* (Server).</span><span class="sxs-lookup"><span data-stu-id="98f1f-105">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="98f1f-106">Funkce je volána s `IJSRuntime.InvokeVoidAsync` a nevrací hodnotu:</span><span class="sxs-lookup"><span data-stu-id="98f1f-106">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> <span data-ttu-id="98f1f-107">Předchozí příklad upravuje objektový model dokumentu (DOM) přímo pro demonstrační účely.</span><span class="sxs-lookup"><span data-stu-id="98f1f-107">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="98f1f-108">Přímo úprava DOM s JavaScriptem se nedoporučuje ve většině Blazorscénářů, protože JavaScript může zasahovat do 's sledování změn.</span><span class="sxs-lookup"><span data-stu-id="98f1f-108">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>

<span data-ttu-id="98f1f-109">Následující komponenta ukazuje, jak používat interop JavaScript jako součást logiky inicializace komponenty způsobem, který je kompatibilní s předběžným vykreslováním.</span><span class="sxs-lookup"><span data-stu-id="98f1f-109">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="98f1f-110">Komponenta ukazuje, že je možné spustit aktualizaci vykreslování zevnitř `OnAfterRenderAsync`.</span><span class="sxs-lookup"><span data-stu-id="98f1f-110">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="98f1f-111">Vývojář se musí vyhnout vytváření nekonečné smyčky v tomto scénáři.</span><span class="sxs-lookup"><span data-stu-id="98f1f-111">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="98f1f-112">Kde `JSRuntime.InvokeAsync` se `ElementRef` nazývá, se `OnAfterRenderAsync` používá pouze v a ne v žádné dřívější metody životního cyklu, protože neexistuje žádný element JavaScript až po vykreslení komponenty.</span><span class="sxs-lookup"><span data-stu-id="98f1f-112">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="98f1f-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) je volána k rerender komponenty s novým stavem získaným z volání interop JavaScript.</span><span class="sxs-lookup"><span data-stu-id="98f1f-113">[StateHasChanged](xref:blazor/lifecycle#state-changes) is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="98f1f-114">Kód nevytváří nekonečnou smyčku, `StateHasChanged` protože `infoFromJs` `null`je volána pouze v případě, že je .</span><span class="sxs-lookup"><span data-stu-id="98f1f-114">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

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

<span data-ttu-id="98f1f-115">Pro předchozí ukázkový kód `setElementText` zadejte funkci `<head>` JavaScript uvnitřBlazor elementu *wwwroot/index.html* (WebAssembly)Blazor nebo *Pages/_Host.cshtml* (Server).</span><span class="sxs-lookup"><span data-stu-id="98f1f-115">For the preceding example code, provide a `setElementText` JavaScript function inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server).</span></span> <span data-ttu-id="98f1f-116">Funkce je volána s `IJSRuntime.InvokeAsync` a vrátí hodnotu:</span><span class="sxs-lookup"><span data-stu-id="98f1f-116">The function is called with `IJSRuntime.InvokeAsync` and returns a value:</span></span>

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> <span data-ttu-id="98f1f-117">Předchozí příklad upravuje objektový model dokumentu (DOM) přímo pro demonstrační účely.</span><span class="sxs-lookup"><span data-stu-id="98f1f-117">The preceding example modifies the Document Object Model (DOM) directly for demonstration purposes only.</span></span> <span data-ttu-id="98f1f-118">Přímo úprava DOM s JavaScriptem se nedoporučuje ve většině Blazorscénářů, protože JavaScript může zasahovat do 's sledování změn.</span><span class="sxs-lookup"><span data-stu-id="98f1f-118">Directly modifying the DOM with JavaScript isn't recommended in most scenarios because JavaScript can interfere with Blazor's change tracking.</span></span>
