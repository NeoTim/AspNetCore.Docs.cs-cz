---
title: Zprostředkovatel komunikace s objekty jazyka Blazor JavaScript
author: guardrex
description: Zjistěte, jak volat funkce jazyka JavaScript od .NET a .NET metody z jazyka JavaScript v aplikacích Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/13/2019
uid: blazor/javascript-interop
ms.openlocfilehash: 8711c9ec0dd5d9bf59fc74b44285329165a21ba4
ms.sourcegitcommit: ccbb84ae307a5bc527441d3d509c20b5c1edde05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2019
ms.locfileid: "65874858"
---
# <a name="blazor-javascript-interop"></a>Zprostředkovatel komunikace s objekty jazyka Blazor JavaScript

Podle [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), a [Luke Latham](https://github.com/guardrex)

Blazor aplikace můžete volat funkce jazyka JavaScript od .NET a .NET metody z kódu jazyka JavaScript.

## <a name="invoke-javascript-functions-from-net-methods"></a>Vyvolání funkce jazyka JavaScript z metod rozhraní .NET

Existují situace, kdy je potřeba volat funkce jazyka JavaScript kód .NET. Například volání JavaScriptu můžete zveřejnit možnosti prohlížeče nebo funkce z knihovny jazyka JavaScript do aplikace.

Chcete-li volat JavaScript z .NET, použijte `IJSRuntime` abstrakce. `InvokeAsync<T>` Metoda přebírá identifikátor funkce JavaScriptu, která chcete vyvolat spolu s libovolný počet argumentů serializovat na JSON. Identifikátor funkce je relativní vzhledem ke globální obor (`window`). Pokud chcete volat `window.someScope.someFunction`, je identifikátor `someScope.someFunction`. Není nutné zaregistrovat funkci předtím, než je volána. Návratový typ `T` musí také být JSON serializovatelný.

Pro serverové aplikace:

* Aplikace na straně serveru zpracovává více požadavků uživatele. Nevolejte `JSRuntime.Current` v součásti pro vyvolání funkce jazyka JavaScript.
* Vložit `IJSRuntime` abstrakce a použití vloženého objektu vydat spolupráce volání JavaScriptu.
* Zatímco Blazor aplikace je před vykreslením, volání do jazyka JavaScript není možné, protože nebyla navázat připojení s prohlížečem. Další informace najdete v tématu [rozpoznat, kdy aplikace Blazor se před vykreslením](#detect-when-a-blazor-app-is-prerendering) oddílu.

Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), experimentální dekodér založené na jazyce JavaScript. Příklad ukazuje, jak vyvolat funkci z jazyka JavaScript C# metody. Funkce JavaScript, která přijímá pole bajtů z C# metody dekóduje pole a vrátí text na komponentu pro zobrazení.

Uvnitř `<head>` prvek *wwwroot/index.html* (Blazor straně klienta) nebo *stránek /\_Host.cshtml* (Blazor serverové), poskytují funkce, která používá `TextDecoder` do dekódování předané pole:

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

Kód jazyka JavaScript, jako je například kódu zobrazeného v předchozím příkladu je také možné načíst ze souboru JavaScriptu (*js*) s odkazem na soubor skriptu:

```html
<script src="exampleJsInterop.js"></script>
```

Následující komponenty:

* Vyvolá `ConvertArray` funkcí jazyka JavaScript s použitím `JsRuntime` při tlačítko součásti (**převést pole**) je vybraná.
* Po zavolání funkce JavaScript, která se převede předané pole na řetězec. Řetězec se vrátí do komponenty pro zobrazení.

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

Použít `IJSRuntime` abstrakce, použijte některý z následujících postupů:

* Vložit `IJSRuntime` abstrakce do komponenty Razor (*.razor*):

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* Vložit `IJSRuntime` abstrakce do třídy (*.cs*):

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* Pro dynamické generování obsahu s [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), použijte `[Inject]` atribut:

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

V aplikaci ukázka na straně klienta, který doprovází v tomto tématu jsou k dispozici aplikaci na straně klienta, která pracovat s modelu DOM na vstup uživatele a zobrazení uvítací zprávy dvě funkce jazyka JavaScript:

* `showPrompt` &ndash; Zobrazí výzvu k zadání tak, aby přijímal vstup uživatele (uživatelské jméno) a vrátí řízení volajícímu název.
* `displayWelcome` &ndash; Přiřadí zobrazení uvítací zprávy z volající objekt modelu DOM se `id` z `welcome`.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Místo `<script>` značka, která odkazuje na soubor jazyka JavaScript v *wwwroot/index.html* souboru (Blazor straně klienta) nebo *stránek /\_Host.cshtml* (Blazor serverové) soubor.

*Wwwroot/index.HTML* (Blazor straně klienta):

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

*Stránky /\_Host.cshtml* (Blazor serverové):

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

Neukládejte `<script>` značky v souboru součásti, protože `<script>` značku nejde dynamicky aktualizovat.

Funkce .NET metody spolupráce pomocí jazyka JavaScript v *exampleJsInterop.js* souboru voláním `IJSRuntime.InvokeAsync<T>`.

`IJSRuntime` Abstrakcí je asynchronní umožní použít scénáře na straně serveru. Pokud aplikace běží na straně klienta a vy chcete volat funkce jazyka JavaScript synchronně, přetypovat dolů na `IJSInProcessRuntime` a volat `Invoke<T>` místo. Doporučujeme většinu spolupráce knihoven jazyka JavaScript použít asynchronní rozhraní API k zajištění, že tyto knihovny jsou k dispozici ve všech scénářích, na straně klienta nebo na straně serveru.

Ukázková aplikace obsahuje komponentu k předvedení zprostředkovatele komunikace s objekty jazyka JavaScript. Komponenty:

* Přijímá vstupu uživatele prostřednictvím řádku jazyka JavaScript.
* Vrátí text na komponentu pro zpracování.
* Volá druhé funkce JavaScriptu, která komunikuje s DOM pro zobrazení uvítací zprávy.

*Pages/JSInterop.razor*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. Při `TriggerJsPrompt` provádí výběrem komponenty **aktivační událost jazyka JavaScript výzvy** tlačítko, JavaScript `showPrompt` funkce součástí *wwwroot/exampleJsInterop.js* soubor volá se.
1. `showPrompt` Funkce přijímá vstup uživatele (uživatelské jméno), což je kódovaný jazykem HTML a vrácené na komponentu. Součást uloží uživatelské jméno v místní proměnné, `name`.
1. Je řetězec uložen ve `name` je zahrnut do zobrazení uvítací zprávy, která se předá do funkce JavaScriptu, `displayWelcome`, který vykreslí zobrazení uvítací zprávy do záhlaví značky.

## <a name="detect-when-a-blazor-app-is-prerendering"></a>Rozpoznat, kdy aplikace Blazor se před vykreslením
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a>Zachycení odkazy na elementy

Některé [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:blazor/javascript-interop) scénáře vyžadují odkazy na prvky jazyka HTML. Například knihovna uživatelského rozhraní může vyžadovat odkaz na prvek pro inicializaci, nebo můžete potřebovat pro volání rozhraní API jako příkaz pro element, jako například `focus` nebo `play`.

Můžete zaznamenat odkazy na elementy HTML v komponentě používá následující postup:

* Přidat `ref` atribut na prvek jazyka HTML.
* Definování pole typu `ElementRef` jejichž název odpovídá hodnotě `ref` atribut.

Následující příklad ukazuje zachycení odkazu na `username` `<input>` element:

```cshtml
<input ref="username" ...>

@functions {
    ElementRef username;
}
```

> [!NOTE]
> Proveďte **není** používat odkazy zachycené element jako způsob vyplnění modelu DOM. To může být v rozporu s modelem deklarativní vykreslovací.

Co se týče kódu .NET, `ElementRef` je neprůhledný popisovač. *Pouze* věc, kterou vám pomůžou s `ElementRef` je komunikace přes kódu jazyka JavaScript pomocí zprostředkovatele komunikace s objekty jazyka JavaScript. Pokud tak učiníte, obdrží kód JavaScript na straně `HTMLElement` instance, které můžete použít s normální modelu DOM rozhraní API.

Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na element:

*exampleJsInterop.js*:

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

Použití `IJSRuntime.InvokeAsync<T>` a volat `exampleJsFunctions.focusElement` s `ElementRef` zaměřit element:

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,7,11-12)]

Pokud chcete použít rozšiřující metodu zaměřit elementu, vytvořte rozšíření statická metoda, která přijímá `IJSRuntime` instance:

```csharp
public static Task Focus(this ElementRef elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

Metoda je volána přímo v objektu. Následující příklad předpokládá, že statické `Focus` metoda je k dispozici `JsInteropClasses` obor názvů:

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,8,12)]

> [!IMPORTANT]
> `username` Proměnná je vyplněný pouze po komponentu vykreslí a zahrnuje její výstup `>` elementu. Pokud se pokusíte předat unpopulated `ElementRef` do kódu jazyka JavaScript, kód jazyka JavaScript obdrží `null`. K manipulaci s odkazy na prvky po vykreslení (Chcete-li nastavit počáteční fokus na prvek) použijte komponentu `OnAfterRenderAsync` nebo `OnAfterRender` [součástí životního cyklu metody](xref:blazor/components#lifecycle-methods).

## <a name="invoke-net-methods-from-javascript-functions"></a>Vyvolání metod rozhraní .NET z funkce jazyka JavaScript

### <a name="static-net-method-call"></a>Volání statické metody rozhraní .NET

Chcete-li volání statické metody rozhraní .NET z jazyka JavaScript, použijte `DotNet.invokeMethod` nebo `DotNet.invokeMethodAsync` funkce. Předat identifikátor statická metoda, kterou chcete volat, název sestavení obsahujícího funkce a žádné argumenty. Asynchronní verze se upřednostňuje pro zajištění podpory scénářů na straně serveru. Volání metody rozhraní .NET z jazyka JavaScript, .NET metoda musí být veřejné, statické a mít `[JSInvokable]` atribut. Ve výchozím nastavení, identifikátor metody je název metody, ale můžete zadat jiný identifikátor pomocí `JSInvokableAttribute` konstruktoru. Volání obecné metody otevřít se momentálně nepodporuje.

Obsahuje ukázkovou aplikaci C# metoda vrátí pole `int`s. `JSInvokable` Atributu se použije pro metodu.

*Pages/JsInterop.razor*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

Obsluhuje klientovi JavaScript vyvolá C# metoda .NET.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Když **aktivační událost .NET statickou metodu ReturnArrayAsync** vybere tlačítko, prohlédněte si výstup konzoly v prohlížeči webové nástroje pro vývojáře.

Výstup konzoly je:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Čtvrtá hodnota pole je vloženo do pole (`data.push(4);`) vrácený `ReturnArrayAsync`.

### <a name="instance-method-call"></a>Volání metody instance

Můžete také volat instanci metody rozhraní .NET z jazyka JavaScript. Chcete-li vyvolat metodu instance .NET z jazyka JavaScript:

* Předejte instanci .NET do jazyka Javasript obalením ho `DotNetObjectRef` instance. .NET instance je předána odkazem pro jazyk JavaScript.
* Vyvolání metody instance .NET na použití instance `invokeMethod` nebo `invokeMethodAsync` funkce. .NET instance můžete také předán jako argument při volání metod rozhraní .NET z jazyka JavaScript.

> [!NOTE]
> Ukázková aplikace zprávy protokolu ke konzole na straně klienta. Následující příklady jsme vám ukázali bude ukázková aplikace prohlédněte výstup konzoly prohlížeče v prohlížeči vývojářské nástroje.

Když **metodu instance aktivační událost .NET HelloHelper.SayHello** výběru tlačítka `ExampleJsInterop.CallHelloHelperSayHello` nazývá a předává název, `Blazor`, metody.

*Pages/JsInterop.razor*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

`CallHelloHelperSayHello` funkce jazyka JavaScript vyvolá `sayHello` s novou instanci třídy `HelloHelper`.

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Název je předán `HelloHelper`pro konstruktor, který nastaví `HelloHelper.Name` vlastnost. Když funkce JavaScript, která `sayHello` provádí, `HelloHelper.SayHello` vrátí `Hello, {Name}!` zprávy, která zapisuje do konzoly pro funkce jazyka JavaScript.

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Výstup v nástrojích pro vývojáře v prohlížeči na webové konzoly:

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a>Sdílet interoperační kód. v knihovně tříd

Interoperační kód jazyka JavaScript, mohou být součástí knihovny tříd, která umožňuje sdílet kód v balíčku NuGet.

Knihovna tříd zpracovává vkládání prostředky jazyka JavaScript v sestavení. Soubory jazyka JavaScript jsou umístěny v *wwwroot* složky. Nástroje postará vkládání prostředků při vytváření knihovny.

Sestavené balíček NuGet je popsána v souboru projektu aplikace, stejně jako jakýkoli normální balíček NuGet se odkazuje. Po obnovení aplikace kód aplikace může volat do jazyka JavaScript, jako by šlo C#.

Další informace naleznete v tématu <xref:blazor/class-libraries>.
