---
title: Volání metod .NET z funkcí JavaScriptu v ASP.NET CoreBlazor
author: guardrex
description: Naučte se volat metody .NET z funkcí JavaScriptu v Blazor aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 9eab35f3fd856debf9f0305aedf012d63b9b800a
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967321"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-blazor"></a>Volání metod .NET z funkcí JavaScriptu v ASP.NET CoreBlazor

[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Skořepa](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor Aplikace může vyvolat funkce JavaScriptu z metod .NET a metod .NET z funkcí jazyka JavaScript. Tyto scénáře se nazývají *interoperabilita JavaScriptu* (interoperabilita*js*).

Tento článek se zabývá vyvoláním metod .NET z JavaScriptu. Informace o volání funkcí jazyka JavaScript z rozhraní .NET naleznete v tématu <xref:blazor/call-javascript-from-dotnet>.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Statické volání metody .NET

Chcete-li vyvolat statickou metodu .NET z JavaScriptu, použijte `DotNet.invokeMethod` funkce `DotNet.invokeMethodAsync` nebo. Předejte identifikátor statické metody, kterou chcete volat, název sestavení obsahující funkce a všechny argumenty. Asynchronní verze je preferována pro podporu Blazor scénářů serveru. Metoda .NET musí být veřejná, statická a musí mít `[JSInvokable]` atribut. Volání otevřených obecných metod není aktuálně podporováno.

Ukázková aplikace obsahuje metodu C# pro návrat `int` pole. `JSInvokable` Atribut je použit pro metodu.

*Stránky/JsInterop. Razor*:

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

JavaScript, který obsluhuje klient, vyvolá metodu C# .NET.

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Pokud je vybráno tlačítko **aktivovat .NET static Method ReturnArrayAsync** , Projděte si výstup konzoly v webových vývojářských nástrojích prohlížeče.

Výstup konzoly:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Čtvrtá hodnota pole je vložena do pole (`data.push(4);`) vráceného `ReturnArrayAsync`.

Ve výchozím nastavení je identifikátor metody název metody, ale můžete zadat jiný identifikátor pomocí `JSInvokableAttribute` konstruktoru:

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

V souboru JavaScriptu na straně klienta:

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('BlazorSample', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

## <a name="instance-method-call"></a>Volání metody instance

Můžete také volat metody instance rozhraní .NET z JavaScriptu. Vyvolání metody instance rozhraní .NET z JavaScriptu:

* Předání instance rozhraní .NET odkazem na jazyk JavaScript:
  * Proveďte statické volání `DotNetObjectReference.Create`.
  * Zabalte instanci v `DotNetObjectReference` instanci a zavolejte `Create` na `DotNetObjectReference` instanci. Dispose `DotNetObjectReference` objektů (příklad se zobrazí později v této části).
* Vyvolat metody instance .NET v instanci pomocí funkcí `invokeMethod` nebo. `invokeMethodAsync` Instance rozhraní .NET může být také předána jako argument při vyvolání jiných metod rozhraní .NET z JavaScriptu.

> [!NOTE]
> Ukázková aplikace protokoluje zprávy do konzoly na straně klienta. Pro následující příklady znázorněné ukázkovou aplikací si Projděte výstup konzoly v prohlížeči v vývojářských nástrojích prohlížeče.

Když je vybráno tlačítko **aktivovat metodu instance .NET HelloHelper. sayHello** , `ExampleJsInterop.CallHelloHelperSayHello` je volána a předá metodě název `Blazor`.

*Stránky/JsInterop. Razor*:

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

`CallHelloHelperSayHello`vyvolá funkci `sayHello` JavaScriptu s novou instancí `HelloHelper`.

*JsInteropClasses/ExampleJsInterop. cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Název je předán `HelloHelper`konstruktoru, který nastaví `HelloHelper.Name` vlastnost. Při spuštění funkce `sayHello` JavaScriptu `HelloHelper.SayHello` vrátí `Hello, {Name}!` zprávu, která je zapsána do konzoly funkcí JavaScriptu.

*JsInteropClasses/HelloHelper. cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Výstup konzoly v vývojářských nástrojích webu v prohlížeči:

```console
Hello, Blazor!
```

Aby nedošlo k nevrácení paměti a povolovalo uvolňování paměti na komponentě, která vytváří `DotNetObjectReference`, proveďte jeden z následujících přístupů:

* Dispose objektu ve třídě, která vytvořila `DotNetObjectReference` instanci:

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

  Předchozí vzor zobrazený ve `ExampleJsInterop` třídě může být také implementován v součásti:

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

* Pokud komponenta nebo třída neodstraní `DotNetObjectReference`, vyřadí objekt na straně klienta voláním: `.dispose()`

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Volání metody instance komponenty

Postup při volání metod .NET komponenty:

* Pomocí funkce `invokeMethod` or `invokeMethodAsync` vytvořte statické volání metody do komponenty.
* Statická metoda součásti zabalí volání své metody instance jako vyvolanou `Action`.

V JavaScriptu na straně klienta:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

*Stránky/JSInteropComponent. Razor*:

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

V případě, že existuje několik komponent, z nichž každá má metody instance pro volání, použijte pomocnou třídu k vyvolání metod `Action`instancí jednotlivých komponent.

V následujícím příkladu:

* `JSInterop` Komponenta obsahuje několik `ListItem` komponent.
* Každá `ListItem` komponenta se skládá ze zprávy a tlačítka.
* Když je `ListItem` vybráno tlačítko komponenty, tato `ListItem` `UpdateMessage` metoda změní text položky seznamu a skryje tlačítko.

*MessageUpdateInvokeHelper.cs*:

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

    [JSInvokable("BlazorSample")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

V JavaScriptu na straně klienta:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

*Shared/ListItem. Razor*:

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

*Stránky/JSInterop. Razor*:

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

## <a name="avoid-circular-object-references"></a>Vyhnout se cyklickým odkazům na objekty

Objekty, které obsahují kruhové odkazy, nelze v klientovi serializovat pro:

* Volání metod .NET.
* Volání metody JavaScriptu z C#, když návratový typ obsahuje cyklické odkazy.

Další informace najdete v následujících problémech:

* [Cyklické odkazy nejsou podporované, musí být dva (dotnet/aspnetcore #20525).](https://github.com/dotnet/aspnetcore/issues/20525)
* [Návrh: Přidání mechanismu pro zpracování cyklických odkazů při serializaci (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:blazor/call-javascript-from-dotnet>
* [Příklad InteropComponent. Razor (dotnet/AspNetCore, úložiště GitHub, větev vydání 3,1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Provádění rozsáhlých přenosů Blazor dat v serverových aplikacích](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
