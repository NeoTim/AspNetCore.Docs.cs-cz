---
title: Razor JavaScript součásti zprostředkovatel komunikace s objekty
author: guardrex
description: Zjistěte, jak volat funkce jazyka JavaScript od .NET a .NET metody z jazyka JavaScript v aplikacích Blazor a součásti syntaxe Razor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/javascript-interop
ms.openlocfilehash: 9844bd5a63f1144867360ac4f31645d36710989c
ms.sourcegitcommit: d913bca90373c07f89b1d1df01af5fc01fc908ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57978497"
---
# <a name="razor-components-javascript-interop"></a><span data-ttu-id="def05-103">Razor JavaScript součásti zprostředkovatel komunikace s objekty</span><span class="sxs-lookup"><span data-stu-id="def05-103">Razor Components JavaScript interop</span></span>

<span data-ttu-id="def05-104">Podle [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="def05-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="def05-105">Razor součásti aplikace můžete volat funkce jazyka JavaScript od .NET a .NET metody z kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="def05-105">A Razor Components app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="def05-106">Vyvolání funkce jazyka JavaScript z metod rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="def05-106">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="def05-107">Existují situace, kdy je potřeba volat funkce jazyka JavaScript kód .NET.</span><span class="sxs-lookup"><span data-stu-id="def05-107">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="def05-108">Například volání JavaScriptu můžete zveřejnit možnosti prohlížeče nebo funkce z knihovny jazyka JavaScript do aplikace.</span><span class="sxs-lookup"><span data-stu-id="def05-108">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="def05-109">Chcete-li volat JavaScript z .NET, použijte `IJSRuntime` abstrakce.</span><span class="sxs-lookup"><span data-stu-id="def05-109">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="def05-110">`InvokeAsync<T>` Metoda přebírá identifikátor funkce JavaScriptu, která chcete vyvolat spolu s libovolný počet argumentů serializovat na JSON.</span><span class="sxs-lookup"><span data-stu-id="def05-110">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="def05-111">Identifikátor funkce je relativní vzhledem ke globální obor (`window`).</span><span class="sxs-lookup"><span data-stu-id="def05-111">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="def05-112">Pokud chcete volat `window.someScope.someFunction`, je identifikátor `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="def05-112">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="def05-113">Není nutné zaregistrovat funkci předtím, než je volána.</span><span class="sxs-lookup"><span data-stu-id="def05-113">There's no need to register the function before it's called.</span></span> <span data-ttu-id="def05-114">Návratový typ `T` musí také být JSON serializovatelný.</span><span class="sxs-lookup"><span data-stu-id="def05-114">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="def05-115">Pro aplikace ASP.NET Core Razor komponenty na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="def05-115">For server-side ASP.NET Core Razor Components apps:</span></span>

* <span data-ttu-id="def05-116">Aplikace na straně serveru zpracovává více požadavků uživatele.</span><span class="sxs-lookup"><span data-stu-id="def05-116">Multiple user requests are processed by the server-side app.</span></span> <span data-ttu-id="def05-117">Nevolejte `JSRuntime.Current` v součásti pro vyvolání funkce jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="def05-117">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="def05-118">Vložit `IJSRuntime` abstrakce a použití vloženého objektu vydat spolupráce volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="def05-118">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>

<span data-ttu-id="def05-119">Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), experimentální dekodér založené na jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="def05-119">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="def05-120">Příklad ukazuje, jak vyvolat funkci z jazyka JavaScript C# metody.</span><span class="sxs-lookup"><span data-stu-id="def05-120">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="def05-121">Funkce JavaScript, která přijímá pole bajtů z C# metody dekóduje pole a vrátí text na komponentu pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="def05-121">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="def05-122">Uvnitř `<head>` prvek *wwwroot/index.html*, poskytují funkce, která používá `TextDecoder` k dekódování předané pole:</span><span class="sxs-lookup"><span data-stu-id="def05-122">Inside the `<head>` element of *wwwroot/index.html*, provide a function that uses `TextDecoder` to decode a passed array:</span></span>

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

<span data-ttu-id="def05-123">Kód jazyka JavaScript, jako je například kódu zobrazeného v předchozím příkladu je také možné načíst ze souboru jazyka JavaScript (*js*) s odkazem na soubor skriptu *wwwroot/index.html* souboru:</span><span class="sxs-lookup"><span data-stu-id="def05-123">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file in the *wwwroot/index.html* file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="def05-124">Následující komponenty:</span><span class="sxs-lookup"><span data-stu-id="def05-124">The following component:</span></span>

* <span data-ttu-id="def05-125">Vyvolá `ConvertArray` funkcí jazyka JavaScript s použitím `JsRuntime` při tlačítko součásti (**převést pole**) je vybraná.</span><span class="sxs-lookup"><span data-stu-id="def05-125">Invokes the `ConvertArray` JavaScript function using `JsRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="def05-126">Po zavolání funkce JavaScript, která se převede předané pole na řetězec.</span><span class="sxs-lookup"><span data-stu-id="def05-126">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="def05-127">Řetězec se vrátí do komponenty pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="def05-127">The string is returned to the component for display.</span></span>

```cshtml
@page "/"
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

<span data-ttu-id="def05-128">Použít `IJSRuntime` abstrakce, použijte některý z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="def05-128">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="def05-129">Vložit `IJSRuntime` abstrakce do souboru Razor (*.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="def05-129">Inject the `IJSRuntime` abstraction into the Razor file (*.cshtml*):</span></span>

  ```cshtml
  @inject IJSRuntime JSRuntime

  @functions {
      public override void OnInit()
      {
          StocksService.OnStockTickerUpdated += stockUpdate =>
          {
              JSRuntime.InvokeAsync<object>(
                  "handleTickerChanged", 
                  stockUpdate.symbol, 
                  stockUpdate.price);
          };
      }
  }
  ```
* <span data-ttu-id="def05-130">Vložit `IJSRuntime` abstrakce do třídy (*.cs*):</span><span class="sxs-lookup"><span data-stu-id="def05-130">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  ```csharp
  public class MyJsInterop
  {
      private readonly IJSRuntime _jsRuntime;

      public MyJsInterop(IJSRuntime jsRuntime)
      {
          _jsRuntime = jsRuntime;
      }

      public Task<string> DoSomething(string data)
      {
          // The doSomething JavaScript method is implemented 
          // in a JavaScript file, such as 'wwwroot/MyJsInterop.js'.
          return _jsRuntime.InvokeAsync<string>(
              "myJsFunctions.doSomething",
              data);
      }
  }
  ```
* <span data-ttu-id="def05-131">Pro dynamické generování obsahu s `BuildRenderTree`, použijte `[Inject]` atribut:</span><span class="sxs-lookup"><span data-stu-id="def05-131">For dynamic content generation with `BuildRenderTree`, use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject] IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="def05-132">V aplikaci ukázka na straně klienta, který doprovází v tomto tématu jsou k dispozici aplikaci na straně klienta, která pracovat s modelu DOM na vstup uživatele a zobrazení uvítací zprávy dvě funkce jazyka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="def05-132">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the client-side app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="def05-133">`showPrompt` &ndash; Zobrazí výzvu k zadání tak, aby přijímal vstup uživatele (uživatelské jméno) a vrátí řízení volajícímu název.</span><span class="sxs-lookup"><span data-stu-id="def05-133">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="def05-134">`displayWelcome` &ndash; Přiřadí zobrazení uvítací zprávy z volající objekt modelu DOM se `id` z `welcome`.</span><span class="sxs-lookup"><span data-stu-id="def05-134">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="def05-135">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="def05-135">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="def05-136">Místo `<script>` značka, která odkazuje na soubor jazyka JavaScript v *wwwroot/index.html* souboru:</span><span class="sxs-lookup"><span data-stu-id="def05-136">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file:</span></span>

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="def05-137">Neukládejte `<script>` značky v souboru součásti, protože `<script>` značku nejde dynamicky aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="def05-137">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="def05-138">Funkce .NET metody spolupráce pomocí jazyka JavaScript v *exampleJsInterop.js* souboru voláním `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="def05-138">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="def05-139">Ukázková aplikace používá dvojici C# metody, `Prompt` a `Display`, který má být vyvolán `showPrompt` a `displayWelcome` funkce jazyka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="def05-139">The sample app uses a pair of C# methods, `Prompt` and `Display`, to invoke the `showPrompt` and `displayWelcome` JavaScript functions:</span></span>

<span data-ttu-id="def05-140">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="def05-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=13-15,21-23)]

<span data-ttu-id="def05-141">`IJSRuntime` Abstrakcí je asynchronní umožní použít scénáře na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="def05-141">The `IJSRuntime` abstraction is asynchronous to allow for server-side scenarios.</span></span> <span data-ttu-id="def05-142">Pokud aplikace běží na straně klienta a vy chcete volat funkce jazyka JavaScript synchronně, přetypovat dolů na `IJSInProcessRuntime` a volat `Invoke<T>` místo.</span><span class="sxs-lookup"><span data-stu-id="def05-142">If the app runs client-side and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="def05-143">Doporučujeme většinu spolupráce knihoven jazyka JavaScript použít asynchronní rozhraní API k zajištění, že tyto knihovny jsou k dispozici ve všech scénářích, na straně klienta nebo na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="def05-143">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios, client-side or server-side.</span></span>

<span data-ttu-id="def05-144">Ukázková aplikace obsahuje komponentu k předvedení zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="def05-144">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="def05-145">Komponenty:</span><span class="sxs-lookup"><span data-stu-id="def05-145">The component:</span></span>

* <span data-ttu-id="def05-146">Přijímá vstupu uživatele prostřednictvím řádku jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="def05-146">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="def05-147">Vrátí text na komponentu pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="def05-147">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="def05-148">Volá druhé funkce JavaScriptu, která komunikuje s DOM pro zobrazení uvítací zprávy.</span><span class="sxs-lookup"><span data-stu-id="def05-148">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="def05-149">*Pages/JSInterop.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="def05-149">*Pages/JSInterop.cshtml*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?start=1&end=21)]

1. <span data-ttu-id="def05-150">Při `TriggerJsPrompt` provádí výběrem komponenty **aktivační událost jazyka JavaScript výzvy** tlačítko, `ExampleJsInterop.Prompt` metoda C# kód je volán.</span><span class="sxs-lookup"><span data-stu-id="def05-150">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the `ExampleJsInterop.Prompt` method in C# code is called.</span></span>
1. <span data-ttu-id="def05-151">`Prompt` Metoda spustí JavaScript `showPrompt` funkce součástí *wwwroot/exampleJsInterop.js* souboru.</span><span class="sxs-lookup"><span data-stu-id="def05-151">The `Prompt` method executes the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file.</span></span>
1. <span data-ttu-id="def05-152">`showPrompt` Funkce přijímá vstup uživatele (uživatelské jméno), což je kódovaný jazykem HTML a vráceno `Prompt` metoda a nakonec zpět do komponenty.</span><span class="sxs-lookup"><span data-stu-id="def05-152">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the `Prompt` method and ultimately back to the component.</span></span> <span data-ttu-id="def05-153">Součást uloží uživatelské jméno v místní proměnné, `name`.</span><span class="sxs-lookup"><span data-stu-id="def05-153">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="def05-154">Je řetězec uložen ve `name` je zahrnut do zobrazení uvítací zprávy, která se předá do sekundy C# metody `ExampleJsInterop.Display`.</span><span class="sxs-lookup"><span data-stu-id="def05-154">The string stored in `name` is incorporated into a welcome message, which is passed to a second C# method, `ExampleJsInterop.Display`.</span></span>
1. <span data-ttu-id="def05-155">`Display` zavolá funkci v jazyce JavaScript `displayWelcome`, který vykreslí zobrazení uvítací zprávy do záhlaví značky.</span><span class="sxs-lookup"><span data-stu-id="def05-155">`Display` calls a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="capture-references-to-elements"></a><span data-ttu-id="def05-156">Zachycení odkazy na elementy</span><span class="sxs-lookup"><span data-stu-id="def05-156">Capture references to elements</span></span>

<span data-ttu-id="def05-157">Některé [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:razor-components/javascript-interop) scénáře vyžadují odkazy na prvky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="def05-157">Some [JavaScript interop](xref:razor-components/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="def05-158">Například knihovna uživatelského rozhraní může vyžadovat odkaz na prvek pro inicializaci, nebo můžete potřebovat pro volání rozhraní API jako příkaz pro element, jako například `focus` nebo `play`.</span><span class="sxs-lookup"><span data-stu-id="def05-158">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="def05-159">Odkazy na elementy HTML v komponentě můžete zachytit tak, že přidáte `ref` atribut na prvek jazyka HTML a pak definování pole typu `ElementRef` jejichž název odpovídá hodnotě `ref` atribut.</span><span class="sxs-lookup"><span data-stu-id="def05-159">You can capture references to HTML elements in a component by adding a `ref` attribute to the HTML element and then defining a field of type `ElementRef` whose name matches the value of the `ref` attribute.</span></span>

<span data-ttu-id="def05-160">Následující příklad ukazuje, zachycení odkazu na element input uživatelské jméno:</span><span class="sxs-lookup"><span data-stu-id="def05-160">The following example shows capturing a reference to the username input element:</span></span>

```cshtml
<input ref="username" ... />

@functions {
    ElementRef username;
}
```

> [!NOTE]
> <span data-ttu-id="def05-161">Proveďte **není** používat odkazy zachycené element jako způsob vyplnění modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="def05-161">Do **not** use captured element references as a way of populating the DOM.</span></span> <span data-ttu-id="def05-162">To může být v rozporu s modelem deklarativní vykreslovací.</span><span class="sxs-lookup"><span data-stu-id="def05-162">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="def05-163">Co se týče kódu .NET, `ElementRef` je neprůhledný popisovač.</span><span class="sxs-lookup"><span data-stu-id="def05-163">As far as .NET code is concerned, an `ElementRef` is an opaque handle.</span></span> <span data-ttu-id="def05-164">*Pouze* věc, kterou vám pomůžou s `ElementRef` je komunikace přes kódu jazyka JavaScript pomocí zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="def05-164">The *only* thing you can do with `ElementRef` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="def05-165">Pokud tak učiníte, obdrží kód JavaScript na straně `HTMLElement` instance, které můžete použít s normální modelu DOM rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="def05-165">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="def05-166">Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na element:</span><span class="sxs-lookup"><span data-stu-id="def05-166">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="def05-167">*mylib.js*:</span><span class="sxs-lookup"><span data-stu-id="def05-167">*mylib.js*:</span></span>

```javascript
window.myLib = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="def05-168">*ElementRefExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="def05-168">*ElementRefExtensions.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.JSInterop;
using System.Threading.Tasks;

namespace MyLib
{
    public static class MyLibElementRefExtensions
    {
        private readonly IJSRuntime _jsRuntime;

        public MyJsInterop(IJSRuntime jsRuntime)
        {
            _jsRuntime = jsRuntime;
        }

        public static Task Focus(this ElementRef elementRef)
        {
            return _jsRuntime.InvokeAsync<object>(
                "myLib.focusElement", elementRef);
        }
    }
}
```

<span data-ttu-id="def05-169">Použití `MyLib` a volat `Focus` na `ElementRef` na vstupy fokus v libovolné součásti:</span><span class="sxs-lookup"><span data-stu-id="def05-169">Use `MyLib` and call `Focus` on an `ElementRef` to focus inputs in any component:</span></span>

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
> <span data-ttu-id="def05-170">`username` Proměnná je vyplněný pouze po komponentu vykreslí a zahrnuje její výstup `<input>` elementu.</span><span class="sxs-lookup"><span data-stu-id="def05-170">The `username` variable is only populated after the component renders and its output includes the `<input>` element.</span></span> <span data-ttu-id="def05-171">Pokud se pokusíte předat unpopulated `ElementRef` do kódu jazyka JavaScript, kód jazyka JavaScript obdrží `null`.</span><span class="sxs-lookup"><span data-stu-id="def05-171">If you try to pass an unpopulated `ElementRef` to JavaScript code, the JavaScript code receives `null`.</span></span> <span data-ttu-id="def05-172">K manipulaci s odkazy na prvky po vykreslení (Chcete-li nastavit počáteční fokus na prvek) použijte komponentu `OnAfterRenderAsync` nebo `OnAfterRender` [součástí životního cyklu metody](xref:razor-components/components#lifecycle-methods).</span><span class="sxs-lookup"><span data-stu-id="def05-172">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:razor-components/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="def05-173">Vyvolání metod rozhraní .NET z funkce jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="def05-173">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="def05-174">Volání statické metody rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="def05-174">Static .NET method call</span></span>

<span data-ttu-id="def05-175">Chcete-li volání statické metody rozhraní .NET z jazyka JavaScript, použijte `DotNet.invokeMethod` nebo `DotNet.invokeMethodAsync` funkce.</span><span class="sxs-lookup"><span data-stu-id="def05-175">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="def05-176">Předat identifikátor statická metoda, kterou chcete volat, název sestavení obsahujícího funkce a žádné argumenty.</span><span class="sxs-lookup"><span data-stu-id="def05-176">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="def05-177">Asynchronní verze se upřednostňuje pro zajištění podpory scénářů na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="def05-177">The asynchronous version is preferred to support server-side scenarios.</span></span> <span data-ttu-id="def05-178">Lze vyvolat z jazyka JavaScript, .NET metoda musí být veřejné, statické a upravený s `[JSInvokable]`.</span><span class="sxs-lookup"><span data-stu-id="def05-178">To be invokable from JavaScript, the .NET method must be public, static, and decorated with `[JSInvokable]`.</span></span> <span data-ttu-id="def05-179">Ve výchozím nastavení, identifikátor metody je název metody, ale můžete zadat jiný identifikátor pomocí `JSInvokableAttribute` konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="def05-179">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="def05-180">Volání obecné metody otevřít se momentálně nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="def05-180">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="def05-181">Obsahuje ukázkovou aplikaci C# metoda vrátí pole `int`s.</span><span class="sxs-lookup"><span data-stu-id="def05-181">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="def05-182">Metoda je doplněn `JSInvokable` atribut.</span><span class="sxs-lookup"><span data-stu-id="def05-182">The method is decorated with the `JSInvokable` attribute.</span></span>

<span data-ttu-id="def05-183">*Pages/JsInterop.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="def05-183">*Pages/JsInterop.cshtml*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?start=48&end=59&highlight=7-11)]

<span data-ttu-id="def05-184">Obsluhuje klientovi JavaScript vyvolá C# metoda .NET.</span><span class="sxs-lookup"><span data-stu-id="def05-184">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="def05-185">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="def05-185">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-12)]

<span data-ttu-id="def05-186">Když **aktivační událost .NET statickou metodu ReturnArrayAsync** vybere tlačítko, prohlédněte si výstup konzoly v prohlížeči webové nástroje pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="def05-186">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="def05-187">Výstup konzoly je:</span><span class="sxs-lookup"><span data-stu-id="def05-187">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="def05-188">Čtvrtá hodnota pole je vloženo do pole (`data.push(4);`) vrácený `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="def05-188">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="def05-189">Volání metody instance</span><span class="sxs-lookup"><span data-stu-id="def05-189">Instance method call</span></span>

<span data-ttu-id="def05-190">Můžete také volat instanci metody rozhraní .NET z jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="def05-190">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="def05-191">Vyvolání metody instance .NET z jazyka JavaScript, nejprve projít .NET instance do jazyka Javasript obalením v `DotNetObjectRef` instance.</span><span class="sxs-lookup"><span data-stu-id="def05-191">To invoke a .NET instance method from JavaScript, first pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectRef` instance.</span></span> <span data-ttu-id="def05-192">.NET instance je předána odkazem pro jazyk JavaScript a můžete vyvolávat metody instance .NET na použití instance `invokeMethod` nebo `invokeMethodAsync` funkce.</span><span class="sxs-lookup"><span data-stu-id="def05-192">The .NET instance is passed by reference to JavaScript, and you can invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="def05-193">.NET instance můžete také předán jako argument při volání metod rozhraní .NET z jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="def05-193">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="def05-194">Ukázková aplikace zprávy protokolu ke konzole na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="def05-194">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="def05-195">Následující příklady jsme vám ukázali bude ukázková aplikace prohlédněte výstup konzoly prohlížeče v prohlížeči vývojářské nástroje.</span><span class="sxs-lookup"><span data-stu-id="def05-195">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="def05-196">Když **metodu instance aktivační událost .NET HelloHelper.SayHello** výběru tlačítka `ExampleJsInterop.CallHelloHelperSayHello` nazývá a předává název, `Blazor`, metody.</span><span class="sxs-lookup"><span data-stu-id="def05-196">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="def05-197">*Pages/JsInterop.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="def05-197">*Pages/JsInterop.cshtml*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?start=61&end=71&highlight=8-9)]

<span data-ttu-id="def05-198">`CallHelloHelperSayHello` funkce jazyka JavaScript vyvolá `sayHello` s novou instanci třídy `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="def05-198">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="def05-199">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="def05-199">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=26-32)]

<span data-ttu-id="def05-200">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="def05-200">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-17)]

<span data-ttu-id="def05-201">Název je předán `HelloHelper`pro konstruktor, který nastaví `HelloHelper.Name` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="def05-201">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="def05-202">Když funkce JavaScript, která `sayHello` provádí, `HelloHelper.SayHello` vrátí `Hello, {Name}!` zprávy, která zapisuje do konzoly pro funkce jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="def05-202">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="def05-203">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="def05-203">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="def05-204">Výstup v nástrojích pro vývojáře v prohlížeči na webové konzoly:</span><span class="sxs-lookup"><span data-stu-id="def05-204">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-razor-component-class-library"></a><span data-ttu-id="def05-205">Sdílení knihovny tříd Razor komponenty interoperační kód.</span><span class="sxs-lookup"><span data-stu-id="def05-205">Share interop code in a Razor Component class library</span></span>

<span data-ttu-id="def05-206">Interoperační kód jazyka JavaScript, mohou být součástí knihovny tříd Razor součásti (`dotnet new razorclasslib`), která umožňuje sdílet kód v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="def05-206">JavaScript interop code can be included in a Razor Component class library (`dotnet new razorclasslib`), which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="def05-207">Knihovny tříd Razor komponenta zpracovává vkládání prostředky jazyka JavaScript v sestavení.</span><span class="sxs-lookup"><span data-stu-id="def05-207">The Razor Component class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="def05-208">Soubory jazyka JavaScript jsou umístěny v *wwwroot* složky.</span><span class="sxs-lookup"><span data-stu-id="def05-208">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="def05-209">Nástroje postará vkládání prostředků při vytváření knihovny.</span><span class="sxs-lookup"><span data-stu-id="def05-209">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="def05-210">Sestavené balíček NuGet je popsána v souboru projektu aplikace, stejně jako jakýkoli normální balíček NuGet se odkazuje.</span><span class="sxs-lookup"><span data-stu-id="def05-210">The built NuGet package is referenced in the project file of the app just as any normal NuGet package is referenced.</span></span> <span data-ttu-id="def05-211">Po obnovení aplikace kód aplikace může volat do jazyka JavaScript, jako by šlo C#.</span><span class="sxs-lookup"><span data-stu-id="def05-211">After the app is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="def05-212">Další informace naleznete v tématu <xref:razor-components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="def05-212">For more information, see <xref:razor-components/class-libraries>.</span></span>
