Při aplikaci na straně serveru Blazor se před vykreslením, nejsou některé akce, jako je například volání do jazyka JavaScript, je to možné, protože nebyl navázat připojení s prohlížečem. Součásti nutné k vykreslení odlišně při předkreslených.

Zpoždění JavaScript volání interop až po navázání připojení s prohlížečem můžete použít `OnAfterRenderAsync` událostí součástí životního cyklu. Tato událost je volána pouze po aplikace plně vykreslením a připojení klienta.

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<input ref="myInput" value="Value set during render" />

@functions {
    ElementRef myInput;

    protected override void OnAfterRender()
    {
        JSRuntime.InvokeAsync<object>(
            "setElementValue", myInput, "Value set after render");
    }
}
```

Tato součást následující demonstruje použití zprostředkovatele komunikace s objekty jazyka JavaScript jako součást logiky inicializace komponenty způsobem, který je kompatibilní s dokončení fáze před vykreslením.

Součást ukazuje, že je možné aktivovat vykreslování aktualizace z uvnitř `OnAfterRenderAsync`. V tomto scénáři. Vývojář musí Vyhněte se vytváření nekonečnou smyčku.

Kde `JSRuntime.InvokeAsync` se nazývá `ElementRef` je použít jenom v `OnAfterRenderAsync` a žádná předchozí metoda životního cyklu protože neexistuje žádný element jazyka JavaScript do po vykreslení komponentu.

`StateHasChanged` je volána k rerender komponentu s nový stav získaný z volání interop jazyka JavaScript. Kód nevytvoří nekonečnou smyčku, protože `StateHasChanged` se volá jenom v případě `infoFromJs` je `null`.

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
    <input id="val-set-by-interop" ref="@myElem" />
</p>

@functions {
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

Podmíněně vykreslit rozdílný obsah založen na tom, jestli aplikace je aktuálně před vykreslením obsahu, použijte `IsConnected` vlastnost `IComponentContext` služby. Při spuštění serveru straně `IsConnected` vrátí jenom `true` Pokud neexistuje aktivní připojení ke klientovi. Vždy vrátí `true` při spuštění na straně klienta.

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
    <button id="increment-count" onclick="@(() => count++)">Click me</button>
</p>

@functions {
    private int count;
}
```
