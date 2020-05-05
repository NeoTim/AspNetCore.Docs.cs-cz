---
title: Volání funkcí jazyka JavaScript z metod .NET v ASP.NET CoreBlazor
author: guardrex
description: Naučte se vyvolat funkce JavaScriptu z metod .NET v Blazor aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 2f12c2a05ef510c38f407ccd69f748aceafd55de
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767162"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a>Volání funkcí jazyka JavaScript z metod .NET v ASP.NET CoreBlazor

[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Skořepa](https://github.com/danroth27)a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor Aplikace může vyvolat funkce JavaScriptu z metod .NET a metod .NET z funkcí jazyka JavaScript. Tyto scénáře se nazývají *interoperabilita JavaScriptu* (interoperabilita*js*).

Tento článek popisuje vyvolání funkcí jazyka JavaScript z rozhraní .NET. Informace o volání metod .NET z JavaScriptu naleznete v tématu <xref:blazor/call-dotnet-from-javascript>.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Chcete-li volat do JavaScriptu z rozhraní .NET `IJSRuntime` , použijte abstrakci. Chcete-li vydat volání interoperability JS `IJSRuntime` , zapište abstrakci do komponenty. `InvokeAsync<T>` Metoda přebírá identifikátor pro funkci JavaScriptu, kterou chcete vyvolat, spolu s libovolným počtem argumentů serializovatelných pomocí JSON. Identifikátor funkce je relativní vzhledem k globálnímu oboru (`window`). Pokud chcete volat `window.someScope.someFunction`, je `someScope.someFunction`identifikátor. Před voláním funkce není nutné ji registrovat. Návratový typ `T` musí být také serializovatelný jako JSON. `T`by měl odpovídat typu .NET, který se nejlépe mapuje na vrácený typ JSON.

Pro Blazor serverové aplikace s povoleným předvykreslováním není možné volat do JavaScriptu během prvotního předgenerování. Volání interoperability JavaScriptu musí být odložena až po navázání spojení s prohlížečem. Další informace najdete v části [zjištění, kdy je Blazor serverová aplikace](#detect-when-a-blazor-server-app-is-prerendering) vykreslovat.

Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), dekodéru založeném na JavaScriptu. Příklad ukazuje, jak vyvolat funkci JavaScriptu z metody jazyka C#. Funkce JavaScriptu přijímá bajtové pole z metody jazyka C#, dekóduje pole a vrátí text do komponenty pro zobrazení.

`<head>` Uvnitř prvku *wwwroot/index.html* (Blazor WebAssembly) nebo *Pages/_Host. cshtml* (Blazor Server) zadejte funkci JavaScriptu, která používá `TextDecoder` k dekódování předaného pole a vrácení dekódovaných hodnot:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Kód jazyka JavaScript, jako je například kód zobrazený v předchozím příkladu, lze také načíst ze souboru JavaScriptu (*. js*) s odkazem na soubor skriptu:

```html
<script src="exampleJsInterop.js"></script>
```

Následující součást:

* Vyvolá funkci `convertArray` jazyka JavaScript pomocí `JSRuntime` funkce když je vybráno tlačítko komponenty (**převést pole**).
* Po volání funkce JavaScriptu je předané pole převedeno na řetězec. Řetězec se vrátí do komponenty pro zobrazení.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Chcete-li `IJSRuntime` použít abstrakci, přijímají některé z následujících přístupů:

* Vložit `IJSRuntime` abstrakci do Razor komponenty (*. Razor*):

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  V rámci `<head>` prvku *wwwroot/index.html* (Blazor WebAssembly) nebo *Pages/_Host. cshtml* (Blazor Server) zadejte funkci `handleTickerChanged` JavaScriptu. Funkce je volána s `IJSRuntime.InvokeVoidAsync` a nevrací hodnotu:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Vložit `IJSRuntime` abstrakci do třídy (*. cs*):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  V rámci `<head>` prvku *wwwroot/index.html* (Blazor WebAssembly) nebo *Pages/_Host. cshtml* (Blazor Server) zadejte funkci `handleTickerChanged` JavaScriptu. Funkce je volána s `JSRuntime.InvokeAsync` a vrátí hodnotu:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Pro generování dynamického obsahu pomocí [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)použijte `[Inject]` atribut:

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

V ukázkové aplikaci na straně klienta, která doprovází toto téma, jsou k dispozici dvě funkce JavaScriptu pro aplikaci, která komunikuje s modelem DOM pro příjem vstupu uživatele a zobrazení uvítací zprávy:

* `showPrompt`&ndash; Vytvoří výzvu k přijetí vstupu uživatele (jméno uživatele) a vrátí název volajícímu.
* `displayWelcome`&ndash; Přiřadí uvítací zprávu od volajícího k objektu modelu DOM s `id` `welcome`příponou.

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Umístěte `<script>` značku, která odkazuje na soubor JavaScriptu v souboru *wwwroot/index.html* (Blazor WebAssembly) nebo *Pages/_Host. cshtml* (Blazor Server).

*wwwroot/index.html* (Blazor WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Pages/_Host. cshtml* (Blazor Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Neumísťujte `<script>` značku do souboru komponenty, protože `<script>` značku nejde dynamicky aktualizovat.

Metody .NET spolupracuje s funkcemi JavaScriptu v souboru *exampleJsInterop. js* voláním `IJSRuntime.InvokeAsync<T>`.

`IJSRuntime` Abstrakce je asynchronní, aby umožňovala Blazor serverové scénáře. Pokud je Blazor aplikace aplikace WebAssembly a chcete vyvolat funkci JavaScriptu synchronně, přetypování směrem dolů `IJSInProcessRuntime` a volání `Invoke<T>` místo toho. Doporučujeme, aby většina knihoven spolupráce v JS používala asynchronní rozhraní API, aby bylo zajištěno, že jsou knihovny dostupné ve všech scénářích.

Ukázková aplikace obsahuje komponentu, která předvádí interoperabilitu JS. Součást:

* Přijímá vstup uživatele prostřednictvím výzvy JavaScriptu.
* Vrátí text do komponenty pro zpracování.
* Volá druhou funkci JavaScriptu, která komunikuje s modelem DOM, aby zobrazila úvodní zprávu.

*Stránky/JSInterop. Razor*:

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. Když `TriggerJsPrompt` se spustí na **příkazovém řádku aktivační procedury JavaScriptu** komponenty, zavolá se funkce `showPrompt` JavaScriptu, která je k dispozici v souboru *wwwroot/exampleJsInterop. js* .
1. `showPrompt` Funkce přijímá vstup uživatele (jméno uživatele), což je kódování HTML a vráceno do komponenty. Komponenta ukládá jméno uživatele do místní proměnné, `name`.
1. Řetězec uložený v `name` je součástí uvítací zprávy, která je předána funkci JavaScriptu, `displayWelcome`která vykresluje úvodní zprávu do značky nadpisu.

## <a name="call-a-void-javascript-function"></a>Volání funkce void JavaScriptu

Funkce jazyka JavaScript, které vracejí [typ void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) nebo [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) , jsou volány s `IJSRuntime.InvokeVoidAsync`.

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a>Rozpoznat, Blazor kdy se serverová aplikace před vykreslením
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Zachytit odkazy na elementy

Některé scénáře interoperability JS vyžadují odkazy na prvky jazyka HTML. Například knihovna uživatelského rozhraní může vyžadovat odkaz na element pro inicializaci nebo může být nutné volat rozhraní API pro příkazy, například `focus` nebo. `play`

Zachyťte odkazy na prvky HTML v součásti pomocí následujícího postupu:

* Přidejte `@ref` atribut do elementu HTML.
* Definujte pole typu `ElementReference` , jehož název odpovídá hodnotě `@ref` atributu.

Následující příklad ukazuje, jak zachytit odkaz na `username` `<input>` prvek:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Pouze odkaz na element lze použít k použití obsahu prázdného prvku, který nekomunikuje s Blazor. Tento scénář je užitečný, když rozhraní API třetí strany poskytuje obsah do elementu. Vzhledem Blazor k tomu, že nekomunikuje s prvkem, neexistuje možnost konfliktu mezi Blazorreprezentací prvku a modelu DOM.
>
> V následujícím příkladu je *nebezpečné* postupovat obsah neuspořádaného seznamu (`ul`), protože Blazor spolupracuje s DOM k naplnění položek seznamu tohoto elementu (`<li>`):
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> Pokud interoperabilita JS povede obsah elementu `MyList` a Blazor pokusí se použít rozdíly pro prvek, rozdíly nebudou odpovídat modelu DOM.

Pokud se jedná o kód .NET, `ElementReference` je neprůhledný popisovač. *Jediná* věc, kterou můžete udělat, `ElementReference` je předat do kódu JAVASCRIPTU přes interoperabilitu js. Když to uděláte, kód na straně JavaScriptu obdrží `HTMLElement` instanci, kterou může použít s normálními rozhraními API DOM.

Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na prvek:

*exampleJsInterop. js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Chcete-li zavolat funkci JavaScriptu, která nevrací hodnotu, `IJSRuntime.InvokeVoidAsync`použijte. Následující kód nastaví fokus na zadání uživatelského jména voláním předchozí funkce JavaScriptu s zachyceným `ElementReference`:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Chcete-li použít metodu rozšíření, vytvořte statickou metodu rozšíření, která obdrží `IJSRuntime` instanci:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

`Focus` Metoda je volána přímo na objektu. Následující příklad předpokládá, že `Focus` metoda je k dispozici z `JsInteropClasses` oboru názvů:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> `username` Proměnná je vyplněna pouze po vykreslení komponenty. Pokud `ElementReference` se vyplněný kód předává kódu JavaScriptu, kód JavaScriptu obdrží hodnotu `null`. Chcete-li manipulovat s odkazy na elementy po dokončení vykreslování komponenty (pro nastavení prvotního zaměření na prvek), použijte [metody životního cyklu komponenty OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).

Při práci s obecnými typy a vrácení hodnoty použijte [\<ValueTask T>](xref:System.Threading.Tasks.ValueTask`1):

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod`je volána přímo na objektu s typem. Následující příklad předpokládá, že `GenericMethod` je k dispozici z `JsInteropClasses` oboru názvů:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Referenční prvky napříč komponentami

`ElementReference` Je zaručena pouze platná v `OnAfterRender` metodě komponenty (a odkaz na element je a `struct`), takže odkaz na prvek nelze předat mezi komponentami.

Pro nadřazenou komponentu, aby byl odkaz na element dostupný pro jiné komponenty, může nadřazená komponenta:

* Umožňuje podřízeným součástem registrovat zpětná volání.
* Vyvolejte zaregistrovaná zpětná `OnAfterRender` volání během události s odkazem na předaný element. Nepřímo tento přístup umožňuje podřízeným komponentám pracovat s odkazem na element nadřazených prvků.

Následující Blazor příklad WebAssembly znázorňuje přístup.

`<head>` V *wwwroot/index.html*:

```html
<style>
    .red { color: red }
</style>
```

`<body>` V *wwwroot/index.html*:

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

*Pages/index. Razor* (nadřazená komponenta):

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

*Pages/index. Razor. cs*:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool _disposing;
        private IList<IObserver<ElementReference>> _subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference _title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnNext(_title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            _disposing = true;

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            _subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (_disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            _subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self._subscriptions.Remove(Observer);
            }
        }
    }
}
```

*Shared/SurveyPrompt. Razor* (podřízená součást):

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

*Shared/SurveyPrompt. Razor. cs*:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable _subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (_subscription != null)
            {
                _subscription.Dispose();
            }

            _subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            _subscription = null;
        }

        public void OnError(Exception error)
        {
            _subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            _subscription?.Dispose();
        }
    }
}
```

## <a name="harden-js-interop-calls"></a>Volání interoperability pro posílení JS

Interoperabilita JS může selhat kvůli chybám sítě a měla by být považována za nespolehlivou. Ve výchozím nastavení Blazor serverová aplikace vyprší volání interoperability js na serveru po jedné minutě. Pokud aplikace může tolerovat více agresivního časového limitu, například 10 sekund, nastavte časový limit pomocí jednoho z následujících přístupů:

* V `Startup.ConfigureServices`části globálně zadejte časový limit:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Za vyvolání v kódu komponenty může jedno volání zadat časový limit:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Další informace o vyčerpání prostředků naleznete v tématu <xref:security/blazor/server/threat-mitigation>.

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Vyhnout se cyklickým odkazům na objekty

Objekty, které obsahují kruhové odkazy, nelze v klientovi serializovat pro:

* Volání metod .NET.
* Volání metody JavaScriptu z C#, když návratový typ obsahuje cyklické odkazy.

Další informace najdete v následujících problémech:

* [Cyklické odkazy nejsou podporované, musí být dva (dotnet/aspnetcore #20525).](https://github.com/dotnet/aspnetcore/issues/20525)
* [Návrh: Přidání mechanismu pro zpracování cyklických odkazů při serializaci (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/call-dotnet-from-javascript>
* [Příklad InteropComponent. Razor (dotnet/AspNetCore, úložiště GitHub, větev vydání 3,1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Provádění rozsáhlých přenosů Blazor dat v serverových aplikacích](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
