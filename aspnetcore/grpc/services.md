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
# <a name="create-grpc-services-and-methods"></a><span data-ttu-id="2a7bd-103">Vytváření služeb a metod gRPC</span><span class="sxs-lookup"><span data-stu-id="2a7bd-103">Create gRPC services and methods</span></span>

<span data-ttu-id="2a7bd-104">Tento dokument vysvětluje, jak vytvořit služby a metody gRPC v jazyce C#.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-104">This document explains how to create gRPC services and methods in C#.</span></span> <span data-ttu-id="2a7bd-105">Témata:</span><span class="sxs-lookup"><span data-stu-id="2a7bd-105">Topics include:</span></span>

* <span data-ttu-id="2a7bd-106">Jak definovat služby a metody v souboru *..* ........</span><span class="sxs-lookup"><span data-stu-id="2a7bd-106">How to define services and methods in *.proto* files.</span></span>
* <span data-ttu-id="2a7bd-107">Generovaný kód pomocí nástrojů gRPC jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-107">Generated code using gRPC C# tooling.</span></span>
* <span data-ttu-id="2a7bd-108">Implementace služeb a metod gRPC</span><span class="sxs-lookup"><span data-stu-id="2a7bd-108">Implementing gRPC services and methods.</span></span>

## <a name="create-new-grpc-services"></a><span data-ttu-id="2a7bd-109">Vytvoření nových služeb gRPC Services</span><span class="sxs-lookup"><span data-stu-id="2a7bd-109">Create new gRPC services</span></span>

<span data-ttu-id="2a7bd-110">[Služba gRPC Services s jazykem C#](xref:grpc/basics) zavádí přístup k vývoji rozhraní API, který se poprvé gRPC.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-110">[gRPC services with C#](xref:grpc/basics) introduced gRPC's contract-first approach to API development.</span></span> <span data-ttu-id="2a7bd-111">Služby a zprávy jsou definovány v souboru *.*</span><span class="sxs-lookup"><span data-stu-id="2a7bd-111">Services and messages are defined in *.proto* files.</span></span> <span data-ttu-id="2a7bd-112">Nástroje jazyka C# pak generují kód ze souborů *.*</span><span class="sxs-lookup"><span data-stu-id="2a7bd-112">C# tooling then generates code from *.proto* files.</span></span> <span data-ttu-id="2a7bd-113">U prostředků na straně serveru je pro každou službu vygenerován abstraktní základní typ, spolu s třídami pro všechny zprávy.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-113">For server-side assets, an abstract base type is generated for each service, along with classes for any messages.</span></span>

<span data-ttu-id="2a7bd-114">Následující soubor *.... proto* :</span><span class="sxs-lookup"><span data-stu-id="2a7bd-114">The following *.proto* file:</span></span>

* <span data-ttu-id="2a7bd-115">Definuje `Greeter` službu.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-115">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="2a7bd-116">`Greeter`Služba definuje `SayHello` volání.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-116">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="2a7bd-117">`SayHello`odešle `HelloRequest` zprávu a přijme zprávu. `HelloReply`</span><span class="sxs-lookup"><span data-stu-id="2a7bd-117">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message</span></span>

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

<span data-ttu-id="2a7bd-118">Nástroje jazyka c# generují `GreeterBase` základní typ jazyka c#:</span><span class="sxs-lookup"><span data-stu-id="2a7bd-118">C# tooling generates the C# `GreeterBase` base type:</span></span>

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

<span data-ttu-id="2a7bd-119">Ve výchozím nastavení vygenerovaná není `GreeterBase` žádná akce.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-119">By default the generated `GreeterBase` doesn't do anything.</span></span> <span data-ttu-id="2a7bd-120">Jeho virtuální `SayHello` Metoda vrátí `UNIMPLEMENTED` chybu všem klientům, kteří je volají.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-120">Its virtual `SayHello` method will return an `UNIMPLEMENTED` error to any clients that call it.</span></span> <span data-ttu-id="2a7bd-121">Aby služba byla užitečná, musí vytvořit konkrétní implementaci `GreeterBase` :</span><span class="sxs-lookup"><span data-stu-id="2a7bd-121">For the service to be useful an app must create a concrete implementation of `GreeterBase`:</span></span>

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> UnaryCall(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

<span data-ttu-id="2a7bd-122">Implementace služby je zaregistrovaná v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-122">The service implementation is registered with the app.</span></span> <span data-ttu-id="2a7bd-123">Pokud je služba hostovaná pomocí ASP.NET Core gRPC, měla by být přidána do kanálu směrování s `MapGrpcService` metodou.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-123">If the service is hosted by ASP.NET Core gRPC, it should be added to the routing pipeline with the `MapGrpcService` method.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="2a7bd-124">Další informace naleznete v tématu <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-124">See <xref:grpc/aspnetcore> for more information.</span></span>

## <a name="implement-grpc-methods"></a><span data-ttu-id="2a7bd-125">Implementace metod gRPC</span><span class="sxs-lookup"><span data-stu-id="2a7bd-125">Implement gRPC methods</span></span>

<span data-ttu-id="2a7bd-126">Služba gRPC může mít různé typy metod.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-126">A gRPC service can have different types of methods.</span></span> <span data-ttu-id="2a7bd-127">Způsob odesílání a přijímání zpráv službou závisí na typu definované metody.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-127">How messages are sent and received by a service depends on the type of method defined.</span></span> <span data-ttu-id="2a7bd-128">Typy metod gRPC jsou:</span><span class="sxs-lookup"><span data-stu-id="2a7bd-128">The gRPC method types are:</span></span>

* <span data-ttu-id="2a7bd-129">Unární</span><span class="sxs-lookup"><span data-stu-id="2a7bd-129">Unary</span></span>
* <span data-ttu-id="2a7bd-130">Streamování serveru</span><span class="sxs-lookup"><span data-stu-id="2a7bd-130">Server streaming</span></span>
* <span data-ttu-id="2a7bd-131">Streamování klientů</span><span class="sxs-lookup"><span data-stu-id="2a7bd-131">Client streaming</span></span>
* <span data-ttu-id="2a7bd-132">Obousměrné streamování</span><span class="sxs-lookup"><span data-stu-id="2a7bd-132">Bi-directional streaming</span></span>

<span data-ttu-id="2a7bd-133">Volání streamování jsou určena `stream` klíčovým slovem v souboru *..* .</span><span class="sxs-lookup"><span data-stu-id="2a7bd-133">Streaming calls are specified with the `stream` keyword in the *.proto* file.</span></span> <span data-ttu-id="2a7bd-134">`stream` lze umístit do zprávy žádosti o volání, zprávy odpovědi nebo obojího.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-134">`stream` can be placed on a call's request message, response message, or both.</span></span>

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

<span data-ttu-id="2a7bd-135">Každý typ volání má jiný podpis metody.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-135">Each call type has a different method signature.</span></span> <span data-ttu-id="2a7bd-136">Přepsání vygenerovaných metod z abstraktního typu základní služby v konkrétní implementaci zajistí, že se použijí správné argumenty a návratový typ.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-136">Overriding generated methods from the abstract base service type in a concrete implementation ensures the correct arguments and return type are used.</span></span>

### <a name="unary-method"></a><span data-ttu-id="2a7bd-137">Unární metoda</span><span class="sxs-lookup"><span data-stu-id="2a7bd-137">Unary method</span></span>

<span data-ttu-id="2a7bd-138">Unární Metoda získá zprávu požadavku jako parametr a vrátí odpověď.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-138">A unary method gets the request message as a parameter, and returns the response.</span></span> <span data-ttu-id="2a7bd-139">Po vrácení odpovědi je dokončeno unární volání.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-139">A unary call is complete when the response is returned.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

<span data-ttu-id="2a7bd-140">Unární volání jsou nejvíce podobná [akcím na řadičích webového rozhraní API](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="2a7bd-140">Unary calls are the most similar to [actions on web API controllers](xref:web-api/index).</span></span> <span data-ttu-id="2a7bd-141">Jedním z důležitých rozdílů gRPC metod, které jsou z akcí, jsou metody gRPC, které nemůžou navazovat části požadavku na jiné argumenty metody.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-141">One important difference gRPC methods have from actions is gRPC methods are not able to bind parts of a request to different method arguments.</span></span> <span data-ttu-id="2a7bd-142">metody gRPC vždy mají jeden argument zprávy pro data příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-142">gRPC methods always have one message argument for the incoming request data.</span></span> <span data-ttu-id="2a7bd-143">Do služby gRPC je stále možné odeslat více hodnot tím, že je zařadíte do pole žádosti:</span><span class="sxs-lookup"><span data-stu-id="2a7bd-143">Multiple values can still be sent to a gRPC service by making them fields on the request message:</span></span>

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a><span data-ttu-id="2a7bd-144">Metoda streamování serveru</span><span class="sxs-lookup"><span data-stu-id="2a7bd-144">Server streaming method</span></span>

<span data-ttu-id="2a7bd-145">Metoda streamování serveru získá zprávu požadavku jako parametr.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-145">A server streaming method gets the request message as a parameter.</span></span> <span data-ttu-id="2a7bd-146">Vzhledem k tomu, že se více zpráv může streamovat zpět volajícímu, `responseStream.WriteAsync` slouží k odesílání zpráv odpovědí.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-146">Because multiple messages can be streamed back to the caller, `responseStream.WriteAsync` is used to send response messages.</span></span> <span data-ttu-id="2a7bd-147">Volání streamování serveru je dokončeno, když se metoda vrátí.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-147">A server streaming call is complete when the method returns.</span></span>

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

<span data-ttu-id="2a7bd-148">Klient nemá žádný způsob, jak odeslat další zprávy nebo data po spuštění metody streamování serveru.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-148">The client has no way to send additional messages or data once the server streaming method has started.</span></span> <span data-ttu-id="2a7bd-149">Některé metody streamování jsou navržené tak, aby běžely trvale.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-149">Some streaming methods are designed to run forever.</span></span> <span data-ttu-id="2a7bd-150">Pro průběžné metody streamování může klient zrušit volání, když už není potřeba.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-150">For continuous streaming methods, a client can cancel the call when it's no longer needed.</span></span> <span data-ttu-id="2a7bd-151">V případě zrušení dojde k odeslání signálu na server a je vyvolána služba [ServerCallContext. CancellationToken](xref:System.Threading.CancellationToken) .</span><span class="sxs-lookup"><span data-stu-id="2a7bd-151">When cancellation happens the client sends a signal to the server and the [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="2a7bd-152">`CancellationToken`Token by měl být použit na serveru s asynchronními metodami tak, aby:</span><span class="sxs-lookup"><span data-stu-id="2a7bd-152">The `CancellationToken` token should be used on the server with async methods so that:</span></span>

* <span data-ttu-id="2a7bd-153">Veškerá asynchronní práce je zrušena spolu s voláním streamování.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-153">Any asynchronous work is canceled together with the streaming call.</span></span>
* <span data-ttu-id="2a7bd-154">Metoda se rychle ukončí.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-154">The method exits quickly.</span></span>

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

### <a name="client-streaming-method"></a><span data-ttu-id="2a7bd-155">Metoda streamování klientů</span><span class="sxs-lookup"><span data-stu-id="2a7bd-155">Client streaming method</span></span>

<span data-ttu-id="2a7bd-156">Metoda streamování klienta se spustí *bez* metody přijímání zprávy.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-156">A client streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="2a7bd-157">`requestStream`Parametr se používá ke čtení zpráv z klienta.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-157">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="2a7bd-158">Po vrácení zprávy s odpovědí je dokončeno volání streamování klienta:</span><span class="sxs-lookup"><span data-stu-id="2a7bd-158">A client streaming call is complete when a response message is returned:</span></span>

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

<span data-ttu-id="2a7bd-159">Při použití C# 8 nebo novější `await foreach` lze syntaxi použít ke čtení zpráv.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-159">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="2a7bd-160">`IAsyncStreamReader<T>.ReadAllAsync()`Metoda rozšíření načte všechny zprávy z datového proudu požadavků:</span><span class="sxs-lookup"><span data-stu-id="2a7bd-160">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the request stream:</span></span>

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

### <a name="bi-directional-streaming-method"></a><span data-ttu-id="2a7bd-161">Obousměrné streamování – metoda</span><span class="sxs-lookup"><span data-stu-id="2a7bd-161">Bi-directional streaming method</span></span>

<span data-ttu-id="2a7bd-162">Obousměrná metoda streamování začíná *bez* metody přijímání zprávy.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-162">A bi-directional streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="2a7bd-163">`requestStream`Parametr se používá ke čtení zpráv z klienta.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-163">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="2a7bd-164">Metoda může zvolit odeslání zprávy pomocí `responseStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="2a7bd-164">The method can choose to send messages with `responseStream.WriteAsync`.</span></span> <span data-ttu-id="2a7bd-165">Volání obousměrného streamování je dokončeno, když metoda vrátí:</span><span class="sxs-lookup"><span data-stu-id="2a7bd-165">A bi-directional streaming call is complete when the the method returns:</span></span>

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

<span data-ttu-id="2a7bd-166">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="2a7bd-166">The preceding code:</span></span>

* <span data-ttu-id="2a7bd-167">Odešle odpověď pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-167">Sends a response for each request.</span></span>
* <span data-ttu-id="2a7bd-168">Je základní využití obousměrného streamování.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-168">Is a basic usage of bi-directional streaming.</span></span>

<span data-ttu-id="2a7bd-169">Je možné podporovat složitější scénáře, jako je například čtení žádostí a posílání odpovědí současně:</span><span class="sxs-lookup"><span data-stu-id="2a7bd-169">It is possible to support more complex scenarios, such as reading requests and sending responses simultaneously:</span></span>

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

<span data-ttu-id="2a7bd-170">V obousměrné streamování může klient a služba kdykoli posílat zprávy.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-170">In a bi-directional streaming method, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="2a7bd-171">Nejlepší implementace obousměrné metody se liší v závislosti na požadavcích.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-171">The best implementation of a bi-directional method varies depending upon requirements.</span></span>

## <a name="access-grpc-request-headers"></a><span data-ttu-id="2a7bd-172">Přístup k hlavičkám žádostí gRPC</span><span class="sxs-lookup"><span data-stu-id="2a7bd-172">Access gRPC request headers</span></span>

<span data-ttu-id="2a7bd-173">Zpráva požadavku není jediným způsobem, jak může klient odesílat data do služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="2a7bd-173">A request message is not the only way for a client to send data to a gRPC service.</span></span> <span data-ttu-id="2a7bd-174">Hodnoty hlaviček jsou k dispozici ve službě pomocí `ServerCallContext.RequestHeaders` .</span><span class="sxs-lookup"><span data-stu-id="2a7bd-174">Header values are available in a service using `ServerCallContext.RequestHeaders`.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a><span data-ttu-id="2a7bd-175">Další materiály</span><span class="sxs-lookup"><span data-stu-id="2a7bd-175">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/client>
