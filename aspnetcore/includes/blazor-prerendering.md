I když je aplikace serveru Blazor předem vykreslovat, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno. Komponenty mohou být při předvykreslování nutné pro vykreslení odlišně.

Chcete-li spojit volání interoperability JavaScriptu až po navázání spojení s prohlížečem, můžete použít událost `OnAfterRenderAsync` životní cyklus komponent. Tato událost se volá jenom v případě, že se aplikace úplně vykreslí a naváže se připojení klienta.

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
