---
title: Osvědčené postupy pro výkon v gRPC pro ASP.NET Core
author: jamesnk
description: Seznamte se s osvědčenými postupy pro vytváření vysoce výkonných služeb gRPC Services.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: f9cefa89ec6e533920b33223b34333f6ebe38428
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876721"
---
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a><span data-ttu-id="04098-103">Osvědčené postupy pro výkon v gRPC pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="04098-103">Performance best practices in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="04098-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="04098-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="04098-105">gRPC je navržená pro vysoce výkonné služby.</span><span class="sxs-lookup"><span data-stu-id="04098-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="04098-106">Tento dokument vysvětluje, jak získat nejlepší možný výkon z gRPC.</span><span class="sxs-lookup"><span data-stu-id="04098-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-channel"></a><span data-ttu-id="04098-107">Znovu použít kanál</span><span class="sxs-lookup"><span data-stu-id="04098-107">Reuse channel</span></span>

<span data-ttu-id="04098-108">Kanál gRPC by měl být znovu použit při provádění volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="04098-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="04098-109">Opětovné použití kanálu umožňuje, aby volání byla multiplexovaná prostřednictvím stávajícího připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="04098-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="04098-110">Pokud je pro každý gRPC hovor vytvořen nový kanál, může se výrazně zvýšit množství času, který je potřeba dokončit.</span><span class="sxs-lookup"><span data-stu-id="04098-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="04098-111">Každé volání bude vyžadovat vícenásobné síťové odezvy mezi klientem a serverem, aby bylo možné vytvořit připojení HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="04098-111">Each call will require multiple network round-trips between the client and the server to create an HTTP/2 connection:</span></span>

1. <span data-ttu-id="04098-112">Otevření soketu</span><span class="sxs-lookup"><span data-stu-id="04098-112">Opening a socket</span></span>
2. <span data-ttu-id="04098-113">Navazování připojení TCP</span><span class="sxs-lookup"><span data-stu-id="04098-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="04098-114">Vyjednávání TLS</span><span class="sxs-lookup"><span data-stu-id="04098-114">Negotiating TLS</span></span>
4. <span data-ttu-id="04098-115">Spouští se připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="04098-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="04098-116">Provádění volání gRPC</span><span class="sxs-lookup"><span data-stu-id="04098-116">Making the gRPC call</span></span>

<span data-ttu-id="04098-117">Kanály jsou bezpečné pro sdílení a opakované použití mezi gRPC voláními:</span><span class="sxs-lookup"><span data-stu-id="04098-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="04098-118">klienti gRPC se vytvářejí pomocí kanálů.</span><span class="sxs-lookup"><span data-stu-id="04098-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="04098-119">gRPC klienti jsou prosté objekty a nemusíte je ukládat do mezipaměti ani je znovu používat.</span><span class="sxs-lookup"><span data-stu-id="04098-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="04098-120">Z kanálu, včetně různých typů klientů, lze vytvořit více klientů gRPC.</span><span class="sxs-lookup"><span data-stu-id="04098-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="04098-121">Kanál a klienti vytvořené z kanálu můžou být bezpečně využívány více vlákny.</span><span class="sxs-lookup"><span data-stu-id="04098-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="04098-122">Klienti vytvoření z kanálu můžou provádět víc souběžných volání.</span><span class="sxs-lookup"><span data-stu-id="04098-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="04098-123">Souběžnost připojení</span><span class="sxs-lookup"><span data-stu-id="04098-123">Connection concurrency</span></span>

<span data-ttu-id="04098-124">Připojení HTTP/2 obvykle omezují [maximální počet souběžných proudů (aktivních požadavků HTTP)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) v připojení najednou.</span><span class="sxs-lookup"><span data-stu-id="04098-124">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="04098-125">Ve výchozím nastavení většina serverů nastavila tento limit na 100 souběžných proudů.</span><span class="sxs-lookup"><span data-stu-id="04098-125">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="04098-126">GRPC kanál používá jedno připojení HTTP/2 a souběžná volání jsou v tomto připojení multiplexovaná.</span><span class="sxs-lookup"><span data-stu-id="04098-126">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="04098-127">Když počet aktivních volání dosáhne limitu datových proudů, budou další volání zařazena do fronty v klientovi.</span><span class="sxs-lookup"><span data-stu-id="04098-127">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="04098-128">Volání ve frontě čekají na dokončení aktivních volání, než se odešlou.</span><span class="sxs-lookup"><span data-stu-id="04098-128">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="04098-129">Aplikace s vysokým zatížením nebo dlouhodobě běžícím voláním gRPC můžou zobrazit problémy s výkonem způsobené voláním služby Řízení front z důvodu tohoto limitu.</span><span class="sxs-lookup"><span data-stu-id="04098-129">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="04098-130">Rozhraní .NET 5 zavádí `SocketsHttpHandler.EnableMultipleHttp2Connections` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="04098-130">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="04098-131">Při nastavení na `true` se při dosažení limitu souběžného streamu vytvoří další připojení HTTP/2 pomocí kanálu.</span><span class="sxs-lookup"><span data-stu-id="04098-131">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="04098-132">Když `GrpcChannel` je vytvořen interní, `SocketsHttpHandler` automaticky se nakonfiguruje tak, aby vytvořil další připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="04098-132">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="04098-133">Pokud aplikace nakonfiguruje svou vlastní obslužnou rutinu, zvažte nastavení `EnableMultipleHttp2Connections` na `true` :</span><span class="sxs-lookup"><span data-stu-id="04098-133">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

<span data-ttu-id="04098-134">Pro aplikace .NET Core 3,1 je k dispozici několik alternativních řešení:</span><span class="sxs-lookup"><span data-stu-id="04098-134">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="04098-135">Vytvářejte samostatné gRPC kanály pro oblasti aplikace s vysokou zátěží.</span><span class="sxs-lookup"><span data-stu-id="04098-135">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="04098-136">Například `Logger` Služba gRPC může mít vysoké zatížení.</span><span class="sxs-lookup"><span data-stu-id="04098-136">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="04098-137">K vytvoření v aplikaci použijte samostatný kanál `LoggerClient` .</span><span class="sxs-lookup"><span data-stu-id="04098-137">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="04098-138">Použijte fond gRPCch kanálů, například vytvořte seznam kanálů gRPC.</span><span class="sxs-lookup"><span data-stu-id="04098-138">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="04098-139">`Random` slouží k výběru kanálu ze seznamu pokaždé, když je potřeba gRPC kanál.</span><span class="sxs-lookup"><span data-stu-id="04098-139">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="04098-140">Použití `Random` náhodné distribuce volání přes více připojení.</span><span class="sxs-lookup"><span data-stu-id="04098-140">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="04098-141">Dalším způsobem, jak tento problém vyřešit, je zvýšení maximálního počtu souběžných streamů na serveru.</span><span class="sxs-lookup"><span data-stu-id="04098-141">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="04098-142">V Kestrel se nakonfiguruje s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .</span><span class="sxs-lookup"><span data-stu-id="04098-142">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="04098-143">Zvýšení maximálního počtu souběžných streamů se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="04098-143">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="04098-144">Příliš mnoho datových proudů u jednoho připojení HTTP/2 přináší nové problémy s výkonem:</span><span class="sxs-lookup"><span data-stu-id="04098-144">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="04098-145">Kolize vláken mezi datovými proudy, které se pokouší o zápis do připojení.</span><span class="sxs-lookup"><span data-stu-id="04098-145">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="04098-146">Ztráta paketů připojení způsobí, že všechna volání budou zablokována ve vrstvě TCP.</span><span class="sxs-lookup"><span data-stu-id="04098-146">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="04098-147">Příkazy pro udržování otevřených připojení</span><span class="sxs-lookup"><span data-stu-id="04098-147">Keep alive pings</span></span>

<span data-ttu-id="04098-148">Pomocí příkazů pro udržování otevřených připojení k protokolu HTTP/2 je možné během období nečinnosti zůstat připojení HTTP/2 neaktivním.</span><span class="sxs-lookup"><span data-stu-id="04098-148">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="04098-149">Pokud je stávající připojení HTTP/2 připravené, když aplikace pokračuje v aktivitě, umožní se rychle provést počáteční volání gRPC, aniž by došlo k prodlevě způsobené převázáním připojení.</span><span class="sxs-lookup"><span data-stu-id="04098-149">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="04098-150">Konfigurace příkazů pro udržování otevřených připojení <xref:System.Net.Http.SocketsHttpHandler> :</span><span class="sxs-lookup"><span data-stu-id="04098-150">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

<span data-ttu-id="04098-151">Předchozí kód nakonfiguruje kanál, který do serveru pošle při každém 60 sekundách během období nečinnosti odpověď na Keep Alive.</span><span class="sxs-lookup"><span data-stu-id="04098-151">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="04098-152">Pomocí tohoto testu se ověří, že server a všechny používané proxy nástroje nezavřou připojení z důvodu nečinnosti.</span><span class="sxs-lookup"><span data-stu-id="04098-152">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="04098-153">Streamování</span><span class="sxs-lookup"><span data-stu-id="04098-153">Streaming</span></span>

<span data-ttu-id="04098-154">gRPC obousměrné streamování se dá použít k nahrazení unárních volání gRPC ve scénářích s vysokým výkonem.</span><span class="sxs-lookup"><span data-stu-id="04098-154">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="04098-155">Jakmile se obousměrný datový proud spustí, streamování zpráv zpět a zpátky je rychlejší než odesílání zpráv s více unárními gRPC voláními.</span><span class="sxs-lookup"><span data-stu-id="04098-155">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="04098-156">Streamované zprávy se odesílají jako data v existující žádosti HTTP/2 a eliminují režii při vytváření nové žádosti HTTP/2 pro každé unární volání.</span><span class="sxs-lookup"><span data-stu-id="04098-156">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="04098-157">Ukázková služba:</span><span class="sxs-lookup"><span data-stu-id="04098-157">Example service:</span></span>

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

<span data-ttu-id="04098-158">Ukázkový klient:</span><span class="sxs-lookup"><span data-stu-id="04098-158">Example client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

<span data-ttu-id="04098-159">Nahrazení unárních volání s obousměrným datovým proudem z důvodu výkonu je pokročilá technika a není vhodná v mnoha situacích.</span><span class="sxs-lookup"><span data-stu-id="04098-159">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="04098-160">Volání streamování je vhodné v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="04098-160">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="04098-161">Vyžaduje se vysoká propustnost nebo nízká latence.</span><span class="sxs-lookup"><span data-stu-id="04098-161">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="04098-162">gRPC a HTTP/2 jsou označeny jako kritické pro výkon.</span><span class="sxs-lookup"><span data-stu-id="04098-162">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="04098-163">Pracovník v klientovi odesílá nebo přijímá pravidelné zprávy se službou gRPC.</span><span class="sxs-lookup"><span data-stu-id="04098-163">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="04098-164">Uvědomte si další složitost a omezení použití volání streamování místo unárních:</span><span class="sxs-lookup"><span data-stu-id="04098-164">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="04098-165">Datový proud může být přerušen pomocí služby nebo chyby připojení.</span><span class="sxs-lookup"><span data-stu-id="04098-165">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="04098-166">Pokud dojde k chybě, vyžaduje se k restartování streamu logika.</span><span class="sxs-lookup"><span data-stu-id="04098-166">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="04098-167">`RequestStream.WriteAsync` není bezpečné pro více vláken.</span><span class="sxs-lookup"><span data-stu-id="04098-167">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="04098-168">V jednom okamžiku může být do datového proudu zapsána pouze jedna zpráva.</span><span class="sxs-lookup"><span data-stu-id="04098-168">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="04098-169">Odesílání zpráv z více vláken přes jeden datový proud vyžaduje frontu producent/příjemce, jako je <xref:System.Threading.Channels.Channel%601> zařazování zpráv.</span><span class="sxs-lookup"><span data-stu-id="04098-169">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="04098-170">Metoda streamování gRPC je omezená na příjem jednoho typu zprávy a odeslání jednoho typu zprávy.</span><span class="sxs-lookup"><span data-stu-id="04098-170">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="04098-171">Například `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` přijímá `RequestMessage` a odesílá `ResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="04098-171">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="04098-172">Protobuf podporu neznámých nebo podmíněných zpráv pomocí `Any` a `oneof` může toto omezení obejít.</span><span class="sxs-lookup"><span data-stu-id="04098-172">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
