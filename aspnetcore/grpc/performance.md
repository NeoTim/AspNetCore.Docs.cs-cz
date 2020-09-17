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
ms.openlocfilehash: 4d50698b8c55f7fb3ef9a2c3102e73e046a22a9c
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722842"
---
# <a name="performance-best-practices-with-grpc"></a>Osvědčené postupy pro výkon s gRPC

Od [James Newton – král](https://twitter.com/jamesnk)

gRPC je navržená pro vysoce výkonné služby. Tento dokument vysvětluje, jak získat nejlepší možný výkon z gRPC.

## <a name="reuse-grpc-channels"></a>Opakované použití kanálů gRPC

Kanál gRPC by měl být znovu použit při provádění volání gRPC. Opětovné použití kanálu umožňuje, aby volání byla multiplexovaná prostřednictvím stávajícího připojení HTTP/2.

Pokud je pro každý gRPC hovor vytvořen nový kanál, může se výrazně zvýšit množství času, který je potřeba dokončit. Každé volání bude vyžadovat vícenásobné síťové odezvy mezi klientem a serverem, aby bylo možné vytvořit nové připojení HTTP/2:

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

gRPC Client Factory nabízí centralizovaný způsob konfigurace kanálů. Automaticky znovu použije základní kanály. Další informace naleznete v tématu <xref:grpc/clientfactory>.

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

## <a name="load-balancing"></a>Vyrovnávání zatížení

Některé nástroje pro vyrovnávání zatížení s gRPC nefungují efektivně. Nástroje pro vyrovnávání zatížení L4 (Transport) fungují na úrovni připojení, a to distribucí připojení TCP mezi koncovými body. Tento přístup funguje dobře pro vyrovnávání volání rozhraní API vytvořeného pomocí protokolu HTTP/1.1. Souběžná volání s HTTP/1.1 se odesílají v různých připojeních, což umožňuje vyrovnávat zatížení mezi koncovými body.

Vzhledem k tomu, že nástroje pro vyrovnávání zatížení L4 provozují na úrovni připojení, nefungují dobře s gRPC. gRPC používá protokol HTTP/2, který multiplexuje více volání v jednom připojení TCP. Všechna volání gRPC přes toto připojení přecházejí do jednoho koncového bodu.

Existují dvě možnosti efektivního vyrovnávání zatížení gRPC:

* Vyrovnávání zatížení na straně klienta
* Vyrovnávání zatížení serveru L7 (aplikace)

> [!NOTE]
> Mezi koncovými body lze vyrovnávat zatížení pouze volání gRPC. Po navázání volání streamování gRPC budou všechny zprávy odesílané přes Stream přejít do jednoho koncového bodu.

### <a name="client-side-load-balancing"></a>Vyrovnávání zatížení na straně klienta

Při vyrovnávání zatížení na straně klienta ví klient o koncových bodech. Pro každé volání gRPC vybere jiný koncový bod pro odeslání volání. Vyrovnávání zatížení na straně klienta je vhodnou volbou, pokud je latence důležitá. Mezi klientem a službou není žádný proxy server, aby bylo volání služby odesíláno přímo. Nevýhodou pro vyrovnávání zatížení na straně klienta je, že každý klient musí sledovat dostupné koncové body, které by měl použít.

TLB vyrovnávání zatížení klienta je postup, ve kterém je stav vyrovnávání zatížení uložený v centrálním umístění. Klienti pravidelně dotazují centrální umístění na informace, které se mají použít při rozhodování o vyrovnávání zatížení.

`Grpc.Net.Client` v současné době nepodporuje vyrovnávání zatížení na straně klienta. [Grpc. Core](https://www.nuget.org/packages/Grpc.Core) je dobrá volba, pokud je v rozhraní .NET vyžadováno vyrovnávání zatížení na straně klienta.

### <a name="proxy-load-balancing"></a>Vyrovnávání zatížení proxy

Server proxy pro L7 (aplikace) funguje na vyšší úrovni než proxy L4 (Transport). Proxy servery L7 rozumí HTTP/2 a jsou schopné distribuovat volání gRPC do proxy serveru na jednom připojení HTTP/2 přes více koncových bodů. Použití proxy serveru je jednodušší než vyrovnávání zatížení na straně klienta, ale může do gRPC volání přidat další latenci.

K dispozici je mnoho serverů proxy L7. Mezi tyto možnosti patří:

* [Zástupné](https://www.envoyproxy.io/) – oblíbený Open Source proxy.
* [Linkerová](https://linkerd.io/) mřížka-služby pro Kubernetes.
* [YARP: reverzní proxy](https://microsoft.github.io/reverse-proxy/) – náhled Open Source proxy serveru napsaný v .NET

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a>Komunikace mezi procesy

gRPC volání mezi klientem a službou se obvykle odesílají přes sokety TCP. Protokol TCP je ideální pro komunikaci přes síť, ale [komunikace mezi procesy (IPC)](https://wikipedia.org/wiki/Inter-process_communication) je efektivnější, pokud je klient a služba ve stejném počítači.

Pro volání gRPC mezi procesy ve stejném počítači zvažte použití přenosu, jako jsou například doménové sokety systému UNIX nebo pojmenované kanály. Další informace naleznete v tématu <xref:grpc/interprocess>.

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
