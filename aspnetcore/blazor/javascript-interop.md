---
title: Zprostředkovatel komunikace s ASP.NET Core Blazor JavaScript
author: guardrex
description: Naučte se volat funkce jazyka JavaScript z metod .NET a .NET z JavaScriptu v aplikacích Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/javascript-interop
ms.openlocfilehash: d681eea5a5e876912bd614fba8ea45a464844496
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447162"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a><span data-ttu-id="e8b69-103">Zprostředkovatel komunikace s ASP.NET Core Blazor JavaScript</span><span class="sxs-lookup"><span data-stu-id="e8b69-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="e8b69-104">[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Skořepa](https://github.com/danroth27)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e8b69-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="e8b69-105">Blazor aplikace může vyvolat JavaScriptové funkce z metod .NET a .NET z kódu JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="e8b69-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e8b69-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="e8b69-107">Vyvolání funkcí jazyka JavaScript z metod .NET</span><span class="sxs-lookup"><span data-stu-id="e8b69-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="e8b69-108">Existují situace, kdy je pro volání funkce JavaScriptu vyžadován kód .NET.</span><span class="sxs-lookup"><span data-stu-id="e8b69-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="e8b69-109">Volání JavaScriptu může například vystavit možnosti prohlížeče nebo funkce z knihovny JavaScriptu do aplikace.</span><span class="sxs-lookup"><span data-stu-id="e8b69-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span> <span data-ttu-id="e8b69-110">Tento scénář se nazývá *interoperabilita JavaScriptu* (interoperabilita*js*).</span><span class="sxs-lookup"><span data-stu-id="e8b69-110">This scenario is called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="e8b69-111">Chcete-li volat do JavaScriptu z rozhraní .NET, použijte `IJSRuntime` abstrakce.</span><span class="sxs-lookup"><span data-stu-id="e8b69-111">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="e8b69-112">Chcete-li vydat volání Interop v JS, zapište `IJSRuntime` abstrakce do komponenty.</span><span class="sxs-lookup"><span data-stu-id="e8b69-112">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="e8b69-113">Metoda `InvokeAsync<T>` přebírá identifikátor pro funkci JavaScriptu, kterou chcete vyvolat, spolu s libovolným počtem argumentů serializovatelných pomocí JSON.</span><span class="sxs-lookup"><span data-stu-id="e8b69-113">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="e8b69-114">Identifikátor funkce je relativní vzhledem k globálnímu oboru (`window`).</span><span class="sxs-lookup"><span data-stu-id="e8b69-114">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="e8b69-115">Pokud chcete volat `window.someScope.someFunction`, je identifikátor `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-115">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="e8b69-116">Před voláním funkce není nutné ji registrovat.</span><span class="sxs-lookup"><span data-stu-id="e8b69-116">There's no need to register the function before it's called.</span></span> <span data-ttu-id="e8b69-117">Návratový typ `T` musí být také serializovatelný jako JSON.</span><span class="sxs-lookup"><span data-stu-id="e8b69-117">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="e8b69-118">`T` by měl odpovídat typu .NET, který se nejlépe mapuje na vrácený typ JSON.</span><span class="sxs-lookup"><span data-stu-id="e8b69-118">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="e8b69-119">Pro Blazor serverových aplikací s povoleným předvykreslováním není možné volat do JavaScriptu během prvotního předgenerování.</span><span class="sxs-lookup"><span data-stu-id="e8b69-119">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="e8b69-120">Volání interoperability JavaScriptu musí být odložena až po navázání spojení s prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="e8b69-120">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="e8b69-121">Další informace najdete v části [zjištění, kdy je aplikace Blazor předvykreslování](#detect-when-a-blazor-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="e8b69-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="e8b69-122">Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), experimentálním dekodéru založeném na JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="e8b69-123">Příklad ukazuje, jak vyvolat funkci JavaScriptu z C# metody.</span><span class="sxs-lookup"><span data-stu-id="e8b69-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="e8b69-124">Funkce JavaScriptu přijímá bajtové pole z C# metody, dekóduje pole a vrátí text do komponenty k zobrazení.</span><span class="sxs-lookup"><span data-stu-id="e8b69-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="e8b69-125">Uvnitř prvku `<head>` *wwwroot/index.html* (Blazor WebAssembly) nebo *pages/_Host. cshtml* (Blazor Server) zadejte funkci javascriptu, která používá `TextDecoder` k dekódování předaného pole a vrácení dekódovaný hodnoty:</span><span class="sxs-lookup"><span data-stu-id="e8b69-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="e8b69-126">Kód jazyka JavaScript, jako je například kód zobrazený v předchozím příkladu, lze také načíst ze souboru JavaScriptu ( *. js*) s odkazem na soubor skriptu:</span><span class="sxs-lookup"><span data-stu-id="e8b69-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="e8b69-127">Následující součást:</span><span class="sxs-lookup"><span data-stu-id="e8b69-127">The following component:</span></span>

* <span data-ttu-id="e8b69-128">Vyvolá funkci `convertArray` JavaScript pomocí `JSRuntime` při výběru tlačítka komponenty (**převést pole**).</span><span class="sxs-lookup"><span data-stu-id="e8b69-128">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="e8b69-129">Po volání funkce JavaScriptu je předané pole převedeno na řetězec.</span><span class="sxs-lookup"><span data-stu-id="e8b69-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="e8b69-130">Řetězec se vrátí do komponenty pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="e8b69-130">The string is returned to the component for display.</span></span>

[!code-razor[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a><span data-ttu-id="e8b69-131">Použití IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="e8b69-131">Use of IJSRuntime</span></span>

<span data-ttu-id="e8b69-132">Chcete-li použít abstrakci `IJSRuntime`, přijímají některé z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="e8b69-132">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="e8b69-133">Vložit abstrakci `IJSRuntime` do komponenty Razor ( *. Razor*):</span><span class="sxs-lookup"><span data-stu-id="e8b69-133">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="e8b69-134">Uvnitř prvku `<head>` *wwwroot/index.html* (Blazor WebAssembly) nebo *pages/_Host. cshtml* (Blazor Server) zadejte funkci JavaScriptu pro `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-134">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="e8b69-135">Funkce je volána pomocí `IJSRuntime.InvokeVoidAsync` a nevrací hodnotu:</span><span class="sxs-lookup"><span data-stu-id="e8b69-135">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="e8b69-136">Vložit abstrakci `IJSRuntime` do třídy ( *. cs*):</span><span class="sxs-lookup"><span data-stu-id="e8b69-136">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="e8b69-137">Uvnitř prvku `<head>` *wwwroot/index.html* (Blazor WebAssembly) nebo *pages/_Host. cshtml* (Blazor Server) zadejte funkci JavaScriptu pro `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-137">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="e8b69-138">Funkce je volána pomocí `JSRuntime.InvokeAsync` a vrací hodnotu:</span><span class="sxs-lookup"><span data-stu-id="e8b69-138">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="e8b69-139">Pro generování dynamického obsahu pomocí [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)použijte atribut `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="e8b69-139">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="e8b69-140">V ukázkové aplikaci na straně klienta, která doprovází toto téma, jsou k dispozici dvě funkce JavaScriptu pro aplikaci, která komunikuje s modelem DOM pro příjem vstupu uživatele a zobrazení uvítací zprávy:</span><span class="sxs-lookup"><span data-stu-id="e8b69-140">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="e8b69-141">`showPrompt` &ndash; vytvoří výzvu k přijetí vstupu uživatele (jméno uživatele) a vrátí název volajícímu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-141">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="e8b69-142">`displayWelcome` &ndash; přiřadí uvítací zprávu od volajícího k objektu modelu DOM pomocí `id` `welcome`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-142">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="e8b69-143">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="e8b69-143">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="e8b69-144">Umístěte značku `<script>`, která odkazuje na soubor JavaScriptu v souboru *wwwroot/index.html* (Blazor sestavení) nebo *stránky/_Host. cshtml* (ServerBlazor).</span><span class="sxs-lookup"><span data-stu-id="e8b69-144">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="e8b69-145">*wwwroot/index.html* (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="e8b69-145">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="e8b69-146">*Pages/_Host. cshtml* (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="e8b69-146">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="e8b69-147">Neumísťujte značku `<script>` do souboru komponenty, protože značku `<script>` nejde dynamicky aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="e8b69-147">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="e8b69-148">Metody .NET spolupracuje s funkcemi JavaScriptu v souboru *exampleJsInterop. js* voláním `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-148">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="e8b69-149">Abstrakce `IJSRuntime` je asynchronní, aby umožňovala scénáře Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="e8b69-149">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="e8b69-150">Pokud je aplikace Blazor aplikace WebAssembly a chcete vyvolat funkci JavaScriptu synchronně, přetypování downcast na `IJSInProcessRuntime` a místo toho volat `Invoke<T>`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-150">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="e8b69-151">Doporučujeme, aby většina knihoven spolupráce v JS používala asynchronní rozhraní API, aby bylo zajištěno, že jsou knihovny dostupné ve všech scénářích.</span><span class="sxs-lookup"><span data-stu-id="e8b69-151">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="e8b69-152">Ukázková aplikace obsahuje komponentu, která předvádí interoperabilitu JS.</span><span class="sxs-lookup"><span data-stu-id="e8b69-152">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="e8b69-153">Součást:</span><span class="sxs-lookup"><span data-stu-id="e8b69-153">The component:</span></span>

* <span data-ttu-id="e8b69-154">Přijímá vstup uživatele prostřednictvím výzvy JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="e8b69-155">Vrátí text do komponenty pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="e8b69-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="e8b69-156">Volá druhou funkci JavaScriptu, která komunikuje s modelem DOM, aby zobrazila úvodní zprávu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="e8b69-157">*Stránky/JSInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="e8b69-157">*Pages/JSInterop.razor*:</span></span>

```razor
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" @onclick="TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@code {
    public async Task TriggerJsPrompt()
    {
        // showPrompt is implemented in wwwroot/exampleJsInterop.js
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");
        // displayWelcome is implemented in wwwroot/exampleJsInterop.js
        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. <span data-ttu-id="e8b69-158">Po spuštění `TriggerJsPrompt` kliknutím na tlačítko **výzvy JavaScriptu triggeru** komponenty se zavolá funkce JavaScriptu `showPrompt`, která je k dispozici v souboru *wwwroot/exampleJsInterop. js* .</span><span class="sxs-lookup"><span data-stu-id="e8b69-158">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="e8b69-159">Funkce `showPrompt` akceptuje vstup uživatele (uživatelské jméno), což je kódování HTML, které se vrátí do komponenty.</span><span class="sxs-lookup"><span data-stu-id="e8b69-159">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="e8b69-160">Komponenta ukládá jméno uživatele do místní proměnné `name`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-160">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="e8b69-161">Řetězec uložený v `name` je součástí uvítací zprávy, která je předána funkci JavaScriptu, `displayWelcome`, která vykresluje uvítací zprávu do značky nadpisu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-161">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="e8b69-162">Volání funkce void JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="e8b69-162">Call a void JavaScript function</span></span>

<span data-ttu-id="e8b69-163">Funkce jazyka JavaScript, které vracejí [typ void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) nebo [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) , jsou volány pomocí `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-163">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a><span data-ttu-id="e8b69-164">Rozpoznat, kdy se předchází aplikace Blazor</span><span class="sxs-lookup"><span data-stu-id="e8b69-164">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="e8b69-165">Zachytit odkazy na elementy</span><span class="sxs-lookup"><span data-stu-id="e8b69-165">Capture references to elements</span></span>

<span data-ttu-id="e8b69-166">Některé scénáře interoperability JS vyžadují odkazy na prvky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="e8b69-166">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="e8b69-167">Například knihovna uživatelského rozhraní může vyžadovat odkaz na element pro inicializaci nebo může být nutné volat rozhraní API pro příkazy, například `focus` nebo `play`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-167">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="e8b69-168">Zachyťte odkazy na prvky HTML v součásti pomocí následujícího postupu:</span><span class="sxs-lookup"><span data-stu-id="e8b69-168">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="e8b69-169">Přidejte atribut `@ref` do elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="e8b69-169">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="e8b69-170">Definujte pole typu `ElementReference`, jehož název odpovídá hodnotě atributu `@ref`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-170">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="e8b69-171">Následující příklad ukazuje, jak zachytit odkaz na prvek `username` `<input>`:</span><span class="sxs-lookup"><span data-stu-id="e8b69-171">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="e8b69-172">Pouze odkaz na element lze použít k použití obsahu prázdného prvku, který nekomunikuje s Blazor.</span><span class="sxs-lookup"><span data-stu-id="e8b69-172">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="e8b69-173">Tento scénář je užitečný, když rozhraní API třetí strany poskytuje obsah do elementu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-173">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="e8b69-174">Vzhledem k tomu, že Blazor nekomunikuje s prvkem, neexistuje žádná možnost konfliktu mezi reprezentací Blazorelementu a DOM.</span><span class="sxs-lookup"><span data-stu-id="e8b69-174">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="e8b69-175">V následujícím příkladu je *nebezpečné* obsah neuspořádaného seznamu (`ul`), protože Blazor vzájemně spolupracuje s DOM k naplnění položek seznamu tohoto elementu (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="e8b69-175">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
>
> ```razor
> <ul ref="MyList">
>     @foreach (var item in Todos)
>     {
>         <li>@item.Text</li>
>     }
> </ul>
> ```
>
> <span data-ttu-id="e8b69-176">Pokud interoperabilita JS podílí obsah elementu `MyList` a Blazor se pokusí použít rozdíly pro prvek, rozdíly nebudou odpovídat modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="e8b69-176">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="e8b69-177">Pokud se jedná o kód .NET, `ElementReference` je neprůhledný popisovač.</span><span class="sxs-lookup"><span data-stu-id="e8b69-177">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="e8b69-178">*Jediná* věc, kterou můžete provést s `ElementReference`, je předána do kódu JavaScriptu přes interoperabilitu js.</span><span class="sxs-lookup"><span data-stu-id="e8b69-178">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="e8b69-179">V takovém případě kód na straně JavaScriptu obdrží instanci `HTMLElement`, kterou lze použít s normálními rozhraními API DOM.</span><span class="sxs-lookup"><span data-stu-id="e8b69-179">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="e8b69-180">Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na prvek:</span><span class="sxs-lookup"><span data-stu-id="e8b69-180">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="e8b69-181">*exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="e8b69-181">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="e8b69-182">Chcete-li zavolat funkci JavaScriptu, která nevrací hodnotu, použijte `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-182">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="e8b69-183">Následující kód nastaví fokus na zadání uživatelského jména voláním předchozí funkce JavaScriptu s zachyceným `ElementReference`:</span><span class="sxs-lookup"><span data-stu-id="e8b69-183">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="e8b69-184">Chcete-li použít metodu rozšíření, vytvořte statickou metodu rozšíření, která obdrží instanci `IJSRuntime`:</span><span class="sxs-lookup"><span data-stu-id="e8b69-184">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="e8b69-185">Metoda `Focus` je volána přímo na objektu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-185">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="e8b69-186">Následující příklad předpokládá, že `Focus` metoda je k dispozici z oboru názvů `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="e8b69-186">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="e8b69-187">Proměnná `username` se naplní až po vykreslení komponenty.</span><span class="sxs-lookup"><span data-stu-id="e8b69-187">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="e8b69-188">Pokud je vyplněný `ElementReference` předán kódu jazyka JavaScript, kód jazyka JavaScript obdrží hodnotu `null`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-188">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="e8b69-189">Chcete-li manipulovat s odkazy na elementy po dokončení vykreslování komponenty (pro nastavení prvotního zaměření na prvek), použijte [metody životního cyklu komponenty OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="e8b69-189">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="e8b69-190">Při práci s obecnými typy a vrácení hodnoty použijte [ValueTask\<t >](xref:System.Threading.Tasks.ValueTask`1):</span><span class="sxs-lookup"><span data-stu-id="e8b69-190">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="e8b69-191">`GenericMethod` se volá přímo na objekt s typem.</span><span class="sxs-lookup"><span data-stu-id="e8b69-191">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="e8b69-192">Následující příklad předpokládá, že `GenericMethod` je k dispozici z oboru názvů `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="e8b69-192">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="e8b69-193">Referenční prvky napříč komponentami</span><span class="sxs-lookup"><span data-stu-id="e8b69-193">Reference elements across components</span></span>

<span data-ttu-id="e8b69-194">`ElementReference` je zajištěna pouze platná v metodě `OnAfterRender` součásti (a odkaz na element je `struct`), takže odkaz na element nelze předat mezi komponentami.</span><span class="sxs-lookup"><span data-stu-id="e8b69-194">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="e8b69-195">Pro nadřazenou komponentu, aby byl odkaz na element dostupný pro jiné komponenty, může nadřazená komponenta:</span><span class="sxs-lookup"><span data-stu-id="e8b69-195">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="e8b69-196">Umožňuje podřízeným součástem registrovat zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="e8b69-196">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="e8b69-197">Vyvolejte zaregistrovaná zpětná volání během události `OnAfterRender` s odkazem na předaný element.</span><span class="sxs-lookup"><span data-stu-id="e8b69-197">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="e8b69-198">Nepřímo tento přístup umožňuje podřízeným komponentám pracovat s odkazem na element nadřazených prvků.</span><span class="sxs-lookup"><span data-stu-id="e8b69-198">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="e8b69-199">Následující příklad Blazor WebAssembly ilustruje přístup.</span><span class="sxs-lookup"><span data-stu-id="e8b69-199">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="e8b69-200">V `<head>` *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="e8b69-200">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="e8b69-201">V `<body>` *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="e8b69-201">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="e8b69-202">*Pages/index. Razor* (nadřazená komponenta):</span><span class="sxs-lookup"><span data-stu-id="e8b69-202">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="e8b69-203">*Pages/index. Razor. cs*:</span><span class="sxs-lookup"><span data-stu-id="e8b69-203">*Pages/Index.razor.cs*:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool _disposing;
        private IList<IObserver<ElementReference>> _subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference _title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnNext(_title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            _disposing = true;

            foreach (var subscription in _subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            _subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (_disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            _subscriptions.Add(observer);

            return new Subscription(observer, this);
        }

        private class Subscription : IDisposable
        {
            public Subscription(IObserver<ElementReference> observer, Index self)
            {
                Observer = observer;
                Self = self;
            }

            public IObserver<ElementReference> Observer { get; }
            public Index Self { get; }

            public void Dispose()
            {
                Self._subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="e8b69-204">*Shared/SurveyPrompt. Razor* (podřízená součást):</span><span class="sxs-lookup"><span data-stu-id="e8b69-204">*Shared/SurveyPrompt.razor* (child component):</span></span>

```razor
@inject IJSRuntime JS

<div class="alert alert-secondary mt-4" role="alert">
    <span class="oi oi-pencil mr-2" aria-hidden="true"></span>
    <strong>@Title</strong>

    <span class="text-nowrap">
        Please take our
        <a target="_blank" class="font-weight-bold" 
            href="https://go.microsoft.com/fwlink/?linkid=2109206">brief survey</a>
    </span>
    and tell us what you think.
</div>

@code {
    [Parameter]
    public string Title { get; set; }
}
```

<span data-ttu-id="e8b69-205">*Shared/SurveyPrompt. Razor. cs*:</span><span class="sxs-lookup"><span data-stu-id="e8b69-205">*Shared/SurveyPrompt.razor.cs*:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable _subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (_subscription != null)
            {
                _subscription.Dispose();
            }

            _subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            _subscription = null;
        }

        public void OnError(Exception error)
        {
            _subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            _subscription?.Dispose();
        }
    }
}
```

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="e8b69-206">Vyvolat metody .NET z funkcí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="e8b69-206">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="e8b69-207">Statické volání metody .NET</span><span class="sxs-lookup"><span data-stu-id="e8b69-207">Static .NET method call</span></span>

<span data-ttu-id="e8b69-208">Chcete-li vyvolat statickou metodu .NET z JavaScriptu, použijte funkce `DotNet.invokeMethod` nebo `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-208">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="e8b69-209">Předejte identifikátor statické metody, kterou chcete volat, název sestavení obsahující funkce a všechny argumenty.</span><span class="sxs-lookup"><span data-stu-id="e8b69-209">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="e8b69-210">Asynchronní verze je preferována k podpoře Blazorch scénářů serveru.</span><span class="sxs-lookup"><span data-stu-id="e8b69-210">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="e8b69-211">Metoda .NET musí být veřejná, statická a musí mít atribut `[JSInvokable]`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-211">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="e8b69-212">Volání otevřených obecných metod není aktuálně podporováno.</span><span class="sxs-lookup"><span data-stu-id="e8b69-212">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="e8b69-213">Ukázková aplikace obsahuje C# metodu pro vrácení pole `int`s.</span><span class="sxs-lookup"><span data-stu-id="e8b69-213">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="e8b69-214">Atribut `JSInvokable` se aplikuje na metodu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-214">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="e8b69-215">*Stránky/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="e8b69-215">*Pages/JsInterop.razor*:</span></span>

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="e8b69-216">JavaScript, který obsluhuje klient, vyvolá C# metodu .NET.</span><span class="sxs-lookup"><span data-stu-id="e8b69-216">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="e8b69-217">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="e8b69-217">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="e8b69-218">Pokud je vybráno tlačítko **aktivovat .NET static Method ReturnArrayAsync** , Projděte si výstup konzoly v webových vývojářských nástrojích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e8b69-218">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="e8b69-219">Výstup konzoly:</span><span class="sxs-lookup"><span data-stu-id="e8b69-219">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="e8b69-220">Hodnota čtvrtého pole je vložena do pole (`data.push(4);`) vráceného `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-220">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="e8b69-221">Ve výchozím nastavení je identifikátor metody název metody, ale můžete zadat jiný identifikátor pomocí `JSInvokableAttribute` konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="e8b69-221">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="e8b69-222">V souboru JavaScriptu na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="e8b69-222">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

### <a name="instance-method-call"></a><span data-ttu-id="e8b69-223">Volání metody instance</span><span class="sxs-lookup"><span data-stu-id="e8b69-223">Instance method call</span></span>

<span data-ttu-id="e8b69-224">Můžete také volat metody instance rozhraní .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-224">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="e8b69-225">Vyvolání metody instance rozhraní .NET z JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="e8b69-225">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="e8b69-226">Předání instance rozhraní .NET odkazem na jazyk JavaScript:</span><span class="sxs-lookup"><span data-stu-id="e8b69-226">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="e8b69-227">Proveďte statické volání `DotNetObjectReference.Create`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-227">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="e8b69-228">Zabalte instanci v instanci `DotNetObjectReference` a volejte `Create` na instanci `DotNetObjectReference`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-228">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="e8b69-229">Dispose objektů `DotNetObjectReference` (příklad se zobrazí později v této části).</span><span class="sxs-lookup"><span data-stu-id="e8b69-229">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="e8b69-230">Vyvolat metody instance .NET v instanci pomocí funkcí `invokeMethod` nebo `invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-230">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="e8b69-231">Instance rozhraní .NET může být také předána jako argument při vyvolání jiných metod rozhraní .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-231">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="e8b69-232">Ukázková aplikace protokoluje zprávy do konzoly na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="e8b69-232">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="e8b69-233">Pro následující příklady znázorněné ukázkovou aplikací si Projděte výstup konzoly v prohlížeči v vývojářských nástrojích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="e8b69-233">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="e8b69-234">Když je vybráno tlačítko **aktivovat metodu instance .NET HelloHelper. sayHello** , je volána `ExampleJsInterop.CallHelloHelperSayHello` a předá do metody název, `Blazor`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-234">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="e8b69-235">*Stránky/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="e8b69-235">*Pages/JsInterop.razor*:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JSRuntime);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="e8b69-236">`CallHelloHelperSayHello` vyvolá funkci JavaScriptu `sayHello` s novou instancí `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-236">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="e8b69-237">*JsInteropClasses/ExampleJsInterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="e8b69-237">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="e8b69-238">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="e8b69-238">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="e8b69-239">Název je předán konstruktoru `HelloHelper`, který nastaví vlastnost `HelloHelper.Name`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-239">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="e8b69-240">Je-li funkce JavaScriptu `sayHello` spuštěna, vrátí `HelloHelper.SayHello` zprávu `Hello, {Name}!`, která je zapsána do konzoly funkcí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-240">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="e8b69-241">*JsInteropClasses/HelloHelper. cs*:</span><span class="sxs-lookup"><span data-stu-id="e8b69-241">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="e8b69-242">Výstup konzoly v vývojářských nástrojích webu v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="e8b69-242">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="e8b69-243">Aby nedošlo k nevrácení paměti a povolovalo uvolňování paměti na komponentě, která vytváří `DotNetObjectReference`, Dispose objektu ve třídě, která vytvořila instanci `DotNetObjectReference`:</span><span class="sxs-lookup"><span data-stu-id="e8b69-243">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

```csharp
public class ExampleJsInterop : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private DotNetObjectReference<HelloHelper> _objRef;

    public ExampleJsInterop(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public ValueTask<string> CallHelloHelperSayHello(string name)
    {
        _objRef = DotNetObjectReference.Create(new HelloHelper(name));

        return _jsRuntime.InvokeAsync<string>(
            "exampleJsFunctions.sayHello",
            _objRef);
    }

    public void Dispose()
    {
        _objRef?.Dispose();
    }
}
```
  
<span data-ttu-id="e8b69-244">Předchozí vzor zobrazený ve třídě `ExampleJsInterop` lze také implementovat do komponenty:</span><span class="sxs-lookup"><span data-stu-id="e8b69-244">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>
  
```razor
@page "/JSInteropComponent"
@using BlazorSample.JsInteropClasses
@implements IDisposable
@inject IJSRuntime JSRuntime

<h1>JavaScript Interop</h1>

<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    private DotNetObjectReference<HelloHelper> _objRef;

    public async Task TriggerNetInstanceMethod()
    {
        _objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

        await JSRuntime.InvokeAsync<string>(
            "exampleJsFunctions.sayHello",
            _objRef);
    }

    public void Dispose()
    {
        _objRef?.Dispose();
    }
}
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="e8b69-245">Sdílení kódu spolupráce v knihovně tříd</span><span class="sxs-lookup"><span data-stu-id="e8b69-245">Share interop code in a class library</span></span>

<span data-ttu-id="e8b69-246">Kód interoperability JS lze zahrnout do knihovny tříd, která umožňuje sdílení kódu v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="e8b69-246">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="e8b69-247">Knihovna tříd zpracovává vložení prostředků JavaScriptu do sestaveného sestavení.</span><span class="sxs-lookup"><span data-stu-id="e8b69-247">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="e8b69-248">Soubory JavaScriptu jsou umístěny ve složce *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="e8b69-248">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="e8b69-249">Nástroj při sestavování knihovny postará o vkládání prostředků.</span><span class="sxs-lookup"><span data-stu-id="e8b69-249">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="e8b69-250">Na sestavený balíček NuGet se odkazuje v souboru projektu aplikace stejným způsobem, jako na který se odkazuje na balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="e8b69-250">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="e8b69-251">Po obnovení balíčku kód aplikace může zavolat do JavaScriptu, jako kdyby byl C#.</span><span class="sxs-lookup"><span data-stu-id="e8b69-251">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="e8b69-252">Další informace naleznete v tématu <xref:blazor/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="e8b69-252">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="e8b69-253">Volání interoperability pro posílení JS</span><span class="sxs-lookup"><span data-stu-id="e8b69-253">Harden JS interop calls</span></span>

<span data-ttu-id="e8b69-254">Interoperabilita JS může selhat kvůli chybám sítě a měla by být považována za nespolehlivou.</span><span class="sxs-lookup"><span data-stu-id="e8b69-254">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="e8b69-255">Ve výchozím nastavení aplikace Blazor serveru vyprší voláním interoperability JS na serveru po jedné minutě.</span><span class="sxs-lookup"><span data-stu-id="e8b69-255">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="e8b69-256">Pokud aplikace může tolerovat více agresivního časového limitu, například 10 sekund, nastavte časový limit pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="e8b69-256">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="e8b69-257">V `Startup.ConfigureServices`globálně v poli zadejte časový limit:</span><span class="sxs-lookup"><span data-stu-id="e8b69-257">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="e8b69-258">Za vyvolání v kódu komponenty může jedno volání zadat časový limit:</span><span class="sxs-lookup"><span data-stu-id="e8b69-258">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="e8b69-259">Další informace o vyčerpání prostředků naleznete v tématu <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="e8b69-259">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

## <a name="perform-large-data-transfers-in-opno-locblazor-server-apps"></a><span data-ttu-id="e8b69-260">Provádění rozsáhlých přenosů dat v Blazor serverových aplikacích</span><span class="sxs-lookup"><span data-stu-id="e8b69-260">Perform large data transfers in Blazor Server apps</span></span>

<span data-ttu-id="e8b69-261">V některých scénářích je nutné přenášet velké objemy dat mezi JavaScriptem a Blazor.</span><span class="sxs-lookup"><span data-stu-id="e8b69-261">In some scenarios, large amounts of data must be transferred between JavaScript and Blazor.</span></span> <span data-ttu-id="e8b69-262">K přenosu velkých objemů dat se obvykle dochází v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="e8b69-262">Typically, large data transfers occur when:</span></span>

* <span data-ttu-id="e8b69-263">Rozhraní API pro systém souborů v prohlížeči slouží k nahrání nebo stažení souboru.</span><span class="sxs-lookup"><span data-stu-id="e8b69-263">Browser file system APIs are used to upload or download a file.</span></span>
* <span data-ttu-id="e8b69-264">Je vyžadována interoperabilita s knihovnou třetích stran.</span><span class="sxs-lookup"><span data-stu-id="e8b69-264">Interop with a third party library is required.</span></span>

<span data-ttu-id="e8b69-265">V Blazorm serveru je omezené omezení, které zabraňuje předávání jednoduchých zpráv, které mohou způsobit problémy s výkonem.</span><span class="sxs-lookup"><span data-stu-id="e8b69-265">In Blazor Server, a limitation is in place to prevent passing single large messages that may result in performance issues.</span></span>

<span data-ttu-id="e8b69-266">Při vývoji kódu, který přenáší data mezi JavaScriptem a Blazor, vezměte v úvahu následující pokyny:</span><span class="sxs-lookup"><span data-stu-id="e8b69-266">Consider the following guidance when developing code that transfers data between JavaScript and Blazor:</span></span>

* <span data-ttu-id="e8b69-267">Rozdělí data na menší části a datové segmenty se postupně odesílají, dokud server neobdrží všechna data.</span><span class="sxs-lookup"><span data-stu-id="e8b69-267">Slice the data into smaller pieces, and send the data segments sequentially until all of the data is received by the server.</span></span>
* <span data-ttu-id="e8b69-268">Nepřiřazujte velké objekty v JavaScriptu C# a kódu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-268">Don't allocate large objects in JavaScript and C# code.</span></span>
* <span data-ttu-id="e8b69-269">Při odesílání nebo přijímání dat Neblokujte hlavní vlákno uživatelského rozhraní pro dlouhá období.</span><span class="sxs-lookup"><span data-stu-id="e8b69-269">Don't block the main UI thread for long periods when sending or receiving data.</span></span>
* <span data-ttu-id="e8b69-270">Uvolněte veškerou paměť, která je spotřebována při dokončení nebo zrušení procesu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-270">Free any memory consumed when the process is completed or cancelled.</span></span>
* <span data-ttu-id="e8b69-271">Vyjistěte následující další požadavky pro účely zabezpečení:</span><span class="sxs-lookup"><span data-stu-id="e8b69-271">Enforce the following additional requirements for security purposes:</span></span>
  * <span data-ttu-id="e8b69-272">Deklarujte maximální velikost souboru nebo dat, která se dají předat.</span><span class="sxs-lookup"><span data-stu-id="e8b69-272">Declare the maximum file or data size that can be passed.</span></span>
  * <span data-ttu-id="e8b69-273">Deklarujte minimální rychlost nahrávání z klienta na server.</span><span class="sxs-lookup"><span data-stu-id="e8b69-273">Declare the minimum upload rate from the client to the server.</span></span>
* <span data-ttu-id="e8b69-274">Po přijetí dat serverem můžou být tato data:</span><span class="sxs-lookup"><span data-stu-id="e8b69-274">After the data is received by the server, the data can be:</span></span>
  * <span data-ttu-id="e8b69-275">Dočasně uloženo do vyrovnávací paměti, dokud nebudou všechny segmenty shromažďovány.</span><span class="sxs-lookup"><span data-stu-id="e8b69-275">Temporarily stored in a memory buffer until all of the segments are collected.</span></span>
  * <span data-ttu-id="e8b69-276">Spotřebované hned.</span><span class="sxs-lookup"><span data-stu-id="e8b69-276">Consumed immediately.</span></span> <span data-ttu-id="e8b69-277">Data můžete například uložit hned do databáze nebo zapsat na disk při přijetí každého segmentu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-277">For example, the data can be stored immediately in a database or written to disk as each segment is received.</span></span>

<span data-ttu-id="e8b69-278">Následující třída pro odeslání souborů zpracovává zprostředkovatele komunikace JS s klientem.</span><span class="sxs-lookup"><span data-stu-id="e8b69-278">The following file uploader class handles JS interop with the client.</span></span> <span data-ttu-id="e8b69-279">Třída odeslání používá zprostředkovatele komunikace JS na:</span><span class="sxs-lookup"><span data-stu-id="e8b69-279">The uploader class uses JS interop to:</span></span>

* <span data-ttu-id="e8b69-280">Cyklické dotazování klienta o odeslání datového segmentu.</span><span class="sxs-lookup"><span data-stu-id="e8b69-280">Poll the client to send a data segment.</span></span>
* <span data-ttu-id="e8b69-281">Zruší transakci, pokud vyprší časový limit pro cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="e8b69-281">Abort the transaction if polling times out.</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;
using Microsoft.JSInterop;

public class FileUploader : IDisposable
{
    private readonly IJSRuntime _jsRuntime;
    private readonly int _segmentSize = 6144;
    private readonly int _maxBase64SegmentSize = 8192;
    private readonly DotNetObjectReference<FileUploader> _thisReference;
    private List<IMemoryOwner<byte>> _uploadedSegments = 
        new List<IMemoryOwner<byte>>();

    public FileUploader(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task<Stream> ReceiveFile(string selector, int maxSize)
    {
        var fileSize = 
            await _jsRuntime.InvokeAsync<int>("getFileSize", selector);

        if (fileSize > maxSize)
        {
            return null;
        }

        var numberOfSegments = Math.Floor(fileSize / (double)_segmentSize) + 1;
        var lastSegmentBytes = 0;
        string base64EncodedSegment;

        for (var i = 0; i < numberOfSegments; i++)
        {
            try
            {
                base64EncodedSegment = 
                    await _jsRuntime.InvokeAsync<string>(
                        "receiveSegment", i, selector);

                if (base64EncodedSegment.Length < _maxBase64SegmentSize && 
                    i < numberOfSegments - 1)
                {
                    return null;
                }
            }
            catch
            {
                return null;
            }

          var current = MemoryPool<byte>.Shared.Rent(_segmentSize);

          if (!Convert.TryFromBase64String(base64EncodedSegment, 
              current.Memory.Slice(0, _segmentSize).Span, out lastSegmentBytes))
          {
              return null;
          }

          _uploadedSegments.Add(current);
        }

        var segments = _uploadedSegments;
        _uploadedSegments = null;

        return new SegmentedStream(segments, _segmentSize, lastSegmentBytes);
    }

    public void Dispose()
    {
        if (_uploadedSegments != null)
        {
            foreach (var segment in _uploadedSegments)
            {
                segment.Dispose();
            }
        }
    }
}
```

<span data-ttu-id="e8b69-282">V předchozím příkladu:</span><span class="sxs-lookup"><span data-stu-id="e8b69-282">In the preceding example:</span></span>

* <span data-ttu-id="e8b69-283">`_maxBase64SegmentSize` je nastavená na `8192`, která se počítá z `_maxBase64SegmentSize = _segmentSize * 4 / 3`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-283">The `_maxBase64SegmentSize` is set to `8192`, which is calculated from `_maxBase64SegmentSize = _segmentSize * 4 / 3`.</span></span>
* <span data-ttu-id="e8b69-284">Rozhraní API pro správu paměti nízké úrovně na úrovni .NET Core slouží k ukládání segmentů paměti na serveru v `_uploadedSegments`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-284">Low level .NET Core memory management APIs are used to store the memory segments on the server in `_uploadedSegments`.</span></span>
* <span data-ttu-id="e8b69-285">Metoda `ReceiveFile` se používá ke zpracování nahrávání prostřednictvím zprostředkovatele komunikace JS:</span><span class="sxs-lookup"><span data-stu-id="e8b69-285">A `ReceiveFile` method is used to handle the upload through JS interop:</span></span>
  * <span data-ttu-id="e8b69-286">Velikost souboru je určena v bajtech přes interoperabilitu JS s `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-286">The file size is determined in bytes through JS interop with `_jsRuntime.InvokeAsync<FileInfo>('getFileSize', selector)`.</span></span>
  * <span data-ttu-id="e8b69-287">Počet segmentů, které se mají přijmout, se vypočítá a uloží v `numberOfSegments`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-287">The number of segments to receive are calculated and stored in `numberOfSegments`.</span></span>
  * <span data-ttu-id="e8b69-288">Segmenty jsou požadovány v `for` smyčka prostřednictvím zprostředkovatele komunikace JS s `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span><span class="sxs-lookup"><span data-stu-id="e8b69-288">The segments are requested in a `for` loop through JS interop with `_jsRuntime.InvokeAsync<string>('receiveSegment', i, selector)`.</span></span> <span data-ttu-id="e8b69-289">Všechny segmenty, ale poslední, musí mít 8 192 bajtů před dekódováním.</span><span class="sxs-lookup"><span data-stu-id="e8b69-289">All segments but the last must be 8,192 bytes before decoding.</span></span> <span data-ttu-id="e8b69-290">Klient je nucen posílat data účinným způsobem.</span><span class="sxs-lookup"><span data-stu-id="e8b69-290">The client is forced to send the data in an efficient manner.</span></span>
  * <span data-ttu-id="e8b69-291">Pro každý přijatý segment jsou kontroly provedeny před dekódováním pomocí <xref:System.Convert.TryFromBase64String*>.</span><span class="sxs-lookup"><span data-stu-id="e8b69-291">For each segment received, checks are performed before decoding with <xref:System.Convert.TryFromBase64String*>.</span></span>
  * <span data-ttu-id="e8b69-292">Po dokončení nahrávání se datový proud s daty vrátí jako nový <xref:System.IO.Stream> (`SegmentedStream`).</span><span class="sxs-lookup"><span data-stu-id="e8b69-292">A stream with the data is returned as a new <xref:System.IO.Stream> (`SegmentedStream`) after the upload is complete.</span></span>

<span data-ttu-id="e8b69-293">Třída segmentace Stream zpřístupňuje seznam segmentů jako typ ReadOnly, který nelze prohledávat <xref:System.IO.Stream>:</span><span class="sxs-lookup"><span data-stu-id="e8b69-293">The segmented stream class exposes the list of segments as a readonly non-seekable <xref:System.IO.Stream>:</span></span>

```csharp
using System;
using System.Buffers;
using System.Collections.Generic;
using System.IO;

public class SegmentedStream : Stream
{
    private readonly ReadOnlySequence<byte> _sequence;
    private long _currentPosition = 0;

    public SegmentedStream(IList<IMemoryOwner<byte>> segments, int segmentSize, 
        int lastSegmentSize)
    {
        if (segments.Count == 1)
        {
            _sequence = new ReadOnlySequence<byte>(
                segments[0].Memory.Slice(0, lastSegmentSize));
            return;
        }

        var sequenceSegment = new BufferSegment<byte>(
            segments[0].Memory.Slice(0, segmentSize));
        var lastSegment = sequenceSegment;

        for (int i = 1; i < segments.Count; i++)
        {
            var isLastSegment = i + 1 == segments.Count;
            lastSegment = lastSegment.Append(segments[i].Memory.Slice(
                0, isLastSegment ? lastSegmentSize : segmentSize));
        }

        _sequence = new ReadOnlySequence<byte>(
            sequenceSegment, 0, lastSegment, lastSegmentSize);
    }

    public override long Position
    {
        get => throw new NotImplementedException();
        set => throw new NotImplementedException();
    }

    public override int Read(byte[] buffer, int offset, int count)
    {
        var bytesToWrite = (int)(_currentPosition + count < _sequence.Length ? 
            count : _sequence.Length - _currentPosition);
        var data = _sequence.Slice(_currentPosition, bytesToWrite);
        data.CopyTo(buffer.AsSpan(offset, bytesToWrite));
        _currentPosition += bytesToWrite;

        return bytesToWrite;
    }

    private class BufferSegment<T> : ReadOnlySequenceSegment<T>
    {
        public BufferSegment(ReadOnlyMemory<T> memory)
        {
            Memory = memory;
        }

        public BufferSegment<T> Append(ReadOnlyMemory<T> memory)
        {
            var segment = new BufferSegment<T>(memory)
            {
                RunningIndex = RunningIndex + Memory.Length
            };

            Next = segment;

            return segment;
        }
    }

    public override bool CanRead => true;

    public override bool CanSeek => false;

    public override bool CanWrite => false;

    public override long Length => throw new NotImplementedException();

    public override void Flush() => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => 
        throw new NotImplementedException();

    public override void SetLength(long value) => 
        throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => 
        throw new NotImplementedException();
}
```

<span data-ttu-id="e8b69-294">Následující kód implementuje funkce JavaScriptu pro příjem dat:</span><span class="sxs-lookup"><span data-stu-id="e8b69-294">The following code implements JavaScript functions to receive the data:</span></span>

```javascript
function getFileSize(selector) {
  const file = getFile(selector);
  return file.size;
}

async function receiveSegment(segmentNumber, selector) {
  const file = getFile(selector);
  var segments = getFileSegments(file);
  var index = segmentNumber * 6144;
  return await getNextChunk(file, index);
}

function getFile(selector) {
  const element = document.querySelector(selector);
  if (!element) {
    throw new Error('Invalid selector');
  }
  const files = element.files;
  if (!files || files.length === 0) {
    throw new Error(`Element ${elementId} doesn't contain any files.`);
  }
  const file = files[0];
  return file;
}

function getFileSegments(file) {
  const segments = Math.floor(size % 6144 === 0 ? size / 6144 : 1 + size / 6144);
  return segments;
}

async function getNextChunk(file, index) {
  const length = file.size - index <= 6144 ? file.size - index : 6144;
  const chunk = file.slice(index, index + length);
  index += length;
  const base64Chunk = await this.base64EncodeAsync(chunk);
  return { base64Chunk, index };
}

async function base64EncodeAsync(chunk) {
  const reader = new FileReader();
  const result = new Promise((resolve, reject) => {
    reader.addEventListener('load',
      () => {
        const base64Chunk = reader.result;
        const cleanChunk = 
          base64Chunk.replace('data:application/octet-stream;base64,', '');
        resolve(cleanChunk);
      },
      false);
    reader.addEventListener('error', reject);
  });
  reader.readAsDataURL(chunk);
  return result;
}
```

## <a name="additional-resources"></a><span data-ttu-id="e8b69-295">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="e8b69-295">Additional resources</span></span>

* [<span data-ttu-id="e8b69-296">Příklad InteropComponent. Razor (dotnet/AspNetCore, úložiště GitHub, větev vydání 3,1)</span><span class="sxs-lookup"><span data-stu-id="e8b69-296">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
