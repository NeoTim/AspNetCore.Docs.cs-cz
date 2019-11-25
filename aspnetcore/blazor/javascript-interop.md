---
title: Zprostředkovatel komunikace s ASP.NET Core Blazor JavaScript
author: guardrex
description: Naučte se volat funkce jazyka JavaScript z metod .NET a .NET z JavaScriptu v aplikacích Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- Blazor
uid: blazor/javascript-interop
ms.openlocfilehash: f55eda512f8dcf0695c2e7f4655db83b26ea4159
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317196"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a>Zprostředkovatel komunikace s ASP.NET Core Blazor JavaScript

[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Skořepa](https://github.com/danroth27)a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor aplikace může vyvolat JavaScriptové funkce z metod .NET a .NET z kódu JavaScriptu.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))

## <a name="invoke-javascript-functions-from-net-methods"></a>Vyvolání funkcí jazyka JavaScript z metod .NET

Existují situace, kdy je pro volání funkce JavaScriptu vyžadován kód .NET. Volání JavaScriptu může například vystavit možnosti prohlížeče nebo funkce z knihovny JavaScriptu do aplikace. Tento scénář se nazývá *interoperabilita JavaScriptu* (interoperabilita*js*).

Chcete-li volat do JavaScriptu z rozhraní .NET, použijte `IJSRuntime` abstrakce. Metoda `InvokeAsync<T>` přebírá identifikátor pro funkci JavaScriptu, kterou chcete vyvolat, spolu s libovolným počtem argumentů serializovatelných pomocí JSON. Identifikátor funkce je relativní vzhledem k globálnímu oboru (`window`). Pokud chcete volat `window.someScope.someFunction`, je identifikátor `someScope.someFunction`. Před voláním funkce není nutné ji registrovat. Návratový typ `T` musí být také serializovatelný jako JSON.

Pro aplikace Blazor Server:

* Aplikace Blazor serveru zpracovává více žádostí uživatele. Nevolejte `JSRuntime.Current` v součásti k vyvolání funkcí jazyka JavaScript.
* Vložení `IJSRuntime` abstrakce a použití vloženého objektu k vydávání volání interoperability JS.
* I když je aplikace Blazor předem vykreslovat, volání do JavaScriptu není možné, protože připojení k prohlížeči nebylo navázáno. Další informace najdete v části [zjištění, kdy je aplikace Blazor předvykreslování](#detect-when-a-blazor-app-is-prerendering) .

Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), experimentálním dekodéru založeném na JavaScriptu. Příklad ukazuje, jak vyvolat funkci JavaScriptu z C# metody. Funkce JavaScriptu přijímá bajtové pole z C# metody, dekóduje pole a vrátí text do komponenty k zobrazení.

Uvnitř prvku `<head>` *wwwroot/index.html* (Blazor WebAssembly) nebo *pages/_Host. cshtml* (Blazor Server) zadejte funkci javascriptu, která používá `TextDecoder` k dekódování předaného pole a vrácení dekódovaný hodnoty:

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

Kód jazyka JavaScript, jako je například kód zobrazený v předchozím příkladu, lze také načíst ze souboru JavaScriptu ( *. js*) s odkazem na soubor skriptu:

```html
<script src="exampleJsInterop.js"></script>
```

Následující součást:

* Vyvolá funkci `convertArray` JavaScript pomocí `JSRuntime` při výběru tlačítka komponenty (**převést pole**).
* Po volání funkce JavaScriptu je předané pole převedeno na řetězec. Řetězec se vrátí do komponenty pro zobrazení.

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a>Použití IJSRuntime

Chcete-li použít abstrakci `IJSRuntime`, přijímají některé z následujících přístupů:

* Vložit abstrakci `IJSRuntime` do komponenty Razor ( *. Razor*):

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  Uvnitř prvku `<head>` *wwwroot/index.html* (Blazor WebAssembly) nebo *pages/_Host. cshtml* (Blazor Server) zadejte funkci JavaScriptu pro `handleTickerChanged`. Funkce je volána pomocí `IJSRuntime.InvokeVoidAsync` a nevrací hodnotu:

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* Vložit abstrakci `IJSRuntime` do třídy ( *. cs*):

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  Uvnitř prvku `<head>` *wwwroot/index.html* (Blazor WebAssembly) nebo *pages/_Host. cshtml* (Blazor Server) zadejte funkci JavaScriptu pro `handleTickerChanged`. Funkce je volána pomocí `JSRuntime.InvokeAsync` a vrací hodnotu:

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* Pro generování dynamického obsahu pomocí [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)použijte atribut `[Inject]`:

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

V ukázkové aplikaci na straně klienta, která doprovází toto téma, jsou k dispozici dvě funkce JavaScriptu pro aplikaci, která komunikuje s modelem DOM pro příjem vstupu uživatele a zobrazení uvítací zprávy:

* `showPrompt` &ndash; vytvoří výzvu k přijetí vstupu uživatele (jméno uživatele) a vrátí název volajícímu.
* `displayWelcome` &ndash; přiřadí uvítací zprávu od volajícího k objektu modelu DOM pomocí `id` `welcome`.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Umístěte značku `<script>`, která odkazuje na soubor JavaScriptu v souboru *wwwroot/index.html* (Blazor sestavení) nebo *stránky/_Host. cshtml* (ServerBlazor).

*wwwroot/index.html* (Blazor WebAssembly):

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=15)]

*Pages/_Host. cshtml* (Blazor Server):

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=21)]

Neumísťujte značku `<script>` do souboru komponenty, protože značku `<script>` nejde dynamicky aktualizovat.

Metody .NET spolupracuje s funkcemi JavaScriptu v souboru *exampleJsInterop. js* voláním `IJSRuntime.InvokeAsync<T>`.

Abstrakce `IJSRuntime` je asynchronní, aby umožňovala scénáře Blazor serveru. Pokud je aplikace Blazor aplikace WebAssembly a chcete vyvolat funkci JavaScriptu synchronně, přetypování downcast na `IJSInProcessRuntime` a místo toho volat `Invoke<T>`. Doporučujeme, aby většina knihoven spolupráce v JS používala asynchronní rozhraní API, aby bylo zajištěno, že jsou knihovny dostupné ve všech scénářích.

Ukázková aplikace obsahuje komponentu, která předvádí interoperabilitu JS. Součást:

* Přijímá vstup uživatele prostřednictvím výzvy JavaScriptu.
* Vrátí text do komponenty pro zpracování.
* Volá druhou funkci JavaScriptu, která komunikuje s modelem DOM, aby zobrazila úvodní zprávu.

*Pages/JSInterop.razor*:

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. Po spuštění `TriggerJsPrompt` kliknutím na tlačítko **výzvy JavaScriptu triggeru** komponenty se zavolá funkce JavaScriptu `showPrompt`, která je k dispozici v souboru *wwwroot/exampleJsInterop. js* .
1. Funkce `showPrompt` akceptuje vstup uživatele (uživatelské jméno), což je kódování HTML, které se vrátí do komponenty. Komponenta ukládá jméno uživatele do místní proměnné `name`.
1. Řetězec uložený v `name` je součástí uvítací zprávy, která je předána funkci JavaScriptu, `displayWelcome`, která vykresluje uvítací zprávu do značky nadpisu.

## <a name="call-a-void-javascript-function"></a>Volání funkce void JavaScriptu

Funkce jazyka JavaScript, které vracejí [typ void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) nebo [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) , jsou volány pomocí `IJSRuntime.InvokeVoidAsync`.

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a>Rozpoznat, kdy se předchází aplikace Blazor
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Zachytit odkazy na elementy

Některé scénáře interoperability JS vyžadují odkazy na prvky jazyka HTML. Například knihovna uživatelského rozhraní může vyžadovat odkaz na element pro inicializaci nebo může být nutné volat rozhraní API pro příkazy, například `focus` nebo `play`.

Zachyťte odkazy na prvky HTML v součásti pomocí následujícího postupu:

* Přidejte atribut `@ref` do elementu HTML.
* Definujte pole typu `ElementReference`, jehož název odpovídá hodnotě atributu `@ref`.

Následující příklad ukazuje, jak zachytit odkaz na prvek `username` `<input>`:

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> Pouze odkaz na element lze použít k použití obsahu prázdného prvku, který nekomunikuje s Blazor. Tento scénář je užitečný, když rozhraní API třetí strany poskytuje obsah do elementu. Vzhledem k tomu, že Blazor nekomunikuje s prvkem, neexistuje žádná možnost konfliktu mezi reprezentací Blazorelementu a DOM.
>
> V následujícím příkladu je *nebezpečné* obsah neuspořádaného seznamu (`ul`), protože Blazor vzájemně spolupracuje s DOM k naplnění položek seznamu tohoto elementu (`<li>`):
>
> ```cshtml
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> Pokud interoperabilita JS podílí obsah elementu `MyList` a Blazor se pokusí použít rozdíly pro prvek, rozdíly nebudou odpovídat modelu DOM.

Pokud se jedná o kód .NET, `ElementReference` je neprůhledný popisovač. *Jediná* věc, kterou můžete provést s `ElementReference`, je předána do kódu JavaScriptu přes interoperabilitu js. V takovém případě kód na straně JavaScriptu obdrží instanci `HTMLElement`, kterou lze použít s normálními rozhraními API DOM.

Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na prvek:

*exampleJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Použití `IJSRuntime.InvokeAsync<T>` a volání `exampleJsFunctions.focusElement` pomocí `ElementReference` k zaměření prvku:

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

Chcete-li použít metodu rozšíření pro zaměření prvku, vytvořte statickou metodu rozšíření, která obdrží instanci `IJSRuntime`:

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Metoda je volána přímo na objektu. Následující příklad předpokládá, že metoda statického `Focus` je k dispozici z oboru názvů `JsInteropClasses`:

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> Proměnná `username` se naplní až po vykreslení komponenty. Pokud je vyplněný `ElementReference` předán kódu jazyka JavaScript, kód jazyka JavaScript obdrží hodnotu `null`. Chcete-li manipulovat s odkazy na elementy po dokončení vykreslování komponenty (pro nastavení prvotního zaměření na element), použijte [metody životního cyklu komponent](xref:blazor/components#lifecycle-methods)`OnAfterRenderAsync` nebo `OnAfterRender`.

## <a name="invoke-net-methods-from-javascript-functions"></a>Vyvolat metody .NET z funkcí JavaScriptu

### <a name="static-net-method-call"></a>Statické volání metody .NET

Chcete-li vyvolat statickou metodu .NET z JavaScriptu, použijte funkce `DotNet.invokeMethod` nebo `DotNet.invokeMethodAsync`. Předejte identifikátor statické metody, kterou chcete volat, název sestavení obsahující funkce a všechny argumenty. Asynchronní verze je preferována k podpoře Blazorch scénářů serveru. Pro vyvolání metody .NET z JavaScriptu musí být metoda .NET veřejná, statická a mít atribut `[JSInvokable]`. Ve výchozím nastavení je identifikátor metody název metody, ale můžete zadat jiný identifikátor pomocí `JSInvokableAttribute` konstruktoru. Volání otevřených obecných metod není aktuálně podporováno.

Ukázková aplikace obsahuje C# metodu pro vrácení pole `int`s. Atribut `JSInvokable` se aplikuje na metodu.

*Pages/JsInterop.razor*:

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

JavaScript, který obsluhuje klient, vyvolá C# metodu .NET.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Pokud je vybráno tlačítko **aktivovat .NET static Method ReturnArrayAsync** , Projděte si výstup konzoly v webových vývojářských nástrojích prohlížeče.

Výstup konzoly:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Hodnota čtvrtého pole je vložena do pole (`data.push(4);`) vráceného `ReturnArrayAsync`.

### <a name="instance-method-call"></a>Volání metody instance

Můžete také volat metody instance rozhraní .NET z JavaScriptu. Vyvolání metody instance rozhraní .NET z JavaScriptu:

* Předejte instanci rozhraní .NET do JavaScriptu tak, že ji zabalíte do instance `DotNetObjectReference`. Instance rozhraní .NET je předána odkazem na JavaScript.
* Vyvolat metody instance .NET v instanci pomocí funkcí `invokeMethod` nebo `invokeMethodAsync`. Instance rozhraní .NET může být také předána jako argument při vyvolání jiných metod rozhraní .NET z JavaScriptu.

> [!NOTE]
> Ukázková aplikace protokoluje zprávy do konzoly na straně klienta. Pro následující příklady znázorněné ukázkovou aplikací si Projděte výstup konzoly v prohlížeči v vývojářských nástrojích prohlížeče.

Když je vybráno tlačítko **aktivovat metodu instance .NET HelloHelper. sayHello** , je volána `ExampleJsInterop.CallHelloHelperSayHello` a předá do metody název, `Blazor`.

*Pages/JsInterop.razor*:

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

`CallHelloHelperSayHello` vyvolá funkci JavaScriptu `sayHello` s novou instancí `HelloHelper`.

*JsInteropClasses/ExampleJsInterop. cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Název je předán konstruktoru `HelloHelper`, který nastaví vlastnost `HelloHelper.Name`. Je-li funkce JavaScriptu `sayHello` spuštěna, vrátí `HelloHelper.SayHello` zprávu `Hello, {Name}!`, která je zapsána do konzoly funkcí JavaScriptu.

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Výstup konzoly v vývojářských nástrojích webu v prohlížeči:

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a>Sdílení kódu spolupráce v knihovně tříd

Kód interoperability JS lze zahrnout do knihovny tříd, která umožňuje sdílení kódu v balíčku NuGet.

Knihovna tříd zpracovává vložení prostředků JavaScriptu do sestaveného sestavení. Soubory JavaScriptu jsou umístěny ve složce *wwwroot* . Nástroj při sestavování knihovny postará o vkládání prostředků.

Na sestavený balíček NuGet se odkazuje v souboru projektu aplikace stejným způsobem, jako na který se odkazuje na balíček NuGet. Po obnovení balíčku kód aplikace může zavolat do JavaScriptu, jako kdyby byl C#.

Další informace najdete v tématu <xref:blazor/class-libraries>.

## <a name="harden-js-interop-calls"></a>Volání interoperability pro posílení JS

Interoperabilita JS může selhat kvůli chybám sítě a měla by být považována za nespolehlivou. Ve výchozím nastavení aplikace Blazor serveru vyprší voláním interoperability JS na serveru po jedné minutě. Pokud aplikace může tolerovat více agresivního časového limitu, například 10 sekund, nastavte časový limit pomocí jednoho z následujících přístupů:

* V `Startup.ConfigureServices`globálně v poli zadejte časový limit:

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* Za vyvolání v kódu komponenty může jedno volání zadat časový limit:

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

Další informace o vyčerpání prostředků naleznete v tématu <xref:security/blazor/server>.
