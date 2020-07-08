---
title: Volání metod .NET z funkcí JavaScriptu v ASP.NET CoreBlazor
author: guardrex
description: Naučte se volat metody .NET z funkcí JavaScriptu v Blazor aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 5dc1c5a4fbaaa756cfaf09c751e516147a04c899
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060017"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a><span data-ttu-id="bc536-103">Volání metod .NET z funkcí JavaScriptu v ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="bc536-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="bc536-104">[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Skořepa](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bc536-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bc536-105">BlazorAplikace může vyvolat funkce JavaScriptu z metod .NET a metod .NET z funkcí jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="bc536-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="bc536-106">Tyto scénáře se nazývají *interoperabilita JavaScriptu* (interoperabilita*js*).</span><span class="sxs-lookup"><span data-stu-id="bc536-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="bc536-107">Tento článek se zabývá vyvoláním metod .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="bc536-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="bc536-108">Informace o volání funkcí jazyka JavaScript z rozhraní .NET naleznete v tématu <xref:blazor/call-javascript-from-dotnet> .</span><span class="sxs-lookup"><span data-stu-id="bc536-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="bc536-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bc536-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="bc536-110">Statické volání metody .NET</span><span class="sxs-lookup"><span data-stu-id="bc536-110">Static .NET method call</span></span>

<span data-ttu-id="bc536-111">Chcete-li vyvolat statickou metodu .NET z JavaScriptu, použijte `DotNet.invokeMethod` `DotNet.invokeMethodAsync` funkce nebo.</span><span class="sxs-lookup"><span data-stu-id="bc536-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="bc536-112">Předejte identifikátor statické metody, kterou chcete volat, název sestavení obsahující funkce a všechny argumenty.</span><span class="sxs-lookup"><span data-stu-id="bc536-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="bc536-113">Asynchronní verze je upřednostňovaná pro podporu Blazor Server scénářů.</span><span class="sxs-lookup"><span data-stu-id="bc536-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="bc536-114">Metoda .NET musí být veřejná, statická a musí mít [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atribut.</span><span class="sxs-lookup"><span data-stu-id="bc536-114">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="bc536-115">Volání otevřených obecných metod není aktuálně podporováno.</span><span class="sxs-lookup"><span data-stu-id="bc536-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="bc536-116">Ukázková aplikace obsahuje metodu C# pro návrat `int` pole.</span><span class="sxs-lookup"><span data-stu-id="bc536-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="bc536-117">[`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute)Atribut je použit pro metodu.</span><span class="sxs-lookup"><span data-stu-id="bc536-117">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="bc536-118">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="bc536-118">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="bc536-119">JavaScript, který obsluhuje klient, vyvolá metodu C# .NET.</span><span class="sxs-lookup"><span data-stu-id="bc536-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="bc536-120">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="bc536-120">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="bc536-121">Pokud **`Trigger .NET static method ReturnArrayAsync`** je vybráno tlačítko, Projděte si výstup konzoly v části webové nástroje pro vývojáře v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="bc536-121">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="bc536-122">Výstup konzoly:</span><span class="sxs-lookup"><span data-stu-id="bc536-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="bc536-123">Čtvrtá hodnota pole je vložena do pole ( `data.push(4);` ) vráceného `ReturnArrayAsync` .</span><span class="sxs-lookup"><span data-stu-id="bc536-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="bc536-124">Ve výchozím nastavení je identifikátor metody název metody, ale můžete zadat jiný identifikátor pomocí [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) konstruktoru atributu:</span><span class="sxs-lookup"><span data-stu-id="bc536-124">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="bc536-125">V souboru JavaScriptu na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="bc536-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="bc536-126">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace aplikace (například `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="bc536-126">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="bc536-127">Volání metody instance</span><span class="sxs-lookup"><span data-stu-id="bc536-127">Instance method call</span></span>

<span data-ttu-id="bc536-128">Můžete také volat metody instance rozhraní .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="bc536-128">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="bc536-129">Vyvolání metody instance rozhraní .NET z JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="bc536-129">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="bc536-130">Předání instance rozhraní .NET odkazem na jazyk JavaScript:</span><span class="sxs-lookup"><span data-stu-id="bc536-130">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="bc536-131">Proveďte statické volání <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="bc536-131">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="bc536-132">Zabalte instanci v <xref:Microsoft.JSInterop.DotNetObjectReference> instanci a zavolejte <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> na <xref:Microsoft.JSInterop.DotNetObjectReference> instanci.</span><span class="sxs-lookup"><span data-stu-id="bc536-132">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="bc536-133">Dispose <xref:Microsoft.JSInterop.DotNetObjectReference> objektů (příklad se zobrazí později v této části).</span><span class="sxs-lookup"><span data-stu-id="bc536-133">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="bc536-134">Vyvolat metody instance .NET v instanci pomocí `invokeMethod` `invokeMethodAsync` funkcí nebo.</span><span class="sxs-lookup"><span data-stu-id="bc536-134">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="bc536-135">Instance rozhraní .NET může být také předána jako argument při vyvolání jiných metod rozhraní .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="bc536-135">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="bc536-136">Ukázková aplikace protokoluje zprávy do konzoly na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="bc536-136">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="bc536-137">Pro následující příklady znázorněné ukázkovou aplikací si Projděte výstup konzoly v prohlížeči v vývojářských nástrojích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="bc536-137">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="bc536-138">Když je **`Trigger .NET instance method HelloHelper.SayHello`** vybráno tlačítko, `ExampleJsInterop.CallHelloHelperSayHello` je volána a předá `Blazor` do metody název.</span><span class="sxs-lookup"><span data-stu-id="bc536-138">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="bc536-139">`Pages/JsInterop.razor`:</span><span class="sxs-lookup"><span data-stu-id="bc536-139">`Pages/JsInterop.razor`:</span></span>

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

<span data-ttu-id="bc536-140">`CallHelloHelperSayHello`vyvolá funkci JavaScriptu `sayHello` s novou instancí `HelloHelper` .</span><span class="sxs-lookup"><span data-stu-id="bc536-140">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="bc536-141">`JsInteropClasses/ExampleJsInterop.cs`:</span><span class="sxs-lookup"><span data-stu-id="bc536-141">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="bc536-142">`wwwroot/exampleJsInterop.js`:</span><span class="sxs-lookup"><span data-stu-id="bc536-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="bc536-143">Název je předán `HelloHelper` konstruktoru, který nastaví `HelloHelper.Name` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="bc536-143">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="bc536-144">Při spuštění funkce JavaScriptu `sayHello` `HelloHelper.SayHello` vrátí `Hello, {Name}!` zprávu, která je zapsána do konzoly funkcí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="bc536-144">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="bc536-145">`JsInteropClasses/HelloHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="bc536-145">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="bc536-146">Výstup konzoly v vývojářských nástrojích webu v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="bc536-146">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="bc536-147">Aby nedošlo k nevrácení paměti a povolovalo uvolňování paměti na komponentě, která vytváří <xref:Microsoft.JSInterop.DotNetObjectReference> , proveďte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="bc536-147">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="bc536-148">Dispose objektu ve třídě, která vytvořila <xref:Microsoft.JSInterop.DotNetObjectReference> instanci:</span><span class="sxs-lookup"><span data-stu-id="bc536-148">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime jsRuntime;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime jsRuntime)
      {
          this.jsRuntime = jsRuntime;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return jsRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="bc536-149">Předchozí vzor zobrazený ve `ExampleJsInterop` třídě může být také implementován v součásti:</span><span class="sxs-lookup"><span data-stu-id="bc536-149">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JSRuntime

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JSRuntime.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  <span data-ttu-id="bc536-150">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace aplikace (například `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="bc536-150">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="bc536-151">Pokud komponenta nebo třída neodstraní, vyřadí <xref:Microsoft.JSInterop.DotNetObjectReference> objekt na straně klienta voláním `.dispose()` :</span><span class="sxs-lookup"><span data-stu-id="bc536-151">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="bc536-152">Volání metody instance komponenty</span><span class="sxs-lookup"><span data-stu-id="bc536-152">Component instance method call</span></span>

<span data-ttu-id="bc536-153">Postup při volání metod .NET komponenty:</span><span class="sxs-lookup"><span data-stu-id="bc536-153">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="bc536-154">Pomocí `invokeMethod` funkce or `invokeMethodAsync` vytvořte statické volání metody do komponenty.</span><span class="sxs-lookup"><span data-stu-id="bc536-154">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="bc536-155">Statická metoda součásti zabalí volání své metody instance jako vyvolanou <xref:System.Action> .</span><span class="sxs-lookup"><span data-stu-id="bc536-155">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

<span data-ttu-id="bc536-156">V JavaScriptu na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="bc536-156">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="bc536-157">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace aplikace (například `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="bc536-157">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="bc536-158">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="bc536-158">`Pages/JSInteropComponent.razor`:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="bc536-159">V případě, že existuje několik komponent, z nichž každá má metody instance pro volání, použijte pomocnou třídu k vyvolání metod instancí <xref:System.Action> jednotlivých komponent.</span><span class="sxs-lookup"><span data-stu-id="bc536-159">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as <xref:System.Action>s) of each component.</span></span>

<span data-ttu-id="bc536-160">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="bc536-160">In the following example:</span></span>

* <span data-ttu-id="bc536-161">`JSInteropExample`Komponenta obsahuje několik `ListItem` komponent.</span><span class="sxs-lookup"><span data-stu-id="bc536-161">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="bc536-162">Každá `ListItem` Komponenta se skládá ze zprávy a tlačítka.</span><span class="sxs-lookup"><span data-stu-id="bc536-162">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="bc536-163">Když `ListItem` je vybráno tlačítko komponenty, tato `ListItem` `UpdateMessage` metoda změní text položky seznamu a skryje tlačítko.</span><span class="sxs-lookup"><span data-stu-id="bc536-163">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="bc536-164">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="bc536-164">`MessageUpdateInvokeHelper.cs`:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="bc536-165">Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace aplikace (například `BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="bc536-165">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="bc536-166">V JavaScriptu na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="bc536-166">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="bc536-167">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="bc536-167">`Shared/ListItem.razor`:</span></span>

```razor
@inject IJSRuntime JsRuntime

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="bc536-168">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="bc536-168">`Pages/JSInteropExample.razor`:</span></span>

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="bc536-169">Vyhnout se cyklickým odkazům na objekty</span><span class="sxs-lookup"><span data-stu-id="bc536-169">Avoid circular object references</span></span>

<span data-ttu-id="bc536-170">Objekty, které obsahují kruhové odkazy, nelze v klientovi serializovat pro:</span><span class="sxs-lookup"><span data-stu-id="bc536-170">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="bc536-171">Volání metod .NET.</span><span class="sxs-lookup"><span data-stu-id="bc536-171">.NET method calls.</span></span>
* <span data-ttu-id="bc536-172">Volání metody JavaScriptu z C#, když návratový typ obsahuje cyklické odkazy.</span><span class="sxs-lookup"><span data-stu-id="bc536-172">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="bc536-173">Další informace najdete v následujících problémech:</span><span class="sxs-lookup"><span data-stu-id="bc536-173">For more information, see the following issues:</span></span>

* [<span data-ttu-id="bc536-174">Cyklické odkazy nejsou podporované, musí být dva (dotnet/aspnetcore #20525).</span><span class="sxs-lookup"><span data-stu-id="bc536-174">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="bc536-175">Návrh: Přidání mechanismu pro zpracování cyklických odkazů při serializaci (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="bc536-175">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="bc536-176">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="bc536-176">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="bc536-177">`InteropComponent.razor`Příklad (dotnet/AspNetCore, úložiště GitHub, větev vydané verze 3,1)</span><span class="sxs-lookup"><span data-stu-id="bc536-177">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="bc536-178">[Provádění rozsáhlých přenosů dat v Blazor Server aplikacích](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="bc536-178">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
