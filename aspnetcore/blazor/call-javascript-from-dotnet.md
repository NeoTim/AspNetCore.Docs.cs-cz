---
<span data-ttu-id="1a7e2-101">title: ' volání funkcí jazyka JavaScript z metod .NET v ASP.NET Core Blazor ' Author: Description: ' Naučte se vyvolat funkce jazyka JavaScript z metod .NET v Blazor aplikacích. '</span><span class="sxs-lookup"><span data-stu-id="1a7e2-101">title: 'Call JavaScript functions from .NET methods in ASP.NET Core Blazor' author: description: 'Learn how to invoke JavaScript functions from .NET methods in Blazor apps.'</span></span>
<span data-ttu-id="1a7e2-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="1a7e2-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1a7e2-103">'Blazor'</span></span>
- <span data-ttu-id="1a7e2-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1a7e2-104">'Identity'</span></span>
- <span data-ttu-id="1a7e2-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1a7e2-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="1a7e2-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1a7e2-106">'Razor'</span></span>
- <span data-ttu-id="1a7e2-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-107">'SignalR' uid:</span></span> 

---
# <a name="call-javascript-functions-from-net-methods-in-aspnet-core-blazor"></a><span data-ttu-id="1a7e2-108">Volání funkcí jazyka JavaScript z metod .NET v ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="1a7e2-108">Call JavaScript functions from .NET methods in ASP.NET Core Blazor</span></span>

<span data-ttu-id="1a7e2-109">[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Skořepa](https://github.com/danroth27)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1a7e2-109">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1a7e2-110">BlazorAplikace může vyvolat funkce JavaScriptu z metod .NET a metod .NET z funkcí jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-110">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="1a7e2-111">Tyto scénáře se nazývají *interoperabilita JavaScriptu* (interoperabilita*js*).</span><span class="sxs-lookup"><span data-stu-id="1a7e2-111">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="1a7e2-112">Tento článek popisuje vyvolání funkcí jazyka JavaScript z rozhraní .NET.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-112">This article covers invoking JavaScript functions from .NET.</span></span> <span data-ttu-id="1a7e2-113">Informace o volání metod .NET z JavaScriptu naleznete v tématu <xref:blazor/call-dotnet-from-javascript> .</span><span class="sxs-lookup"><span data-stu-id="1a7e2-113">For information on how to call .NET methods from JavaScript, see <xref:blazor/call-dotnet-from-javascript>.</span></span>

<span data-ttu-id="1a7e2-114">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1a7e2-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1a7e2-115">Chcete-li volat do JavaScriptu z rozhraní .NET, použijte `IJSRuntime` abstrakci.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-115">To call into JavaScript from .NET, use the `IJSRuntime` abstraction.</span></span> <span data-ttu-id="1a7e2-116">Chcete-li vydat volání interoperability JS, zapište `IJSRuntime` abstrakci do komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-116">To issue JS interop calls, inject the `IJSRuntime` abstraction in your component.</span></span> <span data-ttu-id="1a7e2-117">`InvokeAsync<T>`Metoda přebírá identifikátor pro funkci JavaScriptu, kterou chcete vyvolat, spolu s libovolným počtem argumentů serializovatelných pomocí JSON.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-117">The `InvokeAsync<T>` method takes an identifier for the JavaScript function that you wish to invoke along with any number of JSON-serializable arguments.</span></span> <span data-ttu-id="1a7e2-118">Identifikátor funkce je relativní vzhledem k globálnímu oboru ( `window` ).</span><span class="sxs-lookup"><span data-stu-id="1a7e2-118">The function identifier is relative to the global scope (`window`).</span></span> <span data-ttu-id="1a7e2-119">Pokud chcete volat `window.someScope.someFunction` , je identifikátor `someScope.someFunction` .</span><span class="sxs-lookup"><span data-stu-id="1a7e2-119">If you wish to call `window.someScope.someFunction`, the identifier is `someScope.someFunction`.</span></span> <span data-ttu-id="1a7e2-120">Před voláním funkce není nutné ji registrovat.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-120">There's no need to register the function before it's called.</span></span> <span data-ttu-id="1a7e2-121">Návratový typ `T` musí být také serializovatelný jako JSON.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-121">The return type `T` must also be JSON serializable.</span></span> <span data-ttu-id="1a7e2-122">`T`by měl odpovídat typu .NET, který se nejlépe mapuje na vrácený typ JSON.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-122">`T` should match the .NET type that best maps to the JSON type returned.</span></span>

<span data-ttu-id="1a7e2-123">Pro Blazor serverové aplikace s povoleným předvykreslováním není možné volat do JavaScriptu během prvotního předgenerování.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-123">For Blazor Server apps with prerendering enabled, calling into JavaScript isn't possible during the initial prerendering.</span></span> <span data-ttu-id="1a7e2-124">Volání interoperability JavaScriptu musí být odložena až po navázání spojení s prohlížečem.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-124">JavaScript interop calls must be deferred until after the connection with the browser is established.</span></span> <span data-ttu-id="1a7e2-125">Další informace najdete v části [zjištění, kdy Blazor je serverová aplikace](#detect-when-a-blazor-server-app-is-prerendering) vykreslovat.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-125">For more information, see the [Detect when a Blazor Server app is prerendering](#detect-when-a-blazor-server-app-is-prerendering) section.</span></span>

<span data-ttu-id="1a7e2-126">Následující příklad je založen na [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), dekodéru založeném na JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-126">The following example is based on [TextDecoder](https://developer.mozilla.org/docs/Web/API/TextDecoder), a JavaScript-based decoder.</span></span> <span data-ttu-id="1a7e2-127">Příklad ukazuje, jak vyvolat funkci JavaScriptu z metody jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-127">The example demonstrates how to invoke a JavaScript function from a C# method.</span></span> <span data-ttu-id="1a7e2-128">Funkce JavaScriptu přijímá bajtové pole z metody jazyka C#, dekóduje pole a vrátí text do komponenty pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-128">The JavaScript function accepts a byte array from a C# method, decodes the array, and returns the text to the component for display.</span></span>

<span data-ttu-id="1a7e2-129">Uvnitř `<head>` prvku *wwwroot/index.html* ( Blazor WebAssembly) nebo *Pages/_Host. cshtml* ( Blazor Server) zadejte funkci JavaScriptu, která používá `TextDecoder` k dekódování předaného pole a vrácení dekódovaných hodnot:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-129">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a JavaScript function that uses `TextDecoder` to decode a passed array and return the decoded value:</span></span>

[!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-convertarray.html)]

<span data-ttu-id="1a7e2-130">Kód jazyka JavaScript, jako je například kód zobrazený v předchozím příkladu, lze také načíst ze souboru JavaScriptu (*. js*) s odkazem na soubor skriptu:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-130">JavaScript code, such as the code shown in the preceding example, can also be loaded from a JavaScript file (*.js*) with a reference to the script file:</span></span>

```html
<script src="exampleJsInterop.js"></script>
```

<span data-ttu-id="1a7e2-131">Následující součást:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-131">The following component:</span></span>

* <span data-ttu-id="1a7e2-132">Vyvolá `convertArray` funkci jazyka JavaScript pomocí funkce `JSRuntime` když je vybráno tlačítko komponenty (**převést pole**).</span><span class="sxs-lookup"><span data-stu-id="1a7e2-132">Invokes the `convertArray` JavaScript function using `JSRuntime` when a component button (**Convert Array**) is selected.</span></span>
* <span data-ttu-id="1a7e2-133">Po volání funkce JavaScriptu je předané pole převedeno na řetězec.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-133">After the JavaScript function is called, the passed array is converted into a string.</span></span> <span data-ttu-id="1a7e2-134">Řetězec se vrátí do komponenty pro zobrazení.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-134">The string is returned to the component for display.</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/call-js-example.razor?highlight=2,34-35)]

## <a name="ijsruntime"></a><span data-ttu-id="1a7e2-135">IJSRuntime</span><span class="sxs-lookup"><span data-stu-id="1a7e2-135">IJSRuntime</span></span>

<span data-ttu-id="1a7e2-136">Chcete-li použít `IJSRuntime` abstrakci, přijímají některé z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-136">To use the `IJSRuntime` abstraction, adopt any of the following approaches:</span></span>

* <span data-ttu-id="1a7e2-137">Vložit `IJSRuntime` abstrakci do Razor komponenty (*. Razor*):</span><span class="sxs-lookup"><span data-stu-id="1a7e2-137">Inject the `IJSRuntime` abstraction into the Razor component (*.razor*):</span></span>

  [!code-razor[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction.razor?highlight=1)]

  <span data-ttu-id="1a7e2-138">V rámci `<head>` prvku *wwwroot/index.html* ( Blazor WebAssembly) nebo *Pages/_Host. cshtml* ( Blazor Server) zadejte `handleTickerChanged` funkci JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-138">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="1a7e2-139">Funkce je volána s `IJSRuntime.InvokeVoidAsync` a nevrací hodnotu:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-139">The function is called with `IJSRuntime.InvokeVoidAsync` and doesn't return a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged1.html)]

* <span data-ttu-id="1a7e2-140">Vložit `IJSRuntime` abstrakci do třídy (*. cs*):</span><span class="sxs-lookup"><span data-stu-id="1a7e2-140">Inject the `IJSRuntime` abstraction into a class (*.cs*):</span></span>

  [!code-csharp[](call-javascript-from-dotnet/samples_snapshot/inject-abstraction-class.cs?highlight=5)]

  <span data-ttu-id="1a7e2-141">V rámci `<head>` prvku *wwwroot/index.html* ( Blazor WebAssembly) nebo *Pages/_Host. cshtml* ( Blazor Server) zadejte `handleTickerChanged` funkci JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-141">Inside the `<head>` element of *wwwroot/index.html* (Blazor WebAssembly) or *Pages/_Host.cshtml* (Blazor Server), provide a `handleTickerChanged` JavaScript function.</span></span> <span data-ttu-id="1a7e2-142">Funkce je volána s `JSRuntime.InvokeAsync` a vrátí hodnotu:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-142">The function is called with `JSRuntime.InvokeAsync` and returns a value:</span></span>

  [!code-html[](call-javascript-from-dotnet/samples_snapshot/index-script-handleTickerChanged2.html)]

* <span data-ttu-id="1a7e2-143">Pro generování dynamického obsahu pomocí [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic)použijte `[Inject]` atribut:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-143">For dynamic content generation with [BuildRenderTree](xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic), use the `[Inject]` attribute:</span></span>

  ```razor
  [Inject]
  IJSRuntime JSRuntime { get; set; }
  ```

<span data-ttu-id="1a7e2-144">V ukázkové aplikaci na straně klienta, která doprovází toto téma, jsou k dispozici dvě funkce JavaScriptu pro aplikaci, která komunikuje s modelem DOM pro příjem vstupu uživatele a zobrazení uvítací zprávy:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-144">In the client-side sample app that accompanies this topic, two JavaScript functions are available to the app that interact with the DOM to receive user input and display a welcome message:</span></span>

* <span data-ttu-id="1a7e2-145">`showPrompt`&ndash;Vytvoří výzvu k přijetí vstupu uživatele (jméno uživatele) a vrátí název volajícímu.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-145">`showPrompt` &ndash; Produces a prompt to accept user input (the user's name) and returns the name to the caller.</span></span>
* <span data-ttu-id="1a7e2-146">`displayWelcome`&ndash;Přiřadí uvítací zprávu od volajícího k objektu modelu DOM s `id` `welcome` příponou.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-146">`displayWelcome` &ndash; Assigns a welcome message from the caller to a DOM object with an `id` of `welcome`.</span></span>

<span data-ttu-id="1a7e2-147">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-147">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=2-7)]

<span data-ttu-id="1a7e2-148">Umístěte `<script>` značku, která odkazuje na soubor JavaScriptu v souboru *wwwroot/index.html* ( Blazor WebAssembly) nebo *Pages/_Host. cshtml* ( Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="1a7e2-148">Place the `<script>` tag that references the JavaScript file in the *wwwroot/index.html* file (Blazor WebAssembly) or *Pages/_Host.cshtml* file (Blazor Server).</span></span>

<span data-ttu-id="1a7e2-149">*wwwroot/index.html* ( Blazor WebAssembly):</span><span class="sxs-lookup"><span data-stu-id="1a7e2-149">*wwwroot/index.html* (Blazor WebAssembly):</span></span>

[!code-html[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/index.html?highlight=22)]

<span data-ttu-id="1a7e2-150">*Pages/_Host. cshtml* ( Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="1a7e2-150">*Pages/_Host.cshtml* (Blazor Server):</span></span>

[!code-cshtml[](./common/samples/3.x/BlazorServerSample/Pages/_Host.cshtml?highlight=35)]

<span data-ttu-id="1a7e2-151">Neumísťujte `<script>` značku do souboru komponenty, protože `<script>` značku nejde dynamicky aktualizovat.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-151">Don't place a `<script>` tag in a component file because the `<script>` tag can't be updated dynamically.</span></span>

<span data-ttu-id="1a7e2-152">Metody .NET spolupracuje s funkcemi JavaScriptu v souboru *exampleJsInterop. js* voláním `IJSRuntime.InvokeAsync<T>` .</span><span class="sxs-lookup"><span data-stu-id="1a7e2-152">.NET methods interop with the JavaScript functions in the *exampleJsInterop.js* file by calling `IJSRuntime.InvokeAsync<T>`.</span></span>

<span data-ttu-id="1a7e2-153">`IJSRuntime`Abstrakce je asynchronní, aby umožňovala Blazor serverové scénáře.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-153">The `IJSRuntime` abstraction is asynchronous to allow for Blazor Server scenarios.</span></span> <span data-ttu-id="1a7e2-154">Pokud je aplikace Blazor aplikace WebAssembly a chcete vyvolat funkci JavaScriptu synchronně, přetypování směrem dolů `IJSInProcessRuntime` a volání `Invoke<T>` místo toho.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-154">If the app is a Blazor WebAssembly app and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead.</span></span> <span data-ttu-id="1a7e2-155">Doporučujeme, aby většina knihoven spolupráce v JS používala asynchronní rozhraní API, aby bylo zajištěno, že jsou knihovny dostupné ve všech scénářích.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-155">We recommend that most JS interop libraries use the async APIs to ensure that the libraries are available in all scenarios.</span></span>

<span data-ttu-id="1a7e2-156">Ukázková aplikace obsahuje komponentu, která předvádí interoperabilitu JS.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-156">The sample app includes a component to demonstrate JS interop.</span></span> <span data-ttu-id="1a7e2-157">Součást:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-157">The component:</span></span>

* <span data-ttu-id="1a7e2-158">Přijímá vstup uživatele prostřednictvím výzvy JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-158">Receives user input via a JavaScript prompt.</span></span>
* <span data-ttu-id="1a7e2-159">Vrátí text do komponenty pro zpracování.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-159">Returns the text to the component for processing.</span></span>
* <span data-ttu-id="1a7e2-160">Volá druhou funkci JavaScriptu, která komunikuje s modelem DOM, aby zobrazila úvodní zprávu.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-160">Calls a second JavaScript function that interacts with the DOM to display a welcome message.</span></span>

<span data-ttu-id="1a7e2-161">*Stránky/JSInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-161">*Pages/JSInterop.razor*:</span></span>

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

1. <span data-ttu-id="1a7e2-162">Když `TriggerJsPrompt` se spustí na **příkazovém řádku aktivační procedury JavaScriptu** komponenty, zavolá se funkce JavaScriptu, která `showPrompt` je k dispozici v souboru *wwwroot/exampleJsInterop. js* .</span><span class="sxs-lookup"><span data-stu-id="1a7e2-162">When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the JavaScript `showPrompt` function provided in the *wwwroot/exampleJsInterop.js* file is called.</span></span>
1. <span data-ttu-id="1a7e2-163">`showPrompt`Funkce přijímá vstup uživatele (jméno uživatele), což je kódování HTML a vráceno do komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-163">The `showPrompt` function accepts user input (the user's name), which is HTML-encoded and returned to the component.</span></span> <span data-ttu-id="1a7e2-164">Komponenta ukládá jméno uživatele do místní proměnné, `name` .</span><span class="sxs-lookup"><span data-stu-id="1a7e2-164">The component stores the user's name in a local variable, `name`.</span></span>
1. <span data-ttu-id="1a7e2-165">Řetězec uložený v `name` je součástí uvítací zprávy, která je předána funkci JavaScriptu, `displayWelcome` která vykresluje úvodní zprávu do značky nadpisu.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-165">The string stored in `name` is incorporated into a welcome message, which is passed to a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.</span></span>

## <a name="call-a-void-javascript-function"></a><span data-ttu-id="1a7e2-166">Volání funkce void JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="1a7e2-166">Call a void JavaScript function</span></span>

<span data-ttu-id="1a7e2-167">Funkce jazyka JavaScript, které vracejí [typ void (0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) nebo [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) , jsou volány s `IJSRuntime.InvokeVoidAsync` .</span><span class="sxs-lookup"><span data-stu-id="1a7e2-167">JavaScript functions that return [void(0)/void 0](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/void) or [undefined](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/undefined) are called with `IJSRuntime.InvokeVoidAsync`.</span></span>

## <a name="detect-when-a-blazor-server-app-is-prerendering"></a><span data-ttu-id="1a7e2-168">Rozpoznat, kdy Blazor se serverová aplikace před vykreslením</span><span class="sxs-lookup"><span data-stu-id="1a7e2-168">Detect when a Blazor Server app is prerendering</span></span>
 
[!INCLUDE[](~/includes/blazor-prerendering.md)]

## <a name="capture-references-to-elements"></a><span data-ttu-id="1a7e2-169">Zachytit odkazy na elementy</span><span class="sxs-lookup"><span data-stu-id="1a7e2-169">Capture references to elements</span></span>

<span data-ttu-id="1a7e2-170">Některé scénáře interoperability JS vyžadují odkazy na prvky jazyka HTML.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-170">Some JS interop scenarios require references to HTML elements.</span></span> <span data-ttu-id="1a7e2-171">Například knihovna uživatelského rozhraní může vyžadovat odkaz na element pro inicializaci nebo může být nutné volat rozhraní API pro příkazy, například `focus` nebo `play` .</span><span class="sxs-lookup"><span data-stu-id="1a7e2-171">For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.</span></span>

<span data-ttu-id="1a7e2-172">Zachyťte odkazy na prvky HTML v součásti pomocí následujícího postupu:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-172">Capture references to HTML elements in a component using the following approach:</span></span>

* <span data-ttu-id="1a7e2-173">Přidejte `@ref` atribut do elementu HTML.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-173">Add an `@ref` attribute to the HTML element.</span></span>
* <span data-ttu-id="1a7e2-174">Definujte pole typu, `ElementReference` jehož název odpovídá hodnotě `@ref` atributu.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-174">Define a field of type `ElementReference` whose name matches the value of the `@ref` attribute.</span></span>

<span data-ttu-id="1a7e2-175">Následující příklad ukazuje, jak zachytit odkaz na `username` `<input>` prvek:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-175">The following example shows capturing a reference to the `username` `<input>` element:</span></span>

```razor
<input @ref="username" ... />

@code {
    ElementReference username;
}
```

> [!WARNING]
> <span data-ttu-id="1a7e2-176">Pouze odkaz na element lze použít k použití obsahu prázdného prvku, který nekomunikuje s Blazor .</span><span class="sxs-lookup"><span data-stu-id="1a7e2-176">Only use an element reference to mutate the contents of an empty element that doesn't interact with Blazor.</span></span> <span data-ttu-id="1a7e2-177">Tento scénář je užitečný, když rozhraní API třetí strany poskytuje obsah do elementu.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-177">This scenario is useful when a 3rd party API supplies content to the element.</span></span> <span data-ttu-id="1a7e2-178">Vzhledem k tomu Blazor , že nekomunikuje s prvkem, neexistuje možnost konfliktu mezi Blazor reprezentací prvku a modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-178">Because Blazor doesn't interact with the element, there's no possibility of a conflict between Blazor's representation of the element and the DOM.</span></span>
>
> <span data-ttu-id="1a7e2-179">V následujícím příkladu je *nebezpečné* postupovat obsah neuspořádaného seznamu ( `ul` ), protože Blazor spolupracuje s DOM k naplnění položek seznamu tohoto elementu ( `<li>` ):</span><span class="sxs-lookup"><span data-stu-id="1a7e2-179">In the following example, it's *dangerous* to mutate the contents of the unordered list (`ul`) because Blazor interacts with the DOM to populate this element's list items (`<li>`):</span></span>
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
> <span data-ttu-id="1a7e2-180">Pokud interoperabilita JS `MyList` povede obsah elementu a Blazor pokusí se použít rozdíly pro prvek, rozdíly nebudou odpovídat modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-180">If JS interop mutates the contents of element `MyList` and Blazor attempts to apply diffs to the element, the diffs won't match the DOM.</span></span>

<span data-ttu-id="1a7e2-181">Pokud se jedná o kód .NET, `ElementReference` je neprůhledný popisovač.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-181">As far as .NET code is concerned, an `ElementReference` is an opaque handle.</span></span> <span data-ttu-id="1a7e2-182">*Jediná* věc, kterou můžete udělat, `ElementReference` je předat do kódu JavaScriptu přes interoperabilitu js.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-182">The *only* thing you can do with `ElementReference` is pass it through to JavaScript code via JS interop.</span></span> <span data-ttu-id="1a7e2-183">Když to uděláte, kód na straně JavaScriptu obdrží `HTMLElement` instanci, kterou může použít s normálními rozhraními API DOM.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-183">When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.</span></span>

<span data-ttu-id="1a7e2-184">Například následující kód definuje metodu rozšíření .NET, která umožňuje nastavení fokusu na prvek:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-184">For example, the following code defines a .NET extension method that enables setting the focus on an element:</span></span>

<span data-ttu-id="1a7e2-185">*exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-185">*exampleJsInterop.js*:</span></span>

```javascript
window.exampleJsFunctions = {
  focusElement : function (element) {
    element.focus();
  }
}
```

<span data-ttu-id="1a7e2-186">Chcete-li zavolat funkci JavaScriptu, která nevrací hodnotu, použijte `IJSRuntime.InvokeVoidAsync` .</span><span class="sxs-lookup"><span data-stu-id="1a7e2-186">To call a JavaScript function that doesn't return a value, use `IJSRuntime.InvokeVoidAsync`.</span></span> <span data-ttu-id="1a7e2-187">Následující kód nastaví fokus na zadání uživatelského jména voláním předchozí funkce JavaScriptu s zachyceným `ElementReference` :</span><span class="sxs-lookup"><span data-stu-id="1a7e2-187">The following code sets the focus on the username input by calling the preceding JavaScript function with the captured `ElementReference`:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component1.razor?highlight=1,3,11-12)]

<span data-ttu-id="1a7e2-188">Chcete-li použít metodu rozšíření, vytvořte statickou metodu rozšíření, která obdrží `IJSRuntime` instanci:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-188">To use an extension method, create a static extension method that receives the `IJSRuntime` instance:</span></span>

```csharp
public static async Task Focus(this ElementReference elementRef, IJSRuntime jsRuntime)
{
    await jsRuntime.InvokeVoidAsync(
        "exampleJsFunctions.focusElement", elementRef);
}
```

<span data-ttu-id="1a7e2-189">`Focus`Metoda je volána přímo na objektu.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-189">The `Focus` method is called directly on the object.</span></span> <span data-ttu-id="1a7e2-190">Následující příklad předpokládá, že `Focus` Metoda je k dispozici z `JsInteropClasses` oboru názvů:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-190">The following example assumes that the `Focus` method is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component2.razor?highlight=1-4,12)]

> [!IMPORTANT]
> <span data-ttu-id="1a7e2-191">`username`Proměnná je vyplněna pouze po vykreslení komponenty.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-191">The `username` variable is only populated after the component is rendered.</span></span> <span data-ttu-id="1a7e2-192">Pokud se vyplněný `ElementReference` kód předává kódu JavaScriptu, kód JavaScriptu obdrží hodnotu `null` .</span><span class="sxs-lookup"><span data-stu-id="1a7e2-192">If an unpopulated `ElementReference` is passed to JavaScript code, the JavaScript code receives a value of `null`.</span></span> <span data-ttu-id="1a7e2-193">Chcete-li manipulovat s odkazy na elementy po dokončení vykreslování komponenty (pro nastavení prvotního zaměření na prvek), použijte [metody životního cyklu komponenty OnAfterRenderAsync nebo OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="1a7e2-193">To manipulate element references after the component has finished rendering (to set the initial focus on an element) use the [OnAfterRenderAsync or OnAfterRender component lifecycle methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="1a7e2-194">Při práci s obecnými typy a vrácení hodnoty použijte [ValueTask \< T>](xref:System.Threading.Tasks.ValueTask`1):</span><span class="sxs-lookup"><span data-stu-id="1a7e2-194">When working with generic types and returning a value, use [ValueTask\<T>](xref:System.Threading.Tasks.ValueTask`1):</span></span>

```csharp
public static ValueTask<T> GenericMethod<T>(this ElementReference elementRef, 
    IJSRuntime jsRuntime)
{
    return jsRuntime.InvokeAsync<T>(
        "exampleJsFunctions.doSomethingGeneric", elementRef);
}
```

<span data-ttu-id="1a7e2-195">`GenericMethod`je volána přímo na objektu s typem.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-195">`GenericMethod` is called directly on the object with a type.</span></span> <span data-ttu-id="1a7e2-196">Následující příklad předpokládá, že `GenericMethod` je k dispozici z `JsInteropClasses` oboru názvů:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-196">The following example assumes that the `GenericMethod` is available from the `JsInteropClasses` namespace:</span></span>

[!code-razor[](call-javascript-from-dotnet/samples_snapshot/component3.razor?highlight=17)]

## <a name="reference-elements-across-components"></a><span data-ttu-id="1a7e2-197">Referenční prvky napříč komponentami</span><span class="sxs-lookup"><span data-stu-id="1a7e2-197">Reference elements across components</span></span>

<span data-ttu-id="1a7e2-198">`ElementReference`Je zaručena pouze platná v `OnAfterRender` metodě komponenty (a odkaz na element je a `struct` ), takže odkaz na prvek nelze předat mezi komponentami.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-198">An `ElementReference` is only guaranteed valid in a component's `OnAfterRender` method (and an element reference is a `struct`), so an element reference can't be passed between components.</span></span>

<span data-ttu-id="1a7e2-199">Pro nadřazenou komponentu, aby byl odkaz na element dostupný pro jiné komponenty, může nadřazená komponenta:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-199">For a parent component to make an element reference available to other components, the parent component can:</span></span>

* <span data-ttu-id="1a7e2-200">Umožňuje podřízeným součástem registrovat zpětná volání.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-200">Allow child components to register callbacks.</span></span>
* <span data-ttu-id="1a7e2-201">Vyvolejte zaregistrovaná zpětná volání během `OnAfterRender` události s odkazem na předaný element.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-201">Invoke the registered callbacks during the `OnAfterRender` event with the passed element reference.</span></span> <span data-ttu-id="1a7e2-202">Nepřímo tento přístup umožňuje podřízeným komponentám pracovat s odkazem na element nadřazených prvků.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-202">Indirectly, this approach allows child components to interact with the parent's element reference.</span></span>

<span data-ttu-id="1a7e2-203">Následující Blazor příklad WebAssembly znázorňuje přístup.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-203">The following Blazor WebAssembly example illustrates the approach.</span></span>

<span data-ttu-id="1a7e2-204">V `<head>` *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-204">In the `<head>` of *wwwroot/index.html*:</span></span>

```html
<style>
    .red { color: red }
</style>
```

<span data-ttu-id="1a7e2-205">V `<body>` *wwwroot/index.html*:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-205">In the `<body>` of *wwwroot/index.html*:</span></span>

```html
<script>
    function setElementClass(element, className) {
        /** @type {HTMLElement} **/
        var myElement = element;
        myElement.classList.add(className);
    }
</script>
```

<span data-ttu-id="1a7e2-206">*Pages/index. Razor* (nadřazená komponenta):</span><span class="sxs-lookup"><span data-stu-id="1a7e2-206">*Pages/Index.razor* (parent component):</span></span>

```razor
@page "/"

<h1 @ref="title">Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Parent="this" Title="How is Blazor working for you?" />
```

<span data-ttu-id="1a7e2-207">*Pages/index. Razor. cs*:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-207">*Pages/Index.razor.cs*:</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Pages
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

<span data-ttu-id="1a7e2-208">*Shared/SurveyPrompt. Razor* (podřízená součást):</span><span class="sxs-lookup"><span data-stu-id="1a7e2-208">*Shared/SurveyPrompt.razor* (child component):</span></span>

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

<span data-ttu-id="1a7e2-209">*Shared/SurveyPrompt. Razor. cs*:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-209">*Shared/SurveyPrompt.razor.cs*:</span></span>

```csharp
using System;
using Microsoft.AspNetCore.Components;

namespace BlazorSample.Shared
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

## <a name="harden-js-interop-calls"></a><span data-ttu-id="1a7e2-210">Volání interoperability pro posílení JS</span><span class="sxs-lookup"><span data-stu-id="1a7e2-210">Harden JS interop calls</span></span>

<span data-ttu-id="1a7e2-211">Interoperabilita JS může selhat kvůli chybám sítě a měla by být považována za nespolehlivou.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-211">JS interop may fail due to networking errors and should be treated as unreliable.</span></span> <span data-ttu-id="1a7e2-212">Ve výchozím nastavení Blazor Serverová aplikace vyprší volání interoperability js na serveru po jedné minutě.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-212">By default, a Blazor Server app times out JS interop calls on the server after one minute.</span></span> <span data-ttu-id="1a7e2-213">Pokud aplikace může tolerovat více agresivního časového limitu, například 10 sekund, nastavte časový limit pomocí jednoho z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-213">If an app can tolerate a more aggressive timeout, such as 10 seconds, set the timeout using one of the following approaches:</span></span>

* <span data-ttu-id="1a7e2-214">V `Startup.ConfigureServices` části globálně zadejte časový limit:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-214">Globally in `Startup.ConfigureServices`, specify the timeout:</span></span>

  ```csharp
  services.AddServerSideBlazor(
      options => options.JSInteropDefaultCallTimeout = TimeSpan.FromSeconds({SECONDS}));
  ```

* <span data-ttu-id="1a7e2-215">Za vyvolání v kódu komponenty může jedno volání zadat časový limit:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-215">Per-invocation in component code, a single call can specify the timeout:</span></span>

  ```csharp
  var result = await JSRuntime.InvokeAsync<string>("MyJSOperation", 
      TimeSpan.FromSeconds({SECONDS}), new[] { "Arg1" });
  ```

<span data-ttu-id="1a7e2-216">Další informace o vyčerpání prostředků naleznete v tématu <xref:security/blazor/server/threat-mitigation> .</span><span class="sxs-lookup"><span data-stu-id="1a7e2-216">For more information on resource exhaustion, see <xref:security/blazor/server/threat-mitigation>.</span></span>

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="1a7e2-217">Vyhnout se cyklickým odkazům na objekty</span><span class="sxs-lookup"><span data-stu-id="1a7e2-217">Avoid circular object references</span></span>

<span data-ttu-id="1a7e2-218">Objekty, které obsahují kruhové odkazy, nelze v klientovi serializovat pro:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-218">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="1a7e2-219">Volání metod .NET.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-219">.NET method calls.</span></span>
* <span data-ttu-id="1a7e2-220">Volání metody JavaScriptu z C#, když návratový typ obsahuje cyklické odkazy.</span><span class="sxs-lookup"><span data-stu-id="1a7e2-220">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="1a7e2-221">Další informace najdete v následujících problémech:</span><span class="sxs-lookup"><span data-stu-id="1a7e2-221">For more information, see the following issues:</span></span>

* [<span data-ttu-id="1a7e2-222">Cyklické odkazy nejsou podporované, musí být dva (dotnet/aspnetcore #20525).</span><span class="sxs-lookup"><span data-stu-id="1a7e2-222">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="1a7e2-223">Návrh: Přidání mechanismu pro zpracování cyklických odkazů při serializaci (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="1a7e2-223">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="1a7e2-224">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1a7e2-224">Additional resources</span></span>

* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="1a7e2-225">Příklad InteropComponent. Razor (dotnet/AspNetCore, úložiště GitHub, větev vydání 3,1)</span><span class="sxs-lookup"><span data-stu-id="1a7e2-225">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="1a7e2-226">[Provádění rozsáhlých přenosů dat v Blazor serverových aplikacích](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="1a7e2-226">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
