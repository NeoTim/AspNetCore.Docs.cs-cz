---
title: ASP.NET Core interoperability JavaScriptu pro Blazor
author: guardrex
description: Naučte se volat funkce JavaScriptu z metod .NET a .NET z JavaScriptu v aplikacích Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/07/2019
uid: blazor/javascript-interop
ms.openlocfilehash: 505bd22c92c6723fb8f41621c05ba9fa3a74943b
ms.sourcegitcommit: 04ce94b3c1b01d167f30eed60c1c95446dfe759d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/21/2019
ms.locfileid: "71176444"
---
# <a name="aspnet-core-blazor-javascript-interop"></a><span data-ttu-id="17956-103">ASP.NET Core interoperability JavaScriptu pro Blazor</span><span class="sxs-lookup"><span data-stu-id="17956-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="17956-104">[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Skořepa](https://github.com/danroth27)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="17956-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="17956-105">Aplikace Blazor může vyvolat funkce JavaScriptu z metod .NET a .NET z kódu JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="17956-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="17956-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="17956-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="17956-107">Vyvolání funkcí jazyka JavaScript z metod .NET</span><span class="sxs-lookup"><span data-stu-id="17956-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="17956-108">Existují situace, kdy je pro volání funkce JavaScriptu vyžadován kód .NET.</span><span class="sxs-lookup"><span data-stu-id="17956-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="17956-109">Volání JavaScriptu může například vystavit možnosti prohlížeče nebo funkce z knihovny JavaScriptu do aplikace.</span><span class="sxs-lookup"><span data-stu-id="17956-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span>

<span data-ttu-id="17956-110">Chcete-li volat do JavaScriptu z rozhraní .NET `IJSRuntime` , použijte abstrakci.</span><span class="sxs-lookup"><span data-stu-id="17956-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="17956-111">`InvokeAsync<T>` Metoda přebírá identifikátor pro funkci JavaScriptu, kterou chcete vyvolat, spolu s libovolným počtem argumentů serializovatelných pomocí JSON.</span><span class="sxs-lookup"><span data-stu-id="17956-111">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="17956-112">Identifikátor funkce je relativní vzhledem k globálnímu oboru (`window`).</span><span class="sxs-lookup"><span data-stu-id="17956-112">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="17956-113">Pokud chcete volat `window.someScope.someFunction`, je `someScope.someFunction`identifikátor.</span><span class="sxs-lookup"><span data-stu-id="17956-113">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="17956-114">Před voláním funkce není nutné ji registrovat.</span><span class="sxs-lookup"><span data-stu-id="17956-114">There's no need to register the function before it's called.</span></span> <span data-ttu-id="17956-115">Návratový typ `T` musí být také serializovatelný jako JSON.</span><span class="sxs-lookup"><span data-stu-id="17956-115">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="17956-116">Pro aplikace Blazor serveru:</span><span class="sxs-lookup"><span data-stu-id="17956-116">For Blazor Server apps:</span></span>

* <span data-ttu-id="17956-117">Aplikace Blazor serveru zpracovává více žádostí uživatele.</span><span class="sxs-lookup"><span data-stu-id="17956-117">Multiple user requests are processed by the Blazor Server app.</span></span> <span data-ttu-id="17956-118">Nevolejte `JSRuntime.Current` v součásti k vyvolání funkcí jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="17956-118">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="17956-119">`IJSRuntime` Vložit abstrakci a použít vložený objekt k vystavení volání interoperability JavaScript.</span><span class="sxs-lookup"><span data-stu-id="17956-119">Inject the `IJSRuntime` abstraction and use the injected object to issue JavaScript interop calls.</span></span>
* <span data-ttu-id="17956-120">I když je aplikace Blazor předem vykreslovat, volání do JavaScriptu není možné, protože připojení k prohlížeči nebylo navázáno.</span><span class="sxs-lookup"><span data-stu-id="17956-120">While a Blazor app is prerendering, calling into JavaScript isn't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="17956-121">Další informace najdete v části [zjištění, kdy je aplikace Blazor předvykreslování](#detect-when-a-blazor-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="17956-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="17956-122">Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), experimentálním dekodéru založeném na JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="17956-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="17956-123">Příklad ukazuje, jak vyvolat funkci JavaScriptu z C# metody.</span><span class="sxs-lookup"><span data-stu-id="17956-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="17956-124">Funkce JavaScriptu přijímá bajtové pole z C# metody, dekóduje pole a vrátí text do komponenty k zobrazení.</span><span class="sxs-lookup"><span data-stu-id="17956-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="17956-125">Uvnitř prvku wwwroot/index.html (Blazor WebAssembly) nebo *Pages/_Host. cshtml* (Blazor Server) zadejte funkci, která používá `TextDecoder` k dekódování předaného pole: `<head>`</span><span class="sxs-lookup"><span data-stu-id="17956-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a function that uses `TextDecoder` to decode a passed array:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script.html)]

<span data-ttu-id="17956-126">Kód jazyka JavaScript, jako je například kód zobrazený v předchozím příkladu, lze také načíst ze souboru JavaScriptu ( *. js*) s odkazem na soubor skriptu:</span><span class="sxs-lookup"><span data-stu-id="17956-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="17956-127">Následující součást:</span><span class="sxs-lookup"><span data-stu-id="17956-127">The following component:</span></span>

* <span data-ttu-id="17956-128">Vyvolá funkci `JsRuntime` jazyka JavaScript pomocí funkce když je vybráno tlačítko komponenty (**převést pole**). `ConvertArray`</span><span class="sxs-lookup"><span data-stu-id="17956-128">Invokes the `ConvertArray` JavaScript function using `JsRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="17956-129">Po volání funkce JavaScriptu je předané pole převedeno na řetězec.</span><span class="sxs-lookup"><span data-stu-id="17956-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="17956-130">Řetězec se vrátí do komponenty pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="17956-130">The string is returned to the component for display.</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

<span data-ttu-id="17956-131">Chcete-li `IJSRuntime` použít abstrakci, přijímají některé z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="17956-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="17956-132">Vložit abstrakci do komponenty Razor ( *. Razor*): `IJSRuntime`</span><span class="sxs-lookup"><span data-stu-id="17956-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

* <span data-ttu-id="17956-133">Vložit abstrakci do třídy ( *. cs*): `IJSRuntime`</span><span class="sxs-lookup"><span data-stu-id="17956-133">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

* <span data-ttu-id="17956-134">Pro generování dynamického obsahu pomocí [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)použijte `[Inject]` atribut:</span><span class="sxs-lookup"><span data-stu-id="17956-134">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="17956-135">V ukázkové aplikaci na straně klienta, která doprovází toto téma, jsou k dispozici dvě funkce JavaScriptu pro aplikaci, která komunikuje s modelem DOM pro příjem vstupu uživatele a zobrazení uvítací zprávy:</span><span class="sxs-lookup"><span data-stu-id="17956-135">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="17956-136">`showPrompt`&ndash; Vytvoří výzvu k přijetí vstupu uživatele (jméno uživatele) a vrátí název volajícímu.</span><span class="sxs-lookup"><span data-stu-id="17956-136">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="17956-137">`displayWelcome`Přiřadí uvítací zprávu od volajícího k objektu modelu DOM `id` s `welcome`příponou. &ndash;</span><span class="sxs-lookup"><span data-stu-id="17956-137">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="17956-138">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="17956-138">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="17956-139">Umístěte značku, která odkazuje na soubor JavaScriptu, v souboru wwwroot/index.html (Blazor WebAssembly) nebo *Pages/_Host. cshtml* (Server Blazor). `<script>`</span><span class="sxs-lookup"><span data-stu-id="17956-139">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="17956-140">*wwwroot/index.html* (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="17956-140">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorSample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="17956-141">*Pages/_Host. cshtml* (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="17956-141">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/_Host.cshtml?highlight=29)]

<span data-ttu-id="17956-142">Neumísťujte `<script>` značku do souboru komponenty, `<script>` protože značku nejde dynamicky aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="17956-142">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="17956-143">Metody .NET spolupracuje s funkcemi JavaScriptu v souboru *exampleJsInterop. js* voláním `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="17956-143">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="17956-144">`IJSRuntime` Abstrakce je asynchronní k povolení scénářů Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="17956-144">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="17956-145">Pokud se jedná o aplikaci Blazor WebAssembly a chcete vyvolat funkci JavaScriptu synchronně, přetypování směrem dolů `IJSInProcessRuntime` a volání `Invoke<T>` místo toho.</span><span class="sxs-lookup"><span data-stu-id="17956-145">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="17956-146">Doporučujeme, aby většina knihoven JavaScript Interop používala asynchronní rozhraní API, aby se zajistilo, že jsou knihovny dostupné ve všech scénářích.</span><span class="sxs-lookup"><span data-stu-id="17956-146">We recommend that most JavaScript interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="17956-147">Ukázková aplikace zahrnuje komponentu k předvedení interoperability JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="17956-147">The sample app includes a component to demonstrate JavaScript interop.</span></span> <span data-ttu-id="17956-148">Součást:</span><span class="sxs-lookup"><span data-stu-id="17956-148">The component:</span></span>

* <span data-ttu-id="17956-149">Přijímá vstup uživatele prostřednictvím výzvy JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="17956-149">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="17956-150">Vrátí text do komponenty pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="17956-150">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="17956-151">Volá druhou funkci JavaScriptu, která komunikuje s modelem DOM, aby zobrazila úvodní zprávu.</span><span class="sxs-lookup"><span data-stu-id="17956-151">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="17956-152">*Pages/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="17956-152">*Pages/JSInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="17956-153">Když `TriggerJsPrompt` se spustí na **příkazovém řádku aktivační procedury JavaScriptu** komponenty, zavolá se funkce `showPrompt` JavaScriptu, která je k dispozici v souboru *wwwroot/exampleJsInterop. js* .</span><span class="sxs-lookup"><span data-stu-id="17956-153">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="17956-154">`showPrompt` Funkce přijímá vstup uživatele (jméno uživatele), což je kódování HTML a vráceno do komponenty.</span><span class="sxs-lookup"><span data-stu-id="17956-154">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="17956-155">Komponenta ukládá jméno uživatele do místní proměnné, `name`.</span><span class="sxs-lookup"><span data-stu-id="17956-155">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="17956-156">Řetězec uložený v `name` je součástí uvítací zprávy, která je předána funkci JavaScriptu, `displayWelcome`která vykresluje úvodní zprávu do značky nadpisu.</span><span class="sxs-lookup"><span data-stu-id="17956-156">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="17956-157">Volání funkce void JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="17956-157">Call a void JavaScript function</span></span>

<span data-ttu-id="17956-158">Funkce jazyka JavaScript, které vracejí [typ void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) nebo [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) , jsou volány s `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="17956-158">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-blazor-app-is-prerendering"></a><span data-ttu-id="17956-159">Rozpoznat, kdy se předchází aplikace Blazor</span><span class="sxs-lookup"><span data-stu-id="17956-159">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="17956-160">Zachytit odkazy na elementy</span><span class="sxs-lookup"><span data-stu-id="17956-160">Capture references to elements</span></span>

<span data-ttu-id="17956-161">Některé scénáře [interoperability JavaScriptu](xref:blazor/javascript-interop) vyžadují odkazy na elementy HTML.</span><span class="sxs-lookup"><span data-stu-id="17956-161">Some [JavaScript interop](xref:blazor/javascript-interop) scenarios require references to HTML elements.</span></span> <span data-ttu-id="17956-162">Například knihovna uživatelského rozhraní může vyžadovat odkaz na element pro inicializaci nebo může být nutné volat rozhraní API pro příkazy, například `focus` nebo. `play`</span><span class="sxs-lookup"><span data-stu-id="17956-162">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="17956-163">Zachyťte odkazy na prvky HTML v součásti pomocí následujícího postupu:</span><span class="sxs-lookup"><span data-stu-id="17956-163">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="17956-164">`@ref` Přidejte atribut do elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="17956-164">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="17956-165">Definujte pole typu `ElementReference` , jehož název odpovídá hodnotě `@ref` atributu.</span><span class="sxs-lookup"><span data-stu-id="17956-165">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="17956-166">Následující příklad ukazuje, jak zachytit odkaz na `username` `<input>` prvek:</span><span class="sxs-lookup"><span data-stu-id="17956-166">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!NOTE]
> <span data-ttu-id="17956-167">Nepoužívejte **odkazy** zachycených prvků jako způsob naplnění nebo manipulace s modelem DOM, pokud Blazor komunikuje s prvky, na které odkazuje.</span><span class="sxs-lookup"><span data-stu-id="17956-167">Do **not** use captured element references as a way of populating or manipulating the DOM when Blazor interacts with the elements referenced.</span></span> <span data-ttu-id="17956-168">V takovém případě může dojít k narušování deklarativního vykreslovacího modelu.</span><span class="sxs-lookup"><span data-stu-id="17956-168">Doing so may interfere with the declarative rendering model.</span></span>

<span data-ttu-id="17956-169">Pokud se jedná o kód .NET, `ElementReference` je neprůhledný popisovač.</span><span class="sxs-lookup"><span data-stu-id="17956-169">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="17956-170">*Jediná* věc, kterou můžete udělat `ElementReference` , je předat do kódu JavaScriptu prostřednictvím interoperability JavaScript.</span><span class="sxs-lookup"><span data-stu-id="17956-170">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JavaScript interop.</span></span> <span data-ttu-id="17956-171">Když to uděláte, kód na straně JavaScriptu obdrží `HTMLElement` instanci, kterou může použít s normálními rozhraními API DOM.</span><span class="sxs-lookup"><span data-stu-id="17956-171">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="17956-172">Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na prvek:</span><span class="sxs-lookup"><span data-stu-id="17956-172">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="17956-173">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="17956-173">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="17956-174">Pomocí `IJSRuntime.InvokeAsync<T>` a zavolejte `exampleJsFunctions.focusElement` s a `ElementReference` Zaměřte se na prvek:</span><span class="sxs-lookup"><span data-stu-id="17956-174">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementReference` to focus an element:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="17956-175">Chcete-li použít metodu rozšíření pro zaměření prvku, vytvořte statickou metodu rozšíření, která obdrží `IJSRuntime` instanci:</span><span class="sxs-lookup"><span data-stu-id="17956-175">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="17956-176">Metoda je volána přímo na objektu.</span><span class="sxs-lookup"><span data-stu-id="17956-176">The method is called directly on the object.</span></span> <span data-ttu-id="17956-177">Následující příklad předpokládá, že statická `Focus` metoda je k dispozici `JsInteropClasses` z oboru názvů:</span><span class="sxs-lookup"><span data-stu-id="17956-177">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> <span data-ttu-id="17956-178">`username` Proměnná je vyplněna pouze po vykreslení komponenty.</span><span class="sxs-lookup"><span data-stu-id="17956-178">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="17956-179">Pokud `ElementReference` se vyplněný kód předává kódu JavaScriptu, kód JavaScriptu obdrží `null`hodnotu.</span><span class="sxs-lookup"><span data-stu-id="17956-179">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="17956-180">Chcete-li manipulovat s odkazy na elementy po dokončení vykreslování komponenty (pro nastavení prvotního zaměření na element), `OnAfterRenderAsync` použijte [metody životního cyklu komponenty](xref:blazor/components#lifecycle-methods)nebo. `OnAfterRender`</span><span class="sxs-lookup"><span data-stu-id="17956-180">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:blazor/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="17956-181">Vyvolat metody .NET z funkcí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="17956-181">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="17956-182">Statické volání metody .NET</span><span class="sxs-lookup"><span data-stu-id="17956-182">Static .NET method call</span></span>

<span data-ttu-id="17956-183">Chcete-li vyvolat statickou metodu .NET z JavaScriptu, použijte `DotNet.invokeMethod` funkce `DotNet.invokeMethodAsync` nebo.</span><span class="sxs-lookup"><span data-stu-id="17956-183">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="17956-184">Předejte identifikátor statické metody, kterou chcete volat, název sestavení obsahující funkce a všechny argumenty.</span><span class="sxs-lookup"><span data-stu-id="17956-184">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="17956-185">Asynchronní verze je preferována pro podporu scénářů Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="17956-185">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="17956-186">Pro vyvolání metody .NET z JavaScriptu musí být metoda .NET veřejná, statická a mít `[JSInvokable]` atribut.</span><span class="sxs-lookup"><span data-stu-id="17956-186">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="17956-187">Ve výchozím nastavení je identifikátor metody název metody, ale můžete zadat jiný identifikátor pomocí `JSInvokableAttribute` konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="17956-187">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="17956-188">Volání otevřených obecných metod není aktuálně podporováno.</span><span class="sxs-lookup"><span data-stu-id="17956-188">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="17956-189">Ukázková aplikace obsahuje C# metodu, která vrátí pole `int`s.</span><span class="sxs-lookup"><span data-stu-id="17956-189">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="17956-190">`JSInvokable` Atribut je použit pro metodu.</span><span class="sxs-lookup"><span data-stu-id="17956-190">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="17956-191">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="17956-191">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="17956-192">JavaScript, který obsluhuje klient, vyvolá C# metodu .NET.</span><span class="sxs-lookup"><span data-stu-id="17956-192">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="17956-193">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="17956-193">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="17956-194">Pokud je vybráno tlačítko **aktivovat .NET static Method ReturnArrayAsync** , Projděte si výstup konzoly v webových vývojářských nástrojích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="17956-194">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="17956-195">Výstup konzoly:</span><span class="sxs-lookup"><span data-stu-id="17956-195">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="17956-196">Čtvrtá hodnota pole je vložena do pole (`data.push(4);`) `ReturnArrayAsync`vráceného.</span><span class="sxs-lookup"><span data-stu-id="17956-196">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="17956-197">Volání metody instance</span><span class="sxs-lookup"><span data-stu-id="17956-197">Instance method call</span></span>

<span data-ttu-id="17956-198">Můžete také volat metody instance rozhraní .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="17956-198">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="17956-199">Vyvolání metody instance rozhraní .NET z JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="17956-199">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="17956-200">Předejte instanci rozhraní .NET do JavaScriptu tak, že ji `DotNetObjectReference` zabalíte do instance.</span><span class="sxs-lookup"><span data-stu-id="17956-200">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectReference` instance.</span></span> <span data-ttu-id="17956-201">Instance rozhraní .NET je předána odkazem na JavaScript.</span><span class="sxs-lookup"><span data-stu-id="17956-201">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="17956-202">Vyvolat metody instance .NET v instanci pomocí `invokeMethod` funkcí nebo. `invokeMethodAsync`</span><span class="sxs-lookup"><span data-stu-id="17956-202">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="17956-203">Instance rozhraní .NET může být také předána jako argument při vyvolání jiných metod rozhraní .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="17956-203">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="17956-204">Ukázková aplikace protokoluje zprávy do konzoly na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="17956-204">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="17956-205">Pro následující příklady znázorněné ukázkovou aplikací si Projděte výstup konzoly v prohlížeči v vývojářských nástrojích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="17956-205">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="17956-206">Když je vybráno tlačítko **aktivovat metodu instance .NET HelloHelper. sayHello** , `ExampleJsInterop.CallHelloHelperSayHello` je volána a `Blazor`předá metodě název.</span><span class="sxs-lookup"><span data-stu-id="17956-206">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="17956-207">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="17956-207">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorSample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

<span data-ttu-id="17956-208">`CallHelloHelperSayHello`vyvolá funkci `sayHello` JavaScriptu s novou `HelloHelper`instancí.</span><span class="sxs-lookup"><span data-stu-id="17956-208">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="17956-209">*JsInteropClasses/ExampleJsInterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="17956-209">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="17956-210">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="17956-210">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorSample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="17956-211">Název je předán `HelloHelper`konstruktoru, který `HelloHelper.Name` nastaví vlastnost.</span><span class="sxs-lookup"><span data-stu-id="17956-211">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="17956-212">Při `sayHello` `Hello, {Name}!` spuštění funkce JavaScriptu vrátízprávu,kterájezapsánadokonzolyfunkcíJavaScriptu.`HelloHelper.SayHello`</span><span class="sxs-lookup"><span data-stu-id="17956-212">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="17956-213">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="17956-213">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorSample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="17956-214">Výstup konzoly v vývojářských nástrojích webu v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="17956-214">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="17956-215">Sdílení kódu spolupráce v knihovně tříd</span><span class="sxs-lookup"><span data-stu-id="17956-215">Share interop code in a class library</span></span>

<span data-ttu-id="17956-216">Kód interoperability JavaScriptu může být součástí knihovny tříd, která umožňuje sdílení kódu v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="17956-216">JavaScript interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="17956-217">Knihovna tříd zpracovává vložení prostředků JavaScriptu do sestaveného sestavení.</span><span class="sxs-lookup"><span data-stu-id="17956-217">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="17956-218">Soubory JavaScriptu jsou umístěny ve složce *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="17956-218">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="17956-219">Nástroj při sestavování knihovny postará o vkládání prostředků.</span><span class="sxs-lookup"><span data-stu-id="17956-219">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="17956-220">Na sestavený balíček NuGet se odkazuje v souboru projektu aplikace stejným způsobem, jako na který se odkazuje na balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="17956-220">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="17956-221">Po obnovení balíčku kód aplikace může zavolat do JavaScriptu, jako kdyby byl C#.</span><span class="sxs-lookup"><span data-stu-id="17956-221">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="17956-222">Další informace naleznete v tématu <xref:blazor/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="17956-222">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="17956-223">Volání interoperability pro posílení JS</span><span class="sxs-lookup"><span data-stu-id="17956-223">Harden JS interop calls</span></span>

<span data-ttu-id="17956-224">Interoperabilita JS může selhat kvůli chybám sítě a měla by být považována za nespolehlivou.</span><span class="sxs-lookup"><span data-stu-id="17956-224">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="17956-225">Ve výchozím nastavení se aplikace serveru Blazor vyprší voláním interoperability JS na serveru po jedné minutě.</span><span class="sxs-lookup"><span data-stu-id="17956-225">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="17956-226">Pokud aplikace může tolerovat více agresivního časového limitu, například 10 sekund, nastavte časový limit pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="17956-226">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="17956-227">V `Startup.ConfigureServices`části globálně zadejte časový limit:</span><span class="sxs-lookup"><span data-stu-id="17956-227">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="17956-228">Za vyvolání v kódu komponenty může jedno volání zadat časový limit:</span><span class="sxs-lookup"><span data-stu-id="17956-228">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="17956-229">Další informace o vyčerpání prostředků naleznete v tématu <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="17956-229">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>
