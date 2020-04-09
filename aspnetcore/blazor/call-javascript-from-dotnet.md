---
title: Volání funkcí JavaScriptu z metod .NET v ASP.NET CoreBlazor
author: guardrex
description: Přečtěte si, jak vyvolat funkce Blazor JavaScriptu z metod .NET v aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 0c6b6a0a8f88fa912523e7772fcd84ef4ce3b4ff
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977012"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a>Volání funkcí JavaScriptu z metod .NET v ASP.NET CoreBlazor

[Javier Calvarro Nelson](https://github.com/javiercn), Daniel [Roth](https://github.com/danroth27)a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Aplikace Blazor může vyvolat funkce JavaScriptu z metod .NET a .NET z funkcí JavaScriptu. Tyto scénáře se nazývají *interoperabilita JavaScriptu* *(JS interop).*

Tento článek popisuje vyvolání funkcí JavaScriptu z rozhraní .NET. Informace o volání metod .NET z JavaScriptu naleznete v tématu <xref:blazor/call-dotnet-from-javascript>.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Chcete-li volat do jazyka JavaScript z rozhraní .NET, použijte `IJSRuntime` abstrakce. Chcete-li vydat js interop volání, vstříkněte `IJSRuntime` abstrakci ve vaší součásti. Metoda `InvokeAsync<T>` přebírá identifikátor funkce JavaScript, který chcete vyvolat spolu s libovolným počtem argumentů JSON-serializovatelných. Identifikátor funkce je relativní vzhledem`window`ke globálnímu oboru ( ). Chcete-li volat `window.someScope.someFunction`, identifikátor `someScope.someFunction`je . Není třeba zaregistrovat funkci před tím, než je volána. Návratový `T` typ musí být také JSON serializovatelný. `T`by měl odpovídat typu .NET, který nejlépe mapuje na vrácený typ JSON.

U Blazor serverových aplikací s povoleným předběžným vykreslováním není volání do JavaScriptu možné během počátečního předběžného vykreslování. Volání interop jazyka JavaScript musí být odloženo až po navázání spojení s prohlížečem. Další informace najdete v části [Zjistit, kdy je aplikace Blazor Server předvykačováním.](#detect-when-a-blazor-server-app-is-prerendering)

Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), dekodér založený na JavaScriptu. Příklad ukazuje, jak vyvolat funkci Jazyka JavaScript z metody Jazyka C#. Funkce JavaScript přijímá bajtové pole z metody Jazyka C#, dekóduje pole a vrací text komponentě pro zobrazení.

Uvnitř `<head>` elementu *wwwroot/index.html* wwwroot/index.htmlBlazor (WebAssembly) nebo *Pages/_Host.cshtml* (Server)Blazor zadejte funkci JavaScriptu, která se používá `TextDecoder` k dekódování předávaného pole a vrácení dekódované hodnoty:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Kód JavaScriptu, například kód uvedený v předchozím příkladu, lze také načíst ze souboru JavaScriptu (*.js*) s odkazem na soubor skriptu:

```html
<script src="exampleJsInterop.js"></script>
```

Následující součást:

* Vyvolá funkci `convertArray` JavaScript `JSRuntime` pomocí funkce komponenty **(Převést pole).**
* Po volání funkce JavaScript je předané pole převedeno na řetězec. Řetězec je vrácen součásti pro zobrazení.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>Čas spuštění IJS

Chcete-li `IJSRuntime` použít abstrakci, zachycujte některý z následujících přístupů:

* Vstříkněte `IJSRuntime` abstrakci do komponenty Razor (*.razor*):

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  Uvnitř `<head>` elementu *wwwroot/index.html* wwwroot/index.htmlBlazor ( WebAssembly) nebo *Pages/_Host.cshtml* (Server)Blazor zadejte funkci `handleTickerChanged` JavaScriptu. Funkce je volána s `IJSRuntime.InvokeVoidAsync` a nevrací hodnotu:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Vstříkněte `IJSRuntime` abstrakci do třídy (*.cs*):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  Uvnitř `<head>` elementu *wwwroot/index.html* wwwroot/index.htmlBlazor ( WebAssembly) nebo *Pages/_Host.cshtml* (Server)Blazor zadejte funkci `handleTickerChanged` JavaScriptu. Funkce je volána s `JSRuntime.InvokeAsync` a vrátí hodnotu:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Pro generování dynamického obsahu s `[Inject]` [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)použijte atribut:

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

V ukázkové aplikaci na straně klienta, která doprovází toto téma, jsou k dispozici dvě funkce JavaScriptu pro aplikaci, které interagují s dom přijímat vstup uživatele a zobrazit uvítací zprávu:

* `showPrompt`&ndash; Vytvoří výzvu k přijetí vstupu uživatele (jméno uživatele) a vrátí jméno volajícímu.
* `displayWelcome`&ndash; Přiřadí uvítací zprávu od volajícího objektu `id` DOM s . `welcome`

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Umístěte `<script>` značku, která odkazuje na soubor JavaScriptu, do *souboru wwwroot/index.html* (WebAssembly)Blazor nebo *Pages/_Host.cshtml* (Server).Blazor

*wwwroot/index.html* Blazor (WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

*Stránky/_Host.cshtml* Blazor ( Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Neumisťuj `<script>` značku do `<script>` souboru komponenty, protože ji nelze dynamicky aktualizovat.

Metody .NET se propojí s funkcemi Jazyka JavaScript v `IJSRuntime.InvokeAsync<T>`souboru *exampleJsInterop.js* voláním .

Abstrakce `IJSRuntime` je asynchronní, Blazor aby bylo možné scénáře serveru. Pokud je aplikace Blazor aplikace WebAssembly a chcete vyvolat funkci JavaScriptu synchronně, svržena do `IJSInProcessRuntime` a volání `Invoke<T>` místo. Doporučujeme, aby většina knihoven JS interop používala asynchronní rozhraní API, aby bylo zajištěno, že knihovny jsou k dispozici ve všech scénářích.

Ukázková aplikace obsahuje komponentu pro demonstraci js interop. Součást:

* Přijímá vstup uživatele prostřednictvím výzvy javascriptu.
* Vrátí text komponentě ke zpracování.
* Volá druhou funkci JavaScriptu, která interaguje s DOM zobrazit uvítací zprávu.

*Stránky/JSInterop.břitva*:

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

1. Když `TriggerJsPrompt` je spuštěn a výběrem komponenty **Trigger JavaScript** Prompt `showPrompt` tlačítko, javascript funkce uvedené v souboru *wwwroot/exampleJsInterop.js* se nazývá.
1. Funkce `showPrompt` přijímá vstup uživatele (jméno uživatele), který je kódován html a vrácen do komponenty. Komponenta ukládá jméno uživatele do místní `name`proměnné .
1. Řetězec uložený `name` v aplikaci je začleněn do uvítací zprávy, `displayWelcome`která je předána funkci JavaScript , která vykresluje uvítací zprávu do značky nadpisu.

## <a name="call-a-void-javascript-function"></a>Volání prázdné funkce JavaScriptu

Funkce JavaScriptu, které vracejí [void(0)/void](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) `IJSRuntime.InvokeVoidAsync`0 nebo [undefined,](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) jsou volány pomocí .

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a>Zjištění předběžného Blazor vykreslování aplikace Server
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Zachycení odkazů na prvky

Některé scénáře interop JS vyžadují odkazy na elementy HTML. Knihovna rozhraní může například vyžadovat odkaz na prvek pro inicializaci nebo může být nutné `focus` volat `play`rozhraní API podobné příkazu na elementu, například nebo .

Zachyťte odkazy na elementy HTML v komponentě pomocí následujícího přístupu:

* Přidejte `@ref` atribut do elementu HTML.
* Definujte pole `ElementReference` typu, jehož název `@ref` odpovídá hodnotě atributu.

Následující příklad ukazuje zachycení odkazu `username` `<input>` na prvek:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Odkaz na prvek použijte pouze k mutu obsahu prázdného Blazorprvku, který neinteraguje . Tento scénář je užitečné, když rozhraní API třetí strany poskytuje obsah prvku. Protože Blazor neinteraguje s elementem, neexistuje žádná Blazormožnost konfliktu mezi 's reprezentace prvku a DOM.
>
> V následujícím příkladu je *nebezpečné* zmutovat obsah neuspořádaného seznamu`ul`( ), protože Blazor spolupracuje s dom`<li>`naplnit položky seznamu tohoto prvku ( ):
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
> Pokud JS interop mutuje `MyList` obsah Blazor prvku a pokusí se použít rozdíly na prvek, rozdíly nebude odpovídat DOM.

Pokud jde o kód .NET, a `ElementReference` je neprůhledný popisovač. *Jediné,* co můžete `ElementReference` udělat s je předat ji do kódu JavaScript přes JS interop. Pokud tak učiníte, kód na straně `HTMLElement` JavaScript obdrží instanci, kterou lze použít s normální mise DOM API.

Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na prvek:

*exampleJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Chcete-li volat funkci JavaScriptu, která `IJSRuntime.InvokeVoidAsync`nevrací hodnotu, použijte . Následující kód nastaví zaměření na vstup uživatelského jména voláním předchozí `ElementReference`funkce JavaScriptu se zachyceným :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Chcete-li použít metodu rozšíření, vytvořte `IJSRuntime` statickou metodu rozšíření, která přijímá instanci:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Metoda `Focus` je volána přímo na objekt. Následující příklad předpokládá, `Focus` že metoda je `JsInteropClasses` k dispozici z oboru názvů:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> Proměnná `username` je naplněna pouze po vykreslení komponenty. Pokud je nevyplněný `ElementReference` předán kódu JavaScriptu, kód JavaScript `null`obdrží hodnotu . Chcete-li manipulovat s odkazy na elementy po dokončení vykreslování komponenty (chcete-li nastavit počáteční fokus na elementu), použijte [metody životního cyklu součásti OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).

Při práci s obecnými typy a vrácení hodnoty použijte [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

`GenericMethod`je volána přímo na objekt s typem. Následující příklad předpokládá, `GenericMethod` že je `JsInteropClasses` k dispozici z oboru názvů:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a>Referenční prvky napříč součástmi

A `ElementReference` je zaručena pouze platné `OnAfterRender` v metodě komponenty `struct`(a odkaz na prvek je ), takže odkaz na prvek nelze předat mezi součástmi.

Pro nadřazenou komponentu, která zpřístupní odkaz na element pro jiné součásti, může nadřazená součást:

* Povolit podřízeným součástem registrovat zpětná volání.
* Vyvolat registrovaná zpětná `OnAfterRender` volání během události s odkazem na předávaný element. Nepřímo tento přístup umožňuje podřízené součásti k interakci s odkazem na prvek nadřazené.

Následující Blazor příklad websestavy ilustruje přístup.

V `<head>` *wwwroot/index.html*:

```html
<style>
    .red { color: red }
</style>
```

V `<body>` *wwwroot/index.html*:

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

*Stránky/Index.razor* (nadřazená součást):

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

*Stránky/Index.razor.cs*:

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

*Shared/SurveyPrompt.razor* (podřízená součást):

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

*Sdílené/SurveyPrompt.razor.cs*:

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

## <a name="harden-js-interop-calls"></a>Harden JS interop volání

JS interop může selhat z důvodu chyby sítě a by měly být považovány za nespolehlivé. Ve výchozím Blazor nastavení aplikace Server začasový limit js interop volání na serveru po jedné minutě. Pokud aplikace může tolerovat agresivnější časový rozsah, například 10 sekund, nastavte časový čas pomocí jednoho z následujících přístupů:

* Globálně v `Startup.ConfigureServices`, zadejte časový čas:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Vyvolání v kódu komponenty, jedno volání může určit časový čas:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Další informace o vyčerpání prostředků <xref:security/blazor/server>naleznete v tématu .

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a>Vyhněte se cyklické odkazy na objekty

Objekty, které obsahují cyklické odkazy nelze serializovat na straně klienta pro:

* Volání metody .NET.
* JavaScript metoda volá z Jazyka C#, pokud návratový typ má cyklické odkazy.

Další informace naleznete v následujících problémech:

* [Cyklické odkazy nejsou podporovány, trvat dva (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Návrh: Přidání mechanismu pro zpracování cyklické odkazy při serializace (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/call-dotnet-from-javascript>
* [Příklad InteropComponent.razor (dotnet/AspNetCore GitHub repository, 3.1 release branch)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Provádění velkých přenosů dat v Blazor serverových aplikacích](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
