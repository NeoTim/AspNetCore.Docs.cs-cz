---
title: ASP.NET Core Blazor JavaScript zprostředkovatele komunikace s objekty
author: guardrex
description: Zjistěte, jak volat funkce jazyka JavaScript od .NET a .NET metody z jazyka JavaScript v aplikacích Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: blazor/javascript-interop
ms.openlocfilehash: df511fe77661cd1474e869b28e6858e0452eaae5
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152800"
---
# <a name="aspnet-core-blazor-javascript-interop"></a><span data-ttu-id="1b0ed-103">ASP.NET Core Blazor JavaScript zprostředkovatele komunikace s objekty</span><span class="sxs-lookup"><span data-stu-id="1b0ed-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="1b0ed-104">Podle [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1b0ed-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1b0ed-105">Blazor aplikace můžete volat funkce jazyka JavaScript od .NET a .NET metody z kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="1b0ed-106">Vyvolání funkce jazyka JavaScript z metod rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="1b0ed-106">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="1b0ed-107">Existují situace, kdy je potřeba volat funkce jazyka JavaScript kód .NET.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-107">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="1b0ed-108">Například volání JavaScriptu můžete zveřejnit možnosti prohlížeče nebo funkce z knihovny jazyka JavaScript do aplikace.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-108">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="1b0ed-109">Chcete-li volat JavaScript z .NET, použijte `IJSRuntime` abstrakce.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-109">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="1b0ed-110">`InvokeAsync<T>` Metoda přebírá identifikátor funkce JavaScriptu, která chcete vyvolat spolu s libovolný počet argumentů serializovat na JSON.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-110">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="1b0ed-111">Identifikátor funkce je relativní vzhledem ke globální obor (`window`).</span><span class="sxs-lookup"><span data-stu-id="1b0ed-111">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="1b0ed-112">Pokud chcete volat `window.someScope.someFunction`, je identifikátor `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-112">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="1b0ed-113">Není nutné zaregistrovat funkci předtím, než je volána.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-113">There's no need to register the function before it's called.</span></span> <span data-ttu-id="1b0ed-114">Návratový typ `T` musí také být JSON serializovatelný.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-114">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="1b0ed-115">Pro serverové aplikace:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-115">For server-side apps:</span></span>

* <span data-ttu-id="1b0ed-116">Aplikace na straně serveru zpracovává více požadavků uživatele.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-116">Multiple user requests are processed by the server-side app.</span></span> <span data-ttu-id="1b0ed-117">Nevolejte `JSRuntime.Current` v součásti pro vyvolání funkce jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-117">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="1b0ed-118">Vložit `IJSRuntime` abstrakce a použití vloženého objektu vydat spolupráce volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-118">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>
* <span data-ttu-id="1b0ed-119">Zatímco Blazor aplikace je před vykreslením, volání do jazyka JavaScript není možné, protože nebyla navázat připojení s prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-119">While a Blazor app is prerendering, calling into JavaScript isn't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="1b0ed-120">Další informace najdete v tématu [rozpoznat, kdy aplikace Blazor se před vykreslením](#detect-when-a-blazor-app-is-prerendering) oddílu.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-120">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="1b0ed-121">Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), experimentální dekodér založené na jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-121">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="1b0ed-122">Příklad ukazuje, jak vyvolat funkci z jazyka JavaScript C# metody.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-122">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="1b0ed-123">Funkce JavaScript, která přijímá pole bajtů z C# metody dekóduje pole a vrátí text na komponentu pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="1b0ed-124">Uvnitř `<head>` prvek *wwwroot/index.html* (Blazor straně klienta) nebo *stránek /\_Host.cshtml* (Blazor serverové), poskytují funkce, která používá `TextDecoder` do dekódování předané pole:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-124">Inside the `<head>` element of *wwwroot/index.html* (Blazor client-side) or *Pages/\_Host.cshtml* (Blazor server-side), provide a function that uses `TextDecoder` to decode a passed array:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

<span data-ttu-id="1b0ed-125">Kód jazyka JavaScript, jako je například kódu zobrazeného v předchozím příkladu je také možné načíst ze souboru JavaScriptu (*js*) s odkazem na soubor skriptu:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="1b0ed-126">Následující komponenty:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-126">The following component:</span></span>

* <span data-ttu-id="1b0ed-127">Vyvolá `ConvertArray` funkcí jazyka JavaScript s použitím `JsRuntime` při tlačítko součásti (**převést pole**) je vybraná.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-127">Invokes the `ConvertArray` JavaScript function using `JsRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="1b0ed-128">Po zavolání funkce JavaScript, která se převede předané pole na řetězec.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="1b0ed-129">Řetězec se vrátí do komponenty pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-129">The string is returned to the component for display.</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

<span data-ttu-id="1b0ed-130">Použít `IJSRuntime` abstrakce, použijte některý z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-130">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="1b0ed-131">Vložit `IJSRuntime` abstrakce do komponenty Razor ( *.razor*):</span><span class="sxs-lookup"><span data-stu-id="1b0ed-131">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* <span data-ttu-id="1b0ed-132">Vložit `IJSRuntime` abstrakce do třídy ( *.cs*):</span><span class="sxs-lookup"><span data-stu-id="1b0ed-132">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* <span data-ttu-id="1b0ed-133">Pro dynamické generování obsahu s [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), použijte `[Inject]` atribut:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-133">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="1b0ed-134">V aplikaci ukázka na straně klienta, který doprovází v tomto tématu jsou k dispozici aplikaci na straně klienta, která pracovat s modelu DOM na vstup uživatele a zobrazení uvítací zprávy dvě funkce jazyka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-134">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the client-side app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="1b0ed-135">`showPrompt` &ndash; Zobrazí výzvu k zadání tak, aby přijímal vstup uživatele (uživatelské jméno) a vrátí řízení volajícímu název.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-135">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="1b0ed-136">`displayWelcome` &ndash; Přiřadí zobrazení uvítací zprávy z volající objekt modelu DOM se `id` z `welcome`.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-136">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="1b0ed-137">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-137">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="1b0ed-138">Místo `<script>` značka, která odkazuje na soubor jazyka JavaScript v *wwwroot/index.html* souboru (Blazor straně klienta) nebo *stránek /\_Host.cshtml* (Blazor serverové) soubor.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-138">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor client-side) or *Pages/\_Host.cshtml* file (Blazor server-side).</span></span>

<span data-ttu-id="1b0ed-139">*Wwwroot/index.HTML* (Blazor straně klienta):</span><span class="sxs-lookup"><span data-stu-id="1b0ed-139">*wwwroot/index.html* (Blazor client-side):</span></span>

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="1b0ed-140">*Stránky /\_Host.cshtml* (Blazor serverové):</span><span class="sxs-lookup"><span data-stu-id="1b0ed-140">*Pages/\_Host.cshtml* (Blazor server-side):</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

<span data-ttu-id="1b0ed-141">Neukládejte `<script>` značky v souboru součásti, protože `<script>` značku nejde dynamicky aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-141">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="1b0ed-142">Funkce .NET metody spolupráce pomocí jazyka JavaScript v *exampleJsInterop.js* souboru voláním `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-142">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="1b0ed-143">`IJSRuntime` Abstrakcí je asynchronní umožní použít scénáře na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-143">The `IJSRuntime` abstraction is asynchronous to allow for server-side scenarios.</span></span> <span data-ttu-id="1b0ed-144">Pokud aplikace běží na straně klienta a vy chcete volat funkce jazyka JavaScript synchronně, přetypovat dolů na `IJSInProcessRuntime` a volat `Invoke<T>` místo.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-144">If the app runs client-side and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="1b0ed-145">Doporučujeme většinu spolupráce knihoven jazyka JavaScript použít asynchronní rozhraní API k zajištění, že tyto knihovny jsou k dispozici ve všech scénářích, na straně klienta nebo na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-145">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios, client-side or server-side.</span></span>

<span data-ttu-id="1b0ed-146">Ukázková aplikace obsahuje komponentu k předvedení zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-146">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="1b0ed-147">Komponenty:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-147">The component:</span></span>

* <span data-ttu-id="1b0ed-148">Přijímá vstupu uživatele prostřednictvím řádku jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-148">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="1b0ed-149">Vrátí text na komponentu pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-149">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="1b0ed-150">Volá druhé funkce JavaScriptu, která komunikuje s DOM pro zobrazení uvítací zprávy.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-150">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="1b0ed-151">*Pages/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-151">*Pages/JSInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="1b0ed-152">Při `TriggerJsPrompt` provádí výběrem komponenty **aktivační událost jazyka JavaScript výzvy** tlačítko, JavaScript `showPrompt` funkce součástí *wwwroot/exampleJsInterop.js* soubor volá se.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-152">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="1b0ed-153">`showPrompt` Funkce přijímá vstup uživatele (uživatelské jméno), což je kódovaný jazykem HTML a vrácené na komponentu.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-153">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="1b0ed-154">Součást uloží uživatelské jméno v místní proměnné, `name`.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-154">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="1b0ed-155">Je řetězec uložen ve `name` je zahrnut do zobrazení uvítací zprávy, která se předá do funkce JavaScriptu, `displayWelcome`, který vykreslí zobrazení uvítací zprávy do záhlaví značky.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-155">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="1b0ed-156">Volání funkce void jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="1b0ed-156">Call a void JavaScript function</span></span>

<span data-ttu-id="1b0ed-157">Který vrací funkcí v JavaScriptu [void (0) / void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) nebo [nedefinované](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) jsou volány pomocí `IJSRuntime.InvokeAsync<object>`, která vrací `null`.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-157">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeAsync<object>`, which returns `null`.</span></span>

## <a name="detect-when-a-blazor-app-is-prerendering"></a><span data-ttu-id="1b0ed-158">Rozpoznat, kdy aplikace Blazor se před vykreslením</span><span class="sxs-lookup"><span data-stu-id="1b0ed-158">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="1b0ed-159">Zachycení odkazy na elementy</span><span class="sxs-lookup"><span data-stu-id="1b0ed-159">Capture references to elements</span></span>

<span data-ttu-id="1b0ed-160">Některé [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:blazor/javascript-interop) scénáře vyžadují odkazy na prvky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-160">Some [JavaScript interop](xref:blazor/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="1b0ed-161">Například knihovna uživatelského rozhraní může vyžadovat odkaz na prvek pro inicializaci, nebo můžete potřebovat pro volání rozhraní API jako příkaz pro element, jako například `focus` nebo `play`.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-161">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="1b0ed-162">Můžete zaznamenat odkazy na elementy HTML v komponentě používá následující postup:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-162">You can capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="1b0ed-163">Přidat `@ref` atribut na prvek jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-163">Add a `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="1b0ed-164">Definování pole typu `ElementRef` jejichž název odpovídá hodnotě `@ref` atribut.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-164">Define a field of type `ElementRef` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="1b0ed-165">Následující příklad ukazuje zachycení odkazu na `username` `<input>` element:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-165">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```cshtml
<input @ref="username" ... />

@code {
    ElementRef username;
}
```

> [!NOTE]
> <span data-ttu-id="1b0ed-166">Proveďte **není** používat odkazy zachycené element jako způsob sestavování nebo manipulace s modelu DOM, když Blazor komunikuje s prvky odkazuje.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-166">Do **not** use captured element references as a way of populating or manipulating the DOM when Blazor interacts with the elements referenced.</span></span> <span data-ttu-id="1b0ed-167">To může být v rozporu s modelem deklarativní vykreslovací.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-167">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="1b0ed-168">Co se týče kódu .NET, `ElementRef` je neprůhledný popisovač.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-168">As far as .NET code is concerned, an `ElementRef` is an opaque handle.</span></span> <span data-ttu-id="1b0ed-169">*Pouze* věc, kterou vám pomůžou s `ElementRef` je komunikace přes kódu jazyka JavaScript pomocí zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-169">The *only* thing you can do with `ElementRef` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="1b0ed-170">Pokud tak učiníte, obdrží kód JavaScript na straně `HTMLElement` instance, které můžete použít s normální modelu DOM rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-170">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="1b0ed-171">Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na element:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-171">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="1b0ed-172">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-172">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="1b0ed-173">Použití `IJSRuntime.InvokeAsync<T>` a volat `exampleJsFunctions.focusElement` s `ElementRef` zaměřit element:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-173">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementRef` to focus an element:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,7,11-12)]

<span data-ttu-id="1b0ed-174">Pokud chcete použít rozšiřující metodu zaměřit elementu, vytvořte rozšíření statická metoda, která přijímá `IJSRuntime` instance:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-174">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementRef elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="1b0ed-175">Metoda je volána přímo v objektu.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-175">The method is called directly on the object.</span></span> <span data-ttu-id="1b0ed-176">Následující příklad předpokládá, že statické `Focus` metoda je k dispozici `JsInteropClasses` obor názvů:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-176">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,8,12)]

> [!IMPORTANT]
> <span data-ttu-id="1b0ed-177">`username` Proměnná je vyplněný pouze po komponentu vykreslí a zahrnuje její výstup `>` elementu.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-177">The `username` variable is only populated after the component renders and its output includes the `>` element.</span></span> <span data-ttu-id="1b0ed-178">Pokud se pokusíte předat unpopulated `ElementRef` do kódu jazyka JavaScript, kód jazyka JavaScript obdrží `null`.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-178">If you try to pass an unpopulated `ElementRef` to JavaScript code, the JavaScript code receives `null`.</span></span> <span data-ttu-id="1b0ed-179">K manipulaci s odkazy na prvky po vykreslení (Chcete-li nastavit počáteční fokus na prvek) použijte komponentu `OnAfterRenderAsync` nebo `OnAfterRender` [součástí životního cyklu metody](xref:blazor/components#lifecycle-methods).</span><span class="sxs-lookup"><span data-stu-id="1b0ed-179">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:blazor/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="1b0ed-180">Vyvolání metod rozhraní .NET z funkce jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="1b0ed-180">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="1b0ed-181">Volání statické metody rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="1b0ed-181">Static .NET method call</span></span>

<span data-ttu-id="1b0ed-182">Chcete-li volání statické metody rozhraní .NET z jazyka JavaScript, použijte `DotNet.invokeMethod` nebo `DotNet.invokeMethodAsync` funkce.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-182">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="1b0ed-183">Předat identifikátor statická metoda, kterou chcete volat, název sestavení obsahujícího funkce a žádné argumenty.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-183">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="1b0ed-184">Asynchronní verze se upřednostňuje pro zajištění podpory scénářů na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-184">The asynchronous version is preferred to support server-side scenarios.</span></span> <span data-ttu-id="1b0ed-185">Volání metody rozhraní .NET z jazyka JavaScript, .NET metoda musí být veřejné, statické a mít `[JSInvokable]` atribut.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-185">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="1b0ed-186">Ve výchozím nastavení, identifikátor metody je název metody, ale můžete zadat jiný identifikátor pomocí `JSInvokableAttribute` konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-186">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="1b0ed-187">Volání obecné metody otevřít se momentálně nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-187">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="1b0ed-188">Obsahuje ukázkovou aplikaci C# metoda vrátí pole `int`s.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-188">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="1b0ed-189">`JSInvokable` Atributu se použije pro metodu.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-189">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="1b0ed-190">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-190">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="1b0ed-191">Obsluhuje klientovi JavaScript vyvolá C# metoda .NET.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-191">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="1b0ed-192">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-192">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="1b0ed-193">Když **aktivační událost .NET statickou metodu ReturnArrayAsync** vybere tlačítko, prohlédněte si výstup konzoly v prohlížeči webové nástroje pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-193">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="1b0ed-194">Výstup konzoly je:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-194">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="1b0ed-195">Čtvrtá hodnota pole je vloženo do pole (`data.push(4);`) vrácený `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-195">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="1b0ed-196">Volání metody instance</span><span class="sxs-lookup"><span data-stu-id="1b0ed-196">Instance method call</span></span>

<span data-ttu-id="1b0ed-197">Můžete také volat instanci metody rozhraní .NET z jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-197">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="1b0ed-198">Chcete-li vyvolat metodu instance .NET z jazyka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-198">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="1b0ed-199">Předejte instanci .NET do jazyka Javasript obalením ho `DotNetObjectRef` instance.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-199">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectRef` instance.</span></span> <span data-ttu-id="1b0ed-200">.NET instance je předána odkazem pro jazyk JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-200">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="1b0ed-201">Vyvolání metody instance .NET na použití instance `invokeMethod` nebo `invokeMethodAsync` funkce.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-201">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="1b0ed-202">.NET instance můžete také předán jako argument při volání metod rozhraní .NET z jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-202">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="1b0ed-203">Ukázková aplikace zprávy protokolu ke konzole na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-203">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="1b0ed-204">Následující příklady jsme vám ukázali bude ukázková aplikace prohlédněte výstup konzoly prohlížeče v prohlížeči vývojářské nástroje.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-204">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="1b0ed-205">Když **metodu instance aktivační událost .NET HelloHelper.SayHello** výběru tlačítka `ExampleJsInterop.CallHelloHelperSayHello` nazývá a předává název, `Blazor`, metody.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-205">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="1b0ed-206">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-206">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

<span data-ttu-id="1b0ed-207">`CallHelloHelperSayHello` funkce jazyka JavaScript vyvolá `sayHello` s novou instanci třídy `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-207">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="1b0ed-208">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-208">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="1b0ed-209">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-209">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="1b0ed-210">Název je předán `HelloHelper`pro konstruktor, který nastaví `HelloHelper.Name` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-210">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="1b0ed-211">Když funkce JavaScript, která `sayHello` provádí, `HelloHelper.SayHello` vrátí `Hello, {Name}!` zprávy, která zapisuje do konzoly pro funkce jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-211">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="1b0ed-212">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-212">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="1b0ed-213">Výstup v nástrojích pro vývojáře v prohlížeči na webové konzoly:</span><span class="sxs-lookup"><span data-stu-id="1b0ed-213">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="1b0ed-214">Sdílet interoperační kód. v knihovně tříd</span><span class="sxs-lookup"><span data-stu-id="1b0ed-214">Share interop code in a class library</span></span>

<span data-ttu-id="1b0ed-215">Interoperační kód jazyka JavaScript, mohou být součástí knihovny tříd, která umožňuje sdílet kód v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-215">JavaScript interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="1b0ed-216">Knihovna tříd zpracovává vkládání prostředky jazyka JavaScript v sestavení.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-216">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="1b0ed-217">Soubory jazyka JavaScript jsou umístěny v *wwwroot* složky.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-217">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="1b0ed-218">Nástroje postará vkládání prostředků při vytváření knihovny.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-218">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="1b0ed-219">Sestavené balíček NuGet je popsána v souboru projektu aplikace, stejně jako jakýkoli normální balíček NuGet se odkazuje.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-219">The built NuGet package is referenced in the project file of the app just as any normal NuGet package is referenced.</span></span> <span data-ttu-id="1b0ed-220">Po obnovení aplikace kód aplikace může volat do jazyka JavaScript, jako by šlo C#.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-220">After the app is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="1b0ed-221">Další informace naleznete v tématu <xref:blazor/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="1b0ed-221">For more information, see <xref:blazor/class-libraries>.</span></span>
