---
title: Osvědčené postupy pro výkon s gRPC
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
ms.openlocfilehash: a0a1a6901e07fb0074ca403870378f267d3d4403
ms.sourcegitcommit: c9b03d8a6a4dcc59e4aacb30a691f349235a74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379442"
---
# <a name="performance-best-practices-with-grpc"></a><span data-ttu-id="5d8aa-103">Osvědčené postupy pro výkon s gRPC</span><span class="sxs-lookup"><span data-stu-id="5d8aa-103">Performance best practices with gRPC</span></span>

<span data-ttu-id="5d8aa-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5d8aa-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="5d8aa-105">gRPC je navržená pro vysoce výkonné služby.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="5d8aa-106">Tento dokument vysvětluje, jak získat nejlepší možný výkon z gRPC.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-grpc-channels"></a><span data-ttu-id="5d8aa-107">Opakované použití kanálů gRPC</span><span class="sxs-lookup"><span data-stu-id="5d8aa-107">Reuse gRPC channels</span></span>

<span data-ttu-id="5d8aa-108">Kanál gRPC by měl být znovu použit při provádění volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="5d8aa-109">Opětovné použití kanálu umožňuje, aby volání byla multiplexovaná prostřednictvím stávajícího připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="5d8aa-110">Pokud je pro každý gRPC hovor vytvořen nový kanál, může se výrazně zvýšit množství času, který je potřeba dokončit.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="5d8aa-111">Každé volání bude vyžadovat vícenásobné síťové odezvy mezi klientem a serverem, aby bylo možné vytvořit nové připojení HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="5d8aa-111">Each call will require multiple network round-trips between the client and the server to create a new HTTP/2 connection:</span></span>

1. <span data-ttu-id="5d8aa-112">Otevření soketu</span><span class="sxs-lookup"><span data-stu-id="5d8aa-112">Opening a socket</span></span>
2. <span data-ttu-id="5d8aa-113">Navazování připojení TCP</span><span class="sxs-lookup"><span data-stu-id="5d8aa-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="5d8aa-114">Vyjednávání TLS</span><span class="sxs-lookup"><span data-stu-id="5d8aa-114">Negotiating TLS</span></span>
4. <span data-ttu-id="5d8aa-115">Spouští se připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="5d8aa-116">Provádění volání gRPC</span><span class="sxs-lookup"><span data-stu-id="5d8aa-116">Making the gRPC call</span></span>

<span data-ttu-id="5d8aa-117">Kanály jsou bezpečné pro sdílení a opakované použití mezi gRPC voláními:</span><span class="sxs-lookup"><span data-stu-id="5d8aa-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="5d8aa-118">klienti gRPC se vytvářejí pomocí kanálů.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="5d8aa-119">gRPC klienti jsou prosté objekty a nemusíte je ukládat do mezipaměti ani je znovu používat.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="5d8aa-120">Z kanálu, včetně různých typů klientů, lze vytvořit více klientů gRPC.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="5d8aa-121">Kanál a klienti vytvořené z kanálu můžou být bezpečně využívány více vlákny.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="5d8aa-122">Klienti vytvoření z kanálu můžou provádět víc souběžných volání.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="5d8aa-123">gRPC Client Factory nabízí centralizovaný způsob konfigurace kanálů.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-123">gRPC client factory offers a centralized way to configure channels.</span></span> <span data-ttu-id="5d8aa-124">Automaticky znovu použije základní kanály.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-124">It automatically reuses underlying channels.</span></span> <span data-ttu-id="5d8aa-125">Další informace naleznete v tématu <xref:grpc/clientfactory>.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-125">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="5d8aa-126">Souběžnost připojení</span><span class="sxs-lookup"><span data-stu-id="5d8aa-126">Connection concurrency</span></span>

<span data-ttu-id="5d8aa-127">Připojení HTTP/2 obvykle omezují [maximální počet souběžných proudů (aktivních požadavků HTTP)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) v připojení najednou.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-127">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="5d8aa-128">Ve výchozím nastavení většina serverů nastavila tento limit na 100 souběžných proudů.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-128">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="5d8aa-129">GRPC kanál používá jedno připojení HTTP/2 a souběžná volání jsou v tomto připojení multiplexovaná.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-129">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="5d8aa-130">Když počet aktivních volání dosáhne limitu datových proudů, budou další volání zařazena do fronty v klientovi.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-130">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="5d8aa-131">Volání ve frontě čekají na dokončení aktivních volání, než se odešlou.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-131">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="5d8aa-132">Aplikace s vysokým zatížením nebo dlouhodobě běžícím voláním gRPC můžou zobrazit problémy s výkonem způsobené voláním služby Řízení front z důvodu tohoto limitu.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-132">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5d8aa-133">Rozhraní .NET 5 zavádí `SocketsHttpHandler.EnableMultipleHttp2Connections` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-133">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="5d8aa-134">Při nastavení na `true` se při dosažení limitu souběžného streamu vytvoří další připojení HTTP/2 pomocí kanálu.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-134">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="5d8aa-135">Když `GrpcChannel` je vytvořen interní, `SocketsHttpHandler` automaticky se nakonfiguruje tak, aby vytvořil další připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-135">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="5d8aa-136">Pokud aplikace nakonfiguruje svou vlastní obslužnou rutinu, zvažte nastavení `EnableMultipleHttp2Connections` na `true` :</span><span class="sxs-lookup"><span data-stu-id="5d8aa-136">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

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

<span data-ttu-id="5d8aa-137">Pro aplikace .NET Core 3,1 je k dispozici několik alternativních řešení:</span><span class="sxs-lookup"><span data-stu-id="5d8aa-137">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="5d8aa-138">Vytvářejte samostatné gRPC kanály pro oblasti aplikace s vysokou zátěží.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-138">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="5d8aa-139">Například `Logger` Služba gRPC může mít vysoké zatížení.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-139">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="5d8aa-140">K vytvoření v aplikaci použijte samostatný kanál `LoggerClient` .</span><span class="sxs-lookup"><span data-stu-id="5d8aa-140">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="5d8aa-141">Použijte fond gRPCch kanálů, například vytvořte seznam kanálů gRPC.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-141">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="5d8aa-142">`Random` slouží k výběru kanálu ze seznamu pokaždé, když je potřeba gRPC kanál.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-142">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="5d8aa-143">Použití `Random` náhodné distribuce volání přes více připojení.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-143">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5d8aa-144">Dalším způsobem, jak tento problém vyřešit, je zvýšení maximálního počtu souběžných streamů na serveru.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-144">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="5d8aa-145">V Kestrel se nakonfiguruje s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .</span><span class="sxs-lookup"><span data-stu-id="5d8aa-145">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="5d8aa-146">Zvýšení maximálního počtu souběžných streamů se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-146">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="5d8aa-147">Příliš mnoho datových proudů u jednoho připojení HTTP/2 přináší nové problémy s výkonem:</span><span class="sxs-lookup"><span data-stu-id="5d8aa-147">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="5d8aa-148">Kolize vláken mezi datovými proudy, které se pokouší o zápis do připojení.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-148">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="5d8aa-149">Ztráta paketů připojení způsobí, že všechna volání budou zablokována ve vrstvě TCP.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-149">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

## <a name="load-balancing"></a><span data-ttu-id="5d8aa-150">Vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="5d8aa-150">Load balancing</span></span>

<span data-ttu-id="5d8aa-151">Některé nástroje pro vyrovnávání zatížení s gRPC nefungují efektivně.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-151">Some load balancers don't work effectively with gRPC.</span></span> <span data-ttu-id="5d8aa-152">Nástroje pro vyrovnávání zatížení L4 (Transport) fungují na úrovni připojení, a to distribucí připojení TCP mezi koncovými body.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-152">L4 (transport) load balancers operate at a connection level, by distributing TCP connections across endpoints.</span></span> <span data-ttu-id="5d8aa-153">Tento přístup funguje dobře pro vyrovnávání volání rozhraní API vytvořeného pomocí protokolu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-153">This approach works well for loading balancing API calls made with HTTP/1.1.</span></span> <span data-ttu-id="5d8aa-154">Souběžná volání s HTTP/1.1 se odesílají v různých připojeních, což umožňuje vyrovnávat zatížení mezi koncovými body.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-154">Concurrent calls made with HTTP/1.1 are sent on different connections, allowing calls to be load balanced across endpoints.</span></span>

<span data-ttu-id="5d8aa-155">Vzhledem k tomu, že nástroje pro vyrovnávání zatížení L4 provozují na úrovni připojení, nefungují dobře s gRPC.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-155">Because L4 load balancers operate at a connection level, they don't work well with gRPC.</span></span> <span data-ttu-id="5d8aa-156">gRPC používá protokol HTTP/2, který multiplexuje více volání v jednom připojení TCP.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-156">gRPC uses HTTP/2, which multiplexes multiple calls on a single TCP connection.</span></span> <span data-ttu-id="5d8aa-157">Všechna volání gRPC přes toto připojení přecházejí do jednoho koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-157">All gRPC calls over that connection go to one endpoint.</span></span>

<span data-ttu-id="5d8aa-158">Existují dvě možnosti efektivního vyrovnávání zatížení gRPC:</span><span class="sxs-lookup"><span data-stu-id="5d8aa-158">There are two options to effectively load balance gRPC:</span></span>

* <span data-ttu-id="5d8aa-159">Vyrovnávání zatížení na straně klienta</span><span class="sxs-lookup"><span data-stu-id="5d8aa-159">Client-side load balancing</span></span>
* <span data-ttu-id="5d8aa-160">Vyrovnávání zatížení serveru L7 (aplikace)</span><span class="sxs-lookup"><span data-stu-id="5d8aa-160">L7 (application) proxy load balancing</span></span>

> [!NOTE]
> <span data-ttu-id="5d8aa-161">Mezi koncovými body lze vyrovnávat zatížení pouze volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-161">Only gRPC calls can be load balanced between endpoints.</span></span> <span data-ttu-id="5d8aa-162">Po navázání volání streamování gRPC budou všechny zprávy odesílané přes Stream přejít do jednoho koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-162">Once a streaming gRPC call is established, all messages sent over the stream go to one endpoint.</span></span>

### <a name="client-side-load-balancing"></a><span data-ttu-id="5d8aa-163">Vyrovnávání zatížení na straně klienta</span><span class="sxs-lookup"><span data-stu-id="5d8aa-163">Client-side load balancing</span></span>

<span data-ttu-id="5d8aa-164">Při vyrovnávání zatížení na straně klienta ví klient o koncových bodech.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-164">With client-side load balancing, the client knows about endpoints.</span></span> <span data-ttu-id="5d8aa-165">Pro každé volání gRPC vybere jiný koncový bod pro odeslání volání.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-165">For each gRPC call it selects a different endpoint to send the call to.</span></span> <span data-ttu-id="5d8aa-166">Vyrovnávání zatížení na straně klienta je vhodnou volbou, pokud je latence důležitá.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-166">Client-side load balancing is a good choice when latency is important.</span></span> <span data-ttu-id="5d8aa-167">Mezi klientem a službou není žádný proxy server, aby bylo volání služby odesíláno přímo.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-167">There is no proxy between the client and the service so the call is sent to the service directly.</span></span> <span data-ttu-id="5d8aa-168">Nevýhodou pro vyrovnávání zatížení na straně klienta je, že každý klient musí sledovat dostupné koncové body, které by měl použít.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-168">The downside to client-side load balancing is that each client must keep track of available endpoints it should use.</span></span>

<span data-ttu-id="5d8aa-169">TLB vyrovnávání zatížení klienta je postup, ve kterém je stav vyrovnávání zatížení uložený v centrálním umístění.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-169">Lookaside client load balancing is a technique where load balancing state is stored in a central location.</span></span> <span data-ttu-id="5d8aa-170">Klienti pravidelně dotazují centrální umístění na informace, které se mají použít při rozhodování o vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-170">Clients periodically query the central location for information to use when making load balancing decisions.</span></span>

<span data-ttu-id="5d8aa-171">`Grpc.Net.Client` v současné době nepodporuje vyrovnávání zatížení na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-171">`Grpc.Net.Client` currently doesn't support client-side load balancing.</span></span> <span data-ttu-id="5d8aa-172">[Grpc. Core](https://www.nuget.org/packages/Grpc.Core) je dobrá volba, pokud je v rozhraní .NET vyžadováno vyrovnávání zatížení na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) is a good choice if client-side load balancing is required in .NET.</span></span>

### <a name="proxy-load-balancing"></a><span data-ttu-id="5d8aa-173">Vyrovnávání zatížení proxy</span><span class="sxs-lookup"><span data-stu-id="5d8aa-173">Proxy load balancing</span></span>

<span data-ttu-id="5d8aa-174">Server proxy pro L7 (aplikace) funguje na vyšší úrovni než proxy L4 (Transport).</span><span class="sxs-lookup"><span data-stu-id="5d8aa-174">An L7 (application) proxy works at a higher level than an L4 (transport) proxy.</span></span> <span data-ttu-id="5d8aa-175">Proxy servery L7 rozumí HTTP/2 a jsou schopné distribuovat volání gRPC do proxy serveru na jednom připojení HTTP/2 přes více koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-175">L7 proxies understand HTTP/2, and are able to distribute gRPC calls multiplexed to the proxy on one HTTP/2 connection across multiple endpoints.</span></span> <span data-ttu-id="5d8aa-176">Použití proxy serveru je jednodušší než vyrovnávání zatížení na straně klienta, ale může do gRPC volání přidat další latenci.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-176">Using a proxy is simpler than client-side load balancing, but can add extra latency to gRPC calls.</span></span>

<span data-ttu-id="5d8aa-177">K dispozici je mnoho serverů proxy L7.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-177">There are many L7 proxies available.</span></span> <span data-ttu-id="5d8aa-178">Mezi tyto možnosti patří:</span><span class="sxs-lookup"><span data-stu-id="5d8aa-178">Some options are:</span></span>

* <span data-ttu-id="5d8aa-179">[Zástupné](https://www.envoyproxy.io/) – oblíbený Open Source proxy.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-179">[Envoy](https://www.envoyproxy.io/) - A popular open source proxy.</span></span>
* <span data-ttu-id="5d8aa-180">[Linkerová](https://linkerd.io/) mřížka-služby pro Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-180">[Linkerd](https://linkerd.io/) - Service mesh for Kubernetes.</span></span>
* <span data-ttu-id="5d8aa-181">[YARP: reverzní proxy](https://microsoft.github.io/reverse-proxy/) – náhled Open Source proxy serveru napsaný v .NET</span><span class="sxs-lookup"><span data-stu-id="5d8aa-181">[YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/) - A preview open source proxy written in .NET.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="5d8aa-182">Příkazy pro udržování otevřených připojení</span><span class="sxs-lookup"><span data-stu-id="5d8aa-182">Keep alive pings</span></span>

<span data-ttu-id="5d8aa-183">Pomocí příkazů pro udržování otevřených připojení k protokolu HTTP/2 je možné během období nečinnosti zůstat připojení HTTP/2 neaktivním.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-183">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="5d8aa-184">Pokud je stávající připojení HTTP/2 připravené, když aplikace pokračuje v aktivitě, umožní se rychle provést počáteční volání gRPC, aniž by došlo k prodlevě způsobené převázáním připojení.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-184">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="5d8aa-185">Konfigurace příkazů pro udržování otevřených připojení <xref:System.Net.Http.SocketsHttpHandler> :</span><span class="sxs-lookup"><span data-stu-id="5d8aa-185">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

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

<span data-ttu-id="5d8aa-186">Předchozí kód nakonfiguruje kanál, který do serveru pošle při každém 60 sekundách během období nečinnosti odpověď na Keep Alive.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-186">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="5d8aa-187">Pomocí tohoto testu se ověří, že server a všechny používané proxy nástroje nezavřou připojení z důvodu nečinnosti.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-187">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="5d8aa-188">Streamování</span><span class="sxs-lookup"><span data-stu-id="5d8aa-188">Streaming</span></span>

<span data-ttu-id="5d8aa-189">gRPC obousměrné streamování se dá použít k nahrazení unárních volání gRPC ve scénářích s vysokým výkonem.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-189">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="5d8aa-190">Jakmile se obousměrný datový proud spustí, streamování zpráv zpět a zpátky je rychlejší než odesílání zpráv s více unárními gRPC voláními.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-190">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="5d8aa-191">Streamované zprávy se odesílají jako data v existující žádosti HTTP/2 a eliminují režii při vytváření nové žádosti HTTP/2 pro každé unární volání.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-191">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="5d8aa-192">Ukázková služba:</span><span class="sxs-lookup"><span data-stu-id="5d8aa-192">Example service:</span></span>

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

<span data-ttu-id="5d8aa-193">Ukázkový klient:</span><span class="sxs-lookup"><span data-stu-id="5d8aa-193">Example client:</span></span>

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

<span data-ttu-id="5d8aa-194">Nahrazení unárních volání s obousměrným datovým proudem z důvodu výkonu je pokročilá technika a není vhodná v mnoha situacích.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-194">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="5d8aa-195">Volání streamování je vhodné v těchto případech:</span><span class="sxs-lookup"><span data-stu-id="5d8aa-195">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="5d8aa-196">Vyžaduje se vysoká propustnost nebo nízká latence.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-196">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="5d8aa-197">gRPC a HTTP/2 jsou označeny jako kritické pro výkon.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-197">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="5d8aa-198">Pracovník v klientovi odesílá nebo přijímá pravidelné zprávy se službou gRPC.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-198">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="5d8aa-199">Uvědomte si další složitost a omezení použití volání streamování místo unárních:</span><span class="sxs-lookup"><span data-stu-id="5d8aa-199">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="5d8aa-200">Datový proud může být přerušen pomocí služby nebo chyby připojení.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-200">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="5d8aa-201">Pokud dojde k chybě, vyžaduje se k restartování streamu logika.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-201">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="5d8aa-202">`RequestStream.WriteAsync` není bezpečné pro více vláken.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-202">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="5d8aa-203">V jednom okamžiku může být do datového proudu zapsána pouze jedna zpráva.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-203">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="5d8aa-204">Odesílání zpráv z více vláken přes jeden datový proud vyžaduje frontu producent/příjemce, jako je <xref:System.Threading.Channels.Channel%601> zařazování zpráv.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-204">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="5d8aa-205">Metoda streamování gRPC je omezená na příjem jednoho typu zprávy a odeslání jednoho typu zprávy.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-205">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="5d8aa-206">Například `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` přijímá `RequestMessage` a odesílá `ResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="5d8aa-206">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="5d8aa-207">Protobuf podporu neznámých nebo podmíněných zpráv pomocí `Any` a `oneof` může toto omezení obejít.</span><span class="sxs-lookup"><span data-stu-id="5d8aa-207">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
