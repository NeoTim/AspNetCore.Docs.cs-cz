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
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a><span data-ttu-id="38a8f-103">Zprostředkovatel komunikace s ASP.NET Core Blazor JavaScript</span><span class="sxs-lookup"><span data-stu-id="38a8f-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="38a8f-104">[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Skořepa](https://github.com/danroth27)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="38a8f-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="38a8f-105">Blazor aplikace může vyvolat JavaScriptové funkce z metod .NET a .NET z kódu JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="38a8f-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="38a8f-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="38a8f-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="38a8f-107">Vyvolání funkcí jazyka JavaScript z metod .NET</span><span class="sxs-lookup"><span data-stu-id="38a8f-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="38a8f-108">Existují situace, kdy je pro volání funkce JavaScriptu vyžadován kód .NET.</span><span class="sxs-lookup"><span data-stu-id="38a8f-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="38a8f-109">Volání JavaScriptu může například vystavit možnosti prohlížeče nebo funkce z knihovny JavaScriptu do aplikace.</span><span class="sxs-lookup"><span data-stu-id="38a8f-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span> <span data-ttu-id="38a8f-110">Tento scénář se nazývá *interoperabilita JavaScriptu* (interoperabilita*js*).</span><span class="sxs-lookup"><span data-stu-id="38a8f-110">This scenario is called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="38a8f-111">Chcete-li volat do JavaScriptu z rozhraní .NET, použijte `IJSRuntime` abstrakce.</span><span class="sxs-lookup"><span data-stu-id="38a8f-111">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="38a8f-112">Metoda `InvokeAsync<T>` přebírá identifikátor pro funkci JavaScriptu, kterou chcete vyvolat, spolu s libovolným počtem argumentů serializovatelných pomocí JSON.</span><span class="sxs-lookup"><span data-stu-id="38a8f-112">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="38a8f-113">Identifikátor funkce je relativní vzhledem k globálnímu oboru (`window`).</span><span class="sxs-lookup"><span data-stu-id="38a8f-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="38a8f-114">Pokud chcete volat `window.someScope.someFunction`, je identifikátor `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="38a8f-115">Před voláním funkce není nutné ji registrovat.</span><span class="sxs-lookup"><span data-stu-id="38a8f-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="38a8f-116">Návratový typ `T` musí být také serializovatelný jako JSON.</span><span class="sxs-lookup"><span data-stu-id="38a8f-116">The return type `T` must also be JSON serializable.</span></span>

<span data-ttu-id="38a8f-117">Pro aplikace Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="38a8f-117">For Blazor Server apps:</span></span>

* <span data-ttu-id="38a8f-118">Aplikace Blazor serveru zpracovává více žádostí uživatele.</span><span class="sxs-lookup"><span data-stu-id="38a8f-118">Multiple user requests are processed by the Blazor Server app.</span></span> <span data-ttu-id="38a8f-119">Nevolejte `JSRuntime.Current` v součásti k vyvolání funkcí jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="38a8f-119">Don't call `JSRuntime.Current` in a component to invoke JavaScript functions.</span></span>
* <span data-ttu-id="38a8f-120">Vložení `IJSRuntime` abstrakce a použití vloženého objektu k vydávání volání interoperability JS.</span><span class="sxs-lookup"><span data-stu-id="38a8f-120">Inject the `IJSRuntime` abstraction and use the injected object to issue JS interop calls.</span></span>
* <span data-ttu-id="38a8f-121">I když je aplikace Blazor předem vykreslovat, volání do JavaScriptu není možné, protože připojení k prohlížeči nebylo navázáno.</span><span class="sxs-lookup"><span data-stu-id="38a8f-121">While a Blazor app is prerendering, calling into JavaScript isn't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="38a8f-122">Další informace najdete v části [zjištění, kdy je aplikace Blazor předvykreslování](#detect-when-a-blazor-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="38a8f-122">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="38a8f-123">Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), experimentálním dekodéru založeném na JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="38a8f-123">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="38a8f-124">Příklad ukazuje, jak vyvolat funkci JavaScriptu z C# metody.</span><span class="sxs-lookup"><span data-stu-id="38a8f-124">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="38a8f-125">Funkce JavaScriptu přijímá bajtové pole z C# metody, dekóduje pole a vrátí text do komponenty k zobrazení.</span><span class="sxs-lookup"><span data-stu-id="38a8f-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="38a8f-126">Uvnitř prvku `<head>` *wwwroot/index.html* (Blazor WebAssembly) nebo *pages/_Host. cshtml* (Blazor Server) zadejte funkci javascriptu, která používá `TextDecoder` k dekódování předaného pole a vrácení dekódovaný hodnoty:</span><span class="sxs-lookup"><span data-stu-id="38a8f-126">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="38a8f-127">Kód jazyka JavaScript, jako je například kód zobrazený v předchozím příkladu, lze také načíst ze souboru JavaScriptu ( *. js*) s odkazem na soubor skriptu:</span><span class="sxs-lookup"><span data-stu-id="38a8f-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="38a8f-128">Následující součást:</span><span class="sxs-lookup"><span data-stu-id="38a8f-128">The following component:</span></span>

* <span data-ttu-id="38a8f-129">Vyvolá funkci `convertArray` JavaScript pomocí `JSRuntime` při výběru tlačítka komponenty (**převést pole**).</span><span class="sxs-lookup"><span data-stu-id="38a8f-129">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="38a8f-130">Po volání funkce JavaScriptu je předané pole převedeno na řetězec.</span><span class="sxs-lookup"><span data-stu-id="38a8f-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="38a8f-131">Řetězec se vrátí do komponenty pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="38a8f-131">The string is returned to the component for display.</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a><span data-ttu-id="38a8f-132">Použití IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="38a8f-132">Use of IJSRuntime</span></span>

<span data-ttu-id="38a8f-133">Chcete-li použít abstrakci `IJSRuntime`, přijímají některé z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="38a8f-133">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="38a8f-134">Vložit abstrakci `IJSRuntime` do komponenty Razor ( *. Razor*):</span><span class="sxs-lookup"><span data-stu-id="38a8f-134">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-cshtml[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="38a8f-135">Uvnitř prvku `<head>` *wwwroot/index.html* (Blazor WebAssembly) nebo *pages/_Host. cshtml* (Blazor Server) zadejte funkci JavaScriptu pro `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-135">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="38a8f-136">Funkce je volána pomocí `IJSRuntime.InvokeVoidAsync` a nevrací hodnotu:</span><span class="sxs-lookup"><span data-stu-id="38a8f-136">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="38a8f-137">Vložit abstrakci `IJSRuntime` do třídy ( *. cs*):</span><span class="sxs-lookup"><span data-stu-id="38a8f-137">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="38a8f-138">Uvnitř prvku `<head>` *wwwroot/index.html* (Blazor WebAssembly) nebo *pages/_Host. cshtml* (Blazor Server) zadejte funkci JavaScriptu pro `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-138">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="38a8f-139">Funkce je volána pomocí `JSRuntime.InvokeAsync` a vrací hodnotu:</span><span class="sxs-lookup"><span data-stu-id="38a8f-139">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="38a8f-140">Pro generování dynamického obsahu pomocí [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)použijte atribut `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="38a8f-140">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```csharp
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="38a8f-141">V ukázkové aplikaci na straně klienta, která doprovází toto téma, jsou k dispozici dvě funkce JavaScriptu pro aplikaci, která komunikuje s modelem DOM pro příjem vstupu uživatele a zobrazení uvítací zprávy:</span><span class="sxs-lookup"><span data-stu-id="38a8f-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="38a8f-142">`showPrompt` &ndash; vytvoří výzvu k přijetí vstupu uživatele (jméno uživatele) a vrátí název volajícímu.</span><span class="sxs-lookup"><span data-stu-id="38a8f-142">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="38a8f-143">`displayWelcome` &ndash; přiřadí uvítací zprávu od volajícího k objektu modelu DOM pomocí `id` `welcome`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-143">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="38a8f-144">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="38a8f-144">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="38a8f-145">Umístěte značku `<script>`, která odkazuje na soubor JavaScriptu v souboru *wwwroot/index.html* (Blazor sestavení) nebo *stránky/_Host. cshtml* (ServerBlazor).</span><span class="sxs-lookup"><span data-stu-id="38a8f-145">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="38a8f-146">*wwwroot/index.html* (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="38a8f-146">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="38a8f-147">*Pages/_Host. cshtml* (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="38a8f-147">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=21)]

<span data-ttu-id="38a8f-148">Neumísťujte značku `<script>` do souboru komponenty, protože značku `<script>` nejde dynamicky aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="38a8f-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="38a8f-149">Metody .NET spolupracuje s funkcemi JavaScriptu v souboru *exampleJsInterop. js* voláním `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-149">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="38a8f-150">Abstrakce `IJSRuntime` je asynchronní, aby umožňovala scénáře Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="38a8f-150">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="38a8f-151">Pokud je aplikace Blazor aplikace WebAssembly a chcete vyvolat funkci JavaScriptu synchronně, přetypování downcast na `IJSInProcessRuntime` a místo toho volat `Invoke<T>`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-151">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="38a8f-152">Doporučujeme, aby většina knihoven spolupráce v JS používala asynchronní rozhraní API, aby bylo zajištěno, že jsou knihovny dostupné ve všech scénářích.</span><span class="sxs-lookup"><span data-stu-id="38a8f-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="38a8f-153">Ukázková aplikace obsahuje komponentu, která předvádí interoperabilitu JS.</span><span class="sxs-lookup"><span data-stu-id="38a8f-153">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="38a8f-154">Součást:</span><span class="sxs-lookup"><span data-stu-id="38a8f-154">The component:</span></span>

* <span data-ttu-id="38a8f-155">Přijímá vstup uživatele prostřednictvím výzvy JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="38a8f-155">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="38a8f-156">Vrátí text do komponenty pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="38a8f-156">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="38a8f-157">Volá druhou funkci JavaScriptu, která komunikuje s modelem DOM, aby zobrazila úvodní zprávu.</span><span class="sxs-lookup"><span data-stu-id="38a8f-157">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="38a8f-158">*Stránky/JSInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="38a8f-158">*Pages/JSInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop1&highlight=3,19-21,23-25)]

1. <span data-ttu-id="38a8f-159">Po spuštění `TriggerJsPrompt` kliknutím na tlačítko **výzvy JavaScriptu triggeru** komponenty se zavolá funkce JavaScriptu `showPrompt`, která je k dispozici v souboru *wwwroot/exampleJsInterop. js* .</span><span class="sxs-lookup"><span data-stu-id="38a8f-159">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="38a8f-160">Funkce `showPrompt` akceptuje vstup uživatele (uživatelské jméno), což je kódování HTML, které se vrátí do komponenty.</span><span class="sxs-lookup"><span data-stu-id="38a8f-160">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="38a8f-161">Komponenta ukládá jméno uživatele do místní proměnné `name`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-161">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="38a8f-162">Řetězec uložený v `name` je součástí uvítací zprávy, která je předána funkci JavaScriptu, `displayWelcome`, která vykresluje uvítací zprávu do značky nadpisu.</span><span class="sxs-lookup"><span data-stu-id="38a8f-162">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="38a8f-163">Volání funkce void JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="38a8f-163">Call a void JavaScript function</span></span>

<span data-ttu-id="38a8f-164">Funkce jazyka JavaScript, které vracejí [typ void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) nebo [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) , jsou volány pomocí `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-164">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a><span data-ttu-id="38a8f-165">Rozpoznat, kdy se předchází aplikace Blazor</span><span class="sxs-lookup"><span data-stu-id="38a8f-165">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="38a8f-166">Zachytit odkazy na elementy</span><span class="sxs-lookup"><span data-stu-id="38a8f-166">Capture references to elements</span></span>

<span data-ttu-id="38a8f-167">Některé scénáře interoperability JS vyžadují odkazy na prvky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="38a8f-167">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="38a8f-168">Například knihovna uživatelského rozhraní může vyžadovat odkaz na element pro inicializaci nebo může být nutné volat rozhraní API pro příkazy, například `focus` nebo `play`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-168">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="38a8f-169">Zachyťte odkazy na prvky HTML v součásti pomocí následujícího postupu:</span><span class="sxs-lookup"><span data-stu-id="38a8f-169">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="38a8f-170">Přidejte atribut `@ref` do elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="38a8f-170">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="38a8f-171">Definujte pole typu `ElementReference`, jehož název odpovídá hodnotě atributu `@ref`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-171">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="38a8f-172">Následující příklad ukazuje, jak zachytit odkaz na prvek `username` `<input>`:</span><span class="sxs-lookup"><span data-stu-id="38a8f-172">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```cshtml
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="38a8f-173">Pouze odkaz na element lze použít k použití obsahu prázdného prvku, který nekomunikuje s Blazor.</span><span class="sxs-lookup"><span data-stu-id="38a8f-173">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="38a8f-174">Tento scénář je užitečný, když rozhraní API třetí strany poskytuje obsah do elementu.</span><span class="sxs-lookup"><span data-stu-id="38a8f-174">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="38a8f-175">Vzhledem k tomu, že Blazor nekomunikuje s prvkem, neexistuje žádná možnost konfliktu mezi reprezentací Blazorelementu a DOM.</span><span class="sxs-lookup"><span data-stu-id="38a8f-175">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="38a8f-176">V následujícím příkladu je *nebezpečné* obsah neuspořádaného seznamu (`ul`), protože Blazor vzájemně spolupracuje s DOM k naplnění položek seznamu tohoto elementu (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="38a8f-176">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="38a8f-177">Pokud interoperabilita JS podílí obsah elementu `MyList` a Blazor se pokusí použít rozdíly pro prvek, rozdíly nebudou odpovídat modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="38a8f-177">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="38a8f-178">Pokud se jedná o kód .NET, `ElementReference` je neprůhledný popisovač.</span><span class="sxs-lookup"><span data-stu-id="38a8f-178">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="38a8f-179">*Jediná* věc, kterou můžete provést s `ElementReference`, je předána do kódu JavaScriptu přes interoperabilitu js.</span><span class="sxs-lookup"><span data-stu-id="38a8f-179">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="38a8f-180">V takovém případě kód na straně JavaScriptu obdrží instanci `HTMLElement`, kterou lze použít s normálními rozhraními API DOM.</span><span class="sxs-lookup"><span data-stu-id="38a8f-180">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="38a8f-181">Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na prvek:</span><span class="sxs-lookup"><span data-stu-id="38a8f-181">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="38a8f-182">*exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="38a8f-182">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="38a8f-183">Použití `IJSRuntime.InvokeAsync<T>` a volání `exampleJsFunctions.focusElement` pomocí `ElementReference` k zaměření prvku:</span><span class="sxs-lookup"><span data-stu-id="38a8f-183">Use `IJSRuntime.InvokeAsync<T>` and call `exampleJsFunctions.focusElement` with an `ElementReference` to focus an element:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="38a8f-184">Chcete-li použít metodu rozšíření pro zaměření prvku, vytvořte statickou metodu rozšíření, která obdrží instanci `IJSRuntime`:</span><span class="sxs-lookup"><span data-stu-id="38a8f-184">To use an extension method to focus an element, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<object>(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="38a8f-185">Metoda je volána přímo na objektu.</span><span class="sxs-lookup"><span data-stu-id="38a8f-185">The method is called directly on the object.</span></span> <span data-ttu-id="38a8f-186">Následující příklad předpokládá, že metoda statického `Focus` je k dispozici z oboru názvů `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="38a8f-186">The following example assumes that the static `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-cshtml[](javascript-interop/samples_snapshot/component2.razor?highlight=1,4,12)]

> [!IMPORTANT]
> <span data-ttu-id="38a8f-187">Proměnná `username` se naplní až po vykreslení komponenty.</span><span class="sxs-lookup"><span data-stu-id="38a8f-187">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="38a8f-188">Pokud je vyplněný `ElementReference` předán kódu jazyka JavaScript, kód jazyka JavaScript obdrží hodnotu `null`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-188">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="38a8f-189">Chcete-li manipulovat s odkazy na elementy po dokončení vykreslování komponenty (pro nastavení prvotního zaměření na element), použijte [metody životního cyklu komponent](xref:blazor/components#lifecycle-methods)`OnAfterRenderAsync` nebo `OnAfterRender`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-189">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](xref:blazor/components#lifecycle-methods).</span></span>

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="38a8f-190">Vyvolat metody .NET z funkcí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="38a8f-190">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="38a8f-191">Statické volání metody .NET</span><span class="sxs-lookup"><span data-stu-id="38a8f-191">Static .NET method call</span></span>

<span data-ttu-id="38a8f-192">Chcete-li vyvolat statickou metodu .NET z JavaScriptu, použijte funkce `DotNet.invokeMethod` nebo `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-192">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="38a8f-193">Předejte identifikátor statické metody, kterou chcete volat, název sestavení obsahující funkce a všechny argumenty.</span><span class="sxs-lookup"><span data-stu-id="38a8f-193">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="38a8f-194">Asynchronní verze je preferována k podpoře Blazorch scénářů serveru.</span><span class="sxs-lookup"><span data-stu-id="38a8f-194">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="38a8f-195">Pro vyvolání metody .NET z JavaScriptu musí být metoda .NET veřejná, statická a mít atribut `[JSInvokable]`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-195">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="38a8f-196">Ve výchozím nastavení je identifikátor metody název metody, ale můžete zadat jiný identifikátor pomocí `JSInvokableAttribute` konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="38a8f-196">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="38a8f-197">Volání otevřených obecných metod není aktuálně podporováno.</span><span class="sxs-lookup"><span data-stu-id="38a8f-197">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="38a8f-198">Ukázková aplikace obsahuje C# metodu pro vrácení pole `int`s.</span><span class="sxs-lookup"><span data-stu-id="38a8f-198">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="38a8f-199">Atribut `JSInvokable` se aplikuje na metodu.</span><span class="sxs-lookup"><span data-stu-id="38a8f-199">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="38a8f-200">*Stránky/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="38a8f-200">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop2&highlight=7-11)]

<span data-ttu-id="38a8f-201">JavaScript, který obsluhuje klient, vyvolá C# metodu .NET.</span><span class="sxs-lookup"><span data-stu-id="38a8f-201">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="38a8f-202">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="38a8f-202">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="38a8f-203">Pokud je vybráno tlačítko **aktivovat .NET static Method ReturnArrayAsync** , Projděte si výstup konzoly v webových vývojářských nástrojích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="38a8f-203">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="38a8f-204">Výstup konzoly:</span><span class="sxs-lookup"><span data-stu-id="38a8f-204">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="38a8f-205">Hodnota čtvrtého pole je vložena do pole (`data.push(4);`) vráceného `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-205">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="38a8f-206">Volání metody instance</span><span class="sxs-lookup"><span data-stu-id="38a8f-206">Instance method call</span></span>

<span data-ttu-id="38a8f-207">Můžete také volat metody instance rozhraní .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="38a8f-207">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="38a8f-208">Vyvolání metody instance rozhraní .NET z JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="38a8f-208">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="38a8f-209">Předejte instanci rozhraní .NET do JavaScriptu tak, že ji zabalíte do instance `DotNetObjectReference`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-209">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectReference` instance.</span></span> <span data-ttu-id="38a8f-210">Instance rozhraní .NET je předána odkazem na JavaScript.</span><span class="sxs-lookup"><span data-stu-id="38a8f-210">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="38a8f-211">Vyvolat metody instance .NET v instanci pomocí funkcí `invokeMethod` nebo `invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-211">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="38a8f-212">Instance rozhraní .NET může být také předána jako argument při vyvolání jiných metod rozhraní .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="38a8f-212">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="38a8f-213">Ukázková aplikace protokoluje zprávy do konzoly na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="38a8f-213">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="38a8f-214">Pro následující příklady znázorněné ukázkovou aplikací si Projděte výstup konzoly v prohlížeči v vývojářských nástrojích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="38a8f-214">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="38a8f-215">Když je vybráno tlačítko **aktivovat metodu instance .NET HelloHelper. sayHello** , je volána `ExampleJsInterop.CallHelloHelperSayHello` a předá do metody název, `Blazor`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-215">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="38a8f-216">*Stránky/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="38a8f-216">*Pages/JsInterop.razor*:</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorWebAssemblySample/Pages/JsInterop.razor?name=snippet_JSInterop3&highlight=8-9)]

<span data-ttu-id="38a8f-217">`CallHelloHelperSayHello` vyvolá funkci JavaScriptu `sayHello` s novou instancí `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-217">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="38a8f-218">*JsInteropClasses/ExampleJsInterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="38a8f-218">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="38a8f-219">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="38a8f-219">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="38a8f-220">Název je předán konstruktoru `HelloHelper`, který nastaví vlastnost `HelloHelper.Name`.</span><span class="sxs-lookup"><span data-stu-id="38a8f-220">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="38a8f-221">Je-li funkce JavaScriptu `sayHello` spuštěna, vrátí `HelloHelper.SayHello` zprávu `Hello, {Name}!`, která je zapsána do konzoly funkcí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="38a8f-221">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="38a8f-222">*JsInteropClasses/HelloHelper. cs*:</span><span class="sxs-lookup"><span data-stu-id="38a8f-222">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="38a8f-223">Výstup konzoly v vývojářských nástrojích webu v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="38a8f-223">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="38a8f-224">Sdílení kódu spolupráce v knihovně tříd</span><span class="sxs-lookup"><span data-stu-id="38a8f-224">Share interop code in a class library</span></span>

<span data-ttu-id="38a8f-225">Kód interoperability JS lze zahrnout do knihovny tříd, která umožňuje sdílení kódu v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="38a8f-225">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="38a8f-226">Knihovna tříd zpracovává vložení prostředků JavaScriptu do sestaveného sestavení.</span><span class="sxs-lookup"><span data-stu-id="38a8f-226">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="38a8f-227">Soubory JavaScriptu jsou umístěny ve složce *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="38a8f-227">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="38a8f-228">Nástroj při sestavování knihovny postará o vkládání prostředků.</span><span class="sxs-lookup"><span data-stu-id="38a8f-228">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="38a8f-229">Na sestavený balíček NuGet se odkazuje v souboru projektu aplikace stejným způsobem, jako na který se odkazuje na balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="38a8f-229">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="38a8f-230">Po obnovení balíčku kód aplikace může zavolat do JavaScriptu, jako kdyby byl C#.</span><span class="sxs-lookup"><span data-stu-id="38a8f-230">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="38a8f-231">Další informace najdete v tématu <xref:blazor/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="38a8f-231">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="38a8f-232">Volání interoperability pro posílení JS</span><span class="sxs-lookup"><span data-stu-id="38a8f-232">Harden JS interop calls</span></span>

<span data-ttu-id="38a8f-233">Interoperabilita JS může selhat kvůli chybám sítě a měla by být považována za nespolehlivou.</span><span class="sxs-lookup"><span data-stu-id="38a8f-233">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="38a8f-234">Ve výchozím nastavení aplikace Blazor serveru vyprší voláním interoperability JS na serveru po jedné minutě.</span><span class="sxs-lookup"><span data-stu-id="38a8f-234">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="38a8f-235">Pokud aplikace může tolerovat více agresivního časového limitu, například 10 sekund, nastavte časový limit pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="38a8f-235">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="38a8f-236">V `Startup.ConfigureServices`globálně v poli zadejte časový limit:</span><span class="sxs-lookup"><span data-stu-id="38a8f-236">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="38a8f-237">Za vyvolání v kódu komponenty může jedno volání zadat časový limit:</span><span class="sxs-lookup"><span data-stu-id="38a8f-237">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="38a8f-238">Další informace o vyčerpání prostředků naleznete v tématu <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="38a8f-238">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>
