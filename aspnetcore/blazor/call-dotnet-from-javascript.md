---
title: Volání metod .NET z funkcí JavaScriptu v ASP.NET Core Blazor
author: guardrex
description: Naučte se volat metody .NET z funkcí JavaScriptu v aplikacích Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: dbf44fe7923998c65119e42d97c304890fa95523
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218788"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="fbd25-103">Volání metod .NET z funkcí JavaScriptu v ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="fbd25-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="fbd25-104">[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Skořepa](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fbd25-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="fbd25-105">Blazor aplikace může vyvolat funkce JavaScriptu z metod .NET a metod .NET z funkcí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="fbd25-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="fbd25-106">Tyto scénáře se nazývají *interoperabilita JavaScriptu* (interoperabilita*js*).</span><span class="sxs-lookup"><span data-stu-id="fbd25-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="fbd25-107">Tento článek se zabývá vyvoláním metod .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="fbd25-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="fbd25-108">Informace o volání funkcí jazyka JavaScript z rozhraní .NET naleznete v tématu <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="fbd25-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="fbd25-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fbd25-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="fbd25-110">Statické volání metody .NET</span><span class="sxs-lookup"><span data-stu-id="fbd25-110">Static .NET method call</span></span>

<span data-ttu-id="fbd25-111">Chcete-li vyvolat statickou metodu .NET z JavaScriptu, použijte funkce `DotNet.invokeMethod` nebo `DotNet.invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="fbd25-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="fbd25-112">Předejte identifikátor statické metody, kterou chcete volat, název sestavení obsahující funkce a všechny argumenty.</span><span class="sxs-lookup"><span data-stu-id="fbd25-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="fbd25-113">Asynchronní verze je preferována k podpoře Blazorch scénářů serveru.</span><span class="sxs-lookup"><span data-stu-id="fbd25-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="fbd25-114">Metoda .NET musí být veřejná, statická a musí mít atribut `[JSInvokable]`.</span><span class="sxs-lookup"><span data-stu-id="fbd25-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="fbd25-115">Volání otevřených obecných metod není aktuálně podporováno.</span><span class="sxs-lookup"><span data-stu-id="fbd25-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="fbd25-116">Ukázková aplikace obsahuje C# metodu, která vrátí `int` pole.</span><span class="sxs-lookup"><span data-stu-id="fbd25-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="fbd25-117">Atribut `JSInvokable` se aplikuje na metodu.</span><span class="sxs-lookup"><span data-stu-id="fbd25-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="fbd25-118">*Stránky/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="fbd25-118">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="fbd25-119">JavaScript, který obsluhuje klient, vyvolá C# metodu .NET.</span><span class="sxs-lookup"><span data-stu-id="fbd25-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="fbd25-120">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="fbd25-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="fbd25-121">Pokud je vybráno tlačítko **aktivovat .NET static Method ReturnArrayAsync** , Projděte si výstup konzoly v webových vývojářských nástrojích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="fbd25-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="fbd25-122">Výstup konzoly:</span><span class="sxs-lookup"><span data-stu-id="fbd25-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="fbd25-123">Hodnota čtvrtého pole je vložena do pole (`data.push(4);`) vráceného `ReturnArrayAsync`.</span><span class="sxs-lookup"><span data-stu-id="fbd25-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="fbd25-124">Ve výchozím nastavení je identifikátor metody název metody, ale můžete zadat jiný identifikátor pomocí `JSInvokableAttribute` konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="fbd25-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="fbd25-125">V souboru JavaScriptu na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="fbd25-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="fbd25-126">Volání metody instance</span><span class="sxs-lookup"><span data-stu-id="fbd25-126">Instance method call</span></span>

<span data-ttu-id="fbd25-127">Můžete také volat metody instance rozhraní .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="fbd25-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="fbd25-128">Vyvolání metody instance rozhraní .NET z JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="fbd25-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="fbd25-129">Předání instance rozhraní .NET odkazem na jazyk JavaScript:</span><span class="sxs-lookup"><span data-stu-id="fbd25-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="fbd25-130">Proveďte statické volání `DotNetObjectReference.Create`.</span><span class="sxs-lookup"><span data-stu-id="fbd25-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="fbd25-131">Zabalte instanci v instanci `DotNetObjectReference` a volejte `Create` na instanci `DotNetObjectReference`.</span><span class="sxs-lookup"><span data-stu-id="fbd25-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="fbd25-132">Dispose objektů `DotNetObjectReference` (příklad se zobrazí později v této části).</span><span class="sxs-lookup"><span data-stu-id="fbd25-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="fbd25-133">Vyvolat metody instance .NET v instanci pomocí funkcí `invokeMethod` nebo `invokeMethodAsync`.</span><span class="sxs-lookup"><span data-stu-id="fbd25-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="fbd25-134">Instance rozhraní .NET může být také předána jako argument při vyvolání jiných metod rozhraní .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="fbd25-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="fbd25-135">Ukázková aplikace protokoluje zprávy do konzoly na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="fbd25-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="fbd25-136">Pro následující příklady znázorněné ukázkovou aplikací si Projděte výstup konzoly v prohlížeči v vývojářských nástrojích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="fbd25-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="fbd25-137">Když je vybráno tlačítko **aktivovat metodu instance .NET HelloHelper. sayHello** , je volána `ExampleJsInterop.CallHelloHelperSayHello` a předá do metody název, `Blazor`.</span><span class="sxs-lookup"><span data-stu-id="fbd25-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="fbd25-138">*Stránky/JsInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="fbd25-138">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="fbd25-139">`CallHelloHelperSayHello` vyvolá funkci JavaScriptu `sayHello` s novou instancí `HelloHelper`.</span><span class="sxs-lookup"><span data-stu-id="fbd25-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="fbd25-140">*JsInteropClasses/ExampleJsInterop. cs*:</span><span class="sxs-lookup"><span data-stu-id="fbd25-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="fbd25-141">*wwwroot/exampleJsInterop. js*:</span><span class="sxs-lookup"><span data-stu-id="fbd25-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="fbd25-142">Název je předán konstruktoru `HelloHelper`, který nastaví vlastnost `HelloHelper.Name`.</span><span class="sxs-lookup"><span data-stu-id="fbd25-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="fbd25-143">Je-li funkce JavaScriptu `sayHello` spuštěna, vrátí `HelloHelper.SayHello` zprávu `Hello, {Name}!`, která je zapsána do konzoly funkcí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="fbd25-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="fbd25-144">*JsInteropClasses/HelloHelper. cs*:</span><span class="sxs-lookup"><span data-stu-id="fbd25-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="fbd25-145">Výstup konzoly v vývojářských nástrojích webu v prohlížeči:</span><span class="sxs-lookup"><span data-stu-id="fbd25-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="fbd25-146">Aby nedošlo k nevrácení paměti a povolovalo uvolňování paměti na komponentě, která vytváří `DotNetObjectReference`, proveďte jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fbd25-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="fbd25-147">Dispose objektu ve třídě, která vytvořila instanci `DotNetObjectReference`:</span><span class="sxs-lookup"><span data-stu-id="fbd25-147">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

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

  <span data-ttu-id="fbd25-148">Předchozí vzor zobrazený ve třídě `ExampleJsInterop` lze také implementovat do komponenty:</span><span class="sxs-lookup"><span data-stu-id="fbd25-148">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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

* <span data-ttu-id="fbd25-149">Pokud komponenta nebo třída nevyřadí `DotNetObjectReference`, Dispose objektu na straně klienta voláním `.dispose()`:</span><span class="sxs-lookup"><span data-stu-id="fbd25-149">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="fbd25-150">Volání metody instance komponenty</span><span class="sxs-lookup"><span data-stu-id="fbd25-150">Component instance method call</span></span>

<span data-ttu-id="fbd25-151">Postup při volání metod .NET komponenty:</span><span class="sxs-lookup"><span data-stu-id="fbd25-151">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="fbd25-152">Použijte funkci `invokeMethod` nebo `invokeMethodAsync` k vytvoření statické metody pro volání do komponenty.</span><span class="sxs-lookup"><span data-stu-id="fbd25-152">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="fbd25-153">Statická metoda součásti zabalí volání své metody instance jako vyvolanou `Action`.</span><span class="sxs-lookup"><span data-stu-id="fbd25-153">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="fbd25-154">V JavaScriptu na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="fbd25-154">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="fbd25-155">*Stránky/JSInteropComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="fbd25-155">*Pages/JSInteropComponent.razor*:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @_message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action _action;
    private string _message = "Select the button.";

    protected override void OnInitialized()
    {
        _action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

<span data-ttu-id="fbd25-156">V případě, že existuje několik komponent, z nichž každá má metody instance pro volání, použijte pomocnou třídu k vyvolání metod instance (jako `Action`) jednotlivých komponent.</span><span class="sxs-lookup"><span data-stu-id="fbd25-156">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="fbd25-157">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="fbd25-157">In the following example:</span></span>

* <span data-ttu-id="fbd25-158">Součást `JSInterop` obsahuje několik součástí `ListItem`.</span><span class="sxs-lookup"><span data-stu-id="fbd25-158">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="fbd25-159">Každá komponenta `ListItem` se skládá ze zprávy a tlačítka.</span><span class="sxs-lookup"><span data-stu-id="fbd25-159">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="fbd25-160">Když je vybráno tlačítko komponenty `ListItem`, `ListItem``UpdateMessage` metoda změní text položky seznamu a skryje tlačítko.</span><span class="sxs-lookup"><span data-stu-id="fbd25-160">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="fbd25-161">*MessageUpdateInvokeHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="fbd25-161">*MessageUpdateInvokeHelper.cs*:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action _action;

    public MessageUpdateInvokeHelper(Action action)
    {
        _action = action;
    }

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        _action.Invoke();
    }
}
```

<span data-ttu-id="fbd25-162">V JavaScriptu na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="fbd25-162">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="fbd25-163">*Shared/ListItem. Razor*:</span><span class="sxs-lookup"><span data-stu-id="fbd25-163">*Shared/ListItem.razor*:</span></span>

```razor
@inject IJSRuntime JsRuntime

<li>
    @_message
    <button @onclick="InteropCall" style="display:@_display">InteropCall</button>
</li>

@code {
    private string _message = "Select one of these list item buttons.";
    private string _display = "inline-block";
    private MessageUpdateInvokeHelper _messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        _messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JsRuntime.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(_messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        _message = "UpdateMessage Called!";
        _display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="fbd25-164">*Stránky/JSInterop. Razor*:</span><span class="sxs-lookup"><span data-stu-id="fbd25-164">*Pages/JSInterop.razor*:</span></span>

```razor
@page "/JSInterop"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="additional-resources"></a><span data-ttu-id="fbd25-165">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fbd25-165">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="fbd25-166">Příklad InteropComponent. Razor (dotnet/AspNetCore, úložiště GitHub, větev vydání 3,1)</span><span class="sxs-lookup"><span data-stu-id="fbd25-166">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="fbd25-167">[Provádění rozsáhlých přenosů dat v Blazor serverových aplikacích](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="fbd25-167">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
