---
title: Volání funkcí jazyka JavaScript z metod .NET v ASP.NET Core Blazor
author: guardrex
description: Naučte se vyvolat funkce JavaScriptu z metod .NET v Blazor aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-javascript-from-dotnet
ms.openlocfilehash: d36140067ba6e75f2d00cb86ea488e40d28bd86f
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762162"
---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-no-locblazor"></a><span data-ttu-id="a9867-103">Volání funkcí jazyka JavaScript z metod .NET v ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="a9867-103">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="a9867-104">[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Skořepa](https://github.com/danroth27)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a9867-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a9867-105">BlazorAplikace může vyvolat funkce JavaScriptu z metod .NET a metod .NET z funkcí jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a9867-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="a9867-106">Tyto scénáře se nazývají *interoperabilita JavaScriptu* (interoperabilita*js*).</span><span class="sxs-lookup"><span data-stu-id="a9867-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="a9867-107">Tento článek popisuje vyvolání funkcí jazyka JavaScript z rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="a9867-107">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="a9867-108">Informace o volání metod .NET z JavaScriptu naleznete v tématu <xref:blazor/call-dotnet-from-javascript> .</span><span class="sxs-lookup"><span data-stu-id="a9867-108">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="a9867-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a9867-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a9867-110">Chcete-li volat do JavaScriptu z rozhraní .NET, použijte <xref:Microsoft.JSInterop.IJSRuntime> abstrakci.</span><span class="sxs-lookup"><span data-stu-id="a9867-110">To call into JavaScript from .NET, use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction.</span></span> <span data-ttu-id="a9867-111">Chcete-li vydat volání interoperability JS, zapište <xref:Microsoft.JSInterop.IJSRuntime> abstrakci do komponenty.</span><span class="sxs-lookup"><span data-stu-id="a9867-111">To issue JS interop calls, inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction in your component.</span></span> <span data-ttu-id="a9867-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> přebírá identifikátor funkce JavaScriptu, kterou chcete vyvolat, spolu s libovolným počtem argumentů serializovatelných pomocí JSON.</span><span class="sxs-lookup"><span data-stu-id="a9867-112"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="a9867-113">Identifikátor funkce je relativní vzhledem k globálnímu oboru ( `window` ).</span><span class="sxs-lookup"><span data-stu-id="a9867-113">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="a9867-114">Pokud chcete volat `window.someScope.someFunction` , je identifikátor `someScope.someFunction` .</span><span class="sxs-lookup"><span data-stu-id="a9867-114">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="a9867-115">Před voláním funkce není nutné ji registrovat.</span><span class="sxs-lookup"><span data-stu-id="a9867-115">There's no need to register the function before it's called.</span></span> <span data-ttu-id="a9867-116">Návratový typ `T` musí být také serializovatelný jako JSON.</span><span class="sxs-lookup"><span data-stu-id="a9867-116">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="a9867-117">`T` by měl odpovídat typu .NET, který se nejlépe mapuje na vrácený typ JSON.</span><span class="sxs-lookup"><span data-stu-id="a9867-117">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="a9867-118">Funkce jazyka JavaScript, které vracejí [příslib](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) , jsou volány pomocí <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="a9867-118">JavaScript functions that return a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) are called with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>.</span></span> <span data-ttu-id="a9867-119">`InvokeAsync` rozbalí příslib a vrátí hodnotu, kterou očekává příslib.</span><span class="sxs-lookup"><span data-stu-id="a9867-119">`InvokeAsync` unwraps the Promise and returns the value awaited by the Promise.</span></span>

<span data-ttu-id="a9867-120">Pro Blazor Server aplikace s povoleným předvykreslováním není možné volat do JavaScriptu během prvotního předgenerování.</span><span class="sxs-lookup"><span data-stu-id="a9867-120">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="a9867-121">Volání interoperability JavaScriptu musí být odložena až po navázání spojení s prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="a9867-121">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="a9867-122">Další informace najdete v části [zjištění, kdy Blazor Server se aplikace](#detect-when-a-blazor-server-app-is-prerendering) vykreslovat.</span><span class="sxs-lookup"><span data-stu-id="a9867-122">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="a9867-123">Následující příklad je založen na [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder) dekodéru založeném na jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a9867-123">The following example is based on [`TextDecoder`](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="a9867-124">Příklad ukazuje, jak vyvolat funkci JavaScriptu z metody jazyka C#, která přesměruje požadavek od kódu pro vývojáře na existující rozhraní JavaScript API.</span><span class="sxs-lookup"><span data-stu-id="a9867-124">The example demonstrates how to invoke a JavaScript function from a C# method that offloads a requirement from developer code to an existing JavaScript API.</span></span> <span data-ttu-id="a9867-125">Funkce JavaScriptu přijímá bajtové pole z metody jazyka C#, dekóduje pole a vrátí text do komponenty pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a9867-125">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="a9867-126">Uvnitř `<head>` elementu `wwwroot/index.html` ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` ( Blazor Server ) zadejte funkci JavaScriptu, která používá `TextDecoder` k dekódování předaného pole a vrácení dekódovaných hodnot:</span><span class="sxs-lookup"><span data-stu-id="a9867-126">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="a9867-127">Kód jazyka JavaScript, jako je například kód zobrazený v předchozím příkladu, lze také načíst ze souboru JavaScriptu ( `.js` ) s odkazem na soubor skriptu:</span><span class="sxs-lookup"><span data-stu-id="a9867-127">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (`.js`) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="a9867-128">Následující součást:</span><span class="sxs-lookup"><span data-stu-id="a9867-128">The following component:</span></span>

* <span data-ttu-id="a9867-129">Vyvolá `convertArray` funkci jazyka JavaScript pomocí funkce `JSRuntime` Když **`Convert Array`** je vybráno tlačítko komponenty ().</span><span class="sxs-lookup"><span data-stu-id="a9867-129">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**`Convert Array`**) is selected.</span></span>
* <span data-ttu-id="a9867-130">Po volání funkce JavaScriptu je předané pole převedeno na řetězec.</span><span class="sxs-lookup"><span data-stu-id="a9867-130">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="a9867-131">Řetězec se vrátí do komponenty pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="a9867-131">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="a9867-132">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="a9867-132">IJSRuntime</span></span>

<span data-ttu-id="a9867-133">Chcete-li použít <xref:Microsoft.JSInterop.IJSRuntime> abstrakci, přijímají některé z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="a9867-133">To use the <xref:Microsoft.JSInterop.IJSRuntime> abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="a9867-134">Vložit <xref:Microsoft.JSInterop.IJSRuntime> abstrakci do Razor komponenty ( `.razor` ):</span><span class="sxs-lookup"><span data-stu-id="a9867-134">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into the Razor component (`.razor`):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="a9867-135">Uvnitř `<head>` elementu `wwwroot/index.html` ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` ( Blazor Server ) zadejte `handleTickerChanged` funkci JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="a9867-135">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="a9867-136">Funkce je volána s <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> a nevrací hodnotu:</span><span class="sxs-lookup"><span data-stu-id="a9867-136">The function is called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="a9867-137">Vložit <xref:Microsoft.JSInterop.IJSRuntime> abstrakci do třídy ( `.cs` ):</span><span class="sxs-lookup"><span data-stu-id="a9867-137">Inject the <xref:Microsoft.JSInterop.IJSRuntime> abstraction into a class (`.cs`):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="a9867-138">Uvnitř `<head>` elementu `wwwroot/index.html` ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` ( Blazor Server ) zadejte `handleTickerChanged` funkci JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="a9867-138">Inside the `<head>` element of `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="a9867-139">Funkce je volána s `JSRuntime.InvokeAsync` a vrátí hodnotu:</span><span class="sxs-lookup"><span data-stu-id="a9867-139">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="a9867-140">Pro generování dynamického obsahu pomocí [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)použijte `[Inject]` atribut:</span><span class="sxs-lookup"><span data-stu-id="a9867-140">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="a9867-141">V ukázkové aplikaci na straně klienta, která doprovází toto téma, jsou k dispozici dvě funkce JavaScriptu pro aplikaci, která komunikuje s modelem DOM pro příjem vstupu uživatele a zobrazení uvítací zprávy:</span><span class="sxs-lookup"><span data-stu-id="a9867-141">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="a9867-142">`showPrompt`: Vytvoří výzvu pro přijetí vstupu uživatele (jméno uživatele) a vrátí název volajícímu.</span><span class="sxs-lookup"><span data-stu-id="a9867-142">`showPrompt`: Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="a9867-143">`displayWelcome`: Přiřadí úvodní zprávu od volajícího k objektu modelu DOM s `id` `welcome` příponou.</span><span class="sxs-lookup"><span data-stu-id="a9867-143">`displayWelcome`: Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="a9867-144">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="a9867-144">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="a9867-145">Umístěte `<script>` značku, která odkazuje na soubor JavaScriptu v `wwwroot/index.html` souboru ( Blazor WebAssembly ) nebo `Pages/_Host.cshtml` souboru ( Blazor Server ).</span><span class="sxs-lookup"><span data-stu-id="a9867-145">Place the `<script>` tag that references the JavaScript file in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="a9867-146">`wwwroot/index.html` (Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="a9867-146">`wwwroot/index.html` (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="a9867-147">`Pages/_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="a9867-147">`Pages/_Host.cshtml` (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="a9867-148">Neumísťujte `<script>` značku do souboru komponenty, protože `<script>` značku nejde dynamicky aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="a9867-148">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="a9867-149">Metody .NET spolupracuje s funkcemi JavaScriptu v `exampleJsInterop.js` souboru voláním <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a9867-149">.NET methods interop with the JavaScript functions in the `exampleJsInterop.js` file by calling <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="a9867-150"><xref:Microsoft.JSInterop.IJSRuntime>Abstrakce je asynchronní k povolení pro Blazor Server scénáře.</span><span class="sxs-lookup"><span data-stu-id="a9867-150">The <xref:Microsoft.JSInterop.IJSRuntime> abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="a9867-151">Pokud aplikace je Blazor WebAssembly aplikace a chcete funkci JavaScriptu vyvolat synchronně, přetypování směrem dolů <xref:Microsoft.JSInterop.IJSInProcessRuntime> a volání <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> místo toho.</span><span class="sxs-lookup"><span data-stu-id="a9867-151">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to <xref:Microsoft.JSInterop.IJSInProcessRuntime> and call <xref:Microsoft.JSInterop.IJSInProcessRuntime.Invoke%2A> instead.</span></span> <span data-ttu-id="a9867-152">Doporučujeme, aby většina knihoven spolupráce v JS používala asynchronní rozhraní API, aby bylo zajištěno, že jsou knihovny dostupné ve všech scénářích.</span><span class="sxs-lookup"><span data-stu-id="a9867-152">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="a9867-153">Pokud chcete povolit izolaci JavaScriptu ve standardních [modulech JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), přečtěte si část [ Blazor JavaScript – izolace a objekty reference](#blazor-javascript-isolation-and-object-references) .</span><span class="sxs-lookup"><span data-stu-id="a9867-153">To enable JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules), see the [Blazor JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references) section.</span></span>

::: moniker-end

<span data-ttu-id="a9867-154">Ukázková aplikace obsahuje komponentu, která předvádí interoperabilitu JS.</span><span class="sxs-lookup"><span data-stu-id="a9867-154">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="a9867-155">Součást:</span><span class="sxs-lookup"><span data-stu-id="a9867-155">The component:</span></span>

* <span data-ttu-id="a9867-156">Přijímá vstup uživatele prostřednictvím výzvy JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="a9867-156">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="a9867-157">Vrátí text do komponenty pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="a9867-157">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="a9867-158">Volá druhou funkci JavaScriptu, která komunikuje s modelem DOM, aby zobrazila úvodní zprávu.</span><span class="sxs-lookup"><span data-stu-id="a9867-158">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="a9867-159">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="a9867-159">`Pages/JsInterop.razor`:</span></span>

```razor
@page "/JSInterop"
@using {APP ASSEMBLY}.JsInteropClasses
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

<span data-ttu-id="a9867-160">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace aplikace (například `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="a9867-160">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

1. <span data-ttu-id="a9867-161">Když `TriggerJsPrompt` je spuštěno výběrem **`Trigger JavaScript Prompt`** tlačítka komponenty, `showPrompt` je volána funkce JavaScriptu, která je uvedena v `wwwroot/exampleJsInterop.js` souboru.</span><span class="sxs-lookup"><span data-stu-id="a9867-161">When `TriggerJsPrompt` is executed by selecting the component's **`Trigger JavaScript Prompt`** button, the JavaScript `showPrompt` function provided in the `wwwroot/exampleJsInterop.js` file is called.</span></span>
1. <span data-ttu-id="a9867-162">`showPrompt`Funkce přijímá vstup uživatele (jméno uživatele), což je kódování HTML a vráceno do komponenty.</span><span class="sxs-lookup"><span data-stu-id="a9867-162">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="a9867-163">Komponenta ukládá jméno uživatele do místní proměnné, `name` .</span><span class="sxs-lookup"><span data-stu-id="a9867-163">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="a9867-164">Řetězec uložený v `name` je součástí uvítací zprávy, která je předána funkci JavaScriptu, `displayWelcome` která vykresluje úvodní zprávu do značky nadpisu.</span><span class="sxs-lookup"><span data-stu-id="a9867-164">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="a9867-165">Volání funkce void JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="a9867-165">Call a void JavaScript function</span></span>

<span data-ttu-id="a9867-166">Funkce jazyka JavaScript, které vracejí [typ void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) nebo [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) , jsou volány s <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a9867-166">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span>

## <a name="detect-when-a-no-locblazor-server-app-is-prerendering"></a><span data-ttu-id="a9867-167">Rozpoznat, kdy Blazor Server se aplikace předvykresluje</span><span class="sxs-lookup"><span data-stu-id="a9867-167">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="a9867-168">Zachytit odkazy na elementy</span><span class="sxs-lookup"><span data-stu-id="a9867-168">Capture references to elements</span></span>

<span data-ttu-id="a9867-169">Některé scénáře interoperability JS vyžadují odkazy na prvky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="a9867-169">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="a9867-170">Například knihovna uživatelského rozhraní může vyžadovat odkaz na element pro inicializaci nebo může být nutné volat rozhraní API pro příkazy, například `focus` nebo `play` .</span><span class="sxs-lookup"><span data-stu-id="a9867-170">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="a9867-171">Zachyťte odkazy na prvky HTML v součásti pomocí následujícího postupu:</span><span class="sxs-lookup"><span data-stu-id="a9867-171">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="a9867-172">Přidejte `@ref` atribut do elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="a9867-172">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="a9867-173">Definujte pole typu, <xref:Microsoft.AspNetCore.Components.ElementReference> jehož název odpovídá hodnotě `@ref` atributu.</span><span class="sxs-lookup"><span data-stu-id="a9867-173">Define a field of type <xref:Microsoft.AspNetCore.Components.ElementReference> whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="a9867-174">Následující příklad ukazuje, jak zachytit odkaz na `username` `<input>` prvek:</span><span class="sxs-lookup"><span data-stu-id="a9867-174">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="a9867-175">Pouze odkaz na element lze použít k použití obsahu prázdného prvku, který nekomunikuje s Blazor .</span><span class="sxs-lookup"><span data-stu-id="a9867-175">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="a9867-176">Tento scénář je užitečný, když rozhraní API třetí strany dodává obsah do elementu.</span><span class="sxs-lookup"><span data-stu-id="a9867-176">This scenario is useful when a third-party API supplies content to the element.</span></span> <span data-ttu-id="a9867-177">Vzhledem k tomu Blazor , že nekomunikuje s prvkem, neexistuje možnost konfliktu mezi Blazor reprezentací prvku a modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="a9867-177">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="a9867-178">V následujícím příkladu je *nebezpečné* postupovat obsah neuspořádaného seznamu ( `ul` ), protože Blazor spolupracuje s DOM k naplnění položek seznamu tohoto elementu ( `<li>` ):</span><span class="sxs-lookup"><span data-stu-id="a9867-178">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="a9867-179">Pokud interoperabilita JS `MyList` povede obsah elementu a Blazor pokusí se použít rozdíly pro prvek, rozdíly nebudou odpovídat modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="a9867-179">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="a9867-180">Pokud se jedná o kód .NET, <xref:Microsoft.AspNetCore.Components.ElementReference> je neprůhledný popisovač.</span><span class="sxs-lookup"><span data-stu-id="a9867-180">As far as .NET code is concerned, an <xref:Microsoft.AspNetCore.Components.ElementReference> is an opaque handle.</span></span> <span data-ttu-id="a9867-181">*Jediná* věc, kterou můžete udělat, <xref:Microsoft.AspNetCore.Components.ElementReference> je předat do kódu JavaScriptu přes interoperabilitu js.</span><span class="sxs-lookup"><span data-stu-id="a9867-181">The *only* thing you can do with <xref:Microsoft.AspNetCore.Components.ElementReference> is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="a9867-182">Když to uděláte, kód na straně JavaScriptu obdrží `HTMLElement` instanci, kterou může použít s normálními rozhraními API DOM.</span><span class="sxs-lookup"><span data-stu-id="a9867-182">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="a9867-183">Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na prvek:</span><span class="sxs-lookup"><span data-stu-id="a9867-183">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="a9867-184">`exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="a9867-184">`exampleJsInterop.js`:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="a9867-185">Chcete-li zavolat funkci JavaScriptu, která nevrací hodnotu, použijte <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="a9867-185">To call a JavaScript function that doesn't return a value, use <xref:Microsoft.JSInterop.JSRuntimeExtensions.InvokeVoidAsync%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="a9867-186">Následující kód nastaví fokus na zadání uživatelského jména voláním předchozí funkce JavaScriptu s zachyceným <xref:Microsoft.AspNetCore.Components.ElementReference> :</span><span class="sxs-lookup"><span data-stu-id="a9867-186">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured <xref:Microsoft.AspNetCore.Components.ElementReference>:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="a9867-187">Chcete-li použít metodu rozšíření, vytvořte statickou metodu rozšíření, která obdrží <xref:Microsoft.JSInterop.IJSRuntime> instanci:</span><span class="sxs-lookup"><span data-stu-id="a9867-187">To use an extension method, create a static extension method that receives the <xref:Microsoft.JSInterop.IJSRuntime> instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="a9867-188">`Focus`Metoda je volána přímo na objektu.</span><span class="sxs-lookup"><span data-stu-id="a9867-188">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="a9867-189">Následující příklad předpokládá, že `Focus` Metoda je k dispozici z `JsInteropClasses` oboru názvů:</span><span class="sxs-lookup"><span data-stu-id="a9867-189">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="a9867-190">`username`Proměnná je vyplněna pouze po vykreslení komponenty.</span><span class="sxs-lookup"><span data-stu-id="a9867-190">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="a9867-191">Pokud se vyplněný <xref:Microsoft.AspNetCore.Components.ElementReference> kód předává kódu JavaScriptu, kód JavaScriptu obdrží hodnotu `null` .</span><span class="sxs-lookup"><span data-stu-id="a9867-191">If an unpopulated <xref:Microsoft.AspNetCore.Components.ElementReference> is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="a9867-192">Chcete-li manipulovat s odkazy na elementy po dokončení vykreslování komponenty (pro nastavení prvotního zaměření na element), použijte [ `OnAfterRenderAsync` `OnAfterRender` metody životního cyklu komponenty nebo](xref:blazor/components/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="a9867-192">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [`OnAfterRenderAsync` or `OnAfterRender` component lifecycle methods](xref:blazor/components/lifecycle#after-component-render).</span></span>

<span data-ttu-id="a9867-193">Při práci s obecnými typy a vrácení hodnoty použijte <xref:System.Threading.Tasks.ValueTask%601> :</span><span class="sxs-lookup"><span data-stu-id="a9867-193">When working with generic types and returning a value, use <xref:System.Threading.Tasks.ValueTask%601>:</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="a9867-194">`GenericMethod` je volána přímo na objektu s typem.</span><span class="sxs-lookup"><span data-stu-id="a9867-194">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="a9867-195">Následující příklad předpokládá, že `GenericMethod` je k dispozici z `JsInteropClasses` oboru názvů:</span><span class="sxs-lookup"><span data-stu-id="a9867-195">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="a9867-196">Referenční prvky napříč komponentami</span><span class="sxs-lookup"><span data-stu-id="a9867-196">Reference elements across components</span></span>

<span data-ttu-id="a9867-197"><xref:Microsoft.AspNetCore.Components.ElementReference>Je zaručena pouze platná v <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> metodě komponenty (a odkaz na element je a `struct` ), takže odkaz na prvek nelze předat mezi komponentami.</span><span class="sxs-lookup"><span data-stu-id="a9867-197">An <xref:Microsoft.AspNetCore.Components.ElementReference> is only guaranteed valid in a component's <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="a9867-198">Pro nadřazenou komponentu, aby byl odkaz na element dostupný pro jiné komponenty, může nadřazená komponenta:</span><span class="sxs-lookup"><span data-stu-id="a9867-198">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="a9867-199">Umožňuje podřízeným součástem registrovat zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="a9867-199">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="a9867-200">Vyvolejte zaregistrovaná zpětná volání během <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> události s odkazem na předaný element.</span><span class="sxs-lookup"><span data-stu-id="a9867-200">Invoke the registered callbacks during the <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> event with the passed element reference.</span></span> <span data-ttu-id="a9867-201">Nepřímo tento přístup umožňuje podřízeným komponentám pracovat s odkazem na element nadřazených prvků.</span><span class="sxs-lookup"><span data-stu-id="a9867-201">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="a9867-202">Následující Blazor WebAssembly příklad ilustruje přístup.</span><span class="sxs-lookup"><span data-stu-id="a9867-202">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="a9867-203">V `<head>` `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="a9867-203">In the `<head>` of `wwwroot/index.html`:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="a9867-204">V `<body>` `wwwroot/index.html` :</span><span class="sxs-lookup"><span data-stu-id="a9867-204">In the `<body>` of `wwwroot/index.html`:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="a9867-205">`Pages/Index.razor` (nadřazená součást):</span><span class="sxs-lookup"><span data-stu-id="a9867-205">`Pages/Index.razor` (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="a9867-206">`Pages/Index.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="a9867-206">`Pages/Index.razor.cs`:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Pages
{
    public partial class Index : 
        ComponentBase, IObservable<ElementReference>, IDisposable
    {
        private bool disposing;
        private IList<IObserver<ElementReference>> subscriptions = 
            new List<IObserver<ElementReference>>();
        private ElementReference title;

        protected override void OnAfterRender(bool firstRender)
        {
            base.OnAfterRender(firstRender);

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnNext(title);
                }
                catch (Exception)
                {
                    throw;
                }
            }
        }

        public void Dispose()
        {
            disposing = true;

            foreach (var subscription in subscriptions)
            {
                try
                {
                    subscription.OnCompleted();
                }
                catch (Exception)
                {
                }
            }

            subscriptions.Clear();
        }

        public IDisposable Subscribe(IObserver<ElementReference> observer)
        {
            if (disposing)
            {
                throw new InvalidOperationException("Parent being disposed");
            }

            subscriptions.Add(observer);

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
                Self.subscriptions.Remove(Observer);
            }
        }
    }
}
```

<span data-ttu-id="a9867-207">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace aplikace (například `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="a9867-207">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="a9867-208">`Shared/SurveyPrompt.razor` (podřízená součást):</span><span class="sxs-lookup"><span data-stu-id="a9867-208">`Shared/SurveyPrompt.razor` (child component):</span></span>

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

<span data-ttu-id="a9867-209">`Shared/SurveyPrompt.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="a9867-209">`Shared/SurveyPrompt.razor.cs`:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace {APP ASSEMBLY}.Shared
{
    public partial class SurveyPrompt : 
        ComponentBase, IObserver<ElementReference>, IDisposable
    {
        private IDisposable subscription = null;

        [Parameter]
        public IObservable<ElementReference> Parent { get; set; }

        protected override void OnParametersSet()
        {
            base.OnParametersSet();

            if (subscription != null)
            {
                subscription.Dispose();
            }

            subscription = Parent.Subscribe(this);
        }

        public void OnCompleted()
        {
            subscription = null;
        }

        public void OnError(Exception error)
        {
            subscription = null;
        }

        public void OnNext(ElementReference value)
        {
            JS.InvokeAsync<object>(
                "setElementClass", new object[] { value, "red" });
        }

        public void Dispose()
        {
            subscription?.Dispose();
        }
    }
}
```

<span data-ttu-id="a9867-210">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace aplikace (například `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="a9867-210">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="harden-js-interop-calls"></a><span data-ttu-id="a9867-211">Volání interoperability pro posílení JS</span><span class="sxs-lookup"><span data-stu-id="a9867-211">Harden JS interop calls</span></span>

<span data-ttu-id="a9867-212">Interoperabilita JS může selhat kvůli chybám sítě a měla by být považována za nespolehlivou.</span><span class="sxs-lookup"><span data-stu-id="a9867-212">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="a9867-213">Ve výchozím nastavení Blazor Server vyprší volání spolupráce v aplikaci js na serveru po jedné minutě.</span><span class="sxs-lookup"><span data-stu-id="a9867-213">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="a9867-214">Pokud aplikace může tolerovat více agresivní časový limit, nastavte časový limit pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="a9867-214">If an app can tolerate a more aggressive timeout, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="a9867-215">V `Startup.ConfigureServices` části globálně zadejte časový limit:</span><span class="sxs-lookup"><span data-stu-id="a9867-215">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="a9867-216">Za vyvolání v kódu komponenty může jedno volání zadat časový limit:</span><span class="sxs-lookup"><span data-stu-id="a9867-216">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="a9867-217">Další informace o vyčerpání prostředků naleznete v tématu <xref:blazor/security/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="a9867-217">For more information on resource exhaustion, see <xref:blazor/security/server/threat-mitigation>.</span></span>

[!INCLUDE[](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="a9867-218">Vyhnout se cyklickým odkazům na objekty</span><span class="sxs-lookup"><span data-stu-id="a9867-218">Avoid circular object references</span></span>

<span data-ttu-id="a9867-219">Objekty, které obsahují kruhové odkazy, nelze v klientovi serializovat pro:</span><span class="sxs-lookup"><span data-stu-id="a9867-219">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="a9867-220">Volání metod .NET.</span><span class="sxs-lookup"><span data-stu-id="a9867-220">.NET method calls.</span></span>
* <span data-ttu-id="a9867-221">Volání metody JavaScriptu z C#, když návratový typ obsahuje cyklické odkazy.</span><span class="sxs-lookup"><span data-stu-id="a9867-221">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="a9867-222">Další informace najdete v následujících problémech:</span><span class="sxs-lookup"><span data-stu-id="a9867-222">For more information, see the following issues:</span></span>

* [<span data-ttu-id="a9867-223">Cyklické odkazy nejsou podporované, musí být dva (dotnet/aspnetcore #20525).</span><span class="sxs-lookup"><span data-stu-id="a9867-223">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="a9867-224">Návrh: Přidání mechanismu pro zpracování cyklických odkazů při serializaci (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="a9867-224">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

::: moniker range=">= aspnetcore-5.0"

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="a9867-225">Blazor JavaScript – izolace a odkazy na objekty</span><span class="sxs-lookup"><span data-stu-id="a9867-225">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="a9867-226">Blazor povoluje izolaci JavaScriptu ve standardních [modulech JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span><span class="sxs-lookup"><span data-stu-id="a9867-226">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="a9867-227">Izolace JavaScriptu nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="a9867-227">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="a9867-228">Importovaný JavaScript již neznečišťující globální obor názvů.</span><span class="sxs-lookup"><span data-stu-id="a9867-228">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="a9867-229">Uživatelé knihovny a komponent nejsou pro import souvisejícího JavaScriptu vyžadováni.</span><span class="sxs-lookup"><span data-stu-id="a9867-229">Consumers of a library and components aren't required to import the related JavaScript.</span></span>

<span data-ttu-id="a9867-230">Například následující modul JavaScriptu exportuje funkci JavaScriptu pro zobrazení výzvy prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="a9867-230">For example, the following JavaScript module exports a JavaScript function for showing a browser prompt:</span></span>

```javascript
export function showPrompt(message) {
  return prompt(message, 'Type anything here');
}
```

<span data-ttu-id="a9867-231">Přidejte předchozí modul JavaScriptu do knihovny .NET jako statický webový prostředek ( `wwwroot/exampleJsInterop.js` ) a pak Importujte modul do kódu .NET pomocí <xref:Microsoft.JSInterop.IJSRuntime> služby.</span><span class="sxs-lookup"><span data-stu-id="a9867-231">Add the preceding JavaScript module to a .NET library as a static web asset (`wwwroot/exampleJsInterop.js`) and then import the module into the .NET code using the <xref:Microsoft.JSInterop.IJSRuntime> service.</span></span> <span data-ttu-id="a9867-232">Služba je vložená jako `jsRuntime` (není zobrazená) pro následující příklad:</span><span class="sxs-lookup"><span data-stu-id="a9867-232">The service is injected as `jsRuntime` (not shown) for the following example:</span></span>

```csharp
var module = await jsRuntime.InvokeAsync<IJSObjectReference>(
    "import", "./_content/MyComponents/exampleJsInterop.js");
```

<span data-ttu-id="a9867-233">`import`Identifikátor v předchozím příkladu je speciální identifikátor používaný speciálně pro import modulu JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="a9867-233">The `import` identifier in the preceding example is a special identifier used specifically for importing a JavaScript module.</span></span> <span data-ttu-id="a9867-234">Zadejte modul využívající cestu ke stabilnímu statickému webovému prostředku: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}` .</span><span class="sxs-lookup"><span data-stu-id="a9867-234">Specify the module using its stable static web asset path: `_content/{LIBRARY NAME}/{PATH UNDER WWWROOT}`.</span></span> <span data-ttu-id="a9867-235">Zástupný symbol `{LIBRARY NAME}` je název knihovny.</span><span class="sxs-lookup"><span data-stu-id="a9867-235">The placeholder `{LIBRARY NAME}` is the library name.</span></span> <span data-ttu-id="a9867-236">Zástupný symbol `{PATH UNDER WWWROOT}` je cesta ke skriptu v rámci `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="a9867-236">The placeholder `{PATH UNDER WWWROOT}` is the path to the script under `wwwroot`.</span></span>

<span data-ttu-id="a9867-237"><xref:Microsoft.JSInterop.IJSRuntime> Importuje modul jako `IJSObjectReference` , který představuje odkaz na objekt jazyka JavaScript z kódu .NET.</span><span class="sxs-lookup"><span data-stu-id="a9867-237"><xref:Microsoft.JSInterop.IJSRuntime> imports the module as a `IJSObjectReference`, which represents a reference to a JavaScript object from .NET code.</span></span> <span data-ttu-id="a9867-238">Použijte `IJSObjectReference` k vyvolání exportovaných funkcí jazyka JavaScript z modulu:</span><span class="sxs-lookup"><span data-stu-id="a9867-238">Use the `IJSObjectReference` to invoke exported JavaScript functions from the module:</span></span>

```csharp
public async ValueTask<string> Prompt(string message)
{
    return await module.InvokeAsync<string>("showPrompt", message);
}
```

<span data-ttu-id="a9867-239">`IJSInProcessObjectReference` představuje odkaz na objekt jazyka JavaScript, jehož funkce mohou být vyvolány synchronně.</span><span class="sxs-lookup"><span data-stu-id="a9867-239">`IJSInProcessObjectReference` represents a reference to a JavaScript object whose functions can be invoked synchronously.</span></span>

<span data-ttu-id="a9867-240">`IJSUnmarshalledObjectReference` představuje odkaz na objekt jazyka JavaScript, jehož funkce lze vyvolat bez režie serializace dat .NET.</span><span class="sxs-lookup"><span data-stu-id="a9867-240">`IJSUnmarshalledObjectReference` represents a reference to an JavaScript object whose functions can be invoked without the overhead of serializing .NET data.</span></span> <span data-ttu-id="a9867-241">Tuto možnost lze použít v Blazor WebAssembly případě, kdy je rozhodující výkon:</span><span class="sxs-lookup"><span data-stu-id="a9867-241">This can be used in Blazor WebAssembly when performance is crucial:</span></span>

```javascript
window.unmarshalledInstance = {
  helloWorld: function (personNamePointer) {
    const personName = Blazor.platform.readStringField(value, 0);
    return `Hello ${personName}`;
  }
};
```

```csharp
var unmarshalledRuntime = (IJSUnmarshalledRuntime)jsRuntime;
var jsUnmarshalledReference = unmarshalledRuntime
    .InvokeUnmarshalled<IJSUnmarshalledObjectReference>("unmarshalledInstance");

string helloWorldString = jsUnmarshalledReference.InvokeUnmarshalled<string, string>(
    "helloWorld");
```

## <a name="use-of-javascript-libraries-that-render-ui-dom-elements"></a><span data-ttu-id="a9867-242">Použití knihoven JavaScriptu, které vykreslují uživatelské rozhraní (prvky modelu DOM)</span><span class="sxs-lookup"><span data-stu-id="a9867-242">Use of JavaScript libraries that render UI (DOM elements)</span></span>

<span data-ttu-id="a9867-243">Někdy můžete chtít použít knihovny JavaScriptu, které tvoří viditelné prvky uživatelského rozhraní v modelu DOM prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a9867-243">Sometimes you may wish to use JavaScript libraries that produce visible user interface elements within the browser DOM.</span></span> <span data-ttu-id="a9867-244">Na první pohled se to může zdát obtížné, protože Blazor rozdílový systém se spoléhá na kontrolu nad stromovou strukturou prvků modelu DOM a spustí se v případě chyb, pokud nějaký externí kód povede strom DOM a zruší platnost svého mechanismu pro použití rozdílů.</span><span class="sxs-lookup"><span data-stu-id="a9867-244">At first glance, this might seem difficult because Blazor's diffing system relies on having control over the tree of DOM elements and runs into errors if some external code mutates the DOM tree and invalidates its mechanism for applying diffs.</span></span> <span data-ttu-id="a9867-245">Nejedná se o Blazor omezení specifické pro.</span><span class="sxs-lookup"><span data-stu-id="a9867-245">This isn't a Blazor-specific limitation.</span></span> <span data-ttu-id="a9867-246">K stejné chybě dochází u všech rámců rozhraní založeného na rozdílech.</span><span class="sxs-lookup"><span data-stu-id="a9867-246">The same challenge occurs with any diff-based UI framework.</span></span>

<span data-ttu-id="a9867-247">Naštěstí je snadné vkládat externě generované uživatelské rozhraní v Blazor uživatelském rozhraní komponenty spolehlivě.</span><span class="sxs-lookup"><span data-stu-id="a9867-247">Fortunately, it's straightforward to embed externally-generated UI within a Blazor component UI reliably.</span></span> <span data-ttu-id="a9867-248">Doporučeným postupem je, aby kód komponenty ( `.razor` soubor) vytvořil prázdný element.</span><span class="sxs-lookup"><span data-stu-id="a9867-248">The recommended technique is to have the component's code (`.razor` file) produce an empty element.</span></span> <span data-ttu-id="a9867-249">BlazorJe-li rozdílový systém rozdílů v systému, je prvek vždy prázdný, takže modul pro vykreslování neprovádí přerozdělování do prvku a místo toho zanechá samotný obsah.</span><span class="sxs-lookup"><span data-stu-id="a9867-249">As far as Blazor's diffing system is concerned, the element is always empty, so the renderer does not recurse into the element and instead leaves its contents alone.</span></span> <span data-ttu-id="a9867-250">Díky tomu je bezpečné naplnit prvek libovolným externě spravovaným obsahem.</span><span class="sxs-lookup"><span data-stu-id="a9867-250">This makes it safe to populate the element with arbitrary externally-managed content.</span></span>

<span data-ttu-id="a9867-251">Následující příklad demonstruje koncept.</span><span class="sxs-lookup"><span data-stu-id="a9867-251">The following example demonstrates the concept.</span></span> <span data-ttu-id="a9867-252">V rámci `if` příkazu `firstRender` , pokud je `true` , udělejte něco s `myElement` .</span><span class="sxs-lookup"><span data-stu-id="a9867-252">Within the `if` statement when `firstRender` is `true`, do something with `myElement`.</span></span> <span data-ttu-id="a9867-253">Například zavolejte externí knihovnu JavaScript k naplnění.</span><span class="sxs-lookup"><span data-stu-id="a9867-253">For example, call an external JavaScript library to populate it.</span></span> <span data-ttu-id="a9867-254">Blazor ponechá obsah elementu samotný, dokud se tato součást neodebere.</span><span class="sxs-lookup"><span data-stu-id="a9867-254">Blazor leaves the element's contents alone until this component itself is removed.</span></span> <span data-ttu-id="a9867-255">Po odebrání součásti se odebere také celý podstrom modelu DOM součásti.</span><span class="sxs-lookup"><span data-stu-id="a9867-255">When the component is removed, the component's entire DOM subtree is also removed.</span></span>

```razor
<h1>Hello! This is a Blazor component rendered at @DateTime.Now</h1>

<div @ref="myElement"></div>

@code {
    HtmlElement myElement;
    
    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            ...
        }
    }
}
```

<span data-ttu-id="a9867-256">Jako podrobnější příklad zvažte následující komponentu, která vykresluje interaktivní mapu pomocí [Open-Source rozhraní Mapbox API](https://www.mapbox.com/):</span><span class="sxs-lookup"><span data-stu-id="a9867-256">As a more detailed example, consider the following component that renders an interactive map using the [open-source Mapbox APIs](https://www.mapbox.com/):</span></span>

```razor
@inject IJSRuntime JS
@implements IAsyncDisposable

<div @ref="mapElement" style='width: 400px; height: 300px;'></div>

<button @onclick="() => ShowAsync(51.454514, -2.587910)">Show Bristol, UK</button>
<button @onclick="() => ShowAsync(35.6762, 139.6503)">Show Tokyo, Japan</button>

@code
{
    ElementReference mapElement;
    IJSObjectReference mapModule;
    IJSObjectReference mapInstance;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            mapModule = await JS.InvokeAsync<IJSObjectReference>(
                "import", "./mapComponent.js");
            mapInstance = await mapModule.InvokeAsync<IJSObjectReference>(
                "addMapToElement", mapElement);
        }
    }

    Task ShowAsync(double latitude, double longitude)
        => mapModule.InvokeVoidAsync("setMapCenter", mapInstance, latitude, 
            longitude).AsTask();

    private async ValueTask IAsyncDisposable.DisposeAsync()
    {
        await mapInstance.DisposeAsync();
        await mapModule.DisposeAsync();
    }
}
```

<span data-ttu-id="a9867-257">Odpovídající modul JavaScriptu, který by měl být umístěn na `wwwroot/mapComponent.js` , je následující:</span><span class="sxs-lookup"><span data-stu-id="a9867-257">The corresponding JavaScript module, which should be placed at `wwwroot/mapComponent.js`, is as follows:</span></span>

```javascript
import 'https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.js';

// TO MAKE THE MAP APPEAR YOU MUST ADD YOUR ACCESS TOKEN FROM 
// https://account.mapbox.com
mapboxgl.accessToken = '{ACCESS TOKEN}';

export function addMapToElement(element) {
  return new mapboxgl.Map({
    container: element,
    style: 'mapbox://styles/mapbox/streets-v11',
    center: [-74.5, 40],
    zoom: 9
  });
}

export function setMapCenter(map, latitude, longitude) {
  map.setCenter([longitude, latitude]);
}
```

<span data-ttu-id="a9867-258">V předchozím příkladu nahraďte řetězec `{ACCESS TOKEN}` platným přístupovým tokenem, ze kterého můžete získat https://account.mapbox.com .</span><span class="sxs-lookup"><span data-stu-id="a9867-258">In the preceding example, replace the string `{ACCESS TOKEN}` with a valid access token that you can get from https://account.mapbox.com.</span></span>

<span data-ttu-id="a9867-259">Chcete-li vytvořit správné styly, přidejte následující značku šablony stylů do stránky HTML hostitele ( `index.html` nebo `_Host.cshtml` ):</span><span class="sxs-lookup"><span data-stu-id="a9867-259">To produce correct styling, add the following stylesheet tag to the host HTML page (`index.html` or `_Host.cshtml`):</span></span>

```html
<link rel="stylesheet" href="https://api.mapbox.com/mapbox-gl-js/v1.12.0/mapbox-gl.css" />
```

<span data-ttu-id="a9867-260">Předchozí příklad vytvoří interaktivní mapové uživatelské rozhraní, ve kterém uživatel:</span><span class="sxs-lookup"><span data-stu-id="a9867-260">The preceding example produces an interactive map UI, in which the user:</span></span>

* <span data-ttu-id="a9867-261">Lze přetáhnout nebo zvětšit.</span><span class="sxs-lookup"><span data-stu-id="a9867-261">Can drag to scroll or zoom.</span></span>
* <span data-ttu-id="a9867-262">Kliknutím na tlačítka přejdete na předdefinovaná umístění.</span><span class="sxs-lookup"><span data-stu-id="a9867-262">Click buttons to jump to predefined locations.</span></span>

![Mapa Mapbox ulici pro Tokio, Japonsko s tlačítky pro výběr Bristol, Spojené království a Tokio, Japonsko](https://user-images.githubusercontent.com/1101362/94939821-92ef6700-04ca-11eb-858e-fff6df0053ae.png)

<span data-ttu-id="a9867-264">Klíčové body, které je potřeba pochopit:</span><span class="sxs-lookup"><span data-stu-id="a9867-264">The key points to understand are:</span></span>

 * <span data-ttu-id="a9867-265">Rozhraní `<div>` with `@ref="mapElement"` je ponecháno prázdné, pokud je to v souladu s Blazor .</span><span class="sxs-lookup"><span data-stu-id="a9867-265">The `<div>` with `@ref="mapElement"` is left empty as far as Blazor is concerned.</span></span> <span data-ttu-id="a9867-266">Proto je bezpečné ho `mapbox-gl.js` naplnit a upravovat jeho obsah v průběhu času.</span><span class="sxs-lookup"><span data-stu-id="a9867-266">It's therefore safe for `mapbox-gl.js` to populate it and modify its contents over time.</span></span> <span data-ttu-id="a9867-267">Tuto techniku lze použít s libovolnou knihovnou JavaScriptu, která vykresluje uživatelské rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a9867-267">You can use this technique with any JavaScript library that renders UI.</span></span> <span data-ttu-id="a9867-268">Do komponent můžete dokonce vkládat komponenty z rozhraní zabezpečeného ověřování pomocí JavaScriptu Blazor , a to za předpokladu, že se nepokusí spojit a upravit ostatní části stránky.</span><span class="sxs-lookup"><span data-stu-id="a9867-268">You could even embed components from a third-party JavaScript SPA framework inside Blazor components, as long as they don't try to reach out and modify other parts of the page.</span></span> <span data-ttu-id="a9867-269">*Není bezpečné pro* externí kód JavaScriptu pro úpravu prvků, které Blazor nepovažují za prázdné.</span><span class="sxs-lookup"><span data-stu-id="a9867-269">It is *not* safe for external JavaScript code to modify elements that Blazor does not regard as empty.</span></span>
 * <span data-ttu-id="a9867-270">Při použití tohoto přístupu je potřeba mít na paměti pravidla týkající se toho, jak Blazor zachovává nebo ničí prvky modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="a9867-270">When using this approach, bear in mind the rules about how Blazor retains or destroys DOM elements.</span></span> <span data-ttu-id="a9867-271">V předchozím příkladu komponenta bezpečně zpracovává události kliknutí na tlačítko a aktualizuje existující instanci mapy, protože ve výchozím nastavení jsou zachovány prvky modelu DOM, pokud je to možné.</span><span class="sxs-lookup"><span data-stu-id="a9867-271">In the preceding example, the component safely handles button click events and updates the existing map instance because DOM elements are retained where possible by default.</span></span> <span data-ttu-id="a9867-272">Pokud jste vyhledali seznam prvků mapy ze `@foreach` smyčky, chcete použít `@key` k zajištění zachování instancí komponent.</span><span class="sxs-lookup"><span data-stu-id="a9867-272">If you were rendering a list of map elements from inside a `@foreach` loop, you want to use `@key` to ensure the preservation of component instances.</span></span> <span data-ttu-id="a9867-273">V opačném případě změny v datech seznamu by mohly způsobit nežádoucí chování instancí instancí.</span><span class="sxs-lookup"><span data-stu-id="a9867-273">Otherwise, changes in the list data could cause component instances to retain the state of previous instances in an undesirable manner.</span></span> <span data-ttu-id="a9867-274">Další informace naleznete v tématu [použití @key pro zachování prvků a komponent](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span><span class="sxs-lookup"><span data-stu-id="a9867-274">For more information, see [using @key to preserve elements and components](xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components).</span></span>

<span data-ttu-id="a9867-275">Kromě toho předchozí příklad ukazuje, jak je možné zapouzdřit logiku a závislosti JavaScriptu v rámci modulu ES6 a dynamicky ho načíst pomocí `import` identifikátoru.</span><span class="sxs-lookup"><span data-stu-id="a9867-275">Additionally, the preceding example shows how it's possible to encapsulate JavaScript logic and dependencies within an ES6 module and load it dynamically using the `import` identifier.</span></span> <span data-ttu-id="a9867-276">Další informace naleznete v tématu [izolace JavaScriptu a odkazy na objekty](#blazor-javascript-isolation-and-object-references).</span><span class="sxs-lookup"><span data-stu-id="a9867-276">For more information, see [JavaScript isolation and object references](#blazor-javascript-isolation-and-object-references).</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a9867-277">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="a9867-277">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="a9867-278">Příklad InteropComponent. Razor (dotnet/AspNetCore, úložiště GitHub, větev vydání 3,1)</span><span class="sxs-lookup"><span data-stu-id="a9867-278">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [<span data-ttu-id="a9867-279">Provádění rozsáhlých přenosů dat v Blazor Server aplikacích</span><span class="sxs-lookup"><span data-stu-id="a9867-279">Perform large data transfers in Blazor Server apps</span></span>](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
