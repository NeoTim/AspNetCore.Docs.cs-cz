---
title: Volání metod .NET z funkcí JavaScriptu v ASP.NET Core Blazor
author: guardrex
description: Naučte se volat metody .NET z funkcí JavaScriptu v Blazor aplikacích.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
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
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: 50b79cdb17542e4db57154274f9a7356967253bd
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102702"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a>Volání metod .NET z funkcí JavaScriptu v ASP.NET Core Blazor

[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Skořepa](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)a [Luke Latham](https://github.com/guardrex)

BlazorAplikace může vyvolat funkce JavaScriptu z metod .NET a metod .NET z funkcí jazyka JavaScript. Tyto scénáře se nazývají *interoperabilita JavaScriptu* (interoperabilita*js*).

Tento článek se zabývá vyvoláním metod .NET z JavaScriptu. Informace o volání funkcí jazyka JavaScript z rozhraní .NET naleznete v tématu <xref:blazor/call-javascript-from-dotnet> .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Statické volání metody .NET

Chcete-li vyvolat statickou metodu .NET z JavaScriptu, použijte `DotNet.invokeMethod` `DotNet.invokeMethodAsync` funkce nebo. Předejte identifikátor statické metody, kterou chcete volat, název sestavení obsahující funkce a všechny argumenty. Asynchronní verze je upřednostňovaná pro podporu Blazor Server scénářů. Metoda .NET musí být veřejná, statická a musí mít [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) atribut. Volání otevřených obecných metod není aktuálně podporováno.

Ukázková aplikace obsahuje metodu C# pro návrat `int` pole. [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute)Atribut je použit pro metodu.

`Pages/JsInterop.razor`:

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

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Pokud **`Trigger .NET static method ReturnArrayAsync`** je vybráno tlačítko, Projděte si výstup konzoly v části webové nástroje pro vývojáře v prohlížeči.

Výstup konzoly:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Čtvrtá hodnota pole je vložena do pole ( `data.push(4);` ) vráceného `ReturnArrayAsync` .

Ve výchozím nastavení je identifikátor metody název metody, ale můžete zadat jiný identifikátor pomocí [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) konstruktoru atributu:

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
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace aplikace (například `BlazorSample` ).

## <a name="instance-method-call"></a>Volání metody instance

Můžete také volat metody instance rozhraní .NET z JavaScriptu. Vyvolání metody instance rozhraní .NET z JavaScriptu:

* Předání instance rozhraní .NET odkazem na jazyk JavaScript:
  * Proveďte statické volání <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> .
  * Zabalte instanci v <xref:Microsoft.JSInterop.DotNetObjectReference> instanci a zavolejte <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> na <xref:Microsoft.JSInterop.DotNetObjectReference> instanci. Dispose <xref:Microsoft.JSInterop.DotNetObjectReference> objektů (příklad se zobrazí později v této části).
* Vyvolat metody instance .NET v instanci pomocí `invokeMethod` `invokeMethodAsync` funkcí nebo. Instance rozhraní .NET může být také předána jako argument při vyvolání jiných metod rozhraní .NET z JavaScriptu.

> [!NOTE]
> Ukázková aplikace protokoluje zprávy do konzoly na straně klienta. Pro následující příklady znázorněné ukázkovou aplikací si Projděte výstup konzoly v prohlížeči v vývojářských nástrojích prohlížeče.

Když je **`Trigger .NET instance method HelloHelper.SayHello`** vybráno tlačítko, `ExampleJsInterop.CallHelloHelperSayHello` je volána a předá `Blazor` do metody název.

`Pages/JsInterop.razor`:

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

`CallHelloHelperSayHello` vyvolá funkci JavaScriptu `sayHello` s novou instancí `HelloHelper` .

`JsInteropClasses/ExampleJsInterop.cs`:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

`wwwroot/exampleJsInterop.js`:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Název je předán `HelloHelper` konstruktoru, který nastaví `HelloHelper.Name` vlastnost. Při spuštění funkce JavaScriptu `sayHello` `HelloHelper.SayHello` vrátí `Hello, {Name}!` zprávu, která je zapsána do konzoly funkcí JavaScriptu.

`JsInteropClasses/HelloHelper.cs`:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Výstup konzoly v vývojářských nástrojích webu v prohlížeči:

```console
Hello, Blazor!
```

Aby nedošlo k nevrácení paměti a povolovalo uvolňování paměti na komponentě, která vytváří <xref:Microsoft.JSInterop.DotNetObjectReference> , proveďte jeden z následujících přístupů:

* Dispose objektu ve třídě, která vytvořila <xref:Microsoft.JSInterop.DotNetObjectReference> instanci:

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
  
  Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace aplikace (například `BlazorSample` ).

* Pokud komponenta nebo třída neodstraní, vyřadí <xref:Microsoft.JSInterop.DotNetObjectReference> objekt na straně klienta voláním `.dispose()` :

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Volání metody instance komponenty

Postup při volání metod .NET komponenty:

* Pomocí `invokeMethod` funkce or `invokeMethodAsync` vytvořte statické volání metody do komponenty.
* Statická metoda součásti zabalí volání své metody instance jako vyvolanou <xref:System.Action> .

> [!NOTE]
> Pro Blazor Server aplikace, ve kterých může být několik uživatelů souběžně používáno pomocí stejné komponenty, použijte pomocnou třídu k vyvolání instančních metod.
>
> Další informace naleznete v části [pomocná třída metody instance komponenty](#component-instance-method-helper-class) .

V JavaScriptu na straně klienta:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace aplikace (například `BlazorSample` ).

`Pages/JSInteropComponent.razor`:

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

Předání argumentů metodě instance:

* Přidejte parametry do vyvolání metody JS. V následujícím příkladu je do metody předán název. Do seznamu můžete podle potřeby přidat další parametry.

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace aplikace (například `BlazorSample` ).

* Zadejte správné typy <xref:System.Action> pro parametry. Zadejte seznam parametrů pro metody jazyka C#. Vyvolat <xref:System.Action> ( `UpdateMessage` ) s parametry ( `action.Invoke(name)` ).

  `Pages/JSInteropComponent.razor`:

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  Výstup `message` , když je vybráno tlačítko **metoda volání js** :

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a>Pomocná třída metody instance komponenty

Pomocná třída se používá k vyvolání metody instance jako <xref:System.Action> . Pomocné třídy jsou užitečné, když:

* Na stejné stránce je vykresleno několik komponent stejného typu.
* Blazor ServerPoužije se aplikace, ve které může souběžně používat součást více uživatelů.

V následujícím příkladu:

* `JSInteropExample`Komponenta obsahuje několik `ListItem` komponent.
* Každá `ListItem` Komponenta se skládá ze zprávy a tlačítka.
* Když `ListItem` je vybráno tlačítko komponenty, tato `ListItem` `UpdateMessage` metoda změní text položky seznamu a skryje tlačítko.

`MessageUpdateInvokeHelper.cs`:

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

V JavaScriptu na straně klienta:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

Zástupný symbol `{APP ASSEMBLY}` je název sestavení aplikace aplikace (například `BlazorSample` ).

`Shared/ListItem.razor`:

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

`Pages/JSInteropExample.razor`:

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

## <a name="avoid-circular-object-references"></a>Vyhnout se cyklickým odkazům na objekty

Objekty, které obsahují kruhové odkazy, nelze v klientovi serializovat pro:

* Volání metod .NET.
* Volání metody JavaScriptu z C#, když návratový typ obsahuje cyklické odkazy.

Další informace najdete v následujících problémech:

* [Cyklické odkazy nejsou podporované, musí být dva (dotnet/aspnetcore #20525).](https://github.com/dotnet/aspnetcore/issues/20525)
* [Návrh: Přidání mechanismu pro zpracování cyklických odkazů při serializaci (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Další zdroje informací

* <xref:blazor/call-javascript-from-dotnet>
* [`InteropComponent.razor` Příklad (dotnet/AspNetCore, úložiště GitHub, větev vydané verze 3,1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Provádění rozsáhlých přenosů dat v Blazor Server aplikacích](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
