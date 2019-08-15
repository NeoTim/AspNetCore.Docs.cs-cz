I když je aplikace Blazor na straně serveru předem vykreslovat, některé akce, jako je například volání do JavaScriptu, nejsou možné, protože připojení k prohlížeči nebylo navázáno. Komponenty mohou být při předvykreslování nutné pro vykreslení odlišně.

Chcete-li spojit volání interoperability JavaScriptu až po navázání spojení s prohlížečem, můžete `OnAfterRenderAsync` použít událost životního cyklu součásti. Tato událost se volá jenom v případě, že se aplikace úplně vykreslí a naváže se připojení klienta.

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<input @ref="myInput" @ref:suppressField value="Value set during render" />

@code {
    private ElementReference myInput;

    protected override void OnAfterRender()
    {
        JSRuntime.InvokeAsync<object>(
            "setElementValue", myInput, "Value set after render");
    }
}
```

Následující komponenta ukazuje, jak použít zprostředkovatele komunikace s JavaScriptem jako součást logiky inicializace komponenty způsobem, který je kompatibilní s předvykreslováním. Komponenta ukazuje, že je možné aktivovat aktualizaci vykreslování zevnitř `OnAfterRenderAsync`. Vývojář se musí v tomto scénáři vyhnout vytvoření nekonečné smyčky.

Kde `JSRuntime.InvokeAsync` je volána, `ElementRef` je použita pouze v `OnAfterRenderAsync` a nikoli v předchozí metodě životního cyklu, protože není k dispozici žádný element jazyka JavaScript, dokud není komponenta vykreslena.

`StateHasChanged`se volá, aby se komponenta znovu vykreslila s novým stavem získaným z volání interoperability JavaScript. Kód nevytváří nekonečnou smyčku `StateHasChanged` , protože je volána `infoFromJs` pouze `null`v případě, že je.

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
    <input id="val-set-by-interop" @ref="myElem" @ref:suppressField />
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

Chcete-li podmíněně vykreslovat jiný obsah na základě toho, zda aplikace aktuálně předchází obsah, `IsConnected` použijte vlastnost `IComponentContext` ve službě. Při spuštění na straně serveru se `IsConnected` vrátí `true` jenom v případě, že existuje aktivní připojení k klientovi. Vždycky se vrátí `true` při spuštění na straně klienta.

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
