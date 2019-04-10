---
title: Razor JavaScript součásti zprostředkovatel komunikace s objekty
author: guardrex
description: Zjistěte, jak volat funkce jazyka JavaScript od .NET a .NET metody z jazyka JavaScript v aplikacích Blazor a součásti syntaxe Razor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: razor-components/javascript-interop
ms.openlocfilehash: f2588f4ed1ec2f01218283625fae4632d0a8ae58
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468673"
---
# <a name="razor-components-javascript-interop"></a><span data-ttu-id="59670-103">Razor JavaScript součásti zprostředkovatel komunikace s objekty</span><span class="sxs-lookup"><span data-stu-id="59670-103">Razor Components JavaScript interop</span></span>

<span data-ttu-id="59670-104">Podle [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="59670-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="59670-105">Razor součásti aplikace můžete volat funkce jazyka JavaScript od .NET a .NET metody z kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="59670-105">A Razor Components app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="59670-106">Vyvolání funkce jazyka JavaScript z metod rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="59670-106">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="59670-107">Existují situace, kdy je potřeba volat funkce jazyka JavaScript kód .NET.</span><span class="sxs-lookup"><span data-stu-id="59670-107">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="59670-108">Například volání JavaScriptu můžete zveřejnit možnosti prohlížeče nebo funkce z knihovny jazyka JavaScript do aplikace.</span><span class="sxs-lookup"><span data-stu-id="59670-108">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="59670-109">Chcete-li volat JavaScript z .NET, použijte `IJSRuntime` abstrakce.</span><span class="sxs-lookup"><span data-stu-id="59670-109">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="59670-110">`InvokeAsync<T>` Metoda přebírá identifikátor funkce JavaScriptu, která chcete vyvolat spolu s libovolný počet argumentů serializovat na JSON.</span><span class="sxs-lookup"><span data-stu-id="59670-110">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="59670-111">Identifikátor funkce je relativní vzhledem ke globální obor (`window`).</span><span class="sxs-lookup"><span data-stu-id="59670-111">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="59670-112">Pokud chcete volat `window.someScope.someFunction`, je identifikátor `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="59670-112">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="59670-113">Není nutné zaregistrovat funkci předtím, než je volána.</span><span class="sxs-lookup"><span data-stu-id="59670-113">There's no need to register the function before it's called.</span></span> <span data-ttu-id="59670-114">Návratový typ `T` musí také být JSON serializovatelný.</span><span class="sxs-lookup"><span data-stu-id="59670-114">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="59670-115">Pro aplikace ASP.NET Core Razor komponenty na straně serveru:</span><span class="sxs-lookup"><span data-stu-id="59670-115">For server-side ASP.NET Core Razor Components apps:</span></span>

* <span data-ttu-id="59670-116">Aplikace na straně serveru zpracovává více požadavků uživatele.</span><span class="sxs-lookup"><span data-stu-id="59670-116">Multiple user requests are processed by the server-side app.</span></span> <span data-ttu-id="59670-117">Nevolejte `JSRuntime.Current` v součásti pro vyvolání funkce jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="59670-117">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="59670-118">Vložit `IJSRuntime` abstrakce a použití vloženého objektu vydat spolupráce volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="59670-118">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>

<span data-ttu-id="59670-119">Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), experimentální dekodér založené na jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="59670-119">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="59670-120">Příklad ukazuje, jak vyvolat funkci z jazyka JavaScript C# metody.</span><span class="sxs-lookup"><span data-stu-id="59670-120">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="59670-121">Funkce JavaScript, která přijímá pole bajtů z C# metody dekóduje pole a vrátí text na komponentu pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="59670-121">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="59670-122">Uvnitř `<head>` prvek *wwwroot/index.html*, poskytují funkce, která používá `TextDecoder` k dekódování předané pole:</span><span class="sxs-lookup"><span data-stu-id="59670-122">Inside the `<head>` element of *wwwroot/index.html*, provide a function that uses `TextDecoder` to decode a passed array:</span></span>

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

<span data-ttu-id="59670-123">Kód jazyka JavaScript, jako je například kódu zobrazeného v předchozím příkladu je také možné načíst ze souboru jazyka JavaScript (*js*) s odkazem na soubor skriptu *wwwroot/index.html* souboru:</span><span class="sxs-lookup"><span data-stu-id="59670-123">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file in the *wwwroot/index.html* file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="59670-124">Následující komponenty:</span><span class="sxs-lookup"><span data-stu-id="59670-124">The following component:</span></span>

* <span data-ttu-id="59670-125">Vyvolá `ConvertArray` funkcí jazyka JavaScript s použitím `JsRuntime` při tlačítko součásti (**převést pole**) je vybraná.</span><span class="sxs-lookup"><span data-stu-id="59670-125">Invokes the `ConvertArray` JavaScript function using `JsRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="59670-126">Po zavolání funkce JavaScript, která se převede předané pole na řetězec.</span><span class="sxs-lookup"><span data-stu-id="59670-126">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="59670-127">Řetězec se vrátí do komponenty pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="59670-127">The string is returned to the component for display.</span></span>

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

<span data-ttu-id="59670-128">Použít `IJSRuntime` abstrakce, použijte některý z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="59670-128">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="59670-129">Vložit `IJSRuntime` abstrakce do souboru Razor (*.razor*, *.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="59670-129">Inject the `IJSRuntime` abstraction into the Razor file (*.razor*, *.cshtml*):</span></span>

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

* <span data-ttu-id="59670-130">Vložit `IJSRuntime` abstrakce do třídy (*.cs*):</span><span class="sxs-lookup"><span data-stu-id="59670-130">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

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

* <span data-ttu-id="59670-131">Pro dynamické generování obsahu s `BuildRenderTree`, použijte `[Inject]` atribut:</span><span class="sxs-lookup"><span data-stu-id="59670-131">For dynamic content generation with `BuildRenderTree`, use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject] IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="59670-132">V aplikaci ukázka na straně klienta, který doprovází v tomto tématu jsou k dispozici aplikaci na straně klienta, která pracovat s modelu DOM na vstup uživatele a zobrazení uvítací zprávy dvě funkce jazyka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="59670-132">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the client-side app that interact with the DOM to receive user input and display a welcome message:</span></span>

* `showPrompt` <span data-ttu-id="59670-133">&ndash; Zobrazí výzvu k zadání tak, aby přijímal vstup uživatele (uživatelské jméno) a vrátí řízení volajícímu název.</span><span class="sxs-lookup"><span data-stu-id="59670-133">&ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* `displayWelcome` <span data-ttu-id="59670-134">&ndash; Přiřadí zobrazení uvítací zprávy z volající objekt modelu DOM se `id` z `welcome`.</span><span class="sxs-lookup"><span data-stu-id="59670-134">&ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="59670-135">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="59670-135">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="59670-136">Místo `<script>` značka, která odkazuje na soubor jazyka JavaScript v *wwwroot/index.html* souboru:</span><span class="sxs-lookup"><span data-stu-id="59670-136">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file:</span></span>

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="59670-137">Neukládejte `<script>` značky v souboru součásti, protože `<script>` značku nejde dynamicky aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="59670-137">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="59670-138">Funkce .NET metody spolupráce pomocí jazyka JavaScript v *exampleJsInterop.js* souboru voláním `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="59670-138">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="59670-139">`IJSRuntime` Abstrakcí je asynchronní umožní použít scénáře na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="59670-139">The `IJSRuntime` abstraction is asynchronous to allow for server-side scenarios.</span></span> <span data-ttu-id="59670-140">Pokud aplikace běží na straně klienta a vy chcete volat funkce jazyka JavaScript synchronně, přetypovat dolů na `IJSInProcessRuntime` a volat `Invoke<T>` místo.</span><span class="sxs-lookup"><span data-stu-id="59670-140">If the app runs client-side and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="59670-141">Doporučujeme většinu spolupráce knihoven jazyka JavaScript použít asynchronní rozhraní API k zajištění, že tyto knihovny jsou k dispozici ve všech scénářích, na straně klienta nebo na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="59670-141">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios, client-side or server-side.</span></span>

<span data-ttu-id="59670-142">Ukázková aplikace obsahuje komponentu k předvedení zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="59670-142">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="59670-143">Komponenty:</span><span class="sxs-lookup"><span data-stu-id="59670-143">The component:</span></span>

* <span data-ttu-id="59670-144">Přijímá vstupu uživatele prostřednictvím řádku jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="59670-144">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="59670-145">Vrátí text na komponentu pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="59670-145">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="59670-146">Volá druhé funkce JavaScriptu, která komunikuje s DOM pro zobrazení uvítací zprávy.</span><span class="sxs-lookup"><span data-stu-id="59670-146">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="59670-147">*Pages/JSInterop.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="59670-147">*Pages/JSInterop.cshtml*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="59670-148">Při `TriggerJsPrompt` provádí výběrem komponenty **aktivační událost jazyka JavaScript výzvy** tlačítko, JavaScript `showPrompt` funkce součástí *wwwroot/exampleJsInterop.js* soubor volá se.</span><span class="sxs-lookup"><span data-stu-id="59670-148">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="59670-149">`showPrompt` Funkce přijímá vstup uživatele (uživatelské jméno), což je kódovaný jazykem HTML a vrácené na komponentu.</span><span class="sxs-lookup"><span data-stu-id="59670-149">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="59670-150">Součást uloží uživatelské jméno v místní proměnné, `name`.</span><span class="sxs-lookup"><span data-stu-id="59670-150">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="59670-151">Je řetězec uložen ve `name` je zahrnut do zobrazení uvítací zprávy, která se předá do funkce JavaScriptu, `displayWelcome`, který vykreslí zobrazení uvítací zprávy do záhlaví značky.</span><span class="sxs-lookup"><span data-stu-id="59670-151">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="capture-references-to-elements"></a><span data-ttu-id="59670-152">Zachycení odkazy na elementy</span><span class="sxs-lookup"><span data-stu-id="59670-152">Capture references to elements</span></span>

<span data-ttu-id="59670-153">Některé [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:razor-components/javascript-interop) scénáře vyžadují odkazy na prvky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="59670-153">Some [JavaScript interop](xref:razor-components/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="59670-154">Například knihovna uživatelského rozhraní může vyžadovat odkaz na prvek pro inicializaci, nebo můžete potřebovat pro volání rozhraní API jako příkaz pro element, jako například `focus` nebo `play`.</span><span class="sxs-lookup"><span data-stu-id="59670-154">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="59670-155">Odkazy na elementy HTML v komponentě můžete zachytit tak, že přidáte `ref` atribut na prvek jazyka HTML a pak definování pole typu `ElementRef` jejichž název odpovídá hodnotě `ref` atribut.</span><span class="sxs-lookup"><span data-stu-id="59670-155">You can capture references to HTML elements in a component by adding a `ref` attribute to the HTML element and then defining a field of type `ElementRef` whose name matches the value of the `ref` attribute.</span></span>

<span data-ttu-id="59670-156">Následující příklad ukazuje, zachycení odkazu na element input uživatelské jméno:</span><span class="sxs-lookup"><span data-stu-id="59670-156">The following example shows capturing a reference to the username input element:</span></span>

```cshtml
<input ref="username" ...>

@functions {
    ElementRef username;
}
```

> [!NOTE]
> <span data-ttu-id="59670-157">Proveďte **není** používat odkazy zachycené element jako způsob vyplnění modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="59670-157">Do **not** use captured element references as a way of populating the DOM.</span></span> <span data-ttu-id="59670-158">To může být v rozporu s modelem deklarativní vykreslovací.</span><span class="sxs-lookup"><span data-stu-id="59670-158">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="59670-159">Co se týče kódu .NET, `ElementRef` je neprůhledný popisovač.</span><span class="sxs-lookup"><span data-stu-id="59670-159">As far as .NET code is concerned, an `ElementRef` is an opaque handle.</span></span> <span data-ttu-id="59670-160">*Pouze* věc, kterou vám pomůžou s `ElementRef` je komunikace přes kódu jazyka JavaScript pomocí zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="59670-160">The *only* thing you can do with `ElementRef` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="59670-161">Pokud tak učiníte, obdrží kód JavaScript na straně `HTMLElement` instance, které můžete použít s normální modelu DOM rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="59670-161">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="59670-162">Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na element:</span><span class="sxs-lookup"><span data-stu-id="59670-162">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="59670-163">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="59670-163">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="59670-164">Použití `IJSRuntime.InvokeAsync<T>` a volat `exampleJsFunctions.focusElement` s `ElementRef` zaměřit element:</span><span class="sxs-lookup"><span data-stu-id="59670-164">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementRef` to focus an element:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.cshtml?highlight=1,3,7,11-12)]

<span data-ttu-id="59670-165">Pokud chcete použít rozšiřující metodu zaměřit elementu, vytvořte rozšíření statická metoda, která přijímá `IJSRuntime` instance:</span><span class="sxs-lookup"><span data-stu-id="59670-165">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementRef elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="59670-166">Metoda je volána přímo v objektu.</span><span class="sxs-lookup"><span data-stu-id="59670-166">The method is called directly on the object.</span></span> <span data-ttu-id="59670-167">Následující příklad předpokládá, že statické `Focus` metoda je k dispozici `JsInteropClasses` obor názvů:</span><span class="sxs-lookup"><span data-stu-id="59670-167">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.cshtml?highlight=1,4,8,12)]

> [!IMPORTANT]
> <span data-ttu-id="59670-168">`username` Proměnná je vyplněný pouze po komponentu vykreslí a zahrnuje její výstup `>` elementu.</span><span class="sxs-lookup"><span data-stu-id="59670-168">The `username` variable is only populated after the component renders and its output includes the `>` element.</span></span> <span data-ttu-id="59670-169">Pokud se pokusíte předat unpopulated `ElementRef` do kódu jazyka JavaScript, kód jazyka JavaScript obdrží `null`.</span><span class="sxs-lookup"><span data-stu-id="59670-169">If you try to pass an unpopulated `ElementRef` to JavaScript code, the JavaScript code receives `null`.</span></span> <span data-ttu-id="59670-170">K manipulaci s odkazy na prvky po vykreslení (Chcete-li nastavit počáteční fokus na prvek) použijte komponentu `OnAfterRenderAsync` nebo `OnAfterRender` [součástí životního cyklu metody](xref:razor-components/components#lifecycle-methods).</span><span class="sxs-lookup"><span data-stu-id="59670-170">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:razor-components/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="59670-171">Vyvolání metod rozhraní .NET z funkce jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="59670-171">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="59670-172">Volání statické metody rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="59670-172">Static .NET method call</span></span>

<span data-ttu-id="59670-173">Chcete-li volání statické metody rozhraní .NET z jazyka JavaScript, použijte `DotNet.invokeMethod` nebo `DotNet.invokeMethodAsync` funkce.</span><span class="sxs-lookup"><span data-stu-id="59670-173">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="59670-174">Předat identifikátor statická metoda, kterou chcete volat, název sestavení obsahujícího funkce a žádné argumenty.</span><span class="sxs-lookup"><span data-stu-id="59670-174">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="59670-175">Asynchronní verze se upřednostňuje pro zajištění podpory scénářů na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="59670-175">The asynchronous version is preferred to support server-side scenarios.</span></span> <span data-ttu-id="59670-176">Lze vyvolat z jazyka JavaScript, .NET metoda musí být veřejné, statické a upravený s `[JSInvokable]`.</span><span class="sxs-lookup"><span data-stu-id="59670-176">To be invokable from JavaScript, the .NET method must be public, static, and decorated with `[JSInvokable]`.</span></span> <span data-ttu-id="59670-177">Ve výchozím nastavení, identifikátor metody je název metody, ale můžete zadat jiný identifikátor pomocí `JSInvokableAttribute` konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="59670-177">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="59670-178">Volání obecné metody otevřít se momentálně nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="59670-178">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="59670-179">Obsahuje ukázkovou aplikaci C# metoda vrátí pole `int`s.</span><span class="sxs-lookup"><span data-stu-id="59670-179">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="59670-180">Metoda je doplněn `JSInvokable` atribut.</span><span class="sxs-lookup"><span data-stu-id="59670-180">The method is decorated with the `JSInvokable` attribute.</span></span>

<span data-ttu-id="59670-181">*Pages/JsInterop.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="59670-181">*Pages/JsInterop.cshtml*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="59670-182">Obsluhuje klientovi JavaScript vyvolá C# metoda .NET.</span><span class="sxs-lookup"><span data-stu-id="59670-182">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="59670-183">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="59670-183">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="59670-184">Když **aktivační událost .NET statickou metodu ReturnArrayAsync** vybere tlačítko, prohlédněte si výstup konzoly v prohlížeči webové nástroje pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="59670-184">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="59670-185">Výstup konzoly je:</span><span class="sxs-lookup"><span data-stu-id="59670-185">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="59670-186">Čtvrtá hodnota pole je vloženo do pole (`data.push(4);`) vrácený `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="59670-186">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="59670-187">Volání metody instance</span><span class="sxs-lookup"><span data-stu-id="59670-187">Instance method call</span></span>

<span data-ttu-id="59670-188">Můžete také volat instanci metody rozhraní .NET z jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="59670-188">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="59670-189">Vyvolání metody instance .NET z jazyka JavaScript, nejprve projít .NET instance do jazyka Javasript obalením v `DotNetObjectRef` instance.</span><span class="sxs-lookup"><span data-stu-id="59670-189">To invoke a .NET instance method from JavaScript, first pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectRef` instance.</span></span> <span data-ttu-id="59670-190">.NET instance je předána odkazem pro jazyk JavaScript a můžete vyvolávat metody instance .NET na použití instance `invokeMethod` nebo `invokeMethodAsync` funkce.</span><span class="sxs-lookup"><span data-stu-id="59670-190">The .NET instance is passed by reference to JavaScript, and you can invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="59670-191">.NET instance můžete také předán jako argument při volání metod rozhraní .NET z jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="59670-191">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="59670-192">Ukázková aplikace zprávy protokolu ke konzole na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="59670-192">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="59670-193">Následující příklady jsme vám ukázali bude ukázková aplikace prohlédněte výstup konzoly prohlížeče v prohlížeči vývojářské nástroje.</span><span class="sxs-lookup"><span data-stu-id="59670-193">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="59670-194">Když **metodu instance aktivační událost .NET HelloHelper.SayHello** výběru tlačítka `ExampleJsInterop.CallHelloHelperSayHello` nazývá a předává název, `Blazor`, metody.</span><span class="sxs-lookup"><span data-stu-id="59670-194">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="59670-195">*Pages/JsInterop.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="59670-195">*Pages/JsInterop.cshtml*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.cshtml?name=snippet_JSInterop3&highlight=8-9)]

`CallHelloHelperSayHello` <span data-ttu-id="59670-196">funkce jazyka JavaScript vyvolá `sayHello` s novou instanci třídy `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="59670-196">invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="59670-197">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="59670-197">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="59670-198">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="59670-198">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="59670-199">Název je předán `HelloHelper`pro konstruktor, který nastaví `HelloHelper.Name` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="59670-199">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="59670-200">Když funkce JavaScript, která `sayHello` provádí, `HelloHelper.SayHello` vrátí `Hello, {Name}!` zprávy, která zapisuje do konzoly pro funkce jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="59670-200">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="59670-201">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="59670-201">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="59670-202">Výstup v nástrojích pro vývojáře v prohlížeči na webové konzoly:</span><span class="sxs-lookup"><span data-stu-id="59670-202">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-razor-component-class-library"></a><span data-ttu-id="59670-203">Sdílení knihovny tříd Razor komponenty interoperační kód.</span><span class="sxs-lookup"><span data-stu-id="59670-203">Share interop code in a Razor Component class library</span></span>

<span data-ttu-id="59670-204">Interoperační kód jazyka JavaScript, mohou být součástí knihovny tříd Razor součásti (`dotnet new razorclasslib`), která umožňuje sdílet kód v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="59670-204">JavaScript interop code can be included in a Razor Component class library (`dotnet new razorclasslib`), which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="59670-205">Knihovny tříd Razor komponenta zpracovává vkládání prostředky jazyka JavaScript v sestavení.</span><span class="sxs-lookup"><span data-stu-id="59670-205">The Razor Component class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="59670-206">Soubory jazyka JavaScript jsou umístěny v *wwwroot* složky.</span><span class="sxs-lookup"><span data-stu-id="59670-206">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="59670-207">Nástroje postará vkládání prostředků při vytváření knihovny.</span><span class="sxs-lookup"><span data-stu-id="59670-207">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="59670-208">Sestavené balíček NuGet je popsána v souboru projektu aplikace, stejně jako jakýkoli normální balíček NuGet se odkazuje.</span><span class="sxs-lookup"><span data-stu-id="59670-208">The built NuGet package is referenced in the project file of the app just as any normal NuGet package is referenced.</span></span> <span data-ttu-id="59670-209">Po obnovení aplikace kód aplikace může volat do jazyka JavaScript, jako by šlo C#.</span><span class="sxs-lookup"><span data-stu-id="59670-209">After the app is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="59670-210">Další informace naleznete v tématu <xref:razor-components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="59670-210">For more information, see <xref:razor-components/class-libraries>.</span></span>
