---
title: Volání služeb gRPC Services pomocí klienta .NET
author: jamesnk
description: Naučte se volat služby gRPC Services pomocí klienta .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 56f79b303a8d53699e8eb6156d328c0da1259416
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011136"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="4f065-103">Volání služeb gRPC Services pomocí klienta .NET</span><span class="sxs-lookup"><span data-stu-id="4f065-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="4f065-104">Klientská knihovna .NET gRPC je k dispozici v balíčku NuGet pro [gRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="4f065-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="4f065-105">Tento dokument vysvětluje, jak:</span><span class="sxs-lookup"><span data-stu-id="4f065-105">This document explains how to:</span></span>

* <span data-ttu-id="4f065-106">Nakonfigurujte klienta gRPC, který bude volat služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="4f065-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="4f065-107">GRPC volání unárního, streamování serveru, streamování klientů a metod obousměrného streamování.</span><span class="sxs-lookup"><span data-stu-id="4f065-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="4f065-108">Konfigurace klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="4f065-108">Configure gRPC client</span></span>

<span data-ttu-id="4f065-109">gRPC klienti jsou konkrétní typy klientů, které jsou [vygenerovány ze  *\*souborů. proto* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="4f065-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="4f065-110">Konkrétní klient gRPC má metody, které se převádějí do služby gRPC v  *\*souboru..* .</span><span class="sxs-lookup"><span data-stu-id="4f065-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="4f065-111">Klient gRPC se vytvoří z kanálu.</span><span class="sxs-lookup"><span data-stu-id="4f065-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="4f065-112">Začněte tím `GrpcChannel.ForAddress` , že použijete k vytvoření kanálu a pak pomocí kanálu vytvoříte klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="4f065-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="4f065-113">Kanál představuje dlouhodobé připojení ke službě gRPC.</span><span class="sxs-lookup"><span data-stu-id="4f065-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="4f065-114">Při vytvoření kanálu se nakonfiguruje s možnostmi, které se týkají volání služby.</span><span class="sxs-lookup"><span data-stu-id="4f065-114">When a channel is created it is configured with options related to calling a service.</span></span> <span data-ttu-id="4f065-115">Například, který `HttpClient` se používá k volání, maximální velikost zprávy Send a Receive a protokolování lze zadat v `GrpcChannelOptions` a použít s `GrpcChannel.ForAddress`.</span><span class="sxs-lookup"><span data-stu-id="4f065-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="4f065-116">Úplný seznam možností najdete v tématu [Možnosti konfigurace klienta](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="4f065-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

<span data-ttu-id="4f065-117">Vytvoření kanálu může být náročná operace a opakované použití kanálu pro volání gRPC nabízí výhody týkající se výkonu.</span><span class="sxs-lookup"><span data-stu-id="4f065-117">Creating a channel can be an expensive operation and reusing a channel for gRPC calls offers performance benefits.</span></span> <span data-ttu-id="4f065-118">Z kanálu, včetně různých typů klientů, lze vytvořit více konkrétních gRPC klientů.</span><span class="sxs-lookup"><span data-stu-id="4f065-118">Multiple concrete gRPC clients can be created from a channel, including different types of clients.</span></span> <span data-ttu-id="4f065-119">Konkrétní typy klientů gRPC jsou prosté objekty a v případě potřeby je lze vytvořit.</span><span class="sxs-lookup"><span data-stu-id="4f065-119">Concrete gRPC client types are lightweight objects and can be created when needed.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="4f065-120">`GrpcChannel.ForAddress`není jedinou možností pro vytvoření klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="4f065-120">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="4f065-121">Pokud voláte služby gRPC Services z aplikace ASP.NET Core, zvažte [integraci klientské továrny gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="4f065-121">If you are calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="4f065-122">integrace gRPC s `HttpClientFactory` nabízí centralizovanou alternativu k vytváření klientů gRPC.</span><span class="sxs-lookup"><span data-stu-id="4f065-122">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="4f065-123">Pro [volání nezabezpečených služeb gRPC s klientem .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)je vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4f065-123">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="4f065-124">Provést volání gRPC</span><span class="sxs-lookup"><span data-stu-id="4f065-124">Make gRPC calls</span></span>

<span data-ttu-id="4f065-125">Volání gRPC je zahájeno voláním metody na klientovi.</span><span class="sxs-lookup"><span data-stu-id="4f065-125">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="4f065-126">Klient gRPC zpracuje serializaci zprávy a adresování volání gRPC ke správné službě.</span><span class="sxs-lookup"><span data-stu-id="4f065-126">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="4f065-127">gRPC má různé typy metod.</span><span class="sxs-lookup"><span data-stu-id="4f065-127">gRPC has different types of methods.</span></span> <span data-ttu-id="4f065-128">Způsob, jakým se používá klient pro volání gRPC, závisí na typu volané metody.</span><span class="sxs-lookup"><span data-stu-id="4f065-128">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="4f065-129">Typy metod gRPC jsou:</span><span class="sxs-lookup"><span data-stu-id="4f065-129">The gRPC method types are:</span></span>

* <span data-ttu-id="4f065-130">Unární</span><span class="sxs-lookup"><span data-stu-id="4f065-130">Unary</span></span>
* <span data-ttu-id="4f065-131">Streamování serveru</span><span class="sxs-lookup"><span data-stu-id="4f065-131">Server streaming</span></span>
* <span data-ttu-id="4f065-132">Streamování klientů</span><span class="sxs-lookup"><span data-stu-id="4f065-132">Client streaming</span></span>
* <span data-ttu-id="4f065-133">Obousměrné streamování</span><span class="sxs-lookup"><span data-stu-id="4f065-133">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="4f065-134">Unární volání</span><span class="sxs-lookup"><span data-stu-id="4f065-134">Unary call</span></span>

<span data-ttu-id="4f065-135">Unární volání začíná klientem, který odesílá zprávu požadavku.</span><span class="sxs-lookup"><span data-stu-id="4f065-135">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="4f065-136">Po dokončení služby se vrátí zpráva s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="4f065-136">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="4f065-137">Každá unární metoda služby v  *\*souboru.* proč má za následek dvě metody .NET pro konkrétní typ klienta gRPC pro volání metody: asynchronní metodu a metodu blokování.</span><span class="sxs-lookup"><span data-stu-id="4f065-137">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="4f065-138">Například `GreeterClient` existují dva způsoby volání `SayHello`:</span><span class="sxs-lookup"><span data-stu-id="4f065-138">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="4f065-139">`GreeterClient.SayHelloAsync`-volá `Greeter.SayHello` službu asynchronně.</span><span class="sxs-lookup"><span data-stu-id="4f065-139">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="4f065-140">Může být očekáváno.</span><span class="sxs-lookup"><span data-stu-id="4f065-140">Can be awaited.</span></span>
* <span data-ttu-id="4f065-141">`GreeterClient.SayHello`-volá `Greeter.SayHello` službu a zablokuje se do dokončení.</span><span class="sxs-lookup"><span data-stu-id="4f065-141">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="4f065-142">Nepoužívejte v asynchronním kódu.</span><span class="sxs-lookup"><span data-stu-id="4f065-142">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="4f065-143">Volání streamování serveru</span><span class="sxs-lookup"><span data-stu-id="4f065-143">Server streaming call</span></span>

<span data-ttu-id="4f065-144">Volání streamování serveru začíná klientem, který odesílá zprávu požadavku.</span><span class="sxs-lookup"><span data-stu-id="4f065-144">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="4f065-145">`ResponseStream.MoveNext()`přečte zprávy streamované ze služby.</span><span class="sxs-lookup"><span data-stu-id="4f065-145">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="4f065-146">Volání streamování serveru je po `ResponseStream.MoveNext()` návratu `false`dokončeno.</span><span class="sxs-lookup"><span data-stu-id="4f065-146">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

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

<span data-ttu-id="4f065-147">Pokud používáte C# 8 nebo novější `await foreach` , můžete syntaxi použít ke čtení zpráv.</span><span class="sxs-lookup"><span data-stu-id="4f065-147">If you are using C# 8 or later then the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="4f065-148">Metoda `IAsyncStreamReader<T>.ReadAllAsync()` rozšíření načte všechny zprávy z datového proudu odpovědí:</span><span class="sxs-lookup"><span data-stu-id="4f065-148">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

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

### <a name="client-streaming-call"></a><span data-ttu-id="4f065-149">Volání streamování klientů</span><span class="sxs-lookup"><span data-stu-id="4f065-149">Client streaming call</span></span>

<span data-ttu-id="4f065-150">Volání streamování klienta se spustí *bez* odeslání zprávy klientem.</span><span class="sxs-lookup"><span data-stu-id="4f065-150">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="4f065-151">Klient se může rozhodnout, že odešle zprávy `RequestStream.WriteAsync`Send.</span><span class="sxs-lookup"><span data-stu-id="4f065-151">The client can choose to send sends messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="4f065-152">Až klient dokončí odesílání zpráv `RequestStream.CompleteAsync` , měla by se volat oznámení služby.</span><span class="sxs-lookup"><span data-stu-id="4f065-152">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="4f065-153">Volání je dokončeno, když služba vrátí zprávu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="4f065-153">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="4f065-154">Obousměrné volání streamování</span><span class="sxs-lookup"><span data-stu-id="4f065-154">Bi-directional streaming call</span></span>

<span data-ttu-id="4f065-155">Obousměrné volání streamování se spustí *bez* odeslání zprávy klientem.</span><span class="sxs-lookup"><span data-stu-id="4f065-155">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="4f065-156">Klient nástroje může odeslat zprávy pomocí `RequestStream.WriteAsync`příkazu.</span><span class="sxs-lookup"><span data-stu-id="4f065-156">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="4f065-157">Zprávy streamované ze služby jsou přístupné pomocí `ResponseStream.MoveNext()` nástroje nebo. `ResponseStream.ReadAllAsync()`</span><span class="sxs-lookup"><span data-stu-id="4f065-157">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="4f065-158">Obousměrné volání streamování je dokončeno, když `ResponseStream` nemá žádné další zprávy.</span><span class="sxs-lookup"><span data-stu-id="4f065-158">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="4f065-159">Během volání obousměrného streamování může klient a služba kdykoli odesílat zprávy.</span><span class="sxs-lookup"><span data-stu-id="4f065-159">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="4f065-160">Nejlepší klientská logika pro interakci s obousměrným voláním se liší v závislosti na logice služby.</span><span class="sxs-lookup"><span data-stu-id="4f065-160">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4f065-161">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4f065-161">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
