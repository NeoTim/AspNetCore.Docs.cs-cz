---
title: Volání služeb gRPC pomocí klienta .NET
author: jamesnk
description: Naučte se volat služby gRPC Services pomocí klienta .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
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
uid: grpc/client
ms.openlocfilehash: 9406c2a34126f3e5cd1406a55c3585e7a28f3dd9
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90593044"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="e2fdb-103">Volání služeb gRPC pomocí klienta .NET</span><span class="sxs-lookup"><span data-stu-id="e2fdb-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="e2fdb-104">Klientská knihovna .NET gRPC je k dispozici v balíčku NuGet pro [gRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) .</span><span class="sxs-lookup"><span data-stu-id="e2fdb-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="e2fdb-105">Tento dokument vysvětluje, jak:</span><span class="sxs-lookup"><span data-stu-id="e2fdb-105">This document explains how to:</span></span>

* <span data-ttu-id="e2fdb-106">Nakonfigurujte klienta gRPC, který bude volat služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="e2fdb-107">GRPC volání unárního, streamování serveru, streamování klientů a metod obousměrného streamování.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="e2fdb-108">Konfigurace klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="e2fdb-108">Configure gRPC client</span></span>

<span data-ttu-id="e2fdb-109">gRPC klienti jsou konkrétní typy klientů, které jsou [vygenerovány ze souborů \* \* . proto\* ](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="e2fdb-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="e2fdb-110">Konkrétní klient gRPC má metody, které se převádějí do služby gRPC v souboru \* \* ..\* .</span><span class="sxs-lookup"><span data-stu-id="e2fdb-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="e2fdb-111">Klient gRPC se vytvoří z kanálu.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="e2fdb-112">Začněte tím, že použijete `GrpcChannel.ForAddress` k vytvoření kanálu a pak pomocí kanálu vytvoříte klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="e2fdb-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="e2fdb-113">Kanál představuje dlouhodobé připojení ke službě gRPC.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="e2fdb-114">Při vytvoření kanálu se nakonfiguruje s možnostmi, které se týkají volání služby.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="e2fdb-115">Například, který se `HttpClient` používá k volání, maximální velikost zprávy Send a Receive a protokolování lze zadat v `GrpcChannelOptions` a použít s `GrpcChannel.ForAddress` .</span><span class="sxs-lookup"><span data-stu-id="e2fdb-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="e2fdb-116">Úplný seznam možností najdete v tématu [Možnosti konfigurace klienta](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="e2fdb-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="e2fdb-117">Konfigurace TLS</span><span class="sxs-lookup"><span data-stu-id="e2fdb-117">Configure TLS</span></span>

<span data-ttu-id="e2fdb-118">Klient gRPC musí používat stejné zabezpečení na úrovni připojení jako volaná služba.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-118">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="e2fdb-119">protokol TLS (gRPC Client Transport Layer Security) je nakonfigurován při vytvoření kanálu gRPC.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-119">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="e2fdb-120">Klient gRPC vyvolá chybu při volání služby a zabezpečení kanálu a služby na úrovni připojení se neshoduje.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-120">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="e2fdb-121">Pokud chcete gRPC kanál nakonfigurovat tak, aby používal protokol TLS, ujistěte se, že adresa serveru začíná `https` .</span><span class="sxs-lookup"><span data-stu-id="e2fdb-121">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="e2fdb-122">Například `GrpcChannel.ForAddress("https://localhost:5001")` používá protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-122">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="e2fdb-123">Kanál gRPC automaticky negotates připojení zabezpečené protokolem TLS a používá zabezpečené připojení k zajištění gRPC volání.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-123">The gRPC channel automatically negotates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="e2fdb-124">gRPC podporuje ověřování certifikátu klienta přes protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-124">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="e2fdb-125">Informace o konfiguraci klientských certifikátů pomocí gRPC kanálu najdete v tématu <xref:grpc/authn-and-authz#client-certificate-authentication> .</span><span class="sxs-lookup"><span data-stu-id="e2fdb-125">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="e2fdb-126">Chcete-li volat nezabezpečené služby gRPC, ujistěte se, že adresa serveru začíná `http` .</span><span class="sxs-lookup"><span data-stu-id="e2fdb-126">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="e2fdb-127">Například `GrpcChannel.ForAddress("http://localhost:5000")` používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-127">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="e2fdb-128">V .NET Core 3,1 nebo novější se vyžaduje další konfigurace pro [volání nezabezpečených gRPC služeb pomocí klienta .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span><span class="sxs-lookup"><span data-stu-id="e2fdb-128">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="e2fdb-129">Výkon klienta</span><span class="sxs-lookup"><span data-stu-id="e2fdb-129">Client performance</span></span>

<span data-ttu-id="e2fdb-130">Výkon a využití kanálu a klienta:</span><span class="sxs-lookup"><span data-stu-id="e2fdb-130">Channel and client performance and usage:</span></span>

* <span data-ttu-id="e2fdb-131">Vytvoření kanálu může být náročná operace.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-131">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="e2fdb-132">Použití kanálu pro volání gRPC poskytuje výhody týkající se výkonu.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-132">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="e2fdb-133">klienti gRPC se vytvářejí pomocí kanálů.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-133">gRPC clients are created with channels.</span></span> <span data-ttu-id="e2fdb-134">gRPC klienti jsou prosté objekty a nemusíte je ukládat do mezipaměti ani je znovu používat.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-134">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="e2fdb-135">Z kanálu, včetně různých typů klientů, lze vytvořit více klientů gRPC.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-135">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="e2fdb-136">Kanál a klienti vytvořené z kanálu můžou být bezpečně využívány více vlákny.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-136">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="e2fdb-137">Klienti vytvoření z kanálu můžou provádět víc souběžných volání.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-137">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="e2fdb-138">`GrpcChannel.ForAddress` není jedinou možností pro vytvoření klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-138">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="e2fdb-139">Pokud voláte služby gRPC Services z aplikace ASP.NET Core, zvažte [integraci klientské továrny gRPC](xref:grpc/clientfactory).</span><span class="sxs-lookup"><span data-stu-id="e2fdb-139">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="e2fdb-140">integrace gRPC s `HttpClientFactory` nabízí centralizovanou alternativu k vytváření klientů gRPC.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-140">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="e2fdb-141">Volání gRPC přes HTTP/2 s `Grpc.Net.Client` se v Xamarin v tuto chvíli nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-141">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="e2fdb-142">Pracujeme na vylepšení podpory protokolu HTTP/2 v budoucí verzi Xamarin.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-142">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="e2fdb-143">[Grpc. Core](https://www.nuget.org/packages/Grpc.Core) a [Grpc-web](xref:grpc/browser) představují životaschopné alternativy, které dnes fungují.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-143">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="e2fdb-144">Provést volání gRPC</span><span class="sxs-lookup"><span data-stu-id="e2fdb-144">Make gRPC calls</span></span>

<span data-ttu-id="e2fdb-145">Volání gRPC je zahájeno voláním metody na klientovi.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-145">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="e2fdb-146">Klient gRPC zpracuje serializaci zprávy a adresování volání gRPC ke správné službě.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-146">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="e2fdb-147">gRPC má různé typy metod.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-147">gRPC has different types of methods.</span></span> <span data-ttu-id="e2fdb-148">Způsob, jakým je klient použit k provedení volání gRPC, závisí na typu volané metody.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-148">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="e2fdb-149">Typy metod gRPC jsou:</span><span class="sxs-lookup"><span data-stu-id="e2fdb-149">The gRPC method types are:</span></span>

* <span data-ttu-id="e2fdb-150">Unární</span><span class="sxs-lookup"><span data-stu-id="e2fdb-150">Unary</span></span>
* <span data-ttu-id="e2fdb-151">Streamování serveru</span><span class="sxs-lookup"><span data-stu-id="e2fdb-151">Server streaming</span></span>
* <span data-ttu-id="e2fdb-152">Streamování klientů</span><span class="sxs-lookup"><span data-stu-id="e2fdb-152">Client streaming</span></span>
* <span data-ttu-id="e2fdb-153">Obousměrné streamování</span><span class="sxs-lookup"><span data-stu-id="e2fdb-153">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="e2fdb-154">Unární volání</span><span class="sxs-lookup"><span data-stu-id="e2fdb-154">Unary call</span></span>

<span data-ttu-id="e2fdb-155">Unární volání začíná klientem, který odesílá zprávu požadavku.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-155">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="e2fdb-156">Po dokončení služby se vrátí zpráva s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-156">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="e2fdb-157">Každá unární metoda služby v souboru \* \* .\* proč má za následek dvě metody .NET pro konkrétní typ klienta gRPC pro volání metody: asynchronní metodu a metodu blokování.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-157">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="e2fdb-158">Například `GreeterClient` existují dva způsoby volání `SayHello` :</span><span class="sxs-lookup"><span data-stu-id="e2fdb-158">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="e2fdb-159">`GreeterClient.SayHelloAsync` -volá `Greeter.SayHello` službu asynchronně.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-159">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="e2fdb-160">Může být očekáváno.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-160">Can be awaited.</span></span>
* <span data-ttu-id="e2fdb-161">`GreeterClient.SayHello` -volá `Greeter.SayHello` službu a zablokuje se do dokončení.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-161">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="e2fdb-162">Nepoužívejte v asynchronním kódu.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-162">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="e2fdb-163">Volání streamování serveru</span><span class="sxs-lookup"><span data-stu-id="e2fdb-163">Server streaming call</span></span>

<span data-ttu-id="e2fdb-164">Volání streamování serveru začíná klientem, který odesílá zprávu požadavku.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-164">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="e2fdb-165">`ResponseStream.MoveNext()` přečte zprávy streamované ze služby.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-165">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="e2fdb-166">Volání streamování serveru je po `ResponseStream.MoveNext()` návratu dokončeno `false` .</span><span class="sxs-lookup"><span data-stu-id="e2fdb-166">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="e2fdb-167">Při použití C# 8 nebo novější `await foreach` lze syntaxi použít ke čtení zpráv.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-167">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="e2fdb-168">`IAsyncStreamReader<T>.ReadAllAsync()`Metoda rozšíření načte všechny zprávy z datového proudu odpovědí:</span><span class="sxs-lookup"><span data-stu-id="e2fdb-168">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="e2fdb-169">Volání streamování klientů</span><span class="sxs-lookup"><span data-stu-id="e2fdb-169">Client streaming call</span></span>

<span data-ttu-id="e2fdb-170">Volání streamování klienta se spustí *bez* odeslání zprávy klientem.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-170">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="e2fdb-171">Klient nástroje může odeslat zprávy pomocí příkazu `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="e2fdb-171">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="e2fdb-172">Když klient dokončí posílání zpráv, `RequestStream.CompleteAsync()` měla by se zavolat, aby službu informovaly.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-172">When the client has finished sending messages, `RequestStream.CompleteAsync()` should be called to notify the service.</span></span> <span data-ttu-id="e2fdb-173">Volání je dokončeno, když služba vrátí zprávu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-173">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="e2fdb-174">Obousměrné volání streamování</span><span class="sxs-lookup"><span data-stu-id="e2fdb-174">Bi-directional streaming call</span></span>

<span data-ttu-id="e2fdb-175">Obousměrné volání streamování se spustí *bez* odeslání zprávy klientem.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-175">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="e2fdb-176">Klient nástroje může odeslat zprávy pomocí příkazu `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="e2fdb-176">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="e2fdb-177">Zprávy streamované ze služby jsou přístupné pomocí nástroje `ResponseStream.MoveNext()` nebo `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="e2fdb-177">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="e2fdb-178">Obousměrné volání streamování je dokončeno, když `ResponseStream` nemá žádné další zprávy.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-178">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="e2fdb-179">Chcete-li dosáhnout nejlepšího výkonu a vyhnout se zbytečným chybám v klientech a službě, zkuste plynule volat obousměrné volání streamování.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-179">For best performance, and to avoid unnecessary errors in the client and service, try to complete bi-directional streaming calls gracefully.</span></span> <span data-ttu-id="e2fdb-180">Obousměrné volání se dokončí řádným dodržením, když server dokončil čtení datového proudu požadavků a klient dokončil čtení streamu odpovědí.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-180">A bi-directional call completes gracefully when the server has finished reading the request stream and the client has finished reading the response stream.</span></span> <span data-ttu-id="e2fdb-181">Předchozí ukázkové volání je jeden příklad obousměrného volání, které končí řádným ukončením.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-181">The preceding sample call is one example of a bi-directional call that ends gracefully.</span></span> <span data-ttu-id="e2fdb-182">V volání klienta:</span><span class="sxs-lookup"><span data-stu-id="e2fdb-182">In the call, the client:</span></span>

1. <span data-ttu-id="e2fdb-183">Spustí nové obousměrné volání streamování voláním `EchoClient.Echo` .</span><span class="sxs-lookup"><span data-stu-id="e2fdb-183">Starts a new bi-directional streaming call by calling `EchoClient.Echo`.</span></span>
2. <span data-ttu-id="e2fdb-184">Vytvoří úlohu na pozadí ke čtení zpráv ze služby pomocí `ResponseStream.ReadAllAsync()` .</span><span class="sxs-lookup"><span data-stu-id="e2fdb-184">Creates a background task to read messages from the service using `ResponseStream.ReadAllAsync()`.</span></span>
3. <span data-ttu-id="e2fdb-185">Odesílá zprávy serveru pomocí `RequestStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="e2fdb-185">Sends messages to the server with `RequestStream.WriteAsync`.</span></span>
4. <span data-ttu-id="e2fdb-186">Upozorní server, na kterém dokončil odesílání zpráv `RequestStream.CompleteAsync()` .</span><span class="sxs-lookup"><span data-stu-id="e2fdb-186">Notifies the server it has finished sending messages with `RequestStream.CompleteAsync()`.</span></span>
5. <span data-ttu-id="e2fdb-187">Počká, dokud úloha na pozadí nenačte všechny příchozí zprávy.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-187">Waits until the background task has read all incoming messages.</span></span>

<span data-ttu-id="e2fdb-188">Během volání obousměrného streamování může klient a služba kdykoli odesílat zprávy.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-188">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="e2fdb-189">Nejlepší klientská logika pro interakci s obousměrným voláním se liší v závislosti na logice služby.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-189">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="e2fdb-190">Přístup k gRPC přívěsům</span><span class="sxs-lookup"><span data-stu-id="e2fdb-190">Access gRPC trailers</span></span>

<span data-ttu-id="e2fdb-191">volání gRPC mohou vracet gRPC přípojná vozidla.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-191">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="e2fdb-192">gRPC přípojná vozidla slouží k poskytování metadat názvů a hodnot volání.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-192">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="e2fdb-193">Přípojná rozhraní poskytují podobné funkce hlaviček protokolu HTTP, ale jsou přijaty na konci volání.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-193">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="e2fdb-194">gRPC přípojná vozidla jsou přístupná pomocí `GetTrailers()` , která vrací kolekci metadat.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-194">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="e2fdb-195">Po dokončení odpovědi se vrátí přípojná část, proto musíte před přístupem k přípojným vozidlům čekat na všechny zprávy s odpovědí.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-195">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="e2fdb-196">Unární a volání streamování klientů musí `ResponseAsync` před voláním čekat na `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="e2fdb-196">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="e2fdb-197">Volání serveru a obousměrného streamování musí dokončit čekání na datový proud odpovědi před voláním `GetTrailers()` :</span><span class="sxs-lookup"><span data-stu-id="e2fdb-197">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="e2fdb-198">gRPC přívěsy jsou také přístupné z `RpcException` .</span><span class="sxs-lookup"><span data-stu-id="e2fdb-198">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="e2fdb-199">Služba může vracet přípojná vozidla společně se stavem gRPC bez OK.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-199">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="e2fdb-200">V této situaci jsou přípojná rozhraní načítána z výjimky vyvolané klientem gRPC:</span><span class="sxs-lookup"><span data-stu-id="e2fdb-200">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

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
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="configure-deadline"></a><span data-ttu-id="e2fdb-201">Konfigurovat konečný termín</span><span class="sxs-lookup"><span data-stu-id="e2fdb-201">Configure deadline</span></span>

<span data-ttu-id="e2fdb-202">Doporučuje se nakonfigurovat konečný termín volání gRPC, protože poskytuje horní limit toho, jak dlouho může volání běžet.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-202">Configuring a gRPC call deadline is recommended because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="e2fdb-203">Zastaví se tak nesprávnému chování služeb, které neběží trvale a vyčerpání prostředků serveru.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-203">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="e2fdb-204">Termíny jsou užitečným nástrojem pro vytváření spolehlivých aplikací.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-204">Deadlines are a useful tool for building reliable apps.</span></span>

<span data-ttu-id="e2fdb-205">Nakonfigurujte `CallOptions.Deadline` , aby se nastavil konečný termín pro gRPC volání:</span><span class="sxs-lookup"><span data-stu-id="e2fdb-205">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="e2fdb-206">Další informace naleznete v tématu <xref:grpc/deadlines-cancellation#deadlines>.</span><span class="sxs-lookup"><span data-stu-id="e2fdb-206">For more information, see <xref:grpc/deadlines-cancellation#deadlines>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e2fdb-207">Další materiály</span><span class="sxs-lookup"><span data-stu-id="e2fdb-207">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
