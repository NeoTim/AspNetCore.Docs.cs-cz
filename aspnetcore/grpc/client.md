---
title: Volání služeb gRPC s klientem .NET
author: jamesnk
description: Přečtěte si, jak volat služby gRPC s klientem .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 6a6a649f7194354b16f3d67160be02428cc01170
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667172"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="6a616-103">Volání služeb gRPC s klientem .NET</span><span class="sxs-lookup"><span data-stu-id="6a616-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="6a616-104">Klientská knihovna .NET gRPC je k dispozici v balíčku [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet.</span><span class="sxs-lookup"><span data-stu-id="6a616-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="6a616-105">Tento dokument vysvětluje, jak:</span><span class="sxs-lookup"><span data-stu-id="6a616-105">This document explains how to:</span></span>

* <span data-ttu-id="6a616-106">Nakonfigurujte klienta gRPC pro volání služeb gRPC.</span><span class="sxs-lookup"><span data-stu-id="6a616-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="6a616-107">Proveďte volání gRPC na unární, server streaming, streamování klientů a obousměrné metody streamování.</span><span class="sxs-lookup"><span data-stu-id="6a616-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="6a616-108">Konfigurace klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="6a616-108">Configure gRPC client</span></span>

<span data-ttu-id="6a616-109">gRPC klienti jsou konkrétní typy klientů, které jsou [generovány ze \* \*souborů .proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="6a616-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="6a616-110">Konkrétní klient gRPC má metody, které se překládají do služby gRPC v souboru \* \*.proto.\*</span><span class="sxs-lookup"><span data-stu-id="6a616-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="6a616-111">Klient gRPC je vytvořen z kanálu.</span><span class="sxs-lookup"><span data-stu-id="6a616-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="6a616-112">Začněte `GrpcChannel.ForAddress` pomocí k vytvoření kanálu a potom pomocí kanálu vytvořte klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="6a616-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="6a616-113">Kanál představuje dlouhodobé připojení ke službě gRPC.</span><span class="sxs-lookup"><span data-stu-id="6a616-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="6a616-114">Když je kanál vytvořen, je nakonfigurován s možnostmi souvisejícími s voláním služby.</span><span class="sxs-lookup"><span data-stu-id="6a616-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="6a616-115">Například `HttpClient` použití volání, maximální velikost odesílání a přijímání zpráv a protokolování lze zadat `GrpcChannelOptions` a použít s . `GrpcChannel.ForAddress`</span><span class="sxs-lookup"><span data-stu-id="6a616-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="6a616-116">Úplný seznam možností naleznete v tématu [možnosti konfigurace klienta](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="6a616-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="6a616-117">Výkon a využití kanálu a klienta:</span><span class="sxs-lookup"><span data-stu-id="6a616-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="6a616-118">Vytvoření kanálu může být nákladná operace.</span><span class="sxs-lookup"><span data-stu-id="6a616-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="6a616-119">Opětovné použití kanálu pro volání gRPC poskytuje výhody výkonu.</span><span class="sxs-lookup"><span data-stu-id="6a616-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="6a616-120">klienti gRPC jsou vytvářeny pomocí kanálů.</span><span class="sxs-lookup"><span data-stu-id="6a616-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="6a616-121">gRPC klienti jsou lehké objekty a není nutné do mezipaměti nebo znovu použít.</span><span class="sxs-lookup"><span data-stu-id="6a616-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="6a616-122">Z kanálu lze vytvořit více klientů gRPC, včetně různých typů klientů.</span><span class="sxs-lookup"><span data-stu-id="6a616-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="6a616-123">Kanál a klienty vytvořené z kanálu lze bezpečně používat více vláken.</span><span class="sxs-lookup"><span data-stu-id="6a616-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="6a616-124">Klienti vytvoření z kanálu mohou uskutečňovat více souběžných volání.</span><span class="sxs-lookup"><span data-stu-id="6a616-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="6a616-125">`GrpcChannel.ForAddress`není jedinou možností pro vytvoření gRPC klienta.</span><span class="sxs-lookup"><span data-stu-id="6a616-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="6a616-126">Pokud voláte služby gRPC z aplikace ASP.NET Core, zvažte [integraci gRPC klienta .](xref:grpc/clientfactory)</span><span class="sxs-lookup"><span data-stu-id="6a616-126">If you're calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="6a616-127">gRPC integrace `HttpClientFactory` s nabízí centralizovanou alternativu k vytváření gRPC klientů.</span><span class="sxs-lookup"><span data-stu-id="6a616-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="6a616-128">Další konfigurace je vyžadována pro [volání nezabezpečené služby gRPC s klientem .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="6a616-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="6a616-129">Volání gRPC přes HTTP/2 s `Grpc.Net.Client` není aktuálně podporováno na Xamarin.</span><span class="sxs-lookup"><span data-stu-id="6a616-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="6a616-130">Pracujeme na zlepšení podpory HTTP/2 v budoucí verzi Xamarinu.</span><span class="sxs-lookup"><span data-stu-id="6a616-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="6a616-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) a [gRPC-Web](xref:grpc/browser) jsou životaschopné alternativy, které fungují dnes.</span><span class="sxs-lookup"><span data-stu-id="6a616-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="6a616-132">Volání gRPC</span><span class="sxs-lookup"><span data-stu-id="6a616-132">Make gRPC calls</span></span>

<span data-ttu-id="6a616-133">Volání gRPC je zahájeno voláním metody na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="6a616-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="6a616-134">Klient gRPC bude zpracovávat serializaci zpráv a adresování volání gRPC na správnou službu.</span><span class="sxs-lookup"><span data-stu-id="6a616-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="6a616-135">gRPC má různé typy metod.</span><span class="sxs-lookup"><span data-stu-id="6a616-135">gRPC has different types of methods.</span></span> <span data-ttu-id="6a616-136">Způsob použití klienta k volání gRPC závisí na typu volané metody.</span><span class="sxs-lookup"><span data-stu-id="6a616-136">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="6a616-137">Typy metod gRPC jsou:</span><span class="sxs-lookup"><span data-stu-id="6a616-137">The gRPC method types are:</span></span>

* <span data-ttu-id="6a616-138">Unární</span><span class="sxs-lookup"><span data-stu-id="6a616-138">Unary</span></span>
* <span data-ttu-id="6a616-139">Serverové vysílání</span><span class="sxs-lookup"><span data-stu-id="6a616-139">Server streaming</span></span>
* <span data-ttu-id="6a616-140">Streamování klientů</span><span class="sxs-lookup"><span data-stu-id="6a616-140">Client streaming</span></span>
* <span data-ttu-id="6a616-141">Obousměrné streamování</span><span class="sxs-lookup"><span data-stu-id="6a616-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="6a616-142">Unární volání</span><span class="sxs-lookup"><span data-stu-id="6a616-142">Unary call</span></span>

<span data-ttu-id="6a616-143">Unární volání začíná klientem odesláním zprávy požadavku.</span><span class="sxs-lookup"><span data-stu-id="6a616-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="6a616-144">Po dokončení služby je vrácena zpráva s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="6a616-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="6a616-145">Každá metoda unární služby v souboru \* \*.proto\* bude mít za následek dvě metody .NET na konkrétním typu klienta gRPC pro volání metody: asynchronní metoda a metoda blokování.</span><span class="sxs-lookup"><span data-stu-id="6a616-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="6a616-146">Například `GreeterClient` existují dva způsoby `SayHello`volání :</span><span class="sxs-lookup"><span data-stu-id="6a616-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="6a616-147">`GreeterClient.SayHelloAsync`- `Greeter.SayHello` volání služby asynchronně.</span><span class="sxs-lookup"><span data-stu-id="6a616-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="6a616-148">Dá se čekat.</span><span class="sxs-lookup"><span data-stu-id="6a616-148">Can be awaited.</span></span>
* <span data-ttu-id="6a616-149">`GreeterClient.SayHello`- `Greeter.SayHello` volá službu a bloky až do dokončení.</span><span class="sxs-lookup"><span data-stu-id="6a616-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="6a616-150">Nepoužívejte v asynchronním kódu.</span><span class="sxs-lookup"><span data-stu-id="6a616-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="6a616-151">Volání datového proudu serveru</span><span class="sxs-lookup"><span data-stu-id="6a616-151">Server streaming call</span></span>

<span data-ttu-id="6a616-152">Volání datového proudu serveru začíná odesláním zprávy požadavku klientem.</span><span class="sxs-lookup"><span data-stu-id="6a616-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="6a616-153">`ResponseStream.MoveNext()`čte zprávy vysílané ze služby.</span><span class="sxs-lookup"><span data-stu-id="6a616-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="6a616-154">Volání datového proudu `ResponseStream.MoveNext()` serveru `false`je dokončeno, když vrátí .</span><span class="sxs-lookup"><span data-stu-id="6a616-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

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

<span data-ttu-id="6a616-155">Pokud používáte C# 8 nebo `await foreach` novější, syntaxe lze číst zprávy.</span><span class="sxs-lookup"><span data-stu-id="6a616-155">If you are using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="6a616-156">Metoda `IAsyncStreamReader<T>.ReadAllAsync()` rozšíření přečte všechny zprávy z datového proudu odpovědí:</span><span class="sxs-lookup"><span data-stu-id="6a616-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

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

### <a name="client-streaming-call"></a><span data-ttu-id="6a616-157">Volání datového proudu klienta</span><span class="sxs-lookup"><span data-stu-id="6a616-157">Client streaming call</span></span>

<span data-ttu-id="6a616-158">Volání datového proudu klienta se spustí *bez* odeslání zprávy klientem.</span><span class="sxs-lookup"><span data-stu-id="6a616-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="6a616-159">Klient se může rozhodnout `RequestStream.WriteAsync`odesílat zprávy pomocí aplikace .</span><span class="sxs-lookup"><span data-stu-id="6a616-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="6a616-160">Po dokončení odesílání zpráv `RequestStream.CompleteAsync` klient by měl být volán upozornit službu.</span><span class="sxs-lookup"><span data-stu-id="6a616-160">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="6a616-161">Volání je dokončeno, když služba vrátí zprávu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6a616-161">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="6a616-162">Obousměrné volání streamování</span><span class="sxs-lookup"><span data-stu-id="6a616-162">Bi-directional streaming call</span></span>

<span data-ttu-id="6a616-163">Obousměrné volání streamování se spustí *bez* odeslání zprávy klientem.</span><span class="sxs-lookup"><span data-stu-id="6a616-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="6a616-164">Klient se může rozhodnout `RequestStream.WriteAsync`odesílat zprávy pomocí aplikace .</span><span class="sxs-lookup"><span data-stu-id="6a616-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="6a616-165">Zprávy vysílané ze `ResponseStream.MoveNext()` služby jsou přístupné pomocí služby nebo `ResponseStream.ReadAllAsync()`.</span><span class="sxs-lookup"><span data-stu-id="6a616-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="6a616-166">Obousměrné volání streamování je dokončeno, `ResponseStream` když nemá žádné další zprávy.</span><span class="sxs-lookup"><span data-stu-id="6a616-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="6a616-167">Během obousměrného volání datových proudů může klient a služba kdykoli vzájemně odesílat zprávy.</span><span class="sxs-lookup"><span data-stu-id="6a616-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="6a616-168">Nejlepší logika klienta pro interakci s obousměrným voláním se liší v závislosti na logice služby.</span><span class="sxs-lookup"><span data-stu-id="6a616-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6a616-169">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6a616-169">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
