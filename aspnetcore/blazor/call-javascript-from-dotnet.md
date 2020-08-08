---
title: Volání funkcí jazyka JavaScript z metod .NET v ASP.NET CoreBlazor
author: guardrex
description: Naučte se vyvolat funkce JavaScriptu z metod .NET v Blazor aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 04876976340ff440bb739100f891d8d3612b3754
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012603"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a>Volání funkcí jazyka JavaScript z metod .NET v ASP.NET CoreBlazor

[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Skořepa](https://github.com/danroth27)a [Luke Latham](https://github.com/guardrex)

BlazorAplikace může vyvolat funkce JavaScriptu z metod .NET a metod .NET z funkcí jazyka JavaScript. Tyto scénáře se nazývají *interoperabilita JavaScriptu* (interoperabilita*js*).

Tento článek popisuje vyvolání funkcí jazyka JavaScript z rozhraní .NET. Informace o volání metod .NET z JavaScriptu naleznete v tématu <xref:blazor/call-dotnet-from-javascript> .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Chcete-li volat do JavaScriptu z rozhraní .NET, použijte <xref:Microsoft.JSInterop.IJSRuntime> abstrakci. Chcete-li vydat volání interoperability JS, zapište <xref:Microsoft.JSInterop.IJSRuntime> abstrakci do komponenty. <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>přebírá identifikátor funkce JavaScriptu, kterou chcete vyvolat, spolu s libovolným počtem argumentů serializovatelných pomocí JSON. Identifikátor funkce je relativní vzhledem k globálnímu oboru ( `window` ). Pokud chcete volat `window.someScope.someFunction` , je identifikátor `someScope.someFunction` . Před voláním funkce není nutné ji registrovat. Návratový typ `T` musí být také serializovatelný jako JSON. `T`by měl odpovídat typu .NET, který se nejlépe mapuje na vrácený typ JSON.

Pro Blazor Server aplikace s povoleným předvykreslováním není možné volat do JavaScriptu během prvotního předgenerování. Volání interoperability JavaScriptu musí být odložena až po navázání spojení s prohlížečem. Další informace najdete v části [zjištění, kdy Blazor Server se aplikace](#detect-when-a-blazor-server-app-is-prerendering) vykreslovat.

Následující příklad je založen na [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) dekodéru založeném na jazyce JavaScript. Příklad ukazuje, jak vyvolat funkci JavaScriptu z metody jazyka C#, která přesměruje požadavek od kódu pro vývojáře na existující rozhraní JavaScript API. Funkce JavaScriptu přijímá bajtové pole z metody jazyka C#, dekóduje pole a vrátí text do komponenty pro zobrazení.

Uvnitř `<head>` elementu `wwwroot/index.html` ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` ( Blazor Server ) zadejte funkci JavaScriptu, která používá `TextDecoder` k dekódování předaného pole a vrácení dekódovaných hodnot:

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

Kód jazyka JavaScript, jako je například kód zobrazený v předchozím příkladu, lze také načíst ze souboru JavaScriptu ( `.js` ) s odkazem na soubor skriptu:

```html
<script src="exampleJsInterop.js"></script>
```

Následující součást:

* Vyvolá `convertArray` funkci jazyka JavaScript pomocí funkce `JSRuntime` Když **`Convert Array`** je vybráno tlačítko komponenty ().
* Po volání funkce JavaScriptu je předané pole převedeno na řetězec. Řetězec se vrátí do komponenty pro zobrazení.

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a>IJSRuntime

Chcete-li použít <xref:Microsoft.JSInterop.IJSRuntime> abstrakci, přijímají některé z následujících přístupů:

* Vložit <xref:Microsoft.JSInterop.IJSRuntime> abstrakci do Razor komponenty ( `.razor` ):

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  Uvnitř `<head>` elementu `wwwroot/index.html` ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` ( Blazor Server ) zadejte `handleTickerChanged` funkci JavaScriptu. Funkce je volána s <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> a nevrací hodnotu:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* Vložit <xref:Microsoft.JSInterop.IJSRuntime> abstrakci do třídy ( `.cs` ):

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  Uvnitř `<head>` elementu `wwwroot/index.html` ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` ( Blazor Server ) zadejte `handleTickerChanged` funkci JavaScriptu. Funkce je volána s `JSRuntime.InvokeAsync` a vrátí hodnotu:

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* Pro generování dynamického obsahu pomocí [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)použijte `[Inject]` atribut:

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

V ukázkové aplikaci na straně klienta, která doprovází toto téma, jsou k dispozici dvě funkce JavaScriptu pro aplikaci, která komunikuje s modelem DOM pro příjem vstupu uživatele a zobrazení uvítací zprávy:

* `showPrompt`: Vytvoří výzvu pro přijetí vstupu uživatele (jméno uživatele) a vrátí název volajícímu.
* `displayWelcome`: Přiřadí úvodní zprávu od volajícího k objektu modelu DOM s `id` `welcome` příponou.

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Umístěte `<script>` značku, která odkazuje na soubor JavaScriptu v `wwwroot/index.html` souboru ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` souboru ( Blazor Server ).

`wwwroot/index.html` (Blazor WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

`Pages/_Host.cshtml` (Blazor Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

Neumísťujte `<script>` značku do souboru komponenty, protože `<script>` značku nejde dynamicky aktualizovat.

Metody .NET spolupracuje s funkcemi JavaScriptu v `exampleJsInterop.js` souboru voláním <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .

<xref:Microsoft.JSInterop.IJSRuntime>Abstrakce je asynchronní k povolení pro Blazor Server scénáře. Pokud aplikace je Blazor WebAssembly aplikace a chcete funkci JavaScriptu vyvolat synchronně, přetypování směrem dolů <xref:Microsoft.JSInterop.IJSInProcessRuntime> a volání <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> místo toho. Doporučujeme, aby většina knihoven spolupráce v JS používala asynchronní rozhraní API, aby bylo zajištěno, že jsou knihovny dostupné ve všech scénářích.

Ukázková aplikace obsahuje komponentu, která předvádí interoperabilitu JS. Součást:

* Přijímá vstup uživatele prostřednictvím výzvy JavaScriptu.
* Vrátí text do komponenty pro zpracování.
* Volá druhou funkci JavaScriptu, která komunikuje s modelem DOM, aby zobrazila úvodní zprávu.

`Pages/JsInterop.razor`:

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
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

Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace aplikace (například `BlazorSample` ).

1. Když `TriggerJsPrompt` je spuštěno výběrem **`Trigger JavaScript Prompt`** tlačítka komponenty, `showPrompt` je volána funkce JavaScriptu, která je uvedena v `wwwroot/exampleJsInterop.js` souboru.
1. `showPrompt`Funkce přijímá vstup uživatele (jméno uživatele), což je kódování HTML a vráceno do komponenty. Komponenta ukládá jméno uživatele do místní proměnné, `name` .
1. Řetězec uložený v `name` je součástí uvítací zprávy, která je předána funkci JavaScriptu, `displayWelcome` která vykresluje úvodní zprávu do značky nadpisu.

## <a name="call-a-void-javascript-function"></a>Volání funkce void JavaScriptu

Funkce jazyka JavaScript, které vracejí [typ void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) nebo [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) , jsou volány s <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a>Rozpoznat, kdy Blazor Server se aplikace předvykresluje
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Zachytit odkazy na elementy

Některé scénáře interoperability JS vyžadují odkazy na prvky jazyka HTML. Například knihovna uživatelského rozhraní může vyžadovat odkaz na element pro inicializaci nebo může být nutné volat rozhraní API pro příkazy, například `focus` nebo `play` .

Zachyťte odkazy na prvky HTML v součásti pomocí následujícího postupu:

* Přidejte `@ref` atribut do elementu HTML.
* Definujte pole typu, <xref:Microsoft.AspNetCore.Components.ElementReference> jehož název odpovídá hodnotě `@ref` atributu.

Následující příklad ukazuje, jak zachytit odkaz na `username` `<input>` prvek:

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Pouze odkaz na element lze použít k použití obsahu prázdného prvku, který nekomunikuje s Blazor . Tento scénář je užitečný, když rozhraní API třetí strany dodává obsah do elementu. Vzhledem k tomu Blazor , že nekomunikuje s prvkem, neexistuje možnost konfliktu mezi Blazor reprezentací prvku a modelu DOM.
>
> V následujícím příkladu je *nebezpečné* postupovat obsah neuspořádaného seznamu ( `ul` ), protože Blazor spolupracuje s DOM k naplnění položek seznamu tohoto elementu ( `<li>` ):
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
> Pokud interoperabilita JS `MyList` povede obsah elementu a Blazor pokusí se použít rozdíly pro prvek, rozdíly nebudou odpovídat modelu DOM.

Pokud se jedná o kód .NET, <xref:Microsoft.AspNetCore.Components.ElementReference> je neprůhledný popisovač. *Jediná* věc, kterou můžete udělat, <xref:Microsoft.AspNetCore.Components.ElementReference> je předat do kódu JavaScriptu přes interoperabilitu js. Když to uděláte, kód na straně JavaScriptu obdrží `HTMLElement` instanci, kterou může použít s normálními rozhraními API DOM.

Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na prvek:

`exampleJsInterop.js`:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Chcete-li zavolat funkci JavaScriptu, která nevrací hodnotu, použijte <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> . Následující kód nastaví fokus na zadání uživatelského jména voláním předchozí funkce JavaScriptu s zachyceným <xref:Microsoft.AspNetCore.Components.ElementReference> :

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Chcete-li použít metodu rozšíření, vytvořte statickou metodu rozšíření, která obdrží <xref:Microsoft.JSInterop.IJSRuntime> instanci:

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

`Focus`Metoda je volána přímo na objektu. Následující příklad předpokládá, že `Focus` Metoda je k dispozici z `JsInteropClasses` oboru názvů:

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> `username`Proměnná je vyplněna pouze po vykreslení komponenty. Pokud se vyplněný <xref:Microsoft.AspNetCore.Components.ElementReference> kód předává kódu JavaScriptu, kód JavaScriptu obdrží hodnotu `null` . Chcete-li manipulovat s odkazy na elementy po dokončení vykreslování komponenty (pro nastavení prvotního zaměření na element), použijte [ `OnAfterRenderAsync` `OnAfterRender` metody životního cyklu komponenty nebo](xref:blazor/components/lifecycle#after-component-render).

Při práci s obecnými typy a vrácení hodnoty použijte <xref:System.Threading.Tasks.ValueTask%601> :

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

<xref:Microsoft.AspNetCore.Components.ElementReference>Je zaručena pouze platná v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> metodě komponenty (a odkaz na element je a `struct` ), takže odkaz na prvek nelze předat mezi komponentami.

Pro nadřazenou komponentu, aby byl odkaz na element dostupný pro jiné komponenty, může nadřazená komponenta:

* Umožňuje podřízeným součástem registrovat zpětná volání.
* Vyvolejte zaregistrovaná zpětná volání během <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> události s odkazem na předaný element. Nepřímo tento přístup umožňuje podřízeným komponentám pracovat s odkazem na element nadřazených prvků.

Následující Blazor WebAssembly příklad ilustruje přístup.

V `<head>` `wwwroot/index.html` :

```html
<style>
    .red { color: red }
</style>
```

V `<body>` `wwwroot/index.html` :

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

`Pages/Index.razor`(nadřazená součást):

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

`Pages/Index.razor.cs`:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

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
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace aplikace (například `BlazorSample` ).

`Shared/SurveyPrompt.razor`(podřízená součást):

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

`Shared/SurveyPrompt.razor.cs`:

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace aplikace (například `BlazorSample` ).

## <a name="harden-js-interop-calls"></a>Volání interoperability pro posílení JS

Interoperabilita JS může selhat kvůli chybám sítě a měla by být považována za nespolehlivou. Ve výchozím nastavení Blazor Server vyprší volání spolupráce v aplikaci js na serveru po jedné minutě. Pokud aplikace může tolerovat více agresivní časový limit, nastavte časový limit pomocí jednoho z následujících přístupů:

* V `Startup.ConfigureServices` části globálně zadejte časový limit:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Za vyvolání v kódu komponenty může jedno volání zadat časový limit:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Další informace o vyčerpání prostředků naleznete v tématu <xref:blazor/security/server/threat-mitigation> .

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

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
* [Provádění rozsáhlých přenosů dat v Blazor Server aplikacích](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
