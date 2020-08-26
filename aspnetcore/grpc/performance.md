---
title: Osvědčené postupy z hlediska výkonu
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
ms.openlocfilehash: c6f6a9e5c9aa2f01209c8457a848dc6ec1f5ed88
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88866185"
---
# <a name="performance-best-practices"></a>Osvědčené postupy z hlediska výkonu

Od [James Newton – král](https://twitter.com/jamesnk)

gRPC je navržená pro vysoce výkonné služby. Tento dokument vysvětluje, jak získat nejlepší možný výkon z gRPC.

## <a name="reuse-channel"></a>Znovu použít kanál

Kanál gRPC by měl být znovu použit při provádění volání gRPC. Opětovné použití kanálu umožňuje, aby volání byla multiplexovaná prostřednictvím stávajícího připojení HTTP/2.

Pokud je pro každý gRPC hovor vytvořen nový kanál, může se výrazně zvýšit množství času, který je potřeba dokončit. Každé volání bude vyžadovat vícenásobné síťové odezvy mezi klientem a serverem, aby bylo možné vytvořit připojení HTTP/2:

1. Otevření soketu
2. Navazování připojení TCP
3. Vyjednávání TLS
4. Spouští se připojení HTTP/2.
5. Provádění volání gRPC

Kanály jsou bezpečné pro sdílení a opakované použití mezi gRPC voláními:

* klienti gRPC se vytvářejí pomocí kanálů. gRPC klienti jsou prosté objekty a nemusíte je ukládat do mezipaměti ani je znovu používat.
* Z kanálu, včetně různých typů klientů, lze vytvořit více klientů gRPC.
* Kanál a klienti vytvořené z kanálu můžou být bezpečně využívány více vlákny.
* Klienti vytvoření z kanálu můžou provádět víc souběžných volání.

## <a name="connection-concurrency"></a>Souběžnost připojení

Připojení HTTP/2 obvykle omezují [maximální počet souběžných proudů (aktivních požadavků HTTP)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) v připojení najednou. Ve výchozím nastavení většina serverů nastavila tento limit na 100 souběžných proudů.

GRPC kanál používá jedno připojení HTTP/2 a souběžná volání jsou v tomto připojení multiplexovaná. Když počet aktivních volání dosáhne limitu datových proudů, budou další volání zařazena do fronty v klientovi. Volání ve frontě čekají na dokončení aktivních volání, než se odešlou. Aplikace s vysokým zatížením nebo dlouhodobě běžícím voláním gRPC můžou zobrazit problémy s výkonem způsobené voláním služby Řízení front z důvodu tohoto limitu.

::: moniker range=">= aspnetcore-5.0"

Rozhraní .NET 5 zavádí `SocketsHttpHandler.EnableMultipleHttp2Connections` vlastnost. Při nastavení na `true` se při dosažení limitu souběžného streamu vytvoří další připojení HTTP/2 pomocí kanálu. Když `GrpcChannel` je vytvořen interní, `SocketsHttpHandler` automaticky se nakonfiguruje tak, aby vytvořil další připojení HTTP/2. Pokud aplikace nakonfiguruje svou vlastní obslužnou rutinu, zvažte nastavení `EnableMultipleHttp2Connections` na `true` :

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

Pro aplikace .NET Core 3,1 je k dispozici několik alternativních řešení:

* Vytvářejte samostatné gRPC kanály pro oblasti aplikace s vysokou zátěží. Například `Logger` Služba gRPC může mít vysoké zatížení. K vytvoření v aplikaci použijte samostatný kanál `LoggerClient` .
* Použijte fond gRPCch kanálů, například vytvořte seznam kanálů gRPC. `Random` slouží k výběru kanálu ze seznamu pokaždé, když je potřeba gRPC kanál. Použití `Random` náhodné distribuce volání přes více připojení.

> [!IMPORTANT]
> Dalším způsobem, jak tento problém vyřešit, je zvýšení maximálního počtu souběžných streamů na serveru. V Kestrel se nakonfiguruje s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .
>
> Zvýšení maximálního počtu souběžných streamů se nedoporučuje. Příliš mnoho datových proudů u jednoho připojení HTTP/2 přináší nové problémy s výkonem:
>
> * Kolize vláken mezi datovými proudy, které se pokouší o zápis do připojení.
> * Ztráta paketů připojení způsobí, že všechna volání budou zablokována ve vrstvě TCP.

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a>Příkazy pro udržování otevřených připojení

Pomocí příkazů pro udržování otevřených připojení k protokolu HTTP/2 je možné během období nečinnosti zůstat připojení HTTP/2 neaktivním. Pokud je stávající připojení HTTP/2 připravené, když aplikace pokračuje v aktivitě, umožní se rychle provést počáteční volání gRPC, aniž by došlo k prodlevě způsobené převázáním připojení.

Konfigurace příkazů pro udržování otevřených připojení <xref:System.Net.Http.SocketsHttpHandler> :

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

Předchozí kód nakonfiguruje kanál, který do serveru pošle při každém 60 sekundách během období nečinnosti odpověď na Keep Alive. Pomocí tohoto testu se ověří, že server a všechny používané proxy nástroje nezavřou připojení z důvodu nečinnosti.

::: moniker-end

## <a name="streaming"></a>Streamování

gRPC obousměrné streamování se dá použít k nahrazení unárních volání gRPC ve scénářích s vysokým výkonem. Jakmile se obousměrný datový proud spustí, streamování zpráv zpět a zpátky je rychlejší než odesílání zpráv s více unárními gRPC voláními. Streamované zprávy se odesílají jako data v existující žádosti HTTP/2 a eliminují režii při vytváření nové žádosti HTTP/2 pro každé unární volání.

Ukázková služba:

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

Ukázkový klient:

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

Nahrazení unárních volání s obousměrným datovým proudem z důvodu výkonu je pokročilá technika a není vhodná v mnoha situacích.

Volání streamování je vhodné v těchto případech:

1. Vyžaduje se vysoká propustnost nebo nízká latence.
2. gRPC a HTTP/2 jsou označeny jako kritické pro výkon.
3. Pracovník v klientovi odesílá nebo přijímá pravidelné zprávy se službou gRPC.

Uvědomte si další složitost a omezení použití volání streamování místo unárních:

1. Datový proud může být přerušen pomocí služby nebo chyby připojení. Pokud dojde k chybě, vyžaduje se k restartování streamu logika.
2. `RequestStream.WriteAsync` není bezpečné pro více vláken. V jednom okamžiku může být do datového proudu zapsána pouze jedna zpráva. Odesílání zpráv z více vláken přes jeden datový proud vyžaduje frontu producent/příjemce, jako je <xref:System.Threading.Channels.Channel%601> zařazování zpráv.
3. Metoda streamování gRPC je omezená na příjem jednoho typu zprávy a odeslání jednoho typu zprávy. Například `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` přijímá `RequestMessage` a odesílá `ResponseMessage` . Protobuf podporu neznámých nebo podmíněných zpráv pomocí `Any` a `oneof` může toto omezení obejít.
