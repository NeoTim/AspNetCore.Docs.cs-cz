---
title: Zprostředkovatel komunikace s objekty jazyka Blazor JavaScript
author: guardrex
description: Zjistěte, jak volat funkce jazyka JavaScript od .NET a .NET metody z jazyka JavaScript v aplikacích Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2019
uid: blazor/javascript-interop
ms.openlocfilehash: bed1e3d33de5e8fb2d246b066803cdc95d6731ef
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982666"
---
# <a name="blazor-javascript-interop"></a><span data-ttu-id="3a3e3-103">Zprostředkovatel komunikace s objekty jazyka Blazor JavaScript</span><span class="sxs-lookup"><span data-stu-id="3a3e3-103">Blazor JavaScript interop</span></span>

<span data-ttu-id="3a3e3-104">Podle [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3a3e3-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="3a3e3-105">Blazor aplikace můžete volat funkce jazyka JavaScript od .NET a .NET metody z kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="3a3e3-106">Vyvolání funkce jazyka JavaScript z metod rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="3a3e3-106">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="3a3e3-107">Existují situace, kdy je potřeba volat funkce jazyka JavaScript kód .NET.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-107">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="3a3e3-108">Například volání JavaScriptu můžete zveřejnit možnosti prohlížeče nebo funkce z knihovny jazyka JavaScript do aplikace.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-108">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="3a3e3-109">Chcete-li volat JavaScript z .NET, použijte `IJSRuntime` abstrakce.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-109">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="3a3e3-110">`InvokeAsync<T>` Metoda přebírá identifikátor funkce JavaScriptu, která chcete vyvolat spolu s libovolný počet argumentů serializovat na JSON.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-110">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="3a3e3-111">Identifikátor funkce je relativní vzhledem ke globální obor (`window`).</span><span class="sxs-lookup"><span data-stu-id="3a3e3-111">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="3a3e3-112">Pokud chcete volat `window.someScope.someFunction`, je identifikátor `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-112">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="3a3e3-113">Není nutné zaregistrovat funkci předtím, než je volána.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-113">There's no need to register the function before it's called.</span></span> <span data-ttu-id="3a3e3-114">Návratový typ `T` musí také být JSON serializovatelný.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-114">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="3a3e3-115">Pro serverové aplikace:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-115">For server-side apps:</span></span>

* <span data-ttu-id="3a3e3-116">Aplikace na straně serveru zpracovává více požadavků uživatele.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-116">Multiple user requests are processed by the server-side app.</span></span> <span data-ttu-id="3a3e3-117">Nevolejte `JSRuntime.Current` v součásti pro vyvolání funkce jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-117">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="3a3e3-118">Vložit `IJSRuntime` abstrakce a použití vloženého objektu vydat spolupráce volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-118">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>
* <span data-ttu-id="3a3e3-119">Zatímco Blazor aplikace je před vykreslením, volání do jazyka JavaScript není možné, protože nebyla navázat připojení s prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-119">While a Blazor app is prerendering, calling into JavaScript isn't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="3a3e3-120">Další informace najdete v tématu [rozpoznat, kdy aplikace Blazor se před vykreslením](#detect-when-a-blazor-app-is-prerendering) oddílu.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-120">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="3a3e3-121">Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), experimentální dekodér založené na jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-121">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="3a3e3-122">Příklad ukazuje, jak vyvolat funkci z jazyka JavaScript C# metody.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-122">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="3a3e3-123">Funkce JavaScript, která přijímá pole bajtů z C# metody dekóduje pole a vrátí text na komponentu pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="3a3e3-124">Uvnitř `<head>` prvek *wwwroot/index.html*, poskytují funkce, která používá `TextDecoder` k dekódování předané pole:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-124">Inside the `<head>` element of *wwwroot/index.html*, provide a function that uses `TextDecoder` to decode a passed array:</span></span>

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

<span data-ttu-id="3a3e3-125">Kód jazyka JavaScript, jako je například kódu zobrazeného v předchozím příkladu je také možné načíst ze souboru jazyka JavaScript (*js*) s odkazem na soubor skriptu *wwwroot/index.html* souboru:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file in the *wwwroot/index.html* file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="3a3e3-126">Následující komponenty:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-126">The following component:</span></span>

* <span data-ttu-id="3a3e3-127">Vyvolá `ConvertArray` funkcí jazyka JavaScript s použitím `JsRuntime` při tlačítko součásti (**převést pole**) je vybraná.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-127">Invokes the `ConvertArray` JavaScript function using `JsRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="3a3e3-128">Po zavolání funkce JavaScript, která se převede předané pole na řetězec.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="3a3e3-129">Řetězec se vrátí do komponenty pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-129">The string is returned to the component for display.</span></span>

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

    private async void ConvertArray()
    {
        var text =
            await JsRuntime.InvokeAsync<string>("ConvertArray", QuoteArray);

        ConvertedText = new MarkupString(text);

        StateHasChanged();
    }
}
```

<span data-ttu-id="3a3e3-130">Použít `IJSRuntime` abstrakce, použijte některý z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-130">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="3a3e3-131">Vložit `IJSRuntime` abstrakce do souboru Razor (*.razor*):</span><span class="sxs-lookup"><span data-stu-id="3a3e3-131">Inject the `IJSRuntime` abstraction into the Razor file (*.razor*):</span></span>

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

* <span data-ttu-id="3a3e3-132">Vložit `IJSRuntime` abstrakce do třídy (*.cs*):</span><span class="sxs-lookup"><span data-stu-id="3a3e3-132">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  ```csharp
  public class JsInteropClasses
  {
      private readonly IJSRuntime _jsRuntime;

      public JsInteropClasses(IJSRuntime jsRuntime)
      {
          _jsRuntime = jsRuntime;
      }

      public Task<string> TickerChanged(string data)
      {
          // The handleTickerChanged JavaScript method is implemented
          // in a JavaScript file, such as 'wwwroot/tickerJsInterop.js'.
          return _jsRuntime.InvokeAsync<object>(
              "handleTickerChanged",
              stockUpdate.symbol,
              stockUpdate.price);
      }
  }
  ```

* <span data-ttu-id="3a3e3-133">Pro dynamické generování obsahu s `BuildRenderTree`, použijte `[Inject]` atribut:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-133">For dynamic content generation with `BuildRenderTree`, use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject] IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="3a3e3-134">V aplikaci ukázka na straně klienta, který doprovází v tomto tématu jsou k dispozici aplikaci na straně klienta, která pracovat s modelu DOM na vstup uživatele a zobrazení uvítací zprávy dvě funkce jazyka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-134">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the client-side app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="3a3e3-135">`showPrompt` &ndash; Zobrazí výzvu k zadání tak, aby přijímal vstup uživatele (uživatelské jméno) a vrátí řízení volajícímu název.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-135">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="3a3e3-136">`displayWelcome` &ndash; Přiřadí zobrazení uvítací zprávy z volající objekt modelu DOM se `id` z `welcome`.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-136">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="3a3e3-137">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-137">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="3a3e3-138">Místo `<script>` značka, která odkazuje na soubor jazyka JavaScript v *wwwroot/index.html* souboru:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-138">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file:</span></span>

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="3a3e3-139">Neukládejte `<script>` značky v souboru součásti, protože `<script>` značku nejde dynamicky aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-139">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="3a3e3-140">Funkce .NET metody spolupráce pomocí jazyka JavaScript v *exampleJsInterop.js* souboru voláním `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-140">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="3a3e3-141">`IJSRuntime` Abstrakcí je asynchronní umožní použít scénáře na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-141">The `IJSRuntime` abstraction is asynchronous to allow for server-side scenarios.</span></span> <span data-ttu-id="3a3e3-142">Pokud aplikace běží na straně klienta a vy chcete volat funkce jazyka JavaScript synchronně, přetypovat dolů na `IJSInProcessRuntime` a volat `Invoke<T>` místo.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-142">If the app runs client-side and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="3a3e3-143">Doporučujeme většinu spolupráce knihoven jazyka JavaScript použít asynchronní rozhraní API k zajištění, že tyto knihovny jsou k dispozici ve všech scénářích, na straně klienta nebo na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-143">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios, client-side or server-side.</span></span>

<span data-ttu-id="3a3e3-144">Ukázková aplikace obsahuje komponentu k předvedení zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-144">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="3a3e3-145">Komponenty:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-145">The component:</span></span>

* <span data-ttu-id="3a3e3-146">Přijímá vstupu uživatele prostřednictvím řádku jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-146">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="3a3e3-147">Vrátí text na komponentu pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-147">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="3a3e3-148">Volá druhé funkce JavaScriptu, která komunikuje s DOM pro zobrazení uvítací zprávy.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-148">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="3a3e3-149">*Pages/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-149">*Pages/JSInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="3a3e3-150">Při `TriggerJsPrompt` provádí výběrem komponenty **aktivační událost jazyka JavaScript výzvy** tlačítko, JavaScript `showPrompt` funkce součástí *wwwroot/exampleJsInterop.js* soubor volá se.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-150">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="3a3e3-151">`showPrompt` Funkce přijímá vstup uživatele (uživatelské jméno), což je kódovaný jazykem HTML a vrácené na komponentu.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-151">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="3a3e3-152">Součást uloží uživatelské jméno v místní proměnné, `name`.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-152">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="3a3e3-153">Je řetězec uložen ve `name` je zahrnut do zobrazení uvítací zprávy, která se předá do funkce JavaScriptu, `displayWelcome`, který vykreslí zobrazení uvítací zprávy do záhlaví značky.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-153">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="detect-when-a-blazor-app-is-prerendering"></a><span data-ttu-id="3a3e3-154">Rozpoznat, kdy aplikace Blazor se před vykreslením</span><span class="sxs-lookup"><span data-stu-id="3a3e3-154">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="3a3e3-155">Zachycení odkazy na elementy</span><span class="sxs-lookup"><span data-stu-id="3a3e3-155">Capture references to elements</span></span>

<span data-ttu-id="3a3e3-156">Některé [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:blazor/javascript-interop) scénáře vyžadují odkazy na prvky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-156">Some [JavaScript interop](xref:blazor/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="3a3e3-157">Například knihovna uživatelského rozhraní může vyžadovat odkaz na prvek pro inicializaci, nebo můžete potřebovat pro volání rozhraní API jako příkaz pro element, jako například `focus` nebo `play`.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-157">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="3a3e3-158">Odkazy na elementy HTML v komponentě můžete zachytit tak, že přidáte `ref` atribut na prvek jazyka HTML a pak definování pole typu `ElementRef` jejichž název odpovídá hodnotě `ref` atribut.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-158">You can capture references to HTML elements in a component by adding a `ref` attribute to the HTML element and then defining a field of type `ElementRef` whose name matches the value of the `ref` attribute.</span></span>

<span data-ttu-id="3a3e3-159">Následující příklad ukazuje, zachycení odkazu na element input uživatelské jméno:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-159">The following example shows capturing a reference to the username input element:</span></span>

```cshtml
<input ref="username" ...>

@functions {
    ElementRef username;
}
```

> [!NOTE]
> <span data-ttu-id="3a3e3-160">Proveďte **není** používat odkazy zachycené element jako způsob vyplnění modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-160">Do **not** use captured element references as a way of populating the DOM.</span></span> <span data-ttu-id="3a3e3-161">To může být v rozporu s modelem deklarativní vykreslovací.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-161">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="3a3e3-162">Co se týče kódu .NET, `ElementRef` je neprůhledný popisovač.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-162">As far as .NET code is concerned, an `ElementRef` is an opaque handle.</span></span> <span data-ttu-id="3a3e3-163">*Pouze* věc, kterou vám pomůžou s `ElementRef` je komunikace přes kódu jazyka JavaScript pomocí zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-163">The *only* thing you can do with `ElementRef` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="3a3e3-164">Pokud tak učiníte, obdrží kód JavaScript na straně `HTMLElement` instance, které můžete použít s normální modelu DOM rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-164">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="3a3e3-165">Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na element:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-165">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="3a3e3-166">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-166">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="3a3e3-167">Použití `IJSRuntime.InvokeAsync<T>` a volat `exampleJsFunctions.focusElement` s `ElementRef` zaměřit element:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-167">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementRef` to focus an element:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,7,11-12)]

<span data-ttu-id="3a3e3-168">Pokud chcete použít rozšiřující metodu zaměřit elementu, vytvořte rozšíření statická metoda, která přijímá `IJSRuntime` instance:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-168">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementRef elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="3a3e3-169">Metoda je volána přímo v objektu.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-169">The method is called directly on the object.</span></span> <span data-ttu-id="3a3e3-170">Následující příklad předpokládá, že statické `Focus` metoda je k dispozici `JsInteropClasses` obor názvů:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-170">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,8,12)]

> [!IMPORTANT]
> <span data-ttu-id="3a3e3-171">`username` Proměnná je vyplněný pouze po komponentu vykreslí a zahrnuje její výstup `>` elementu.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-171">The `username` variable is only populated after the component renders and its output includes the `>` element.</span></span> <span data-ttu-id="3a3e3-172">Pokud se pokusíte předat unpopulated `ElementRef` do kódu jazyka JavaScript, kód jazyka JavaScript obdrží `null`.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-172">If you try to pass an unpopulated `ElementRef` to JavaScript code, the JavaScript code receives `null`.</span></span> <span data-ttu-id="3a3e3-173">K manipulaci s odkazy na prvky po vykreslení (Chcete-li nastavit počáteční fokus na prvek) použijte komponentu `OnAfterRenderAsync` nebo `OnAfterRender` [součástí životního cyklu metody](xref:blazor/components#lifecycle-methods).</span><span class="sxs-lookup"><span data-stu-id="3a3e3-173">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:blazor/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="3a3e3-174">Vyvolání metod rozhraní .NET z funkce jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="3a3e3-174">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="3a3e3-175">Volání statické metody rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="3a3e3-175">Static .NET method call</span></span>

<span data-ttu-id="3a3e3-176">Chcete-li volání statické metody rozhraní .NET z jazyka JavaScript, použijte `DotNet.invokeMethod` nebo `DotNet.invokeMethodAsync` funkce.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-176">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="3a3e3-177">Předat identifikátor statická metoda, kterou chcete volat, název sestavení obsahujícího funkce a žádné argumenty.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-177">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="3a3e3-178">Asynchronní verze se upřednostňuje pro zajištění podpory scénářů na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-178">The asynchronous version is preferred to support server-side scenarios.</span></span> <span data-ttu-id="3a3e3-179">Lze vyvolat z jazyka JavaScript, .NET metoda musí být veřejné, statické a upravený s `[JSInvokable]`.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-179">To be invokable from JavaScript, the .NET method must be public, static, and decorated with `[JSInvokable]`.</span></span> <span data-ttu-id="3a3e3-180">Ve výchozím nastavení, identifikátor metody je název metody, ale můžete zadat jiný identifikátor pomocí `JSInvokableAttribute` konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-180">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="3a3e3-181">Volání obecné metody otevřít se momentálně nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-181">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="3a3e3-182">Obsahuje ukázkovou aplikaci C# metoda vrátí pole `int`s.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-182">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="3a3e3-183">Metoda je doplněn `JSInvokable` atribut.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-183">The method is decorated with the `JSInvokable` attribute.</span></span>

<span data-ttu-id="3a3e3-184">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-184">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="3a3e3-185">Obsluhuje klientovi JavaScript vyvolá C# metoda .NET.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-185">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="3a3e3-186">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-186">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="3a3e3-187">Když **aktivační událost .NET statickou metodu ReturnArrayAsync** vybere tlačítko, prohlédněte si výstup konzoly v prohlížeči webové nástroje pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-187">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="3a3e3-188">Výstup konzoly je:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-188">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="3a3e3-189">Čtvrtá hodnota pole je vloženo do pole (`data.push(4);`) vrácený `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-189">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="3a3e3-190">Volání metody instance</span><span class="sxs-lookup"><span data-stu-id="3a3e3-190">Instance method call</span></span>

<span data-ttu-id="3a3e3-191">Můžete také volat instanci metody rozhraní .NET z jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-191">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="3a3e3-192">Vyvolání metody instance .NET z jazyka JavaScript, nejprve projít .NET instance do jazyka Javasript obalením v `DotNetObjectRef` instance.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-192">To invoke a .NET instance method from JavaScript, first pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectRef` instance.</span></span> <span data-ttu-id="3a3e3-193">.NET instance je předána odkazem pro jazyk JavaScript a můžete vyvolávat metody instance .NET na použití instance `invokeMethod` nebo `invokeMethodAsync` funkce.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-193">The .NET instance is passed by reference to JavaScript, and you can invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="3a3e3-194">.NET instance můžete také předán jako argument při volání metod rozhraní .NET z jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-194">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="3a3e3-195">Ukázková aplikace zprávy protokolu ke konzole na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-195">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="3a3e3-196">Následující příklady jsme vám ukázali bude ukázková aplikace prohlédněte výstup konzoly prohlížeče v prohlížeči vývojářské nástroje.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-196">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="3a3e3-197">Když **metodu instance aktivační událost .NET HelloHelper.SayHello** výběru tlačítka `ExampleJsInterop.CallHelloHelperSayHello` nazývá a předává název, `Blazor`, metody.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-197">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="3a3e3-198">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-198">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

<span data-ttu-id="3a3e3-199">`CallHelloHelperSayHello` funkce jazyka JavaScript vyvolá `sayHello` s novou instanci třídy `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-199">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="3a3e3-200">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-200">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="3a3e3-201">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-201">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="3a3e3-202">Název je předán `HelloHelper`pro konstruktor, který nastaví `HelloHelper.Name` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-202">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="3a3e3-203">Když funkce JavaScript, která `sayHello` provádí, `HelloHelper.SayHello` vrátí `Hello, {Name}!` zprávy, která zapisuje do konzoly pro funkce jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-203">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="3a3e3-204">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-204">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="3a3e3-205">Výstup v nástrojích pro vývojáře v prohlížeči na webové konzoly:</span><span class="sxs-lookup"><span data-stu-id="3a3e3-205">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-blazor-class-library"></a><span data-ttu-id="3a3e3-206">Sdílení knihovny tříd Blazor interoperační kód.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-206">Share interop code in a Blazor class library</span></span>

<span data-ttu-id="3a3e3-207">Interoperační kód jazyka JavaScript, mohou být součástí knihovny tříd Blazor (`dotnet new blazorlib`), která umožňuje sdílet kód v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-207">JavaScript interop code can be included in a Blazor class library (`dotnet new blazorlib`), which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="3a3e3-208">Knihovna tříd Blazor zpracovává vkládání prostředky jazyka JavaScript v sestavení.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-208">The Blazor class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="3a3e3-209">Soubory jazyka JavaScript jsou umístěny v *wwwroot* složky.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-209">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="3a3e3-210">Nástroje postará vkládání prostředků při vytváření knihovny.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-210">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="3a3e3-211">Sestavené balíček NuGet je popsána v souboru projektu aplikace, stejně jako jakýkoli normální balíček NuGet se odkazuje.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-211">The built NuGet package is referenced in the project file of the app just as any normal NuGet package is referenced.</span></span> <span data-ttu-id="3a3e3-212">Po obnovení aplikace kód aplikace může volat do jazyka JavaScript, jako by šlo C#.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-212">After the app is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="3a3e3-213">Další informace naleznete v tématu <xref:blazor/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="3a3e3-213">For more information, see <xref:blazor/class-libraries>.</span></span>
