---
title: Volání služeb gRPC pomocí klienta .NET
author: jamesnk
description: Naučte se volat služby gRPC Services pomocí klienta .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/client
ms.openlocfilehash: 9ebe36cdb17e858fd82216b090e3e89169197101
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406183"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="276ad-103">Volání služeb gRPC pomocí klienta .NET</span><span class="sxs-lookup"><span data-stu-id="276ad-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="276ad-104">Klientská knihovna .NET gRPC je k dispozici v balíčku NuGet pro [gRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="276ad-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="276ad-105">Tento dokument vysvětluje, jak:</span><span class="sxs-lookup"><span data-stu-id="276ad-105">This document explains how to:</span></span>

* <span data-ttu-id="276ad-106">Nakonfigurujte klienta gRPC, který bude volat služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="276ad-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="276ad-107">GRPC volání unárního, streamování serveru, streamování klientů a metod obousměrného streamování.</span><span class="sxs-lookup"><span data-stu-id="276ad-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="276ad-108">Konfigurace klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="276ad-108">Configure gRPC client</span></span>

<span data-ttu-id="276ad-109">gRPC klienti jsou konkrétní typy klientů, které jsou [vygenerovány ze souborů \* \* . proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="276ad-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="276ad-110">Konkrétní klient gRPC má metody, které se převádějí do služby gRPC v souboru \* \* ..\* .</span><span class="sxs-lookup"><span data-stu-id="276ad-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="276ad-111">Klient gRPC se vytvoří z kanálu.</span><span class="sxs-lookup"><span data-stu-id="276ad-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="276ad-112">Začněte tím, že použijete `GrpcChannel.ForAddress` k vytvoření kanálu a pak pomocí kanálu vytvoříte klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="276ad-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="276ad-113">Kanál představuje dlouhodobé připojení ke službě gRPC.</span><span class="sxs-lookup"><span data-stu-id="276ad-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="276ad-114">Při vytvoření kanálu se nakonfiguruje s možnostmi, které se týkají volání služby.</span><span class="sxs-lookup"><span data-stu-id="276ad-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="276ad-115">Například, který se `HttpClient` používá k volání, maximální velikost zprávy Send a Receive a protokolování lze zadat v `GrpcChannelOptions` a použít s `GrpcChannel.ForAddress` .</span><span class="sxs-lookup"><span data-stu-id="276ad-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="276ad-116">Úplný seznam možností najdete v tématu [Možnosti konfigurace klienta](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="276ad-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="276ad-117">Výkon a využití kanálu a klienta:</span><span class="sxs-lookup"><span data-stu-id="276ad-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="276ad-118">Vytvoření kanálu může být náročná operace.</span><span class="sxs-lookup"><span data-stu-id="276ad-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="276ad-119">Použití kanálu pro volání gRPC poskytuje výhody týkající se výkonu.</span><span class="sxs-lookup"><span data-stu-id="276ad-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="276ad-120">klienti gRPC se vytvářejí pomocí kanálů.</span><span class="sxs-lookup"><span data-stu-id="276ad-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="276ad-121">gRPC klienti jsou prosté objekty a nemusíte je ukládat do mezipaměti ani je znovu používat.</span><span class="sxs-lookup"><span data-stu-id="276ad-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="276ad-122">Z kanálu, včetně různých typů klientů, lze vytvořit více klientů gRPC.</span><span class="sxs-lookup"><span data-stu-id="276ad-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="276ad-123">Kanál a klienti vytvořené z kanálu můžou být bezpečně využívány více vlákny.</span><span class="sxs-lookup"><span data-stu-id="276ad-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="276ad-124">Klienti vytvoření z kanálu můžou provádět víc souběžných volání.</span><span class="sxs-lookup"><span data-stu-id="276ad-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="276ad-125">`GrpcChannel.ForAddress`není jedinou možností pro vytvoření klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="276ad-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="276ad-126">Pokud voláte služby gRPC Services z aplikace ASP.NET Core, zvažte [integraci klientské továrny gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="276ad-126">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="276ad-127">integrace gRPC s `HttpClientFactory` nabízí centralizovanou alternativu k vytváření klientů gRPC.</span><span class="sxs-lookup"><span data-stu-id="276ad-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="276ad-128">Pro [volání nezabezpečených služeb gRPC s klientem .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)je vyžadována další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="276ad-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="276ad-129">Volání gRPC přes HTTP/2 s `Grpc.Net.Client` se v Xamarin v tuto chvíli nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="276ad-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="276ad-130">Pracujeme na vylepšení podpory protokolu HTTP/2 v budoucí verzi Xamarin.</span><span class="sxs-lookup"><span data-stu-id="276ad-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="276ad-131">[Grpc. Core](https://www.nuget.org/packages/Grpc.Core) a [Grpc-web](xref:grpc/browser) představují životaschopné alternativy, které dnes fungují.</span><span class="sxs-lookup"><span data-stu-id="276ad-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="276ad-132">Provést volání gRPC</span><span class="sxs-lookup"><span data-stu-id="276ad-132">Make gRPC calls</span></span>

<span data-ttu-id="276ad-133">Volání gRPC je zahájeno voláním metody na klientovi.</span><span class="sxs-lookup"><span data-stu-id="276ad-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="276ad-134">Klient gRPC zpracuje serializaci zprávy a adresování volání gRPC ke správné službě.</span><span class="sxs-lookup"><span data-stu-id="276ad-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="276ad-135">gRPC má různé typy metod.</span><span class="sxs-lookup"><span data-stu-id="276ad-135">gRPC has different types of methods.</span></span> <span data-ttu-id="276ad-136">Způsob, jakým je klient použit k provedení volání gRPC, závisí na typu volané metody.</span><span class="sxs-lookup"><span data-stu-id="276ad-136">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="276ad-137">Typy metod gRPC jsou:</span><span class="sxs-lookup"><span data-stu-id="276ad-137">The gRPC method types are:</span></span>

* <span data-ttu-id="276ad-138">Unární</span><span class="sxs-lookup"><span data-stu-id="276ad-138">Unary</span></span>
* <span data-ttu-id="276ad-139">Streamování serveru</span><span class="sxs-lookup"><span data-stu-id="276ad-139">Server streaming</span></span>
* <span data-ttu-id="276ad-140">Streamování klientů</span><span class="sxs-lookup"><span data-stu-id="276ad-140">Client streaming</span></span>
* <span data-ttu-id="276ad-141">Obousměrné streamování</span><span class="sxs-lookup"><span data-stu-id="276ad-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="276ad-142">Unární volání</span><span class="sxs-lookup"><span data-stu-id="276ad-142">Unary call</span></span>

<span data-ttu-id="276ad-143">Unární volání začíná klientem, který odesílá zprávu požadavku.</span><span class="sxs-lookup"><span data-stu-id="276ad-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="276ad-144">Po dokončení služby se vrátí zpráva s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="276ad-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="276ad-145">Každá unární metoda služby v souboru \* \* .\* proč má za následek dvě metody .NET pro konkrétní typ klienta gRPC pro volání metody: asynchronní metodu a metodu blokování.</span><span class="sxs-lookup"><span data-stu-id="276ad-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="276ad-146">Například `GreeterClient` existují dva způsoby volání `SayHello` :</span><span class="sxs-lookup"><span data-stu-id="276ad-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="276ad-147">`GreeterClient.SayHelloAsync`-volá `Greeter.SayHello` službu asynchronně.</span><span class="sxs-lookup"><span data-stu-id="276ad-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="276ad-148">Může být očekáváno.</span><span class="sxs-lookup"><span data-stu-id="276ad-148">Can be awaited.</span></span>
* <span data-ttu-id="276ad-149">`GreeterClient.SayHello`-volá `Greeter.SayHello` službu a zablokuje se do dokončení.</span><span class="sxs-lookup"><span data-stu-id="276ad-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="276ad-150">Nepoužívejte v asynchronním kódu.</span><span class="sxs-lookup"><span data-stu-id="276ad-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="276ad-151">Volání streamování serveru</span><span class="sxs-lookup"><span data-stu-id="276ad-151">Server streaming call</span></span>

<span data-ttu-id="276ad-152">Volání streamování serveru začíná klientem, který odesílá zprávu požadavku.</span><span class="sxs-lookup"><span data-stu-id="276ad-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="276ad-153">`ResponseStream.MoveNext()`přečte zprávy streamované ze služby.</span><span class="sxs-lookup"><span data-stu-id="276ad-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="276ad-154">Volání streamování serveru je po `ResponseStream.MoveNext()` návratu dokončeno `false` .</span><span class="sxs-lookup"><span data-stu-id="276ad-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="276ad-155">Při použití C# 8 nebo novější `await foreach` lze syntaxi použít ke čtení zpráv.</span><span class="sxs-lookup"><span data-stu-id="276ad-155">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="276ad-156">`IAsyncStreamReader<T>.ReadAllAsync()`Metoda rozšíření načte všechny zprávy z datového proudu odpovědí:</span><span class="sxs-lookup"><span data-stu-id="276ad-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="276ad-157">Volání streamování klientů</span><span class="sxs-lookup"><span data-stu-id="276ad-157">Client streaming call</span></span>

<span data-ttu-id="276ad-158">Volání streamování klienta se spustí *bez* odeslání zprávy klientem.</span><span class="sxs-lookup"><span data-stu-id="276ad-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="276ad-159">Klient nástroje může odeslat zprávy pomocí příkazu `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="276ad-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="276ad-160">Když klient dokončí posílání zpráv, `RequestStream.CompleteAsync` měla by se zavolat, aby službu informovaly.</span><span class="sxs-lookup"><span data-stu-id="276ad-160">When the client has finished sending messages, `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="276ad-161">Volání je dokončeno, když služba vrátí zprávu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="276ad-161">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="276ad-162">Obousměrné volání streamování</span><span class="sxs-lookup"><span data-stu-id="276ad-162">Bi-directional streaming call</span></span>

<span data-ttu-id="276ad-163">Obousměrné volání streamování se spustí *bez* odeslání zprávy klientem.</span><span class="sxs-lookup"><span data-stu-id="276ad-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="276ad-164">Klient nástroje může odeslat zprávy pomocí příkazu `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="276ad-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="276ad-165">Zprávy streamované ze služby jsou přístupné pomocí nástroje `ResponseStream.MoveNext()` nebo `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="276ad-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="276ad-166">Obousměrné volání streamování je dokončeno, když `ResponseStream` nemá žádné další zprávy.</span><span class="sxs-lookup"><span data-stu-id="276ad-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

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
```

<span data-ttu-id="276ad-167">Během volání obousměrného streamování může klient a služba kdykoli odesílat zprávy.</span><span class="sxs-lookup"><span data-stu-id="276ad-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="276ad-168">Nejlepší klientská logika pro interakci s obousměrným voláním se liší v závislosti na logice služby.</span><span class="sxs-lookup"><span data-stu-id="276ad-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="276ad-169">Přístup k gRPC přívěsům</span><span class="sxs-lookup"><span data-stu-id="276ad-169">Access gRPC trailers</span></span>

<span data-ttu-id="276ad-170">volání gRPC mohou vracet gRPC přípojná vozidla.</span><span class="sxs-lookup"><span data-stu-id="276ad-170">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="276ad-171">gRPC přípojná vozidla slouží k poskytování metadat názvů a hodnot volání.</span><span class="sxs-lookup"><span data-stu-id="276ad-171">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="276ad-172">Přípojná rozhraní poskytují podobné funkce hlaviček protokolu HTTP, ale jsou přijaty na konci volání.</span><span class="sxs-lookup"><span data-stu-id="276ad-172">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="276ad-173">gRPC přípojná vozidla jsou přístupná pomocí `GetTrailers()` , která vrací kolekci metadat.</span><span class="sxs-lookup"><span data-stu-id="276ad-173">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="276ad-174">Po dokončení odpovědi se vrátí přípojná část, proto musíte před přístupem k přípojným vozidlům čekat na všechny zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="276ad-174">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="276ad-175">Unární a volání streamování klientů musí `ResponseAsync` před voláním čekat na `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="276ad-175">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

<span data-ttu-id="276ad-176">Volání serveru a obousměrného streamování musí dokončit čekání na datový proud odpovědi před voláním `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="276ad-176">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.First(e => e.Key == "my-trailer-name");
```

<span data-ttu-id="276ad-177">gRPC přívěsy jsou také přístupné z `RpcException` .</span><span class="sxs-lookup"><span data-stu-id="276ad-177">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="276ad-178">Služba může vracet přípojná vozidla společně se stavem gRPC bez OK.</span><span class="sxs-lookup"><span data-stu-id="276ad-178">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="276ad-179">V této situaci jsou přípojná rozhraní načítána z výjimky vyvolané klientem gRPC:</span><span class="sxs-lookup"><span data-stu-id="276ad-179">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.First(e => e.Key == "my-trailer-name");
}
```

## <a name="additional-resources"></a><span data-ttu-id="276ad-180">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="276ad-180">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
