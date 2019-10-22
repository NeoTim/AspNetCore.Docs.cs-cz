I když je aplikace serveru Blazor předem vykreslovat, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno. Komponenty mohou být při předvykreslování nutné pro vykreslení odlišně.

Chcete-li spojit volání interoperability JavaScriptu až po navázání spojení s prohlížečem, můžete použít událost `OnAfterRenderAsync` životní cyklus komponent. Tato událost se volá jenom v případě, že se aplikace úplně vykreslí a naváže se připojení klienta.

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

Pro předchozí příklad kódu poskytněte funkci `setElementText` JavaScriptu uvnitř elementu `<head>` *wwwroot/index.html* (Blazor WebAssembly) nebo *Pages/_Host. cshtml* (Server Blazor). Funkce je volána pomocí `IJSRuntime.InvokeVoidAsync` a nevrací hodnotu:

```html
<!--  -->
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> Předchozí příklad upravuje model DOM (Document Object Model) (DOM) přímo pro demonstrační účely. Přímá úprava modelu DOM pomocí JavaScriptu se ve většině scénářů nedoporučuje, protože JavaScript může kolidovat se sledováním změn v Blazor.

Následující komponenta ukazuje, jak použít zprostředkovatele komunikace s JavaScriptem jako součást logiky inicializace komponenty způsobem, který je kompatibilní s předvykreslováním. Tato součást ukazuje, že je možné aktivovat aktualizaci vykreslování z `OnAfterRenderAsync` zevnitř. Vývojář se musí v tomto scénáři vyhnout vytvoření nekonečné smyčky.

Tam, kde je volána `JSRuntime.InvokeAsync`, `ElementRef` je použita pouze v `OnAfterRenderAsync` a nikoli v dřívějším způsobu životního cyklu, protože není k dispozici žádný element JavaScriptu, dokud není komponenta vykreslena.

`StateHasChanged` se volá, aby se komponenta znovu vykreslila s novým stavem získaným z volání interoperability JavaScript. Kód nevytváří nekonečnou smyčku, protože `StateHasChanged` je volána pouze v případě, že `infoFromJs` je `null`.

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

Pro předchozí příklad kódu poskytněte funkci `setElementText` JavaScriptu uvnitř elementu `<head>` *wwwroot/index.html* (Blazor WebAssembly) nebo *Pages/_Host. cshtml* (Server Blazor). Funkce je volána pomocí `IJSRuntime.InvokeAsync` a vrací hodnotu:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> Předchozí příklad upravuje model DOM (Document Object Model) (DOM) přímo pro demonstrační účely. Přímá úprava modelu DOM pomocí JavaScriptu se ve většině scénářů nedoporučuje, protože JavaScript může kolidovat se sledováním změn v Blazor.
