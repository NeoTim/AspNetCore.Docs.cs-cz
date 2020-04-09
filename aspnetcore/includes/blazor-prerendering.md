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
Zatímco Blazor aplikace Server je předběžné vykreslování, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno. Součásti může být nutné vykreslit odlišně, když jsou předem vykresleny.

Chcete-li odložit volání meziop jazyka JavaScript až po navázání připojení k prohlížeči, můžete použít [událost životního cyklu komponenty OnAfterRenderAsync](xref:blazor/lifecycle#after-component-render). Tato událost je volána pouze po úplné vykreslení aplikace a navázání připojení klienta.

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

Pro předchozí ukázkový kód `setElementText` zadejte funkci `<head>` JavaScript uvnitřBlazor elementu *wwwroot/index.html* (WebAssembly)Blazor nebo *Pages/_Host.cshtml* (Server). Funkce je volána s `IJSRuntime.InvokeVoidAsync` a nevrací hodnotu:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> Předchozí příklad upravuje objektový model dokumentu (DOM) přímo pro demonstrační účely. Přímo úprava DOM s JavaScriptem se nedoporučuje ve většině Blazorscénářů, protože JavaScript může zasahovat do 's sledování změn.

Následující komponenta ukazuje, jak používat interop JavaScript jako součást logiky inicializace komponenty způsobem, který je kompatibilní s předběžným vykreslováním. Komponenta ukazuje, že je možné spustit aktualizaci vykreslování zevnitř `OnAfterRenderAsync`. Vývojář se musí vyhnout vytváření nekonečné smyčky v tomto scénáři.

Kde `JSRuntime.InvokeAsync` se `ElementRef` nazývá, se `OnAfterRenderAsync` používá pouze v a ne v žádné dřívější metody životního cyklu, protože neexistuje žádný element JavaScript až po vykreslení komponenty.

[StateHasChanged](xref:blazor/lifecycle#state-changes) je volána k rerender komponenty s novým stavem získaným z volání interop JavaScript. Kód nevytváří nekonečnou smyčku, `StateHasChanged` protože `infoFromJs` `null`je volána pouze v případě, že je .

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

Pro předchozí ukázkový kód `setElementText` zadejte funkci `<head>` JavaScript uvnitřBlazor elementu *wwwroot/index.html* (WebAssembly)Blazor nebo *Pages/_Host.cshtml* (Server). Funkce je volána s `IJSRuntime.InvokeAsync` a vrátí hodnotu:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> Předchozí příklad upravuje objektový model dokumentu (DOM) přímo pro demonstrační účely. Přímo úprava DOM s JavaScriptem se nedoporučuje ve většině Blazorscénářů, protože JavaScript může zasahovat do 's sledování změn.
