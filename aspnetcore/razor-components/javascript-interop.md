---
title: Razor JavaScript součásti zprostředkovatel komunikace s objekty
author: guardrex
description: Zjistěte, jak volat funkce jazyka JavaScript od .NET a .NET metody z jazyka JavaScript v aplikacích Blazor a součásti syntaxe Razor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/javascript-interop
ms.openlocfilehash: 9f822fee8990b03ff15ffa9857a2ddd95328a6ce
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159266"
---
# <a name="razor-components-javascript-interop"></a>Razor JavaScript součásti zprostředkovatel komunikace s objekty

Podle [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), a [Luke Latham](https://github.com/guardrex)

Razor součásti aplikace můžete volat funkce jazyka JavaScript od .NET a .NET metody z kódu jazyka JavaScript.

## <a name="invoke-javascript-functions-from-net-methods"></a>Vyvolání funkce jazyka JavaScript z metod rozhraní .NET

Existují situace, kdy je potřeba volat funkce jazyka JavaScript kód .NET. Například volání JavaScriptu můžete zveřejnit možnosti prohlížeče nebo funkce z knihovny jazyka JavaScript do aplikace.

Chcete-li volat JavaScript z .NET, použijte `IJSRuntime` abstrakce. `InvokeAsync<T>` Metodu na `IJSRuntime` přebírá identifikátor funkce JavaScript, která chcete vyvolat spolu s libovolný počet argumentů serializovat na JSON. Identifikátor funkce je relativní vzhledem ke globální obor (`window`). Pokud chcete volat `window.someScope.someFunction`, je identifikátor `someScope.someFunction`. Není nutné zaregistrovat funkci předtím, než je volána. Návratový typ `T` musí také být JSON serializovatelný.

Pro aplikace ASP.NET Core Razor komponenty na straně serveru:

* Aplikace na straně serveru zpracovává více požadavků uživatele. Nevolejte `JSRuntime.Current` v součásti pro vyvolání funkce jazyka JavaScript.
* Vložit `IJSRuntime` abstrakce a použití vloženého objektu vydat spolupráce volání JavaScriptu.

Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), experimentální dekodér založené na jazyce JavaScript. Příklad ukazuje, jak vyvolat funkci z jazyka JavaScript C# metody. Funkce JavaScript, která přijímá pole bajtů z C# metody dekóduje pole a vrátí text na komponentu pro zobrazení.

Uvnitř `<head>` prvek *wwwroot/index.html*, poskytují funkce, která používá `TextDecoder` k dekódování předané pole:

```html
<script>
  window.ConvertArray = (win1251Array) => {
    var win1251decoder = new TextDecoder('windows-1251');
    var bytes = new Uint8Array(win1251Array);
    var decodedArray = win1251decoder.decode(bytes);
    console.log(decodedArray);
    return decodedArray;
  };
</script>
```

Kód jazyka JavaScript, jako je například kódu zobrazeného v předchozím příkladu je také možné načíst soubor JavaScript s odkazem na soubor skriptu *wwwroot/index.html* souboru.

Následující komponenty:

* Vyvolá `ConvertArray` funkcí jazyka JavaScript s použitím `JsRuntime` při tlačítko součásti (**převést pole**) je vybraná.
* Po zavolání funkce JavaScript, která se převede předané pole na řetězec. Řetězec se vrátí do komponenty pro zobrazení.

```cshtml
@page "/"
@using Microsoft.JSInterop;
@inject IJSRuntime JsRuntime;

<h1>Call JavaScript Function Example</h1>

<button type="button" class="btn btn-primary" onclick="@ConvertArray">
    Convert Array
</button>

<p class="mt-2" style="font-size:1.6em">
    <span class="badge badge-success">
        @ConvertedText
    </span>
</p>

@functions {
    // Quote (c)2005 Universal Pictures: Serenity
    // https://www.uphe.com/movies/serenity
    // David Krumholtz on IMDB: https://www.imdb.com/name/nm0472710/

    private MarkupString ConvertedText =
        new MarkupString("Select the <b>Convert Array</b> button.");

    private uint[] QuoteArray = new uint[]
        {
            60, 101, 109, 62, 67, 97, 110, 39, 116, 32, 115, 116, 111, 112, 32,
            116, 104, 101, 32, 115, 105, 103, 110, 97, 108, 44, 32, 77, 97,
            108, 46, 60, 47, 101, 109, 62, 32, 45, 32, 77, 114, 46, 32, 85, 110,
            105, 118, 101, 114, 115, 101, 10, 10,
        };

    async void ConvertArray()
    {
        var text =
            await JsRuntime.InvokeAsync<string>("ConvertArray", QuoteArray);

        ConvertedText = new MarkupString(text);

        StateHasChanged();
    }
}
```

Pro Blazor aplikace na straně klienta `IJSRuntime` abstrakcí je přístupný z `JSRuntime.Current`, která odkazuje na žádost uživatele. Protože se nachází pouze jeden uživatel Blazor aplikace na straně klienta, použití `JSRuntime.Current` k vyvolání JavaScript funkce fungují normálně. Používejte pouze `JSRuntime.Current` v aplikacích Blazor na straně klienta.

V aplikaci ukázka na straně klienta, který doprovází v tomto tématu jsou k dispozici aplikaci na straně klienta, která pracovat s modelu DOM na vstup uživatele a zobrazení uvítací zprávy dvě funkce jazyka JavaScript:

* `showPrompt` &ndash; Zobrazí výzvu k zadání tak, aby přijímal vstup uživatele (uživatelské jméno) a vrátí řízení volajícímu název.
* `displayWelcome` &ndash; Přiřadí zobrazení uvítací zprávy z volající objekt modelu DOM se `id` z `welcome`.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

Místo `<script>` značka, která odkazuje na soubor jazyka JavaScript v *wwwroot/index.html* souboru:

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=16)]

V souboru součásti není umístit značku skriptu, protože značku skriptu nejde dynamicky aktualizovat.

Interoperabilita metod rozhraní .NET s funkce jazyka JavaScript pomocí volání `InvokeAsync<T>` metodu na `IJSRuntime`.

Ukázková aplikace používá dvojici C# metody, `Prompt` a `Display`, který má být vyvolán `showPrompt` a `displayWelcome` funkce jazyka JavaScript:

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=6-8,14-16)]

`IJSRuntime` Abstrakcí je asynchronní umožní použít scénáře na straně serveru. Pokud aplikace běží na straně klienta a vy chcete volat funkce jazyka JavaScript synchronně, přetypovat dolů na `IJSInProcessRuntime` a volat `Invoke<T>` místo. Doporučujeme vám, že nejlépe využil spolupráce knihovny JavaScript asynchronní rozhraní API, aby tyto knihovny jsou k dispozici ve všech scénářích, na straně klienta nebo na straně serveru.

Ukázková aplikace obsahuje komponentu k předvedení JS zprostředkovatele komunikace s objekty. Komponenty:

* Přijímá vstupu uživatele prostřednictvím řádku JS.
* Vrátí text na komponentu pro zpracování.
* Volá druhé JS funkci, která komunikuje s DOM pro zobrazení uvítací zprávy.

*Pages/JSInterop.cshtml*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?start=1&end=21&highlight=2-3,9-11,13,16-20)]

1. Při `TriggerJsPrompt` provádí výběrem komponenty **aktivační událost jazyka JavaScript výzvy** tlačítko, `ExampleJsInterop.Prompt` metoda C# kód je volán.
1. `Prompt` Metoda spustí JavaScript `showPrompt` funkce součástí *wwwroot/exampleJsInterop.js* souboru.
1. `showPrompt` Funkce přijímá vstup uživatele (uživatelské jméno), což je kódovaný jazykem HTML a vráceno `Prompt` metoda a nakonec zpět do komponenty. Součást uloží uživatelské jméno v místní proměnné, `name`.
1. Je řetězec uložen ve `name` je zahrnut do zobrazení uvítací zprávy, která se předá do sekundy C# metody `ExampleJsInterop.Display`.
1. `Display` zavolá funkci v jazyce JavaScript `displayWelcome`, který vykreslí zobrazení uvítací zprávy do záhlaví značky.

## <a name="capture-references-to-elements"></a>Zachycení odkazy na elementy

Některé [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:razor-components/javascript-interop) scénáře vyžadují odkazy na prvky jazyka HTML. Například knihovna uživatelského rozhraní může vyžadovat odkaz na prvek pro inicializaci, nebo můžete potřebovat pro volání rozhraní API jako příkaz pro element, jako například `focus` nebo `play`.

Odkazy na elementy HTML v komponentě můžete zachytit tak, že přidáte `ref` atribut na prvek jazyka HTML a pak definování pole typu `ElementRef` jejichž název odpovídá hodnotě `ref` atribut.

Následující příklad ukazuje, zachycení odkazu na element input uživatelské jméno:

```csharp
<input ref="username" ... />

@functions {
    ElementRef username;
}
```

> [!NOTE]
> Proveďte **není** používat odkazy zachycené element jako způsob vyplnění modelu DOM. To může být v rozporu s modelem deklarativní vykreslovací.

Co se týče kódu .NET, `ElementRef` je neprůhledný popisovač. *Pouze* věc, kterou vám pomůžou s ním je komunikace přes kódu jazyka JavaScript pomocí zprostředkovatele komunikace s objekty jazyka JavaScript. Pokud tak učiníte, obdrží kód JavaScript na straně `HTMLElement` instance, které můžete použít s normální modelu DOM rozhraní API.

Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na element:

*mylib.js*:

```javascript
window.myLib = {
  focusElement : function (element) {
    element.focus();
  }
}
```

*ElementRefExtensions.cs*:

```csharp
using Microsoft.AspNetCore.Blazor;
using Microsoft.JSInterop;
using System.Threading.Tasks;

namespace MyLib
{
    public static class MyLibElementRefExtensions
    {
        public static Task Focus(this ElementRef elementRef)
        {
            return JSRuntime.Current.InvokeAsync<object>("myLib.focusElement", elementRef);
        }
    }
}
```

Teď můžete soustředit vstupů v libovolné součásti:

```cshtml
@using MyLib

<input ref="username" />
<button onclick="@SetFocus">Set focus</button>

@functions {
    ElementRef username;

    void SetFocus()
    {
        username.Focus();
    }
}
```

> [!IMPORTANT]
> `username` Proměnná je vyplněný pouze po komponentu vykreslí a zahrnuje její výstup `<input>` elementu. Pokud se pokusíte předat unpopulated `ElementRef` do kódu jazyka JavaScript, kód jazyka JavaScript obdrží `null`. K manipulaci s odkazy na prvky po vykreslení (Chcete-li nastavit počáteční fokus na prvek) použijte komponentu `OnAfterRenderAsync` nebo `OnAfterRender` [součástí životního cyklu metody](xref:razor-components/components#lifecycle-methods).

## <a name="invoke-net-methods-from-javascript-functions"></a>Vyvolání metod rozhraní .NET z funkce jazyka JavaScript

### <a name="static-net-method-call"></a>Volání statické metody rozhraní .NET

Chcete-li volání statické metody rozhraní .NET z jazyka JavaScript, použijte `DotNet.invokeMethod` nebo `DotNet.invokeMethodAsync` funkce. Předat identifikátor statická metoda, kterou chcete volat, název sestavení obsahujícího funkce a žádné argumenty. Znovu se upřednostňuje asynchronní verze pro zajištění podpory scénářů na straně serveru. Lze vyvolat z jazyka JavaScript, .NET metoda musí být veřejné, statické a upravený s `[JSInvokable]`. Ve výchozím nastavení, identifikátor metody je název metody, ale můžete zadat jiný identifikátor pomocí `JSInvokableAttribute` konstruktoru. Volání obecné metody otevřít se momentálně nepodporuje.

Obsahuje ukázkovou aplikaci C# metoda vrátí pole `int`s. Metoda je doplněn `JSInvokable` atribut.

*Pages/JsInterop.cshtml*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?start=47&end=58&highlight=7-11)]

Obsluhuje klientovi JavaScript vyvolá C# metoda .NET.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-12)]

Když **aktivační událost .NET statickou metodu ReturnArrayAsync** vybere tlačítko, prohlédněte si výstup konzoly v prohlížeči webové nástroje pro vývojáře:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Čtvrtá hodnota pole je vloženo do pole (`data.push(4);`) vrácený `ReturnArrayAsync`.

### <a name="instance-method-call"></a>Volání metody instance

Můžete také volat instanci metody rozhraní .NET z jazyka JavaScript. Vyvolání metody instance .NET z jazyka JavaScript, nejprve projít .NET instance do jazyka Javasript obalením v `DotNetObjectRef` instance. .NET instance je předána odkazem pro jazyk JavaScript a můžete vyvolávat metody instance .NET na použití instance `invokeMethod` nebo `invokeMethodAsync` funkce. .NET instance můžete také předán jako argument při volání metod rozhraní .NET z jazyka JavaScript.

> [!NOTE]
> Ukázková aplikace zprávy protokolu ke konzole na straně klienta. Následující příklady jsme vám ukázali bude ukázková aplikace prohlédněte výstup konzoly prohlížeče v prohlížeči vývojářské nástroje.

Když **metodu instance aktivační událost .NET HelloHelper.SayHello** výběru tlačítka `ExampleJsInterop.CallHelloHelperSayHello` nazývá a předává název, `Blazor`, metody.

*Pages/JsInterop.cshtml*:

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?start=60&end=69&highlight=8)]

`CallHelloHelperSayHello` funkce jazyka JavaScript vyvolá `sayHello` s novou instanci třídy `HelloHelper`.

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=19-25)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-17)]

Název je předán `HelloHelper`pro konstruktor, který nastaví `HelloHelper.Name` vlastnost. Když funkce JavaScript, která `sayHello` provádí, `HelloHelper.SayHello` vrátí `Hello, {Name}!` zprávy, která zapisuje do konzoly pro funkce jazyka JavaScript.

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Výstup v nástrojích pro vývojáře v prohlížeči na webové konzoly:

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-razor-component-class-library"></a>Sdílení knihovny tříd Razor komponenty interoperační kód.

Interoperační kód jazyka JavaScript, mohou být součástí knihovny tříd Razor součásti (`dotnet new blazorlib`), která umožňuje sdílet kód v balíčku NuGet.

Knihovny tříd Razor komponenta zpracovává vkládání prostředky jazyka JavaScript v sestavení. Soubory jazyka JavaScript jsou umístěny v *wwwroot* složky a nástrojů se postará o vkládání prostředků při vytváření knihovny.

Sestavené balíček NuGet je popsána v souboru projektu aplikace, stejně jako jakýkoli normální balíček NuGet se odkazuje. Po obnovení aplikace, kód aplikace může volat do jazyka JavaScript, jako by šlo C#.
