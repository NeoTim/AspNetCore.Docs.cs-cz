---
title: Zprostředkovatel komunikace s ASP.NET Core Blazor JavaScript
author: guardrex
description: Naučte se volat funkce jazyka JavaScript z metod .NET a .NET z JavaScriptu v aplikacích Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/javascript-interop
ms.openlocfilehash: e1b9c84dace193768c6f3fbb5636ef675d65a20d
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159890"
---
# <a name="aspnet-core-opno-locblazor-javascript-interop"></a><span data-ttu-id="8c145-103">Zprostředkovatel komunikace s ASP.NET Core Blazor JavaScript</span><span class="sxs-lookup"><span data-stu-id="8c145-103">ASP.NET Core Blazor JavaScript interop</span></span>

<span data-ttu-id="8c145-104">[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Skořepa](https://github.com/danroth27)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8c145-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="8c145-105">Blazor aplikace může vyvolat JavaScriptové funkce z metod .NET a .NET z kódu JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="8c145-105">A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code.</span></span>

<span data-ttu-id="8c145-106">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8c145-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="invoke-javascript-functions-from-net-methods"></a><span data-ttu-id="8c145-107">Vyvolání funkcí jazyka JavaScript z metod .NET</span><span class="sxs-lookup"><span data-stu-id="8c145-107">Invoke JavaScript functions from .NET methods</span></span>

<span data-ttu-id="8c145-108">Existují situace, kdy je pro volání funkce JavaScriptu vyžadován kód .NET.</span><span class="sxs-lookup"><span data-stu-id="8c145-108">There are times when .NET code is required to call a JavaScript function.</span></span> <span data-ttu-id="8c145-109">Volání JavaScriptu může například vystavit možnosti prohlížeče nebo funkce z knihovny JavaScriptu do aplikace.</span><span class="sxs-lookup"><span data-stu-id="8c145-109">For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the app.</span></span> <span data-ttu-id="8c145-110">Tento scénář se nazývá *interoperabilita JavaScriptu* (interoperabilita*js*).</span><span class="sxs-lookup"><span data-stu-id="8c145-110">This scenario is called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="8c145-111">Chcete-li volat do JavaScriptu z rozhraní .NET, použijte `IJSRuntime` abstrakce.</span><span class="sxs-lookup"><span data-stu-id="8c145-111">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="8c145-112">Chcete-li vydat volání Interop v JS, zapište `IJSRuntime` abstrakce do komponenty.</span><span class="sxs-lookup"><span data-stu-id="8c145-112">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="8c145-113">Metoda `InvokeAsync<T>` přebírá identifikátor pro funkci JavaScriptu, kterou chcete vyvolat, spolu s libovolným počtem argumentů serializovatelných pomocí JSON.</span><span class="sxs-lookup"><span data-stu-id="8c145-113">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="8c145-114">Identifikátor funkce je relativní vzhledem k globálnímu oboru (`window`).</span><span class="sxs-lookup"><span data-stu-id="8c145-114">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="8c145-115">Pokud chcete volat `window.someScope.someFunction`, je identifikátor `someScope.someFunction`.</span><span class="sxs-lookup"><span data-stu-id="8c145-115">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="8c145-116">Před voláním funkce není nutné ji registrovat.</span><span class="sxs-lookup"><span data-stu-id="8c145-116">There's no need to register the function before it's called.</span></span> <span data-ttu-id="8c145-117">Návratový typ `T` musí být také serializovatelný jako JSON.</span><span class="sxs-lookup"><span data-stu-id="8c145-117">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="8c145-118">`T` by měl odpovídat typu .NET, který se nejlépe mapuje na vrácený typ JSON.</span><span class="sxs-lookup"><span data-stu-id="8c145-118">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="8c145-119">Pro Blazor serverových aplikací s povoleným předvykreslováním není možné volat do JavaScriptu během prvotního předgenerování.</span><span class="sxs-lookup"><span data-stu-id="8c145-119">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="8c145-120">Volání interoperability JavaScriptu musí být odložena až po navázání spojení s prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="8c145-120">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="8c145-121">Další informace najdete v části [zjištění, kdy je aplikace Blazor předvykreslování](#detect-when-a-blazor-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="8c145-121">For more information, see the [Detect when a Blazor app is prerendering](#detect-when-a-blazor-app-is-prerendering) section.</span></span>

<span data-ttu-id="8c145-122">Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), experimentálním dekodéru založeném na JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="8c145-122">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), an experimental JavaScript-based decoder.</span></span> <span data-ttu-id="8c145-123">Příklad ukazuje, jak vyvolat funkci JavaScriptu z C# metody.</span><span class="sxs-lookup"><span data-stu-id="8c145-123">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="8c145-124">Funkce JavaScriptu přijímá bajtové pole z C# metody, dekóduje pole a vrátí text do komponenty k zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8c145-124">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="8c145-125">Uvnitř prvku `<head>` *wwwroot/index.html* (Blazor WebAssembly) nebo *pages/_Host. cshtml* (Blazor Server) zadejte funkci javascriptu, která používá `TextDecoder` k dekódování předaného pole a vrácení dekódovaný hodnoty:</span><span class="sxs-lookup"><span data-stu-id="8c145-125">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](javascript-interop/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="8c145-126">Kód jazyka JavaScript, jako je například kód zobrazený v předchozím příkladu, lze také načíst ze souboru JavaScriptu ( *. js*) s odkazem na soubor skriptu:</span><span class="sxs-lookup"><span data-stu-id="8c145-126">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="8c145-127">Následující součást:</span><span class="sxs-lookup"><span data-stu-id="8c145-127">The following component:</span></span>

* <span data-ttu-id="8c145-128">Vyvolá funkci `convertArray` JavaScript pomocí `JSRuntime` při výběru tlačítka komponenty (**převést pole**).</span><span class="sxs-lookup"><span data-stu-id="8c145-128">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="8c145-129">Po volání funkce JavaScriptu je předané pole převedeno na řetězec.</span><span class="sxs-lookup"><span data-stu-id="8c145-129">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="8c145-130">Řetězec se vrátí do komponenty pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8c145-130">The string is returned to the component for display.</span></span>

[!code-razor[](javascript-interop/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="use-of-ijsruntime"></a><span data-ttu-id="8c145-131">Použití IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="8c145-131">Use of IJSRuntime</span></span>

<span data-ttu-id="8c145-132">Chcete-li použít abstrakci `IJSRuntime`, přijímají některé z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="8c145-132">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="8c145-133">Vložit abstrakci `IJSRuntime` do komponenty Razor ( *. Razor*):</span><span class="sxs-lookup"><span data-stu-id="8c145-133">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](javascript-interop/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="8c145-134">Uvnitř prvku `<head>` *wwwroot/index.html* (Blazor WebAssembly) nebo *pages/_Host. cshtml* (Blazor Server) zadejte funkci JavaScriptu pro `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="8c145-134">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="8c145-135">Funkce je volána pomocí `IJSRuntime.InvokeVoidAsync` a nevrací hodnotu:</span><span class="sxs-lookup"><span data-stu-id="8c145-135">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="8c145-136">Vložit abstrakci `IJSRuntime` do třídy ( *. cs*):</span><span class="sxs-lookup"><span data-stu-id="8c145-136">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](javascript-interop/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="8c145-137">Uvnitř prvku `<head>` *wwwroot/index.html* (Blazor WebAssembly) nebo *pages/_Host. cshtml* (Blazor Server) zadejte funkci JavaScriptu pro `handleTickerChanged`.</span><span class="sxs-lookup"><span data-stu-id="8c145-137">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="8c145-138">Funkce je volána pomocí `JSRuntime.InvokeAsync` a vrací hodnotu:</span><span class="sxs-lookup"><span data-stu-id="8c145-138">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](javascript-interop/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="8c145-139">Pro generování dynamického obsahu pomocí [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic)použijte atribut `[Inject]`:</span><span class="sxs-lookup"><span data-stu-id="8c145-139">For dynamic content generation with [BuildRenderTree](xref:blazor/components#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="8c145-140">V ukázkové aplikaci na straně klienta, která doprovází toto téma, jsou k dispozici dvě funkce JavaScriptu pro aplikaci, která komunikuje s modelem DOM pro příjem vstupu uživatele a zobrazení uvítací zprávy:</span><span class="sxs-lookup"><span data-stu-id="8c145-140">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="8c145-141">`showPrompt` &ndash; vytvoří výzvu k přijetí vstupu uživatele (jméno uživatele) a vrátí název volajícímu.</span><span class="sxs-lookup"><span data-stu-id="8c145-141">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="8c145-142">`displayWelcome` &ndash; přiřadí uvítací zprávu od volajícího k objektu modelu DOM pomocí `id` `welcome`.</span><span class="sxs-lookup"><span data-stu-id="8c145-142">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="8c145-143">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="8c145-143">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="8c145-144">Umístěte značku `<script>`, která odkazuje na soubor JavaScriptu v souboru *wwwroot/index.html* (Blazor sestavení) nebo *stránky/_Host. cshtml* (ServerBlazor).</span><span class="sxs-lookup"><span data-stu-id="8c145-144">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="8c145-145">*wwwroot/index.html* (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="8c145-145">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=15)]

<span data-ttu-id="8c145-146">*Pages/_Host. cshtml* (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="8c145-146">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=21)]

<span data-ttu-id="8c145-147">Neumísťujte značku `<script>` do souboru komponenty, protože značku `<script>` nejde dynamicky aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="8c145-147">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="8c145-148">Metody .NET spolupracuje s funkcemi JavaScriptu v souboru *exampleJsInterop. js* voláním `IJSRuntime.InvokeAsync<T>`.</span><span class="sxs-lookup"><span data-stu-id="8c145-148">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="8c145-149">Abstrakce `IJSRuntime` je asynchronní, aby umožňovala scénáře Blazor serveru.</span><span class="sxs-lookup"><span data-stu-id="8c145-149">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="8c145-150">Pokud je aplikace Blazor aplikace WebAssembly a chcete vyvolat funkci JavaScriptu synchronně, přetypování downcast na `IJSInProcessRuntime` a místo toho volat `Invoke<T>`.</span><span class="sxs-lookup"><span data-stu-id="8c145-150">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="8c145-151">Doporučujeme, aby většina knihoven spolupráce v JS používala asynchronní rozhraní API, aby bylo zajištěno, že jsou knihovny dostupné ve všech scénářích.</span><span class="sxs-lookup"><span data-stu-id="8c145-151">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="8c145-152">Ukázková aplikace obsahuje komponentu, která předvádí interoperabilitu JS.</span><span class="sxs-lookup"><span data-stu-id="8c145-152">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="8c145-153">Součást:</span><span class="sxs-lookup"><span data-stu-id="8c145-153">The component:</span></span>

* <span data-ttu-id="8c145-154">Přijímá vstup uživatele prostřednictvím výzvy JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="8c145-154">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="8c145-155">Vrátí text do komponenty pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="8c145-155">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="8c145-156">Volá druhou funkci JavaScriptu, která komunikuje s modelem DOM, aby zobrazila úvodní zprávu.</span><span class="sxs-lookup"><span data-stu-id="8c145-156">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="8c145-157">*Pages/JSInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="8c145-157">*Pages/JSInterop.razor*:</span></span>

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

1. <span data-ttu-id="8c145-158">Po spuštění `TriggerJsPrompt` kliknutím na tlačítko **výzvy JavaScriptu triggeru** komponenty se zavolá funkce JavaScriptu `showPrompt`, která je k dispozici v souboru *wwwroot/exampleJsInterop. js* .</span><span class="sxs-lookup"><span data-stu-id="8c145-158">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="8c145-159">Funkce `showPrompt` akceptuje vstup uživatele (uživatelské jméno), což je kódování HTML, které se vrátí do komponenty.</span><span class="sxs-lookup"><span data-stu-id="8c145-159">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="8c145-160">Komponenta ukládá jméno uživatele do místní proměnné `name`.</span><span class="sxs-lookup"><span data-stu-id="8c145-160">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="8c145-161">Řetězec uložený v `name` je součástí uvítací zprávy, která je předána funkci JavaScriptu, `displayWelcome`, která vykresluje uvítací zprávu do značky nadpisu.</span><span class="sxs-lookup"><span data-stu-id="8c145-161">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="8c145-162">Volání funkce void JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="8c145-162">Call a void JavaScript function</span></span>

<span data-ttu-id="8c145-163">Funkce jazyka JavaScript, které vracejí [typ void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) nebo [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) , jsou volány pomocí `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="8c145-163">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-app-is-prerendering"></a><span data-ttu-id="8c145-164">Rozpoznat, kdy se předchází aplikace Blazor</span><span class="sxs-lookup"><span data-stu-id="8c145-164">Detect when a Blazor app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="8c145-165">Zachytit odkazy na elementy</span><span class="sxs-lookup"><span data-stu-id="8c145-165">Capture references to elements</span></span>

<span data-ttu-id="8c145-166">Některé scénáře interoperability JS vyžadují odkazy na prvky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="8c145-166">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="8c145-167">Například knihovna uživatelského rozhraní může vyžadovat odkaz na element pro inicializaci nebo může být nutné volat rozhraní API pro příkazy, například `focus` nebo `play`.</span><span class="sxs-lookup"><span data-stu-id="8c145-167">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="8c145-168">Zachyťte odkazy na prvky HTML v součásti pomocí následujícího postupu:</span><span class="sxs-lookup"><span data-stu-id="8c145-168">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="8c145-169">Přidejte atribut `@ref` do elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="8c145-169">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="8c145-170">Definujte pole typu `ElementReference`, jehož název odpovídá hodnotě atributu `@ref`.</span><span class="sxs-lookup"><span data-stu-id="8c145-170">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="8c145-171">Následující příklad ukazuje, jak zachytit odkaz na prvek `username` `<input>`:</span><span class="sxs-lookup"><span data-stu-id="8c145-171">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="8c145-172">Pouze odkaz na element lze použít k použití obsahu prázdného prvku, který nekomunikuje s Blazor.</span><span class="sxs-lookup"><span data-stu-id="8c145-172">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="8c145-173">Tento scénář je užitečný, když rozhraní API třetí strany poskytuje obsah do elementu.</span><span class="sxs-lookup"><span data-stu-id="8c145-173">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="8c145-174">Vzhledem k tomu, že Blazor nekomunikuje s prvkem, neexistuje žádná možnost konfliktu mezi reprezentací Blazorelementu a DOM.</span><span class="sxs-lookup"><span data-stu-id="8c145-174">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="8c145-175">V následujícím příkladu je *nebezpečné* obsah neuspořádaného seznamu (`ul`), protože Blazor vzájemně spolupracuje s DOM k naplnění položek seznamu tohoto elementu (`<li>`):</span><span class="sxs-lookup"><span data-stu-id="8c145-175">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="8c145-176">Pokud interoperabilita JS podílí obsah elementu `MyList` a Blazor se pokusí použít rozdíly pro prvek, rozdíly nebudou odpovídat modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="8c145-176">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="8c145-177">Pokud se jedná o kód .NET, `ElementReference` je neprůhledný popisovač.</span><span class="sxs-lookup"><span data-stu-id="8c145-177">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="8c145-178">*Jediná* věc, kterou můžete provést s `ElementReference`, je předána do kódu JavaScriptu přes interoperabilitu js.</span><span class="sxs-lookup"><span data-stu-id="8c145-178">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="8c145-179">V takovém případě kód na straně JavaScriptu obdrží instanci `HTMLElement`, kterou lze použít s normálními rozhraními API DOM.</span><span class="sxs-lookup"><span data-stu-id="8c145-179">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="8c145-180">Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na prvek:</span><span class="sxs-lookup"><span data-stu-id="8c145-180">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="8c145-181">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="8c145-181">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="8c145-182">Chcete-li zavolat funkci JavaScriptu, která nevrací hodnotu, použijte `IJSRuntime.InvokeVoidAsync`.</span><span class="sxs-lookup"><span data-stu-id="8c145-182">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="8c145-183">Následující kód nastaví fokus na zadání uživatelského jména voláním předchozí funkce JavaScriptu s zachyceným `ElementReference`:</span><span class="sxs-lookup"><span data-stu-id="8c145-183">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="8c145-184">Chcete-li použít metodu rozšíření, vytvořte statickou metodu rozšíření, která obdrží instanci `IJSRuntime`:</span><span class="sxs-lookup"><span data-stu-id="8c145-184">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="8c145-185">Metoda `Focus` je volána přímo na objektu.</span><span class="sxs-lookup"><span data-stu-id="8c145-185">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="8c145-186">Následující příklad předpokládá, že `Focus` metoda je k dispozici z oboru názvů `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="8c145-186">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="8c145-187">Proměnná `username` se naplní až po vykreslení komponenty.</span><span class="sxs-lookup"><span data-stu-id="8c145-187">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="8c145-188">Pokud je vyplněný `ElementReference` předán kódu jazyka JavaScript, kód jazyka JavaScript obdrží hodnotu `null`.</span><span class="sxs-lookup"><span data-stu-id="8c145-188">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="8c145-189">Chcete-li manipulovat s odkazy na elementy po dokončení vykreslování komponenty (pro nastavení prvotního zaměření na prvek), použijte [metody životního cyklu komponenty OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="8c145-189">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="8c145-190">Při práci s obecnými typy a vrácení hodnoty použijte [ValueTask\<t >](xref:System.Threading.Tasks.ValueTask`1):</span><span class="sxs-lookup"><span data-stu-id="8c145-190">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="8c145-191">`GenericMethod` se volá přímo na objekt s typem.</span><span class="sxs-lookup"><span data-stu-id="8c145-191">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="8c145-192">Následující příklad předpokládá, že `GenericMethod` je k dispozici z oboru názvů `JsInteropClasses`:</span><span class="sxs-lookup"><span data-stu-id="8c145-192">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](javascript-interop/samples_snapshot/component3.razor?highlight=17)]

## <a name="invoke-net-methods-from-javascript-functions"></a><span data-ttu-id="8c145-193">Vyvolat metody .NET z funkcí JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="8c145-193">Invoke .NET methods from JavaScript functions</span></span>

### <a name="static-net-method-call"></a><span data-ttu-id="8c145-194">Statické volání metody .NET</span><span class="sxs-lookup"><span data-stu-id="8c145-194">Static .NET method call</span></span>

<span data-ttu-id="8c145-195">Chcete-li vyvolat statickou metodu .NET z JavaScriptu, použijte funkce `DotNet.invokeMethod` nebo `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="8c145-195">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="8c145-196">Předejte identifikátor statické metody, kterou chcete volat, název sestavení obsahující funkce a všechny argumenty.</span><span class="sxs-lookup"><span data-stu-id="8c145-196">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="8c145-197">Asynchronní verze je preferována k podpoře Blazorch scénářů serveru.</span><span class="sxs-lookup"><span data-stu-id="8c145-197">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="8c145-198">Pro vyvolání metody .NET z JavaScriptu musí být metoda .NET veřejná, statická a mít atribut `[JSInvokable]`.</span><span class="sxs-lookup"><span data-stu-id="8c145-198">To invoke a .NET method from JavaScript, the .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="8c145-199">Ve výchozím nastavení je identifikátor metody název metody, ale můžete zadat jiný identifikátor pomocí `JSInvokableAttribute` konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="8c145-199">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor.</span></span> <span data-ttu-id="8c145-200">Volání otevřených obecných metod není aktuálně podporováno.</span><span class="sxs-lookup"><span data-stu-id="8c145-200">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="8c145-201">Ukázková aplikace obsahuje C# metodu pro vrácení pole `int`s.</span><span class="sxs-lookup"><span data-stu-id="8c145-201">The sample app includes a C# method to return an array of `int`s.</span></span> <span data-ttu-id="8c145-202">Atribut `JSInvokable` se aplikuje na metodu.</span><span class="sxs-lookup"><span data-stu-id="8c145-202">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="8c145-203">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="8c145-203">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="8c145-204">JavaScript, který obsluhuje klient, vyvolá C# metodu .NET.</span><span class="sxs-lookup"><span data-stu-id="8c145-204">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="8c145-205">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="8c145-205">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="8c145-206">Pokud je vybráno tlačítko **aktivovat .NET static Method ReturnArrayAsync** , Projděte si výstup konzoly v webových vývojářských nástrojích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="8c145-206">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="8c145-207">Výstup konzoly:</span><span class="sxs-lookup"><span data-stu-id="8c145-207">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="8c145-208">Hodnota čtvrtého pole je vložena do pole (`data.push(4);`) vráceného `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="8c145-208">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

### <a name="instance-method-call"></a><span data-ttu-id="8c145-209">Volání metody instance</span><span class="sxs-lookup"><span data-stu-id="8c145-209">Instance method call</span></span>

<span data-ttu-id="8c145-210">Můžete také volat metody instance rozhraní .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="8c145-210">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="8c145-211">Vyvolání metody instance rozhraní .NET z JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="8c145-211">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="8c145-212">Předejte instanci rozhraní .NET do JavaScriptu tak, že ji zabalíte do instance `DotNetObjectReference`.</span><span class="sxs-lookup"><span data-stu-id="8c145-212">Pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectReference` instance.</span></span> <span data-ttu-id="8c145-213">Instance rozhraní .NET je předána odkazem na JavaScript.</span><span class="sxs-lookup"><span data-stu-id="8c145-213">The .NET instance is passed by reference to JavaScript.</span></span>
* <span data-ttu-id="8c145-214">Vyvolat metody instance .NET v instanci pomocí funkcí `invokeMethod` nebo `invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="8c145-214">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="8c145-215">Instance rozhraní .NET může být také předána jako argument při vyvolání jiných metod rozhraní .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="8c145-215">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="8c145-216">Ukázková aplikace protokoluje zprávy do konzoly na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="8c145-216">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="8c145-217">Pro následující příklady znázorněné ukázkovou aplikací si Projděte výstup konzoly v prohlížeči v vývojářských nástrojích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="8c145-217">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="8c145-218">Když je vybráno tlačítko **aktivovat metodu instance .NET HelloHelper. sayHello** , je volána `ExampleJsInterop.CallHelloHelperSayHello` a předá do metody název, `Blazor`.</span><span class="sxs-lookup"><span data-stu-id="8c145-218">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="8c145-219">*Pages/JsInterop.razor*:</span><span class="sxs-lookup"><span data-stu-id="8c145-219">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="8c145-220">`CallHelloHelperSayHello` vyvolá funkci JavaScriptu `sayHello` s novou instancí `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="8c145-220">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="8c145-221">*JsInteropClasses/ExampleJsInterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="8c145-221">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

<span data-ttu-id="8c145-222">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="8c145-222">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="8c145-223">Název je předán konstruktoru `HelloHelper`, který nastaví vlastnost `HelloHelper.Name`.</span><span class="sxs-lookup"><span data-stu-id="8c145-223">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="8c145-224">Je-li funkce JavaScriptu `sayHello` spuštěna, vrátí `HelloHelper.SayHello` zprávu `Hello, {Name}!`, která je zapsána do konzoly funkcí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="8c145-224">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="8c145-225">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c145-225">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="8c145-226">Výstup konzoly v vývojářských nástrojích webu v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="8c145-226">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="8c145-227">Sdílení kódu spolupráce v knihovně tříd</span><span class="sxs-lookup"><span data-stu-id="8c145-227">Share interop code in a class library</span></span>

<span data-ttu-id="8c145-228">Kód interoperability JS lze zahrnout do knihovny tříd, která umožňuje sdílení kódu v balíčku NuGet.</span><span class="sxs-lookup"><span data-stu-id="8c145-228">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="8c145-229">Knihovna tříd zpracovává vložení prostředků JavaScriptu do sestaveného sestavení.</span><span class="sxs-lookup"><span data-stu-id="8c145-229">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="8c145-230">Soubory JavaScriptu jsou umístěny ve složce *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="8c145-230">The JavaScript files are placed in the *wwwroot* folder.</span></span> <span data-ttu-id="8c145-231">Nástroj při sestavování knihovny postará o vkládání prostředků.</span><span class="sxs-lookup"><span data-stu-id="8c145-231">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="8c145-232">Na sestavený balíček NuGet se odkazuje v souboru projektu aplikace stejným způsobem, jako na který se odkazuje na balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="8c145-232">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="8c145-233">Po obnovení balíčku kód aplikace může zavolat do JavaScriptu, jako kdyby byl C#.</span><span class="sxs-lookup"><span data-stu-id="8c145-233">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="8c145-234">Další informace najdete v tématu <xref:blazor/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="8c145-234">For more information, see <xref:blazor/class-libraries>.</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="8c145-235">Volání interoperability pro posílení JS</span><span class="sxs-lookup"><span data-stu-id="8c145-235">Harden JS interop calls</span></span>

<span data-ttu-id="8c145-236">Interoperabilita JS může selhat kvůli chybám sítě a měla by být považována za nespolehlivou.</span><span class="sxs-lookup"><span data-stu-id="8c145-236">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="8c145-237">Ve výchozím nastavení aplikace Blazor serveru vyprší voláním interoperability JS na serveru po jedné minutě.</span><span class="sxs-lookup"><span data-stu-id="8c145-237">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="8c145-238">Pokud aplikace může tolerovat více agresivního časového limitu, například 10 sekund, nastavte časový limit pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="8c145-238">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="8c145-239">V `Startup.ConfigureServices`globálně v poli zadejte časový limit:</span><span class="sxs-lookup"><span data-stu-id="8c145-239">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="8c145-240">Za vyvolání v kódu komponenty může jedno volání zadat časový limit:</span><span class="sxs-lookup"><span data-stu-id="8c145-240">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="8c145-241">Další informace o vyčerpání prostředků naleznete v tématu <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="8c145-241">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8c145-242">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="8c145-242">Additional resources</span></span>

* [<span data-ttu-id="8c145-243">Příklad InteropComponent. Razor (dotnet/AspNetCore, úložiště GitHub, větev vydání 3,1)</span><span class="sxs-lookup"><span data-stu-id="8c145-243">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
