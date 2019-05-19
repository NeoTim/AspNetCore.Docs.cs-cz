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
# <a name="blazor-javascript-interop"></a><span data-ttu-id="80748-103">Zprostředkovatel komunikace s objekty jazyka Blazor JavaScript</span><span class="sxs-lookup"><span data-stu-id="80748-103">Blazor JavaScript interop</span></span>

<span data-ttu-id="80748-104">Podle [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="80748-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="80748-105">Blazor aplikace můžete volat funkce jazyka JavaScript od .NET a .NET metody z kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80748-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="80748-106">Vyvolání funkce jazyka JavaScript z metod rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="80748-106">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="80748-107">Existují situace, kdy je potřeba volat funkce jazyka JavaScript kód .NET.</span><span class="sxs-lookup"><span data-stu-id="80748-107">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="80748-108">Například volání JavaScriptu můžete zveřejnit možnosti prohlížeče nebo funkce z knihovny jazyka JavaScript do aplikace.</span><span class="sxs-lookup"><span data-stu-id="80748-108">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="80748-109">Chcete-li volat JavaScript z .NET, použijte `IJSRuntime` abstrakce.</span><span class="sxs-lookup"><span data-stu-id="80748-109">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="80748-110">`InvokeAsync<T>` Metoda přebírá identifikátor funkce JavaScriptu, která chcete vyvolat spolu s libovolný počet argumentů serializovat na JSON.</span><span class="sxs-lookup"><span data-stu-id="80748-110">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="80748-111">Identifikátor funkce je relativní vzhledem ke globální obor (`window`).</span><span class="sxs-lookup"><span data-stu-id="80748-111">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="80748-112">Pokud chcete volat `window.someScope.someFunction`, je identifikátor `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="80748-112">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="80748-113">Není nutné zaregistrovat funkci předtím, než je volána.</span><span class="sxs-lookup"><span data-stu-id="80748-113">There's no need to register the function before it's called.</span></span> <span data-ttu-id="80748-114">Návratový typ `T` musí také být JSON serializovatelný.</span><span class="sxs-lookup"><span data-stu-id="80748-114">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="80748-115">Pro serverové aplikace:</span><span class="sxs-lookup"><span data-stu-id="80748-115">For server-side apps:</span></span>

* <span data-ttu-id="80748-116">Aplikace na straně serveru zpracovává více požadavků uživatele.</span><span class="sxs-lookup"><span data-stu-id="80748-116">Multiple user requests are processed by the server-side app.</span></span> <span data-ttu-id="80748-117">Nevolejte `JSRuntime.Current` v součásti pro vyvolání funkce jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80748-117">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="80748-118">Vložit `IJSRuntime` abstrakce a použití vloženého objektu vydat spolupráce volání JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="80748-118">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>
* <span data-ttu-id="80748-119">Zatímco Blazor aplikace je před vykreslením, volání do jazyka JavaScript není možné, protože nebyla navázat připojení s prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="80748-119">While a Blazor app is prerendering, calling into JavaScript isn't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="80748-120">Další informace najdete v tématu [rozpoznat, kdy aplikace Blazor se před vykreslením](#detect-when-a-blazor-app-is-prerendering) oddílu.</span><span class="sxs-lookup"><span data-stu-id="80748-120">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="80748-121">Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), experimentální dekodér založené na jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80748-121">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="80748-122">Příklad ukazuje, jak vyvolat funkci z jazyka JavaScript C# metody.</span><span class="sxs-lookup"><span data-stu-id="80748-122">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="80748-123">Funkce JavaScript, která přijímá pole bajtů z C# metody dekóduje pole a vrátí text na komponentu pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="80748-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="80748-124">Uvnitř `<head>` prvek *wwwroot/index.html* (Blazor straně klienta) nebo *stránek /\_Host.cshtml* (Blazor serverové), poskytují funkce, která používá `TextDecoder` do dekódování předané pole:</span><span class="sxs-lookup"><span data-stu-id="80748-124">Inside the `<head>` element of *wwwroot/index.html* (Blazor client-side) or *Pages/\_Host.cshtml* (Blazor server-side), provide a function that uses `TextDecoder` to decode a passed array:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

<span data-ttu-id="80748-125">Kód jazyka JavaScript, jako je například kódu zobrazeného v předchozím příkladu je také možné načíst ze souboru JavaScriptu (*js*) s odkazem na soubor skriptu:</span><span class="sxs-lookup"><span data-stu-id="80748-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="80748-126">Následující komponenty:</span><span class="sxs-lookup"><span data-stu-id="80748-126">The following component:</span></span>

* <span data-ttu-id="80748-127">Vyvolá `ConvertArray` funkcí jazyka JavaScript s použitím `JsRuntime` při tlačítko součásti (**převést pole**) je vybraná.</span><span class="sxs-lookup"><span data-stu-id="80748-127">Invokes the `ConvertArray` JavaScript function using `JsRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="80748-128">Po zavolání funkce JavaScript, která se převede předané pole na řetězec.</span><span class="sxs-lookup"><span data-stu-id="80748-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="80748-129">Řetězec se vrátí do komponenty pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="80748-129">The string is returned to the component for display.</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

<span data-ttu-id="80748-130">Použít `IJSRuntime` abstrakce, použijte některý z následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="80748-130">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="80748-131">Vložit `IJSRuntime` abstrakce do komponenty Razor (*.razor*):</span><span class="sxs-lookup"><span data-stu-id="80748-131">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* <span data-ttu-id="80748-132">Vložit `IJSRuntime` abstrakce do třídy (*.cs*):</span><span class="sxs-lookup"><span data-stu-id="80748-132">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* <span data-ttu-id="80748-133">Pro dynamické generování obsahu s [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), použijte `[Inject]` atribut:</span><span class="sxs-lookup"><span data-stu-id="80748-133">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="80748-134">V aplikaci ukázka na straně klienta, který doprovází v tomto tématu jsou k dispozici aplikaci na straně klienta, která pracovat s modelu DOM na vstup uživatele a zobrazení uvítací zprávy dvě funkce jazyka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="80748-134">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the client-side app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="80748-135">`showPrompt` &ndash; Zobrazí výzvu k zadání tak, aby přijímal vstup uživatele (uživatelské jméno) a vrátí řízení volajícímu název.</span><span class="sxs-lookup"><span data-stu-id="80748-135">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="80748-136">`displayWelcome` &ndash; Přiřadí zobrazení uvítací zprávy z volající objekt modelu DOM se `id` z `welcome`.</span><span class="sxs-lookup"><span data-stu-id="80748-136">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="80748-137">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="80748-137">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="80748-138">Místo `<script>` značka, která odkazuje na soubor jazyka JavaScript v *wwwroot/index.html* souboru (Blazor straně klienta) nebo *stránek /\_Host.cshtml* (Blazor serverové) soubor.</span><span class="sxs-lookup"><span data-stu-id="80748-138">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor client-side) or *Pages/\_Host.cshtml* file (Blazor server-side).</span></span>

<span data-ttu-id="80748-139">*Wwwroot/index.HTML* (Blazor straně klienta):</span><span class="sxs-lookup"><span data-stu-id="80748-139">*wwwroot/index.html* (Blazor client-side):</span></span>

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="80748-140">*Stránky /\_Host.cshtml* (Blazor serverové):</span><span class="sxs-lookup"><span data-stu-id="80748-140">*Pages/\_Host.cshtml* (Blazor server-side):</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

<span data-ttu-id="80748-141">Neukládejte `<script>` značky v souboru součásti, protože `<script>` značku nejde dynamicky aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="80748-141">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="80748-142">Funkce .NET metody spolupráce pomocí jazyka JavaScript v *exampleJsInterop.js* souboru voláním `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="80748-142">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="80748-143">`IJSRuntime` Abstrakcí je asynchronní umožní použít scénáře na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="80748-143">The `IJSRuntime` abstraction is asynchronous to allow for server-side scenarios.</span></span> <span data-ttu-id="80748-144">Pokud aplikace běží na straně klienta a vy chcete volat funkce jazyka JavaScript synchronně, přetypovat dolů na `IJSInProcessRuntime` a volat `Invoke<T>` místo.</span><span class="sxs-lookup"><span data-stu-id="80748-144">If the app runs client-side and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="80748-145">Doporučujeme většinu spolupráce knihoven jazyka JavaScript použít asynchronní rozhraní API k zajištění, že tyto knihovny jsou k dispozici ve všech scénářích, na straně klienta nebo na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="80748-145">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios, client-side or server-side.</span></span>

<span data-ttu-id="80748-146">Ukázková aplikace obsahuje komponentu k předvedení zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80748-146">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="80748-147">Komponenty:</span><span class="sxs-lookup"><span data-stu-id="80748-147">The component:</span></span>

* <span data-ttu-id="80748-148">Přijímá vstupu uživatele prostřednictvím řádku jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80748-148">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="80748-149">Vrátí text na komponentu pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="80748-149">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="80748-150">Volá druhé funkce JavaScriptu, která komunikuje s DOM pro zobrazení uvítací zprávy.</span><span class="sxs-lookup"><span data-stu-id="80748-150">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="80748-151">*Pages/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="80748-151">*Pages/JSInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="80748-152">Při `TriggerJsPrompt` provádí výběrem komponenty **aktivační událost jazyka JavaScript výzvy** tlačítko, JavaScript `showPrompt` funkce součástí *wwwroot/exampleJsInterop.js* soubor volá se.</span><span class="sxs-lookup"><span data-stu-id="80748-152">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="80748-153">`showPrompt` Funkce přijímá vstup uživatele (uživatelské jméno), což je kódovaný jazykem HTML a vrácené na komponentu.</span><span class="sxs-lookup"><span data-stu-id="80748-153">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="80748-154">Součást uloží uživatelské jméno v místní proměnné, `name`.</span><span class="sxs-lookup"><span data-stu-id="80748-154">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="80748-155">Je řetězec uložen ve `name` je zahrnut do zobrazení uvítací zprávy, která se předá do funkce JavaScriptu, `displayWelcome`, který vykreslí zobrazení uvítací zprávy do záhlaví značky.</span><span class="sxs-lookup"><span data-stu-id="80748-155">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="detect-when-a-blazor-app-is-prerendering"></a><span data-ttu-id="80748-156">Rozpoznat, kdy aplikace Blazor se před vykreslením</span><span class="sxs-lookup"><span data-stu-id="80748-156">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="80748-157">Zachycení odkazy na elementy</span><span class="sxs-lookup"><span data-stu-id="80748-157">Capture references to elements</span></span>

<span data-ttu-id="80748-158">Některé [zprostředkovatele komunikace s objekty jazyka JavaScript](xref:blazor/javascript-interop) scénáře vyžadují odkazy na prvky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="80748-158">Some [JavaScript interop](xref:blazor/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="80748-159">Například knihovna uživatelského rozhraní může vyžadovat odkaz na prvek pro inicializaci, nebo můžete potřebovat pro volání rozhraní API jako příkaz pro element, jako například `focus` nebo `play`.</span><span class="sxs-lookup"><span data-stu-id="80748-159">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="80748-160">Můžete zaznamenat odkazy na elementy HTML v komponentě používá následující postup:</span><span class="sxs-lookup"><span data-stu-id="80748-160">You can capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="80748-161">Přidat `ref` atribut na prvek jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="80748-161">Add a `ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="80748-162">Definování pole typu `ElementRef` jejichž název odpovídá hodnotě `ref` atribut.</span><span class="sxs-lookup"><span data-stu-id="80748-162">Define a field of type `ElementRef` whose name matches the value of the `ref` attribute.</span></span>

<span data-ttu-id="80748-163">Následující příklad ukazuje zachycení odkazu na `username` `<input>` element:</span><span class="sxs-lookup"><span data-stu-id="80748-163">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```cshtml
<input ref="username" ...>

@functions {
    ElementRef username;
}
```

> [!NOTE]
> <span data-ttu-id="80748-164">Proveďte **není** používat odkazy zachycené element jako způsob vyplnění modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="80748-164">Do **not** use captured element references as a way of populating the DOM.</span></span> <span data-ttu-id="80748-165">To může být v rozporu s modelem deklarativní vykreslovací.</span><span class="sxs-lookup"><span data-stu-id="80748-165">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="80748-166">Co se týče kódu .NET, `ElementRef` je neprůhledný popisovač.</span><span class="sxs-lookup"><span data-stu-id="80748-166">As far as .NET code is concerned, an `ElementRef` is an opaque handle.</span></span> <span data-ttu-id="80748-167">*Pouze* věc, kterou vám pomůžou s `ElementRef` je komunikace přes kódu jazyka JavaScript pomocí zprostředkovatele komunikace s objekty jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80748-167">The *only* thing you can do with `ElementRef` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="80748-168">Pokud tak učiníte, obdrží kód JavaScript na straně `HTMLElement` instance, které můžete použít s normální modelu DOM rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="80748-168">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="80748-169">Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na element:</span><span class="sxs-lookup"><span data-stu-id="80748-169">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="80748-170">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="80748-170">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="80748-171">Použití `IJSRuntime.InvokeAsync<T>` a volat `exampleJsFunctions.focusElement` s `ElementRef` zaměřit element:</span><span class="sxs-lookup"><span data-stu-id="80748-171">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementRef` to focus an element:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,7,11-12)]

<span data-ttu-id="80748-172">Pokud chcete použít rozšiřující metodu zaměřit elementu, vytvořte rozšíření statická metoda, která přijímá `IJSRuntime` instance:</span><span class="sxs-lookup"><span data-stu-id="80748-172">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementRef elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="80748-173">Metoda je volána přímo v objektu.</span><span class="sxs-lookup"><span data-stu-id="80748-173">The method is called directly on the object.</span></span> <span data-ttu-id="80748-174">Následující příklad předpokládá, že statické `Focus` metoda je k dispozici `JsInteropClasses` obor názvů:</span><span class="sxs-lookup"><span data-stu-id="80748-174">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,8,12)]

> [!IMPORTANT]
> <span data-ttu-id="80748-175">`username` Proměnná je vyplněný pouze po komponentu vykreslí a zahrnuje její výstup `>` elementu.</span><span class="sxs-lookup"><span data-stu-id="80748-175">The `username` variable is only populated after the component renders and its output includes the `>` element.</span></span> <span data-ttu-id="80748-176">Pokud se pokusíte předat unpopulated `ElementRef` do kódu jazyka JavaScript, kód jazyka JavaScript obdrží `null`.</span><span class="sxs-lookup"><span data-stu-id="80748-176">If you try to pass an unpopulated `ElementRef` to JavaScript code, the JavaScript code receives `null`.</span></span> <span data-ttu-id="80748-177">K manipulaci s odkazy na prvky po vykreslení (Chcete-li nastavit počáteční fokus na prvek) použijte komponentu `OnAfterRenderAsync` nebo `OnAfterRender` [součástí životního cyklu metody](xref:blazor/components#lifecycle-methods).</span><span class="sxs-lookup"><span data-stu-id="80748-177">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:blazor/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="80748-178">Vyvolání metod rozhraní .NET z funkce jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="80748-178">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="80748-179">Volání statické metody rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="80748-179">Static .NET method call</span></span>

<span data-ttu-id="80748-180">Chcete-li volání statické metody rozhraní .NET z jazyka JavaScript, použijte `DotNet.invokeMethod` nebo `DotNet.invokeMethodAsync` funkce.</span><span class="sxs-lookup"><span data-stu-id="80748-180">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="80748-181">Předat identifikátor statická metoda, kterou chcete volat, název sestavení obsahujícího funkce a žádné argumenty.</span><span class="sxs-lookup"><span data-stu-id="80748-181">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="80748-182">Asynchronní verze se upřednostňuje pro zajištění podpory scénářů na straně serveru.</span><span class="sxs-lookup"><span data-stu-id="80748-182">The asynchronous version is preferred to support server-side scenarios.</span></span> <span data-ttu-id="80748-183">Volání metody rozhraní .NET z jazyka JavaScript, .NET metoda musí být veřejné, statické a mít `[JSInvokable]` atribut.</span><span class="sxs-lookup"><span data-stu-id="80748-183">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="80748-184">Ve výchozím nastavení, identifikátor metody je název metody, ale můžete zadat jiný identifikátor pomocí `JSInvokableAttribute` konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="80748-184">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="80748-185">Volání obecné metody otevřít se momentálně nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="80748-185">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="80748-186">Obsahuje ukázkovou aplikaci C# metoda vrátí pole `int`s.</span><span class="sxs-lookup"><span data-stu-id="80748-186">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="80748-187">`JSInvokable` Atributu se použije pro metodu.</span><span class="sxs-lookup"><span data-stu-id="80748-187">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="80748-188">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="80748-188">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="80748-189">Obsluhuje klientovi JavaScript vyvolá C# metoda .NET.</span><span class="sxs-lookup"><span data-stu-id="80748-189">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="80748-190">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="80748-190">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="80748-191">Když **aktivační událost .NET statickou metodu ReturnArrayAsync** vybere tlačítko, prohlédněte si výstup konzoly v prohlížeči webové nástroje pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="80748-191">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="80748-192">Výstup konzoly je:</span><span class="sxs-lookup"><span data-stu-id="80748-192">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="80748-193">Čtvrtá hodnota pole je vloženo do pole (`data.push(4);`) vrácený `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="80748-193">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="80748-194">Volání metody instance</span><span class="sxs-lookup"><span data-stu-id="80748-194">Instance method call</span></span>

<span data-ttu-id="80748-195">Můžete také volat instanci metody rozhraní .NET z jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80748-195">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="80748-196">Chcete-li vyvolat metodu instance .NET z jazyka JavaScript:</span><span class="sxs-lookup"><span data-stu-id="80748-196">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="80748-197">Předejte instanci .NET do jazyka Javasript obalením ho `DotNetObjectRef` instance.</span><span class="sxs-lookup"><span data-stu-id="80748-197">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectRef` instance.</span></span> <span data-ttu-id="80748-198">.NET instance je předána odkazem pro jazyk JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80748-198">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="80748-199">Vyvolání metody instance .NET na použití instance `invokeMethod` nebo `invokeMethodAsync` funkce.</span><span class="sxs-lookup"><span data-stu-id="80748-199">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="80748-200">.NET instance můžete také předán jako argument při volání metod rozhraní .NET z jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80748-200">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="80748-201">Ukázková aplikace zprávy protokolu ke konzole na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="80748-201">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="80748-202">Následující příklady jsme vám ukázali bude ukázková aplikace prohlédněte výstup konzoly prohlížeče v prohlížeči vývojářské nástroje.</span><span class="sxs-lookup"><span data-stu-id="80748-202">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="80748-203">Když **metodu instance aktivační událost .NET HelloHelper.SayHello** výběru tlačítka `ExampleJsInterop.CallHelloHelperSayHello` nazývá a předává název, `Blazor`, metody.</span><span class="sxs-lookup"><span data-stu-id="80748-203">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="80748-204">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="80748-204">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

<span data-ttu-id="80748-205">`CallHelloHelperSayHello` funkce jazyka JavaScript vyvolá `sayHello` s novou instanci třídy `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="80748-205">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="80748-206">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="80748-206">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="80748-207">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="80748-207">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="80748-208">Název je předán `HelloHelper`pro konstruktor, který nastaví `HelloHelper.Name` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="80748-208">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="80748-209">Když funkce JavaScript, která `sayHello` provádí, `HelloHelper.SayHello` vrátí `Hello, {Name}!` zprávy, která zapisuje do konzoly pro funkce jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="80748-209">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="80748-210">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="80748-210">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="80748-211">Výstup v nástrojích pro vývojáře v prohlížeči na webové konzoly:</span><span class="sxs-lookup"><span data-stu-id="80748-211">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="80748-212">Sdílet interoperační kód. v knihovně tříd</span><span class="sxs-lookup"><span data-stu-id="80748-212">Share interop code in a class library</span></span>

<span data-ttu-id="80748-213">Interoperační kód jazyka JavaScript, mohou být součástí knihovny tříd, která umožňuje sdílet kód v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="80748-213">JavaScript interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="80748-214">Knihovna tříd zpracovává vkládání prostředky jazyka JavaScript v sestavení.</span><span class="sxs-lookup"><span data-stu-id="80748-214">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="80748-215">Soubory jazyka JavaScript jsou umístěny v *wwwroot* složky.</span><span class="sxs-lookup"><span data-stu-id="80748-215">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="80748-216">Nástroje postará vkládání prostředků při vytváření knihovny.</span><span class="sxs-lookup"><span data-stu-id="80748-216">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="80748-217">Sestavené balíček NuGet je popsána v souboru projektu aplikace, stejně jako jakýkoli normální balíček NuGet se odkazuje.</span><span class="sxs-lookup"><span data-stu-id="80748-217">The built NuGet package is referenced in the project file of the app just as any normal NuGet package is referenced.</span></span> <span data-ttu-id="80748-218">Po obnovení aplikace kód aplikace může volat do jazyka JavaScript, jako by šlo C#.</span><span class="sxs-lookup"><span data-stu-id="80748-218">After the app is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="80748-219">Další informace naleznete v tématu <xref:blazor/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="80748-219">For more information, see <xref:blazor/class-libraries>.</span></span>
