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
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a>Volání metod .NET z funkcí JavaScriptu v ASP.NET CoreBlazor

[Javier Calvarro Nelson](https://github.com/javiercn), Daniel [Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co)a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Aplikace Blazor může vyvolat funkce JavaScriptu z metod .NET a .NET z funkcí JavaScriptu. Tyto scénáře se nazývají *interoperabilita JavaScriptu* *(JS interop).*

Tento článek popisuje vyvolání metod .NET z JavaScriptu. Informace o volání funkcí JavaScriptu z <xref:blazor/call-javascript-from-dotnet>rozhraní .NET naleznete v tématu .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="static-net-method-call"></a>Statické volání metody .NET

Chcete-li vyvolat statickou metodu .NET z JavaScriptu, použijte funkce `DotNet.invokeMethod` or. `DotNet.invokeMethodAsync` Předejte identifikátor statické metody, kterou chcete volat, název sestavení obsahující funkci a všechny argumenty. Asynchronní verze je upřednostňována pro podporu Blazor scénářů serveru. Metoda .NET musí být veřejná, `[JSInvokable]` statická a musí mít atribut. Volání otevřených obecných metod není aktuálně podporováno.

Ukázková aplikace obsahuje metodu C# pro vrácení `int` pole. Atribut `JSInvokable` je použit pro metodu.

*Stránky/JsInterop.břitva*:

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

JavaScript obsluhovaný klientovi vyvolá metodu C# .NET.

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Když je vybráno tlačítko **Trigger .NET static method ReturnArrayAsync,** zkontrolujte výstup konzoly v nástrojích webového vývojáře prohlížeče.

Výstup konzoly je:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Čtvrtá hodnota pole je posunuta do pole (`data.push(4);`) vráceného . `ReturnArrayAsync`

Ve výchozím nastavení je identifikátor metody název metody, ale můžete `JSInvokableAttribute` zadat jiný identifikátor pomocí konstruktoru:

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

V souboru JavaScript na straně klienta:

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

Můžete také volat metody instance .NET z JavaScriptu. Postup vyvolání metody instance .NET z JavaScriptu:

* Předajte instanci .NET odkazem na JavaScript:
  * Proveďte statické `DotNetObjectReference.Create`volání .
  * Zabalit instanci `DotNetObjectReference` v `Create` instanci a volání na `DotNetObjectReference` instanci. Zlikvidujte `DotNetObjectReference` objekty (příklad se zobrazí dále v této části).
* Vyvolat metody instance .NET na `invokeMethod` `invokeMethodAsync` instanci pomocí funkce or. Instanci .NET lze také předat jako argument při vyvolání jiných metod .NET z JavaScriptu.

> [!NOTE]
> Ukázková aplikace protokoluje zprávy do konzole na straně klienta. Následující příklady demonstrované ukázkovou aplikací našetujte výstup konzoly prohlížeče v vývojářských nástrojích prohlížeče.

Když je **vybrána metoda instance Trigger .NET HelloHelper.SayHello,** `ExampleJsInterop.CallHelloHelperSayHello` `Blazor`je volána a předá metodě název , .

*Stránky/JsInterop.břitva*:

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

`CallHelloHelperSayHello`vyvolá funkci `sayHello` JavaScript s novou `HelloHelper`instancí aplikace .

*JsInteropClasses/ExampleJsInterop.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

*wwwroot/exampleJsInterop.js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Název je předán `HelloHelper`konstruktoru společnosti , `HelloHelper.Name` který nastaví vlastnost. Po spuštění funkce `sayHello` JavaScript `HelloHelper.SayHello` vrátí `Hello, {Name}!` zprávu, která je do konzoly zapsána funkcí JavaScriptu.

*JsInteropClasses/HelloHelper.cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Výstup konzoly v nástrojích pro webové vývojáře prohlížeče:

```console
Hello, Blazor!
```

Chcete-li se vyhnout nevracení paměti a `DotNetObjectReference`povolit uvolňování paměti na součást, která vytvoří , přijmout jeden z následujících přístupů:

* Dispose objektu ve třídě, `DotNetObjectReference` která vytvořila instanci:

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

  Předchozí vzor zobrazený `ExampleJsInterop` ve třídě lze také implementovat v komponentě:

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

* Pokud součást nebo třída není vyřazení `DotNetObjectReference`, nakládat objekt na `.dispose()`straně klienta voláním :

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a>Volání metody instance komponenty

Chcete-li vyvolat metody rozhraní .NET komponenty:

* Pomocí `invokeMethod` funkce `invokeMethodAsync` nebo můžete provést volání komponenty statickou metodou.
* Statická metoda komponenty zabalí volání metody instance jako `Action`vyvolání .

V javascriptu na straně klienta:

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethod('BlazorSample', 'UpdateMessageCaller');
}
```

*Stránky/JSInteropComponent.holicí strojek*:

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

Pokud existuje několik součástí, z nichž každá s metodami instance volat, použijte `Action`pomocnou třídu k vyvolání metod instance (jako s) každé součásti.

V následujícím příkladu:

* Komponenta `JSInterop` obsahuje `ListItem` několik součástí.
* Každá `ListItem` součást se skládá ze zprávy a tlačítka.
* Je-li vybráno `ListItem` tlačítko `ListItem`komponenty, `UpdateMessage` tato metoda změní text položky seznamu a skryje tlačítko.

*MessageUpdateInvokeHelper.cs*:

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

V javascriptu na straně klienta:

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethod('BlazorSample', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

*Shared/ListItem.holicí strojek*:

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

*Stránky/JSInterop.břitva*:

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

## <a name="avoid-circular-object-references"></a>Vyhněte se cyklické odkazy na objekty

Objekty, které obsahují cyklické odkazy nelze serializovat na straně klienta pro:

* Volání metody .NET.
* JavaScript metoda volá z Jazyka C#, pokud návratový typ má cyklické odkazy.

Další informace naleznete v následujících problémech:

* [Cyklické odkazy nejsou podporovány, trvat dva (dotnet/aspnetcore #20525)](https://github.com/dotnet/aspnetcore/issues/20525)
* [Návrh: Přidání mechanismu pro zpracování cyklické odkazy při serializace (dotnet/runtime #30820)](https://github.com/dotnet/runtime/issues/30820)

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/call-javascript-from-dotnet>
* [Příklad InteropComponent.razor (dotnet/AspNetCore GitHub repository, 3.1 release branch)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Provádění velkých přenosů dat v Blazor serverových aplikacích](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
