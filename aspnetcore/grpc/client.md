---
title: Volání služeb gRPC Services pomocí klienta .NET
author: jamesnk
description: Naučte se volat služby gRPC Services pomocí klienta .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 6a6a649f7194354b16f3d67160be02428cc01170
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667172"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="a415b-103">Volání služeb gRPC Services pomocí klienta .NET</span><span class="sxs-lookup"><span data-stu-id="a415b-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="a415b-104">Klientská knihovna .NET gRPC je k dispozici v balíčku NuGet pro [gRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="a415b-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="a415b-105">Tento dokument vysvětluje, jak:</span><span class="sxs-lookup"><span data-stu-id="a415b-105">This document explains how to:</span></span>

* <span data-ttu-id="a415b-106">Nakonfigurujte klienta gRPC, který bude volat služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="a415b-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="a415b-107">GRPC volání unárního, streamování serveru, streamování klientů a metod obousměrného streamování.</span><span class="sxs-lookup"><span data-stu-id="a415b-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="a415b-108">Konfigurace klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="a415b-108">Configure gRPC client</span></span>

<span data-ttu-id="a415b-109">gRPC klienti jsou konkrétní typy klientů, které jsou [generovány z *\*.* soubory](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="a415b-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="a415b-110">Konkrétní klient gRPC má metody, které se převádějí do služby gRPC v souboru *\*..* .</span><span class="sxs-lookup"><span data-stu-id="a415b-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="a415b-111">Klient gRPC se vytvoří z kanálu.</span><span class="sxs-lookup"><span data-stu-id="a415b-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="a415b-112">Začněte vytvořením kanálu pomocí `GrpcChannel.ForAddress` a pak pomocí kanálu vytvořte klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="a415b-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="a415b-113">Kanál představuje dlouhodobé připojení ke službě gRPC.</span><span class="sxs-lookup"><span data-stu-id="a415b-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="a415b-114">Při vytvoření kanálu se nakonfiguruje s možnostmi, které se týkají volání služby.</span><span class="sxs-lookup"><span data-stu-id="a415b-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="a415b-115">Například `HttpClient` použít k volání, maximální velikost zprávy Send a Receive a protokolování lze zadat v `GrpcChannelOptions` a použít u `GrpcChannel.ForAddress`.</span><span class="sxs-lookup"><span data-stu-id="a415b-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="a415b-116">Úplný seznam možností najdete v tématu [Možnosti konfigurace klienta](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="a415b-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="a415b-117">Výkon a využití kanálu a klienta:</span><span class="sxs-lookup"><span data-stu-id="a415b-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="a415b-118">Vytvoření kanálu může být náročná operace.</span><span class="sxs-lookup"><span data-stu-id="a415b-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="a415b-119">Použití kanálu pro volání gRPC poskytuje výhody týkající se výkonu.</span><span class="sxs-lookup"><span data-stu-id="a415b-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="a415b-120">klienti gRPC se vytvářejí pomocí kanálů.</span><span class="sxs-lookup"><span data-stu-id="a415b-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="a415b-121">gRPC klienti jsou prosté objekty a nemusíte je ukládat do mezipaměti ani je znovu používat.</span><span class="sxs-lookup"><span data-stu-id="a415b-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="a415b-122">Z kanálu, včetně různých typů klientů, lze vytvořit více klientů gRPC.</span><span class="sxs-lookup"><span data-stu-id="a415b-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="a415b-123">Kanál a klienti vytvořené z kanálu můžou být bezpečně využívány více vlákny.</span><span class="sxs-lookup"><span data-stu-id="a415b-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="a415b-124">Klienti vytvoření z kanálu můžou provádět víc souběžných volání.</span><span class="sxs-lookup"><span data-stu-id="a415b-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="a415b-125">`GrpcChannel.ForAddress` není jedinou možností pro vytvoření klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="a415b-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="a415b-126">Pokud voláte služby gRPC Services z aplikace ASP.NET Core, zvažte [integraci klientské továrny gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="a415b-126">If you're calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="a415b-127">gRPC Integration with `HttpClientFactory` nabízí centralizovanou alternativu k vytváření klientů gRPC.</span><span class="sxs-lookup"><span data-stu-id="a415b-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="a415b-128">Pro [volání nezabezpečených služeb gRPC s klientem .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)je vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a415b-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="a415b-129">Volání gRPC přes HTTP/2 s `Grpc.Net.Client` v současnosti není v Xamarin podporováno.</span><span class="sxs-lookup"><span data-stu-id="a415b-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="a415b-130">Pracujeme na vylepšení podpory protokolu HTTP/2 v budoucí verzi Xamarin.</span><span class="sxs-lookup"><span data-stu-id="a415b-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="a415b-131">[Grpc. Core](https://www.nuget.org/packages/Grpc.Core) a [Grpc-web](xref:grpc/browser) představují životaschopné alternativy, které dnes fungují.</span><span class="sxs-lookup"><span data-stu-id="a415b-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="a415b-132">Provést volání gRPC</span><span class="sxs-lookup"><span data-stu-id="a415b-132">Make gRPC calls</span></span>

<span data-ttu-id="a415b-133">Volání gRPC je zahájeno voláním metody na klientovi.</span><span class="sxs-lookup"><span data-stu-id="a415b-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="a415b-134">Klient gRPC zpracuje serializaci zprávy a adresování volání gRPC ke správné službě.</span><span class="sxs-lookup"><span data-stu-id="a415b-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="a415b-135">gRPC má různé typy metod.</span><span class="sxs-lookup"><span data-stu-id="a415b-135">gRPC has different types of methods.</span></span> <span data-ttu-id="a415b-136">Způsob, jakým se používá klient pro volání gRPC, závisí na typu volané metody.</span><span class="sxs-lookup"><span data-stu-id="a415b-136">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="a415b-137">Typy metod gRPC jsou:</span><span class="sxs-lookup"><span data-stu-id="a415b-137">The gRPC method types are:</span></span>

* <span data-ttu-id="a415b-138">Unární</span><span class="sxs-lookup"><span data-stu-id="a415b-138">Unary</span></span>
* <span data-ttu-id="a415b-139">Streamování serveru</span><span class="sxs-lookup"><span data-stu-id="a415b-139">Server streaming</span></span>
* <span data-ttu-id="a415b-140">Streamování klientů</span><span class="sxs-lookup"><span data-stu-id="a415b-140">Client streaming</span></span>
* <span data-ttu-id="a415b-141">Obousměrné streamování</span><span class="sxs-lookup"><span data-stu-id="a415b-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="a415b-142">Unární volání</span><span class="sxs-lookup"><span data-stu-id="a415b-142">Unary call</span></span>

<span data-ttu-id="a415b-143">Unární volání začíná klientem, který odesílá zprávu požadavku.</span><span class="sxs-lookup"><span data-stu-id="a415b-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="a415b-144">Po dokončení služby se vrátí zpráva s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="a415b-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="a415b-145">Každá unární metoda služby v *\*.* důvod bude mít za následek dvou metod .NET pro konkrétní typ klienta gRPC pro volání metody: asynchronní metodu a metodu blokování.</span><span class="sxs-lookup"><span data-stu-id="a415b-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="a415b-146">Například na `GreeterClient` existují dva způsoby volání `SayHello`:</span><span class="sxs-lookup"><span data-stu-id="a415b-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="a415b-147">`GreeterClient.SayHelloAsync` – volání služby `Greeter.SayHello` asynchronně.</span><span class="sxs-lookup"><span data-stu-id="a415b-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="a415b-148">Může být očekáváno.</span><span class="sxs-lookup"><span data-stu-id="a415b-148">Can be awaited.</span></span>
* <span data-ttu-id="a415b-149">`GreeterClient.SayHello` – zavolá `Greeter.SayHello` službu a zablokuje až do dokončení.</span><span class="sxs-lookup"><span data-stu-id="a415b-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="a415b-150">Nepoužívejte v asynchronním kódu.</span><span class="sxs-lookup"><span data-stu-id="a415b-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="a415b-151">Volání streamování serveru</span><span class="sxs-lookup"><span data-stu-id="a415b-151">Server streaming call</span></span>

<span data-ttu-id="a415b-152">Volání streamování serveru začíná klientem, který odesílá zprávu požadavku.</span><span class="sxs-lookup"><span data-stu-id="a415b-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="a415b-153">`ResponseStream.MoveNext()` čte zprávy streamované ze služby.</span><span class="sxs-lookup"><span data-stu-id="a415b-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="a415b-154">Volání streamování serveru je dokončeno, když `ResponseStream.MoveNext()` vrátí `false`.</span><span class="sxs-lookup"><span data-stu-id="a415b-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

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

<span data-ttu-id="a415b-155">Pokud používáte C# 8 nebo novější, je možné použít syntaxi `await foreach` ke čtení zpráv.</span><span class="sxs-lookup"><span data-stu-id="a415b-155">If you are using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="a415b-156">Metoda rozšíření `IAsyncStreamReader<T>.ReadAllAsync()` načte všechny zprávy z datového proudu odpovědí:</span><span class="sxs-lookup"><span data-stu-id="a415b-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

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

### <a name="client-streaming-call"></a><span data-ttu-id="a415b-157">Volání streamování klientů</span><span class="sxs-lookup"><span data-stu-id="a415b-157">Client streaming call</span></span>

<span data-ttu-id="a415b-158">Volání streamování klienta se spustí *bez* odeslání zprávy klientem.</span><span class="sxs-lookup"><span data-stu-id="a415b-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="a415b-159">Klient se může rozhodnout odeslat zprávy pomocí `RequestStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="a415b-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="a415b-160">Jakmile klient dokončí odesílání zpráv `RequestStream.CompleteAsync` by měla být volána pro upozornění služby.</span><span class="sxs-lookup"><span data-stu-id="a415b-160">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="a415b-161">Volání je dokončeno, když služba vrátí zprávu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a415b-161">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="a415b-162">Obousměrné volání streamování</span><span class="sxs-lookup"><span data-stu-id="a415b-162">Bi-directional streaming call</span></span>

<span data-ttu-id="a415b-163">Obousměrné volání streamování se spustí *bez* odeslání zprávy klientem.</span><span class="sxs-lookup"><span data-stu-id="a415b-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="a415b-164">Klient se může rozhodnout odeslat zprávy pomocí `RequestStream.WriteAsync`.</span><span class="sxs-lookup"><span data-stu-id="a415b-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="a415b-165">Zprávy, které jsou streamované ze služby, jsou dostupné `ResponseStream.MoveNext()` nebo `ResponseStream.ReadAllAsync()`.</span><span class="sxs-lookup"><span data-stu-id="a415b-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="a415b-166">Obousměrné volání streamování je dokončeno, když `ResponseStream` nemá žádné další zprávy.</span><span class="sxs-lookup"><span data-stu-id="a415b-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="a415b-167">Během volání obousměrného streamování může klient a služba kdykoli odesílat zprávy.</span><span class="sxs-lookup"><span data-stu-id="a415b-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="a415b-168">Nejlepší klientská logika pro interakci s obousměrným voláním se liší v závislosti na logice služby.</span><span class="sxs-lookup"><span data-stu-id="a415b-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a415b-169">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a415b-169">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
