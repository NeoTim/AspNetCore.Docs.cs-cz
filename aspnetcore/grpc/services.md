---
title: Vytváření služeb a metod gRPC
author: jamesnk
description: Naučte se vytvářet gRPC služby a metody.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
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
uid: grpc/services
ms.openlocfilehash: fde589b2de9d908db26a2557c5f57c715625aadc
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945663"
---
# <a name="create-grpc-services-and-methods"></a>Vytváření služeb a metod gRPC

Tento dokument vysvětluje, jak vytvořit služby a metody gRPC v jazyce C#. Témata:

* Jak definovat služby a metody v souboru *..* ........
* Generovaný kód pomocí nástrojů gRPC jazyka C#.
* Implementace služeb a metod gRPC

## <a name="create-new-grpc-services"></a>Vytvoření nových služeb gRPC Services

[Služba gRPC Services s jazykem C#](xref:grpc/basics) zavádí přístup k vývoji rozhraní API, který se poprvé gRPC. Služby a zprávy jsou definovány v souboru *.* Nástroje jazyka C# pak generují kód ze souborů *.* U prostředků na straně serveru je pro každou službu vygenerován abstraktní základní typ, spolu s třídami pro všechny zprávy.

Následující soubor *.... proto* :

* Definuje `Greeter` službu.
* `Greeter`Služba definuje `SayHello` volání.
* `SayHello`odešle `HelloRequest` zprávu a přijme zprávu. `HelloReply`

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

Nástroje jazyka c# generují `GreeterBase` základní typ jazyka c#:

```csharp
public abstract partial class GreeterBase
{
    public virtual Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        throw new RpcException(new Status(StatusCode.Unimplemented, ""));
    }
}

public class HelloRequest
{
    public string Name { get; set; }
}

public class HelloReply
{
    public string Message { get; set; }
}
```

Ve výchozím nastavení vygenerovaná není `GreeterBase` žádná akce. Jeho virtuální `SayHello` Metoda vrátí `UNIMPLEMENTED` chybu všem klientům, kteří je volají. Aby služba byla užitečná, musí vytvořit konkrétní implementaci `GreeterBase` :

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> UnaryCall(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

Implementace služby je zaregistrovaná v aplikaci. Pokud je služba hostovaná pomocí ASP.NET Core gRPC, měla by být přidána do kanálu směrování s `MapGrpcService` metodou.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Další informace naleznete v tématu <xref:grpc/aspnetcore>.

## <a name="implement-grpc-methods"></a>Implementace metod gRPC

Služba gRPC může mít různé typy metod. Způsob odesílání a přijímání zpráv službou závisí na typu definované metody. Typy metod gRPC jsou:

* Unární
* Streamování serveru
* Streamování klientů
* Obousměrné streamování

Volání streamování jsou určena `stream` klíčovým slovem v souboru *..* . `stream` lze umístit do zprávy žádosti o volání, zprávy odpovědi nebo obojího.

```protobuf
syntax = "proto3";

service ExampleService {
  // Unary
  rpc UnaryCall (ExampleRequest) returns (ExampleResponse);

  // Server streaming
  rpc StreamingFromServer (ExampleRequest) returns (stream ExampleResponse);

  // Client streaming
  rpc StreamingFromClient (stream ExampleRequest) returns (ExampleResponse);

  // Bi-directional streaming
  rpc StreamingBothWays (stream ExampleRequest) returns (stream ExampleResponse);
}
```

Každý typ volání má jiný podpis metody. Přepsání vygenerovaných metod z abstraktního typu základní služby v konkrétní implementaci zajistí, že se použijí správné argumenty a návratový typ.

### <a name="unary-method"></a>Unární metoda

Unární Metoda získá zprávu požadavku jako parametr a vrátí odpověď. Po vrácení odpovědi je dokončeno unární volání.

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

Unární volání jsou nejvíce podobná [akcím na řadičích webového rozhraní API](xref:web-api/index). Jedním z důležitých rozdílů gRPC metod, které jsou z akcí, jsou metody gRPC, které nemůžou navazovat části požadavku na jiné argumenty metody. metody gRPC vždy mají jeden argument zprávy pro data příchozích požadavků. Do služby gRPC je stále možné odeslat více hodnot tím, že je zařadíte do pole žádosti:

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a>Metoda streamování serveru

Metoda streamování serveru získá zprávu požadavku jako parametr. Vzhledem k tomu, že se více zpráv může streamovat zpět volajícímu, `responseStream.WriteAsync` slouží k odesílání zpráv odpovědí. Volání streamování serveru je dokončeno, když se metoda vrátí.

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    for (var i = 0; i < 5; i++)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1));
    }
}
```

Klient nemá žádný způsob, jak odeslat další zprávy nebo data po spuštění metody streamování serveru. Některé metody streamování jsou navržené tak, aby běžely trvale. Pro průběžné metody streamování může klient zrušit volání, když už není potřeba. V případě zrušení dojde k odeslání signálu na server a je vyvolána služba [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) . `CancellationToken`Token by měl být použit na serveru s asynchronními metodami tak, aby:

* Veškerá asynchronní práce je zrušena spolu s voláním streamování.
* Metoda se rychle ukončí.

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    while (!context.CancellationToken.IsCancellationRequested)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

### <a name="client-streaming-method"></a>Metoda streamování klientů

Metoda streamování klienta se spustí *bez* metody přijímání zprávy. `requestStream`Parametr se používá ke čtení zpráv z klienta. Po vrácení zprávy s odpovědí je dokončeno volání streamování klienta:

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    while (await requestStream.MoveNext())
    {
        var message = requestStream.Current;
        // ...
    }
    return new ExampleResponse();
}
```

Při použití C# 8 nebo novější `await foreach` lze syntaxi použít ke čtení zpráv. `IAsyncStreamReader<T>.ReadAllAsync()`Metoda rozšíření načte všechny zprávy z datového proudu požadavků:

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        // ...
    }
    return new ExampleResponse();
}
```

### <a name="bi-directional-streaming-method"></a>Obousměrné streamování – metoda

Obousměrná metoda streamování začíná *bez* metody přijímání zprávy. `requestStream`Parametr se používá ke čtení zpráv z klienta. Metoda může zvolit odeslání zprávy pomocí `responseStream.WriteAsync` . Volání obousměrného streamování je dokončeno, když metoda vrátí:

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        await responseStream.WriteAsync(new ExampleResponse());
    }
}
```

Předcházející kód:

* Odešle odpověď pro každý požadavek.
* Je základní využití obousměrného streamování.

Je možné podporovat složitější scénáře, jako je například čtení žádostí a posílání odpovědí současně:

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    // Read requests in a background task.
    var readTask = Task.Run(async () =>
    {
        await foreach (var message in requestStream.ReadAllAsync())
        {
            // Process request.
        }
    });
    
    // Send responses until the client signals that it is complete.
    while (!readTask.IsCompleted)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

V obousměrné streamování může klient a služba kdykoli posílat zprávy. Nejlepší implementace obousměrné metody se liší v závislosti na požadavcích.

## <a name="access-grpc-request-headers"></a>Přístup k hlavičkám žádostí gRPC

Zpráva požadavku není jediným způsobem, jak může klient odesílat data do služby gRPC. Hodnoty hlaviček jsou k dispozici ve službě pomocí `ServerCallContext.RequestHeaders` .

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a>Další materiály

* <xref:grpc/basics>
* <xref:grpc/client>
