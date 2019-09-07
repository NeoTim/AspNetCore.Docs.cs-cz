---
title: Volání služeb gRPC Services pomocí klienta .NET
author: jamesnk
description: Naučte se volat služby gRPC Services pomocí klienta .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 27e4b3e7307ae49bacb01a46fbc1b55b6967c7a0
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773696"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="c28f7-103">Volání služeb gRPC Services pomocí klienta .NET</span><span class="sxs-lookup"><span data-stu-id="c28f7-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="c28f7-104">Klientská knihovna .NET gRPC je k dispozici v balíčku NuGet pro [gRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="c28f7-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="c28f7-105">Tento dokument vysvětluje, jak:</span><span class="sxs-lookup"><span data-stu-id="c28f7-105">This document explains how to:</span></span>

* <span data-ttu-id="c28f7-106">Nakonfigurujte klienta gRPC, který bude volat služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="c28f7-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="c28f7-107">GRPC volání unárního, streamování serveru, streamování klientů a metod obousměrného streamování.</span><span class="sxs-lookup"><span data-stu-id="c28f7-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="c28f7-108">Konfigurace klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="c28f7-108">Configure gRPC client</span></span>

<span data-ttu-id="c28f7-109">gRPC klienti jsou konkrétní typy klientů, které jsou [vygenerovány ze  *\*souborů. proto* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="c28f7-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="c28f7-110">Konkrétní klient gRPC má metody, které se převádějí do služby gRPC v  *\*souboru..* .</span><span class="sxs-lookup"><span data-stu-id="c28f7-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="c28f7-111">Klient gRPC se vytvoří z kanálu.</span><span class="sxs-lookup"><span data-stu-id="c28f7-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="c28f7-112">Začněte tím `GrpcChannel.ForAddress` , že použijete k vytvoření kanálu a pak pomocí kanálu vytvoříte klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="c28f7-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="c28f7-113">Kanál představuje dlouhodobé připojení ke službě gRPC.</span><span class="sxs-lookup"><span data-stu-id="c28f7-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="c28f7-114">Při vytvoření kanálu se nakonfiguruje s možnostmi, které se týkají volání služby.</span><span class="sxs-lookup"><span data-stu-id="c28f7-114">When a channel is created it is configured with options related to calling a service.</span></span> <span data-ttu-id="c28f7-115">Například, který `HttpClient` se používá k volání, maximální velikost zprávy Send a Receive a protokolování lze zadat v `GrpcChannelOptions` a použít s `GrpcChannel.ForAddress`.</span><span class="sxs-lookup"><span data-stu-id="c28f7-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="c28f7-116">Úplný seznam možností najdete v tématu [Možnosti konfigurace klienta](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="c28f7-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

<span data-ttu-id="c28f7-117">Vytvoření kanálu může být náročná operace a opakované použití kanálu pro volání gRPC nabízí výhody týkající se výkonu.</span><span class="sxs-lookup"><span data-stu-id="c28f7-117">Creating a channel can be an expensive operation and reusing a channel for gRPC calls offers performance benefits.</span></span> <span data-ttu-id="c28f7-118">Z kanálu, včetně různých typů klientů, lze vytvořit více konkrétních gRPC klientů.</span><span class="sxs-lookup"><span data-stu-id="c28f7-118">Multiple concrete gRPC clients can be created from a channel, including different types of clients.</span></span> <span data-ttu-id="c28f7-119">Konkrétní typy klientů gRPC jsou prosté objekty a v případě potřeby je lze vytvořit.</span><span class="sxs-lookup"><span data-stu-id="c28f7-119">Concrete gRPC client types are lightweight objects and can be created when needed.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="c28f7-120">`GrpcChannel.ForAddress`není jedinou možností pro vytvoření klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="c28f7-120">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="c28f7-121">Pokud voláte služby gRPC Services z aplikace ASP.NET Core, zvažte [integraci klientské továrny gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="c28f7-121">If you are calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="c28f7-122">integrace gRPC s `HttpClientFactory` nabízí centralizovanou alternativu k vytváření klientů gRPC.</span><span class="sxs-lookup"><span data-stu-id="c28f7-122">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="c28f7-123">Provést volání gRPC</span><span class="sxs-lookup"><span data-stu-id="c28f7-123">Make gRPC calls</span></span>

<span data-ttu-id="c28f7-124">Volání gRPC je zahájeno voláním metody na klientovi.</span><span class="sxs-lookup"><span data-stu-id="c28f7-124">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="c28f7-125">Klient gRPC zpracuje serializaci zprávy a adresování volání gRPC ke správné službě.</span><span class="sxs-lookup"><span data-stu-id="c28f7-125">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="c28f7-126">gRPC má různé typy metod.</span><span class="sxs-lookup"><span data-stu-id="c28f7-126">gRPC has different types of methods.</span></span> <span data-ttu-id="c28f7-127">Způsob, jakým se používá klient pro volání gRPC, závisí na typu volané metody.</span><span class="sxs-lookup"><span data-stu-id="c28f7-127">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="c28f7-128">Typy metod gRPC jsou:</span><span class="sxs-lookup"><span data-stu-id="c28f7-128">The gRPC method types are:</span></span>

* <span data-ttu-id="c28f7-129">Unární</span><span class="sxs-lookup"><span data-stu-id="c28f7-129">Unary</span></span>
* <span data-ttu-id="c28f7-130">Streamování serveru</span><span class="sxs-lookup"><span data-stu-id="c28f7-130">Server streaming</span></span>
* <span data-ttu-id="c28f7-131">Streamování klientů</span><span class="sxs-lookup"><span data-stu-id="c28f7-131">Client streaming</span></span>
* <span data-ttu-id="c28f7-132">Obousměrné streamování</span><span class="sxs-lookup"><span data-stu-id="c28f7-132">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="c28f7-133">Unární volání</span><span class="sxs-lookup"><span data-stu-id="c28f7-133">Unary call</span></span>

<span data-ttu-id="c28f7-134">Unární volání začíná klientem, který odesílá zprávu požadavku.</span><span class="sxs-lookup"><span data-stu-id="c28f7-134">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="c28f7-135">Po dokončení služby se vrátí zpráva s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="c28f7-135">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="c28f7-136">Každá unární metoda služby v  *\*souboru.* proč má za následek dvě metody .NET pro konkrétní typ klienta gRPC pro volání metody: asynchronní metodu a metodu blokování.</span><span class="sxs-lookup"><span data-stu-id="c28f7-136">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="c28f7-137">Například `GreeterClient` existují dva způsoby volání `SayHello`:</span><span class="sxs-lookup"><span data-stu-id="c28f7-137">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="c28f7-138">`GreeterClient.SayHelloAsync`-volá `Greeter.SayHello` službu asynchronně.</span><span class="sxs-lookup"><span data-stu-id="c28f7-138">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="c28f7-139">Může být očekáváno.</span><span class="sxs-lookup"><span data-stu-id="c28f7-139">Can be awaited.</span></span>
* <span data-ttu-id="c28f7-140">`GreeterClient.SayHello`-volá `Greeter.SayHello` službu a zablokuje se do dokončení.</span><span class="sxs-lookup"><span data-stu-id="c28f7-140">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="c28f7-141">Nepoužívejte v asynchronním kódu.</span><span class="sxs-lookup"><span data-stu-id="c28f7-141">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="c28f7-142">Volání streamování serveru</span><span class="sxs-lookup"><span data-stu-id="c28f7-142">Server streaming call</span></span>

<span data-ttu-id="c28f7-143">Volání streamování serveru začíná klientem, který odesílá zprávu požadavku.</span><span class="sxs-lookup"><span data-stu-id="c28f7-143">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="c28f7-144">`ResponseStream.MoveNext()`přečte zprávy streamované ze služby.</span><span class="sxs-lookup"><span data-stu-id="c28f7-144">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="c28f7-145">Volání streamování serveru je po `ResponseStream.MoveNext()` návratu `false`dokončeno.</span><span class="sxs-lookup"><span data-stu-id="c28f7-145">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    while (await call.ResponseStream.MoveNext())
    {
        Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

<span data-ttu-id="c28f7-146">Pokud používáte C# 8 nebo novější `await foreach` , můžete syntaxi použít ke čtení zpráv.</span><span class="sxs-lookup"><span data-stu-id="c28f7-146">If you are using C# 8 or later then the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="c28f7-147">Metoda `IAsyncStreamReader<T>.ReadAllAsync()` rozšíření načte všechny zprávy z datového proudu odpovědí:</span><span class="sxs-lookup"><span data-stu-id="c28f7-147">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine("Greeting: " + response.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="c28f7-148">Volání streamování klientů</span><span class="sxs-lookup"><span data-stu-id="c28f7-148">Client streaming call</span></span>

<span data-ttu-id="c28f7-149">Volání streamování klienta se spustí *bez* odeslání zprávy klientem.</span><span class="sxs-lookup"><span data-stu-id="c28f7-149">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="c28f7-150">Klient se může rozhodnout, že odešle zprávy `RequestStream.WriteAsync`Send.</span><span class="sxs-lookup"><span data-stu-id="c28f7-150">The client can choose to send sends messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="c28f7-151">Až klient dokončí odesílání zpráv `RequestStream.CompleteAsync` , měla by se volat oznámení služby.</span><span class="sxs-lookup"><span data-stu-id="c28f7-151">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="c28f7-152">Volání je dokončeno, když služba vrátí zprávu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c28f7-152">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using (var call = client.AccumulateCount())
{
    for (var i = 0; i < 3; i++)
    {
        await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
    }
    await call.RequestStream.CompleteAsync();

    var response = await call;
    Console.WriteLine($"Count: {response.Count}");
    // Count: 3
}
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="c28f7-153">Obousměrné volání streamování</span><span class="sxs-lookup"><span data-stu-id="c28f7-153">Bi-directional streaming call</span></span>

<span data-ttu-id="c28f7-154">Obousměrné volání streamování se spustí *bez* odeslání zprávy klientem.</span><span class="sxs-lookup"><span data-stu-id="c28f7-154">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="c28f7-155">Klient nástroje může odeslat zprávy pomocí `RequestStream.WriteAsync`příkazu.</span><span class="sxs-lookup"><span data-stu-id="c28f7-155">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="c28f7-156">Zprávy streamované ze služby jsou přístupné pomocí `ResponseStream.MoveNext()` nástroje nebo. `ResponseStream.ReadAllAsync()`</span><span class="sxs-lookup"><span data-stu-id="c28f7-156">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="c28f7-157">Obousměrné volání streamování je dokončeno, když `ResponseStream` nemá žádné další zprávy.</span><span class="sxs-lookup"><span data-stu-id="c28f7-157">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
using (var call = client.Echo())
{
    Console.WriteLine("Starting background task to receive messages");
    var readTask = Task.Run(async () =>
    {
        await foreach (var response in call.ResponseStream.ReadAllAsync())
        {
            Console.WriteLine(response.Message);
            // Echo messages sent to the service
        }
    });

    Console.WriteLine("Starting to send messages");
    Console.WriteLine("Type a message to echo then press enter.");
    while (true)
    {
        var result = Console.ReadLine();
        if (string.IsNullOrEmpty(result))
        {
            break;
        }

        await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
    }

    Console.WriteLine("Disconnecting");
    await call.RequestStream.CompleteAsync();
    await readTask;
}
```

<span data-ttu-id="c28f7-158">Během volání obousměrného streamování může klient a služba kdykoli odesílat zprávy.</span><span class="sxs-lookup"><span data-stu-id="c28f7-158">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="c28f7-159">Nejlepší klientská logika pro interakci s obousměrným voláním se liší v závislosti na logice služby.</span><span class="sxs-lookup"><span data-stu-id="c28f7-159">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c28f7-160">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c28f7-160">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
