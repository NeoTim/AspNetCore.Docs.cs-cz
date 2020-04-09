---
title: Volání metod .NET z funkcí JavaScriptu v ASP.NET CoreBlazor
author: guardrex
description: Přečtěte si, jak vyvolat metody Blazor .NET z funkcí JavaScriptu v aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: e2344dd15efd243a405373b6cf0362f28b48173a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976947"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a><span data-ttu-id="25fc3-103">Volání metod .NET z funkcí JavaScriptu v ASP.NET CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="25fc3-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="25fc3-104">[Javier Calvarro Nelson](https://github.com/javiercn), Daniel [Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="25fc3-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="25fc3-105">Aplikace Blazor může vyvolat funkce JavaScriptu z metod .NET a .NET z funkcí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="25fc3-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="25fc3-106">Tyto scénáře se nazývají *interoperabilita JavaScriptu* *(JS interop).*</span><span class="sxs-lookup"><span data-stu-id="25fc3-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="25fc3-107">Tento článek popisuje vyvolání metod .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="25fc3-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="25fc3-108">Informace o volání funkcí JavaScriptu z <xref:blazor/call-javascript-from-dotnet>rozhraní .NET naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="25fc3-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="25fc3-109">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="25fc3-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="25fc3-110">Statické volání metody .NET</span><span class="sxs-lookup"><span data-stu-id="25fc3-110">Static .NET method call</span></span>

<span data-ttu-id="25fc3-111">Chcete-li vyvolat statickou metodu .NET z JavaScriptu, použijte funkce `DotNet.invokeMethod` or. `DotNet.invokeMethodAsync`</span><span class="sxs-lookup"><span data-stu-id="25fc3-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="25fc3-112">Předejte identifikátor statické metody, kterou chcete volat, název sestavení obsahující funkci a všechny argumenty.</span><span class="sxs-lookup"><span data-stu-id="25fc3-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="25fc3-113">Asynchronní verze je upřednostňována pro podporu Blazor scénářů serveru.</span><span class="sxs-lookup"><span data-stu-id="25fc3-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="25fc3-114">Metoda .NET musí být veřejná, `[JSInvokable]` statická a musí mít atribut.</span><span class="sxs-lookup"><span data-stu-id="25fc3-114">The .NET method must be public, static, and have the `[JSInvokable]` attribute.</span></span> <span data-ttu-id="25fc3-115">Volání otevřených obecných metod není aktuálně podporováno.</span><span class="sxs-lookup"><span data-stu-id="25fc3-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="25fc3-116">Ukázková aplikace obsahuje metodu C# pro vrácení `int` pole.</span><span class="sxs-lookup"><span data-stu-id="25fc3-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="25fc3-117">Atribut `JSInvokable` je použit pro metodu.</span><span class="sxs-lookup"><span data-stu-id="25fc3-117">The `JSInvokable` attribute is applied to the method.</span></span>

<span data-ttu-id="25fc3-118">*Stránky/JsInterop.břitva*:</span><span class="sxs-lookup"><span data-stu-id="25fc3-118">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="25fc3-119">JavaScript obsluhovaný klientovi vyvolá metodu C# .NET.</span><span class="sxs-lookup"><span data-stu-id="25fc3-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="25fc3-120">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="25fc3-120">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="25fc3-121">Když je vybráno tlačítko **Trigger .NET static method ReturnArrayAsync,** zkontrolujte výstup konzoly v nástrojích webového vývojáře prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="25fc3-121">When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="25fc3-122">Výstup konzoly je:</span><span class="sxs-lookup"><span data-stu-id="25fc3-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="25fc3-123">Čtvrtá hodnota pole je posunuta do pole (`data.push(4);`) vráceného . `ReturnArrayAsync`</span><span class="sxs-lookup"><span data-stu-id="25fc3-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="25fc3-124">Ve výchozím nastavení je identifikátor metody název metody, ale můžete `JSInvokableAttribute` zadat jiný identifikátor pomocí konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="25fc3-124">By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute` constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="25fc3-125">V souboru JavaScript na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="25fc3-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a><span data-ttu-id="25fc3-126">Volání metody instance</span><span class="sxs-lookup"><span data-stu-id="25fc3-126">Instance method call</span></span>

<span data-ttu-id="25fc3-127">Můžete také volat metody instance .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="25fc3-127">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="25fc3-128">Postup vyvolání metody instance .NET z JavaScriptu:</span><span class="sxs-lookup"><span data-stu-id="25fc3-128">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="25fc3-129">Předajte instanci .NET odkazem na JavaScript:</span><span class="sxs-lookup"><span data-stu-id="25fc3-129">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="25fc3-130">Proveďte statické `DotNetObjectReference.Create`volání .</span><span class="sxs-lookup"><span data-stu-id="25fc3-130">Make a static call to `DotNetObjectReference.Create`.</span></span>
  * <span data-ttu-id="25fc3-131">Zabalit instanci `DotNetObjectReference` v `Create` instanci a volání na `DotNetObjectReference` instanci.</span><span class="sxs-lookup"><span data-stu-id="25fc3-131">Wrap the instance in a `DotNetObjectReference` instance and call `Create` on the `DotNetObjectReference` instance.</span></span> <span data-ttu-id="25fc3-132">Zlikvidujte `DotNetObjectReference` objekty (příklad se zobrazí dále v této části).</span><span class="sxs-lookup"><span data-stu-id="25fc3-132">Dispose of `DotNetObjectReference` objects (an example appears later in this section).</span></span>
* <span data-ttu-id="25fc3-133">Vyvolat metody instance .NET na `invokeMethod` `invokeMethodAsync` instanci pomocí funkce or.</span><span class="sxs-lookup"><span data-stu-id="25fc3-133">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="25fc3-134">Instanci .NET lze také předat jako argument při vyvolání jiných metod .NET z JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="25fc3-134">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="25fc3-135">Ukázková aplikace protokoluje zprávy do konzole na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="25fc3-135">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="25fc3-136">Následující příklady demonstrované ukázkovou aplikací našetujte výstup konzoly prohlížeče v vývojářských nástrojích prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="25fc3-136">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="25fc3-137">Když je **vybrána metoda instance Trigger .NET HelloHelper.SayHello,** `ExampleJsInterop.CallHelloHelperSayHello` `Blazor`je volána a předá metodě název , .</span><span class="sxs-lookup"><span data-stu-id="25fc3-137">When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="25fc3-138">*Stránky/JsInterop.břitva*:</span><span class="sxs-lookup"><span data-stu-id="25fc3-138">*Pages/JsInterop.razor*:</span></span>

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

<span data-ttu-id="25fc3-139">`CallHelloHelperSayHello`vyvolá funkci `sayHello` JavaScript s novou `HelloHelper`instancí aplikace .</span><span class="sxs-lookup"><span data-stu-id="25fc3-139">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="25fc3-140">*JsInteropClasses/ExampleJsInterop.cs*:</span><span class="sxs-lookup"><span data-stu-id="25fc3-140">*JsInteropClasses/ExampleJsInterop.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="25fc3-141">*wwwroot/exampleJsInterop.js*:</span><span class="sxs-lookup"><span data-stu-id="25fc3-141">*wwwroot/exampleJsInterop.js*:</span></span>

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="25fc3-142">Název je předán `HelloHelper`konstruktoru společnosti , `HelloHelper.Name` který nastaví vlastnost.</span><span class="sxs-lookup"><span data-stu-id="25fc3-142">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="25fc3-143">Po spuštění funkce `sayHello` JavaScript `HelloHelper.SayHello` vrátí `Hello, {Name}!` zprávu, která je do konzoly zapsána funkcí JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="25fc3-143">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="25fc3-144">*JsInteropClasses/HelloHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="25fc3-144">*JsInteropClasses/HelloHelper.cs*:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="25fc3-145">Výstup konzoly v nástrojích pro webové vývojáře prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="25fc3-145">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="25fc3-146">Chcete-li se vyhnout nevracení paměti a `DotNetObjectReference`povolit uvolňování paměti na součást, která vytvoří , přijmout jeden z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="25fc3-146">To avoid a memory leak and allow garbage collection on a component that creates a `DotNetObjectReference`, adopt one of the following approaches:</span></span>

* <span data-ttu-id="25fc3-147">Dispose objektu ve třídě, `DotNetObjectReference` která vytvořila instanci:</span><span class="sxs-lookup"><span data-stu-id="25fc3-147">Dispose of the object in the class that created the `DotNetObjectReference` instance:</span></span>

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

  <span data-ttu-id="25fc3-148">Předchozí vzor zobrazený `ExampleJsInterop` ve třídě lze také implementovat v komponentě:</span><span class="sxs-lookup"><span data-stu-id="25fc3-148">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

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

* <span data-ttu-id="25fc3-149">Pokud součást nebo třída není vyřazení `DotNetObjectReference`, nakládat objekt na `.dispose()`straně klienta voláním :</span><span class="sxs-lookup"><span data-stu-id="25fc3-149">When the component or class doesn't dispose of the `DotNetObjectReference`, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="25fc3-150">Volání metody instance komponenty</span><span class="sxs-lookup"><span data-stu-id="25fc3-150">Component instance method call</span></span>

<span data-ttu-id="25fc3-151">Chcete-li vyvolat metody rozhraní .NET komponenty:</span><span class="sxs-lookup"><span data-stu-id="25fc3-151">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="25fc3-152">Pomocí `invokeMethod` funkce `invokeMethodAsync` nebo můžete provést volání komponenty statickou metodou.</span><span class="sxs-lookup"><span data-stu-id="25fc3-152">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="25fc3-153">Statická metoda komponenty zabalí volání metody instance jako `Action`vyvolání .</span><span class="sxs-lookup"><span data-stu-id="25fc3-153">The component's static method wraps the call to its instance method as an invoked `Action`.</span></span>

<span data-ttu-id="25fc3-154">V javascriptu na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="25fc3-154">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

<span data-ttu-id="25fc3-155">*Stránky/JSInteropComponent.holicí strojek*:</span><span class="sxs-lookup"><span data-stu-id="25fc3-155">*Pages/JSInteropComponent.razor*:</span></span>

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

<span data-ttu-id="25fc3-156">Pokud existuje několik součástí, z nichž každá s metodami instance volat, použijte `Action`pomocnou třídu k vyvolání metod instance (jako s) každé součásti.</span><span class="sxs-lookup"><span data-stu-id="25fc3-156">When there are several components, each with instance methods to call, use a helper class to invoke the instance methods (as `Action`s) of each component.</span></span>

<span data-ttu-id="25fc3-157">V následujícím příkladu:</span><span class="sxs-lookup"><span data-stu-id="25fc3-157">In the following example:</span></span>

* <span data-ttu-id="25fc3-158">Komponenta `JSInterop` obsahuje `ListItem` několik součástí.</span><span class="sxs-lookup"><span data-stu-id="25fc3-158">The `JSInterop` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="25fc3-159">Každá `ListItem` součást se skládá ze zprávy a tlačítka.</span><span class="sxs-lookup"><span data-stu-id="25fc3-159">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="25fc3-160">Je-li vybráno `ListItem` tlačítko `ListItem`komponenty, `UpdateMessage` tato metoda změní text položky seznamu a skryje tlačítko.</span><span class="sxs-lookup"><span data-stu-id="25fc3-160">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="25fc3-161">*MessageUpdateInvokeHelper.cs*:</span><span class="sxs-lookup"><span data-stu-id="25fc3-161">*MessageUpdateInvokeHelper.cs*:</span></span>

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

<span data-ttu-id="25fc3-162">V javascriptu na straně klienta:</span><span class="sxs-lookup"><span data-stu-id="25fc3-162">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="25fc3-163">*Shared/ListItem.holicí strojek*:</span><span class="sxs-lookup"><span data-stu-id="25fc3-163">*Shared/ListItem.razor*:</span></span>

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

<span data-ttu-id="25fc3-164">*Stránky/JSInterop.břitva*:</span><span class="sxs-lookup"><span data-stu-id="25fc3-164">*Pages/JSInterop.razor*:</span></span>

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

## <a name="avoid-circular-object-references"></a><span data-ttu-id="25fc3-165">Vyhněte se cyklické odkazy na objekty</span><span class="sxs-lookup"><span data-stu-id="25fc3-165">Avoid circular object references</span></span>

<span data-ttu-id="25fc3-166">Objekty, které obsahují cyklické odkazy nelze serializovat na straně klienta pro:</span><span class="sxs-lookup"><span data-stu-id="25fc3-166">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="25fc3-167">Volání metody .NET.</span><span class="sxs-lookup"><span data-stu-id="25fc3-167">.NET method calls.</span></span>
* <span data-ttu-id="25fc3-168">JavaScript metoda volá z Jazyka C#, pokud návratový typ má cyklické odkazy.</span><span class="sxs-lookup"><span data-stu-id="25fc3-168">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="25fc3-169">Další informace naleznete v následujících problémech:</span><span class="sxs-lookup"><span data-stu-id="25fc3-169">For more information, see the following issues:</span></span>

* [<span data-ttu-id="25fc3-170">Cyklické odkazy nejsou podporovány, trvat dva (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="25fc3-170">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="25fc3-171">Návrh: Přidání mechanismu pro zpracování cyklické odkazy při serializace (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="25fc3-171">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a><span data-ttu-id="25fc3-172">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="25fc3-172">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="25fc3-173">Příklad InteropComponent.razor (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span><span class="sxs-lookup"><span data-stu-id="25fc3-173">InteropComponent.razor example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* <span data-ttu-id="25fc3-174">[Provádění velkých přenosů dat v Blazor serverových aplikacích](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span><span class="sxs-lookup"><span data-stu-id="25fc3-174">[Perform large data transfers in Blazor Server apps](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)</span></span>
