---
title: Volání funkcí JavaScriptu z metod .NET v ASP.NET CoreBlazor
author: guardrex
description: Přečtěte si, jak vyvolat funkce Blazor JavaScriptu z metod .NET v aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: 0c6b6a0a8f88fa912523e7772fcd84ef4ce3b4ff
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977012"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="8dbe2-103">Volání funkcí JavaScriptu z metod .NET v ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="8dbe2-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="8dbe2-104">[Javier Calvarro Nelson](https://github.com/javiercn), Daniel [Roth](https://github.com/danroth27)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8dbe2-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="8dbe2-105">Aplikace Blazor může vyvolat funkce JavaScriptu z metod .NET a .NET z funkcí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="8dbe2-106">Tyto scénáře se nazývají *interoperabilita JavaScriptu* *(JS interop).*</span><span class="sxs-lookup"><span data-stu-id="8dbe2-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="8dbe2-107">Tento článek popisuje vyvolání funkcí JavaScriptu z rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="8dbe2-108">Informace o volání metod .NET z JavaScriptu naleznete v tématu <xref:blazor/call-dotnet-from-javascript>.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="8dbe2-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="8dbe2-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="8dbe2-110">Chcete-li volat do jazyka JavaScript z rozhraní .NET, použijte `IJSRuntime` abstrakce.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-110">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="8dbe2-111">Chcete-li vydat js interop volání, vstříkněte `IJSRuntime` abstrakci ve vaší součásti.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-111">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="8dbe2-112">Metoda `InvokeAsync<T>` přebírá identifikátor funkce JavaScript, který chcete vyvolat spolu s libovolným počtem argumentů JSON-serializovatelných.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-112">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="8dbe2-113">Identifikátor funkce je relativní vzhledem`window`ke globálnímu oboru ( ).</span><span class="sxs-lookup"><span data-stu-id="8dbe2-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="8dbe2-114">Chcete-li volat `window.someScope.someFunction`, identifikátor `someScope.someFunction`je .</span><span class="sxs-lookup"><span data-stu-id="8dbe2-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="8dbe2-115">Není třeba zaregistrovat funkci před tím, než je volána.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="8dbe2-116">Návratový `T` typ musí být také JSON serializovatelný.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="8dbe2-117">`T`by měl odpovídat typu .NET, který nejlépe mapuje na vrácený typ JSON.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="8dbe2-118">U Blazor serverových aplikací s povoleným předběžným vykreslováním není volání do JavaScriptu možné během počátečního předběžného vykreslování.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-118">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="8dbe2-119">Volání interop jazyka JavaScript musí být odloženo až po navázání spojení s prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-119">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="8dbe2-120">Další informace najdete v části [Zjistit, kdy je aplikace Blazor Server předvykačováním.](#detect-when-a-blazor-server-app-is-prerendering)</span><span class="sxs-lookup"><span data-stu-id="8dbe2-120">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="8dbe2-121">Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), dekodér založený na JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-121">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="8dbe2-122">Příklad ukazuje, jak vyvolat funkci Jazyka JavaScript z metody Jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-122">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="8dbe2-123">Funkce JavaScript přijímá bajtové pole z metody Jazyka C#, dekóduje pole a vrací text komponentě pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-123">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="8dbe2-124">Uvnitř `<head>` elementu *wwwroot/index.html* wwwroot/index.htmlBlazor (WebAssembly) nebo *Pages/_Host.cshtml* (Server)Blazor zadejte funkci JavaScriptu, která se používá `TextDecoder` k dekódování předávaného pole a vrácení dekódované hodnoty:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-124">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="8dbe2-125">Kód JavaScriptu, například kód uvedený v předchozím příkladu, lze také načíst ze souboru JavaScriptu (*.js*) s odkazem na soubor skriptu:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-125">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="8dbe2-126">Následující součást:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-126">The following component:</span></span>

* <span data-ttu-id="8dbe2-127">Vyvolá funkci `convertArray` JavaScript `JSRuntime` pomocí funkce komponenty **(Převést pole).**</span><span class="sxs-lookup"><span data-stu-id="8dbe2-127">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="8dbe2-128">Po volání funkce JavaScript je předané pole převedeno na řetězec.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-128">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="8dbe2-129">Řetězec je vrácen součásti pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-129">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="8dbe2-130">Čas spuštění IJS</span><span class="sxs-lookup"><span data-stu-id="8dbe2-130">IJSRuntime</span></span>

<span data-ttu-id="8dbe2-131">Chcete-li `IJSRuntime` použít abstrakci, zachycujte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-131">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="8dbe2-132">Vstříkněte `IJSRuntime` abstrakci do komponenty Razor (*.razor*):</span><span class="sxs-lookup"><span data-stu-id="8dbe2-132">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="8dbe2-133">Uvnitř `<head>` elementu *wwwroot/index.html* wwwroot/index.htmlBlazor ( WebAssembly) nebo *Pages/_Host.cshtml* (Server)Blazor zadejte funkci `handleTickerChanged` JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-133">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="8dbe2-134">Funkce je volána s `IJSRuntime.InvokeVoidAsync` a nevrací hodnotu:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-134">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="8dbe2-135">Vstříkněte `IJSRuntime` abstrakci do třídy (*.cs*):</span><span class="sxs-lookup"><span data-stu-id="8dbe2-135">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="8dbe2-136">Uvnitř `<head>` elementu *wwwroot/index.html* wwwroot/index.htmlBlazor ( WebAssembly) nebo *Pages/_Host.cshtml* (Server)Blazor zadejte funkci `handleTickerChanged` JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-136">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="8dbe2-137">Funkce je volána s `JSRuntime.InvokeAsync` a vrátí hodnotu:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-137">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="8dbe2-138">Pro generování dynamického obsahu s `[Inject]` [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)použijte atribut:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-138">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="8dbe2-139">V ukázkové aplikaci na straně klienta, která doprovází toto téma, jsou k dispozici dvě funkce JavaScriptu pro aplikaci, které interagují s dom přijímat vstup uživatele a zobrazit uvítací zprávu:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-139">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="8dbe2-140">`showPrompt`&ndash; Vytvoří výzvu k přijetí vstupu uživatele (jméno uživatele) a vrátí jméno volajícímu.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-140">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="8dbe2-141">`displayWelcome`&ndash; Přiřadí uvítací zprávu od volajícího objektu `id` DOM s . `welcome`</span><span class="sxs-lookup"><span data-stu-id="8dbe2-141">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="8dbe2-142">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-142">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="8dbe2-143">Umístěte `<script>` značku, která odkazuje na soubor JavaScriptu, do *souboru wwwroot/index.html* (WebAssembly)Blazor nebo *Pages/_Host.cshtml* (Server).Blazor</span><span class="sxs-lookup"><span data-stu-id="8dbe2-143">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="8dbe2-144">*wwwroot/index.html* Blazor (WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="8dbe2-144">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="8dbe2-145">*Stránky/_Host.cshtml* Blazor ( Server):</span><span class="sxs-lookup"><span data-stu-id="8dbe2-145">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="8dbe2-146">Neumisťuj `<script>` značku do `<script>` souboru komponenty, protože ji nelze dynamicky aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-146">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="8dbe2-147">Metody .NET se propojí s funkcemi Jazyka JavaScript v `IJSRuntime.InvokeAsync<T>`souboru *exampleJsInterop.js* voláním .</span><span class="sxs-lookup"><span data-stu-id="8dbe2-147">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="8dbe2-148">Abstrakce `IJSRuntime` je asynchronní, Blazor aby bylo možné scénáře serveru.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-148">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="8dbe2-149">Pokud je aplikace Blazor aplikace WebAssembly a chcete vyvolat funkci JavaScriptu synchronně, svržena do `IJSInProcessRuntime` a volání `Invoke<T>` místo.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-149">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="8dbe2-150">Doporučujeme, aby většina knihoven JS interop používala asynchronní rozhraní API, aby bylo zajištěno, že knihovny jsou k dispozici ve všech scénářích.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-150">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="8dbe2-151">Ukázková aplikace obsahuje komponentu pro demonstraci js interop.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-151">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="8dbe2-152">Součást:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-152">The component:</span></span>

* <span data-ttu-id="8dbe2-153">Přijímá vstup uživatele prostřednictvím výzvy javascriptu.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-153">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="8dbe2-154">Vrátí text komponentě ke zpracování.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-154">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="8dbe2-155">Volá druhou funkci JavaScriptu, která interaguje s DOM zobrazit uvítací zprávu.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-155">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="8dbe2-156">*Stránky/JSInterop.břitva*:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-156">*Pages/JSInterop.razor*:</span></span>

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
        var name = await JSRuntime.InvokeAsync<string>(
                "exampleJsFunctions.showPrompt",
                "What's your name?");

        await JSRuntime.InvokeVoidAsync(
                "exampleJsFunctions.displayWelcome",
                $"Hello {name}! Welcome to Blazor!");
    }
}
```

1. <span data-ttu-id="8dbe2-157">Když `TriggerJsPrompt` je spuštěn a výběrem komponenty **Trigger JavaScript** Prompt `showPrompt` tlačítko, javascript funkce uvedené v souboru *wwwroot/exampleJsInterop.js* se nazývá.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-157">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="8dbe2-158">Funkce `showPrompt` přijímá vstup uživatele (jméno uživatele), který je kódován html a vrácen do komponenty.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-158">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="8dbe2-159">Komponenta ukládá jméno uživatele do místní `name`proměnné .</span><span class="sxs-lookup"><span data-stu-id="8dbe2-159">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="8dbe2-160">Řetězec uložený `name` v aplikaci je začleněn do uvítací zprávy, `displayWelcome`která je předána funkci JavaScript , která vykresluje uvítací zprávu do značky nadpisu.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-160">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="8dbe2-161">Volání prázdné funkce JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="8dbe2-161">Call a void JavaScript function</span></span>

<span data-ttu-id="8dbe2-162">Funkce JavaScriptu, které vracejí [void(0)/void](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) `IJSRuntime.InvokeVoidAsync`0 nebo [undefined,](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) jsou volány pomocí .</span><span class="sxs-lookup"><span data-stu-id="8dbe2-162">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-opno-locblazor-server-app-is-prerendering"></a><span data-ttu-id="8dbe2-163">Zjištění předběžného Blazor vykreslování aplikace Server</span><span class="sxs-lookup"><span data-stu-id="8dbe2-163">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="8dbe2-164">Zachycení odkazů na prvky</span><span class="sxs-lookup"><span data-stu-id="8dbe2-164">Capture references to elements</span></span>

<span data-ttu-id="8dbe2-165">Některé scénáře interop JS vyžadují odkazy na elementy HTML.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-165">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="8dbe2-166">Knihovna rozhraní může například vyžadovat odkaz na prvek pro inicializaci nebo může být nutné `focus` volat `play`rozhraní API podobné příkazu na elementu, například nebo .</span><span class="sxs-lookup"><span data-stu-id="8dbe2-166">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="8dbe2-167">Zachyťte odkazy na elementy HTML v komponentě pomocí následujícího přístupu:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-167">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="8dbe2-168">Přidejte `@ref` atribut do elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-168">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="8dbe2-169">Definujte pole `ElementReference` typu, jehož název `@ref` odpovídá hodnotě atributu.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-169">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="8dbe2-170">Následující příklad ukazuje zachycení odkazu `username` `<input>` na prvek:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-170">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="8dbe2-171">Odkaz na prvek použijte pouze k mutu obsahu prázdného Blazorprvku, který neinteraguje .</span><span class="sxs-lookup"><span data-stu-id="8dbe2-171">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="8dbe2-172">Tento scénář je užitečné, když rozhraní API třetí strany poskytuje obsah prvku.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-172">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="8dbe2-173">Protože Blazor neinteraguje s elementem, neexistuje žádná Blazormožnost konfliktu mezi 's reprezentace prvku a DOM.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-173">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="8dbe2-174">V následujícím příkladu je *nebezpečné* zmutovat obsah neuspořádaného seznamu`ul`( ), protože Blazor spolupracuje s dom`<li>`naplnit položky seznamu tohoto prvku ( ):</span><span class="sxs-lookup"><span data-stu-id="8dbe2-174">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="8dbe2-175">Pokud JS interop mutuje `MyList` obsah Blazor prvku a pokusí se použít rozdíly na prvek, rozdíly nebude odpovídat DOM.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-175">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="8dbe2-176">Pokud jde o kód .NET, a `ElementReference` je neprůhledný popisovač.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-176">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="8dbe2-177">*Jediné,* co můžete `ElementReference` udělat s je předat ji do kódu JavaScript přes JS interop.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-177">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="8dbe2-178">Pokud tak učiníte, kód na straně `HTMLElement` JavaScript obdrží instanci, kterou lze použít s normální mise DOM API.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-178">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="8dbe2-179">Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na prvek:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-179">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="8dbe2-180">*exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-180">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="8dbe2-181">Chcete-li volat funkci JavaScriptu, která `IJSRuntime.InvokeVoidAsync`nevrací hodnotu, použijte .</span><span class="sxs-lookup"><span data-stu-id="8dbe2-181">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="8dbe2-182">Následující kód nastaví zaměření na vstup uživatelského jména voláním předchozí `ElementReference`funkce JavaScriptu se zachyceným :</span><span class="sxs-lookup"><span data-stu-id="8dbe2-182">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="8dbe2-183">Chcete-li použít metodu rozšíření, vytvořte `IJSRuntime` statickou metodu rozšíření, která přijímá instanci:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-183">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="8dbe2-184">Metoda `Focus` je volána přímo na objekt.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-184">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="8dbe2-185">Následující příklad předpokládá, `Focus` že metoda je `JsInteropClasses` k dispozici z oboru názvů:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-185">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="8dbe2-186">Proměnná `username` je naplněna pouze po vykreslení komponenty.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-186">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="8dbe2-187">Pokud je nevyplněný `ElementReference` předán kódu JavaScriptu, kód JavaScript `null`obdrží hodnotu .</span><span class="sxs-lookup"><span data-stu-id="8dbe2-187">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="8dbe2-188">Chcete-li manipulovat s odkazy na elementy po dokončení vykreslování komponenty (chcete-li nastavit počáteční fokus na elementu), použijte [metody životního cyklu součásti OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="8dbe2-188">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="8dbe2-189">Při práci s obecnými typy a vrácení hodnoty použijte [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span><span class="sxs-lookup"><span data-stu-id="8dbe2-189">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="8dbe2-190">`GenericMethod`je volána přímo na objekt s typem.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-190">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="8dbe2-191">Následující příklad předpokládá, `GenericMethod` že je `JsInteropClasses` k dispozici z oboru názvů:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-191">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="8dbe2-192">Referenční prvky napříč součástmi</span><span class="sxs-lookup"><span data-stu-id="8dbe2-192">Reference elements across components</span></span>

<span data-ttu-id="8dbe2-193">A `ElementReference` je zaručena pouze platné `OnAfterRender` v metodě komponenty `struct`(a odkaz na prvek je ), takže odkaz na prvek nelze předat mezi součástmi.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-193">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="8dbe2-194">Pro nadřazenou komponentu, která zpřístupní odkaz na element pro jiné součásti, může nadřazená součást:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-194">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="8dbe2-195">Povolit podřízeným součástem registrovat zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-195">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="8dbe2-196">Vyvolat registrovaná zpětná `OnAfterRender` volání během události s odkazem na předávaný element.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-196">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="8dbe2-197">Nepřímo tento přístup umožňuje podřízené součásti k interakci s odkazem na prvek nadřazené.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-197">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="8dbe2-198">Následující Blazor příklad websestavy ilustruje přístup.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-198">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="8dbe2-199">V `<head>` *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-199">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="8dbe2-200">V `<body>` *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-200">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="8dbe2-201">*Stránky/Index.razor* (nadřazená součást):</span><span class="sxs-lookup"><span data-stu-id="8dbe2-201">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="_title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="8dbe2-202">*Stránky/Index.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-202">*Pages/Index.razor.cs*:</span></span>

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

<span data-ttu-id="8dbe2-203">*Shared/SurveyPrompt.razor* (podřízená součást):</span><span class="sxs-lookup"><span data-stu-id="8dbe2-203">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="8dbe2-204">*Sdílené/SurveyPrompt.razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-204">*Shared/SurveyPrompt.razor.cs*:</span></span>

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

## <a name="harden-js-interop-calls"></a><span data-ttu-id="8dbe2-205">Harden JS interop volání</span><span class="sxs-lookup"><span data-stu-id="8dbe2-205">Harden JS interop calls</span></span>

<span data-ttu-id="8dbe2-206">JS interop může selhat z důvodu chyby sítě a by měly být považovány za nespolehlivé.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-206">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="8dbe2-207">Ve výchozím Blazor nastavení aplikace Server začasový limit js interop volání na serveru po jedné minutě.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-207">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="8dbe2-208">Pokud aplikace může tolerovat agresivnější časový rozsah, například 10 sekund, nastavte časový čas pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-208">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="8dbe2-209">Globálně v `Startup.ConfigureServices`, zadejte časový čas:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-209">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="8dbe2-210">Vyvolání v kódu komponenty, jedno volání může určit časový čas:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-210">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="8dbe2-211">Další informace o vyčerpání prostředků <xref:security/blazor/server>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="8dbe2-211">For more information on resource exhaustion, see <xref:security/blazor/server>.</span></span>

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="8dbe2-212">Vyhněte se cyklické odkazy na objekty</span><span class="sxs-lookup"><span data-stu-id="8dbe2-212">Avoid circular object references</span></span>

<span data-ttu-id="8dbe2-213">Objekty, které obsahují cyklické odkazy nelze serializovat na straně klienta pro:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-213">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="8dbe2-214">Volání metody .NET.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-214">.NET method calls.</span></span>
* <span data-ttu-id="8dbe2-215">JavaScript metoda volá z Jazyka C#, pokud návratový typ má cyklické odkazy.</span><span class="sxs-lookup"><span data-stu-id="8dbe2-215">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="8dbe2-216">Další informace naleznete v následujících problémech:</span><span class="sxs-lookup"><span data-stu-id="8dbe2-216">For more information, see the following issues:</span></span>

* [<span data-ttu-id="8dbe2-217">Cyklické odkazy nejsou podporovány, trvat dva (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="8dbe2-217">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="8dbe2-218">Návrh: Přidání mechanismu pro zpracování cyklické odkazy při serializace (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="8dbe2-218">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="8dbe2-219">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="8dbe2-219">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="8dbe2-220">Příklad InteropComponent.razor (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span><span class="sxs-lookup"><span data-stu-id="8dbe2-220">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="8dbe2-221">[Provádění velkých přenosů dat v Blazor serverových aplikacích](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="8dbe2-221">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
