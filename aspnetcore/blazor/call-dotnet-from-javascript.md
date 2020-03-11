---
title: Volání metod .NET z funkcí JavaScriptu v ASP.NET Core Blazor
author: guardrex
description: Naučte se volat metody .NET z funkcí JavaScriptu v aplikacích Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/19/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: f4964341e261c65269eedafbbd6e676c1054f427
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659444"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-opno-locblazor"></a>Volání metod .NET z funkcí JavaScriptu v ASP.NET Core Blazor

[Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Skořepa](https://github.com/danroth27)a [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor aplikace může vyvolat funkce JavaScriptu z metod .NET a metod .NET z funkcí JavaScriptu. Tyto scénáře se nazývají *interoperabilita JavaScriptu* (interoperabilita*js*).

Tento článek se zabývá vyvoláním metod .NET z JavaScriptu. Informace o volání funkcí jazyka JavaScript z rozhraní .NET naleznete v tématu <xref:blazor/call-javascript-from-dotnet>.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="static-net-method-call"></a>Statické volání metody .NET

Chcete-li vyvolat statickou metodu .NET z JavaScriptu, použijte funkce `DotNet.invokeMethod` nebo `DotNet.invokeMethodAsync`. Předejte identifikátor statické metody, kterou chcete volat, název sestavení obsahující funkce a všechny argumenty. Asynchronní verze je preferována k podpoře Blazorch scénářů serveru. Metoda .NET musí být veřejná, statická a musí mít atribut `[JSInvokable]`. Volání otevřených obecných metod není aktuálně podporováno.

Ukázková aplikace obsahuje C# metodu, která vrátí `int` pole. Atribut `JSInvokable` se aplikuje na metodu.

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

JavaScript, který obsluhuje klient, vyvolá C# metodu .NET.

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

Pokud je vybráno tlačítko **aktivovat .NET static Method ReturnArrayAsync** , Projděte si výstup konzoly v webových vývojářských nástrojích prohlížeče.

Výstup konzoly:

```console
Array(4) [ 1, 2, 3, 4 ]
```

Hodnota čtvrtého pole je vložena do pole (`data.push(4);`) vráceného `ReturnArrayAsync`.

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
  * Zabalte instanci v instanci `DotNetObjectReference` a volejte `Create` na instanci `DotNetObjectReference`. Dispose objektů `DotNetObjectReference` (příklad se zobrazí později v této části).
* Vyvolat metody instance .NET v instanci pomocí funkcí `invokeMethod` nebo `invokeMethodAsync`. Instance rozhraní .NET může být také předána jako argument při vyvolání jiných metod rozhraní .NET z JavaScriptu.

> [!NOTE]
> Ukázková aplikace protokoluje zprávy do konzoly na straně klienta. Pro následující příklady znázorněné ukázkovou aplikací si Projděte výstup konzoly v prohlížeči v vývojářských nástrojích prohlížeče.

Když je vybráno tlačítko **aktivovat metodu instance .NET HelloHelper. sayHello** , je volána `ExampleJsInterop.CallHelloHelperSayHello` a předá do metody název, `Blazor`.

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

`CallHelloHelperSayHello` vyvolá funkci JavaScriptu `sayHello` s novou instancí `HelloHelper`.

*JsInteropClasses/ExampleJsInterop. cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=10-16)]

*wwwroot/exampleJsInterop. js*:

[!code-javascript[](./common/samples/3.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

Název je předán konstruktoru `HelloHelper`, který nastaví vlastnost `HelloHelper.Name`. Je-li funkce JavaScriptu `sayHello` spuštěna, vrátí `HelloHelper.SayHello` zprávu `Hello, {Name}!`, která je zapsána do konzoly funkcí JavaScriptu.

*JsInteropClasses/HelloHelper. cs*:

[!code-csharp[](./common/samples/3.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

Výstup konzoly v vývojářských nástrojích webu v prohlížeči:

```console
Hello, Blazor!
```

Aby nedošlo k nevrácení paměti a povolovalo uvolňování paměti na komponentě, která vytváří `DotNetObjectReference`, Dispose objektu ve třídě, která vytvořila instanci `DotNetObjectReference`:

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
  
Předchozí vzor zobrazený ve třídě `ExampleJsInterop` lze také implementovat do komponenty:
  
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

[!INCLUDE[Share interop code in a class library](~/includes/blazor-share-interop-code.md)]

## <a name="additional-resources"></a>Další zdroje

* <xref:blazor/call-javascript-from-dotnet>
* [Příklad InteropComponent. Razor (dotnet/AspNetCore, úložiště GitHub, větev vydání 3,1)](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
* [Provádění rozsáhlých přenosů dat v Blazor serverových aplikacích](xref:blazor/advanced-scenarios#perform-large-data-transfers-in-blazor-server-apps)
