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
# <a name="call-grpc-services-with-the-net-client"></a>Volání služeb gRPC pomocí klienta .NET

Klientská knihovna .NET gRPC je k dispozici v balíčku NuGet pro [gRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) . Tento dokument vysvětluje, jak:

* Nakonfigurujte klienta gRPC, který bude volat služby gRPC.
* GRPC volání unárního, streamování serveru, streamování klientů a metod obousměrného streamování.

## <a name="configure-grpc-client"></a>Konfigurace klienta gRPC

gRPC klienti jsou konkrétní typy klientů, které jsou [vygenerovány ze souborů * \* . proto* ](xref:grpc/basics#generated-c-assets). Konkrétní klient gRPC má metody, které se převádějí do služby gRPC v souboru * \* ..* .

Klient gRPC se vytvoří z kanálu. Začněte tím, že použijete `GrpcChannel.ForAddress` k vytvoření kanálu a pak pomocí kanálu vytvoříte klienta gRPC:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Kanál představuje dlouhodobé připojení ke službě gRPC. Při vytvoření kanálu se nakonfiguruje s možnostmi, které se týkají volání služby. Například, který se `HttpClient` používá k volání, maximální velikost zprávy Send a Receive a protokolování lze zadat v `GrpcChannelOptions` a použít s `GrpcChannel.ForAddress` . Úplný seznam možností najdete v tématu [Možnosti konfigurace klienta](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a>Konfigurace TLS

Klient gRPC musí používat stejné zabezpečení na úrovni připojení jako volaná služba. protokol TLS (gRPC Client Transport Layer Security) je nakonfigurován při vytvoření kanálu gRPC. Klient gRPC vyvolá chybu při volání služby a zabezpečení kanálu a služby na úrovni připojení se neshoduje.

Pokud chcete gRPC kanál nakonfigurovat tak, aby používal protokol TLS, ujistěte se, že adresa serveru začíná `https` . Například `GrpcChannel.ForAddress("https://localhost:5001")` používá protokol HTTPS. Kanál gRPC automaticky negotates připojení zabezpečené protokolem TLS a používá zabezpečené připojení k zajištění gRPC volání.

> [!TIP]
> gRPC podporuje ověřování certifikátu klienta přes protokol TLS. Informace o konfiguraci klientských certifikátů pomocí gRPC kanálu najdete v tématu <xref:grpc/authn-and-authz#client-certificate-authentication> .

Chcete-li volat nezabezpečené služby gRPC, ujistěte se, že adresa serveru začíná `http` . Například `GrpcChannel.ForAddress("http://localhost:5000")` používá protokol HTTP. V .NET Core 3,1 nebo novější se vyžaduje další konfigurace pro [volání nezabezpečených gRPC služeb pomocí klienta .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).

### <a name="client-performance"></a>Výkon klienta

Výkon a využití kanálu a klienta:

* Vytvoření kanálu může být náročná operace. Použití kanálu pro volání gRPC poskytuje výhody týkající se výkonu.
* klienti gRPC se vytvářejí pomocí kanálů. gRPC klienti jsou prosté objekty a nemusíte je ukládat do mezipaměti ani je znovu používat.
* Z kanálu, včetně různých typů klientů, lze vytvořit více klientů gRPC.
* Kanál a klienti vytvořené z kanálu můžou být bezpečně využívány více vlákny.
* Klienti vytvoření z kanálu můžou provádět víc souběžných volání.

`GrpcChannel.ForAddress` není jedinou možností pro vytvoření klienta gRPC. Pokud voláte služby gRPC Services z aplikace ASP.NET Core, zvažte [integraci klientské továrny gRPC](xref:grpc/clientfactory). integrace gRPC s `HttpClientFactory` nabízí centralizovanou alternativu k vytváření klientů gRPC.

> [!NOTE]
> Volání gRPC přes HTTP/2 s `Grpc.Net.Client` se v Xamarin v tuto chvíli nepodporuje. Pracujeme na vylepšení podpory protokolu HTTP/2 v budoucí verzi Xamarin. [Grpc. Core](https://www.nuget.org/packages/Grpc.Core) a [Grpc-web](xref:grpc/browser) představují životaschopné alternativy, které dnes fungují.

## <a name="make-grpc-calls"></a>Provést volání gRPC

Volání gRPC je zahájeno voláním metody na klientovi. Klient gRPC zpracuje serializaci zprávy a adresování volání gRPC ke správné službě.

gRPC má různé typy metod. Způsob, jakým je klient použit k provedení volání gRPC, závisí na typu volané metody. Typy metod gRPC jsou:

* Unární
* Streamování serveru
* Streamování klientů
* Obousměrné streamování

### <a name="unary-call"></a>Unární volání

Unární volání začíná klientem, který odesílá zprávu požadavku. Po dokončení služby se vrátí zpráva s odpovědí.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

Každá unární metoda služby v souboru * \* .* proč má za následek dvě metody .NET pro konkrétní typ klienta gRPC pro volání metody: asynchronní metodu a metodu blokování. Například `GreeterClient` existují dva způsoby volání `SayHello` :

* `GreeterClient.SayHelloAsync` -volá `Greeter.SayHello` službu asynchronně. Může být očekáváno.
* `GreeterClient.SayHello` -volá `Greeter.SayHello` službu a zablokuje se do dokončení. Nepoužívejte v asynchronním kódu.

### <a name="server-streaming-call"></a>Volání streamování serveru

Volání streamování serveru začíná klientem, který odesílá zprávu požadavku. `ResponseStream.MoveNext()` přečte zprávy streamované ze služby. Volání streamování serveru je po `ResponseStream.MoveNext()` návratu dokončeno `false` .

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

Při použití C# 8 nebo novější `await foreach` lze syntaxi použít ke čtení zpráv. `IAsyncStreamReader<T>.ReadAllAsync()`Metoda rozšíření načte všechny zprávy z datového proudu odpovědí:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a>Volání streamování klientů

Volání streamování klienta se spustí *bez* odeslání zprávy klientem. Klient nástroje může odeslat zprávy pomocí příkazu `RequestStream.WriteAsync` . Když klient dokončí posílání zpráv, `RequestStream.CompleteAsync()` měla by se zavolat, aby službu informovaly. Volání je dokončeno, když služba vrátí zprávu odpovědi.

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

### <a name="bi-directional-streaming-call"></a>Obousměrné volání streamování

Obousměrné volání streamování se spustí *bez* odeslání zprávy klientem. Klient nástroje může odeslat zprávy pomocí příkazu `RequestStream.WriteAsync` . Zprávy streamované ze služby jsou přístupné pomocí nástroje `ResponseStream.MoveNext()` nebo `ResponseStream.ReadAllAsync()` . Obousměrné volání streamování je dokončeno, když `ResponseStream` nemá žádné další zprávy.

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

Chcete-li dosáhnout nejlepšího výkonu a vyhnout se zbytečným chybám v klientech a službě, zkuste plynule volat obousměrné volání streamování. Obousměrné volání se dokončí řádným dodržením, když server dokončil čtení datového proudu požadavků a klient dokončil čtení streamu odpovědí. Předchozí ukázkové volání je jeden příklad obousměrného volání, které končí řádným ukončením. V volání klienta:

1. Spustí nové obousměrné volání streamování voláním `EchoClient.Echo` .
2. Vytvoří úlohu na pozadí ke čtení zpráv ze služby pomocí `ResponseStream.ReadAllAsync()` .
3. Odesílá zprávy serveru pomocí `RequestStream.WriteAsync` .
4. Upozorní server, na kterém dokončil odesílání zpráv `RequestStream.CompleteAsync()` .
5. Počká, dokud úloha na pozadí nenačte všechny příchozí zprávy.

Během volání obousměrného streamování může klient a služba kdykoli odesílat zprávy. Nejlepší klientská logika pro interakci s obousměrným voláním se liší v závislosti na logice služby.

## <a name="access-grpc-trailers"></a>Přístup k gRPC přívěsům

volání gRPC mohou vracet gRPC přípojná vozidla. gRPC přípojná vozidla slouží k poskytování metadat názvů a hodnot volání. Přípojná rozhraní poskytují podobné funkce hlaviček protokolu HTTP, ale jsou přijaty na konci volání.

gRPC přípojná vozidla jsou přístupná pomocí `GetTrailers()` , která vrací kolekci metadat. Po dokončení odpovědi se vrátí přípojná část, proto musíte před přístupem k přípojným vozidlům čekat na všechny zprávy s odpovědí.

Unární a volání streamování klientů musí `ResponseAsync` před voláním čekat na `GetTrailers()` :

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

Volání serveru a obousměrného streamování musí dokončit čekání na datový proud odpovědi před voláním `GetTrailers()` :

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

gRPC přívěsy jsou také přístupné z `RpcException` . Služba může vracet přípojná vozidla společně se stavem gRPC bez OK. V této situaci jsou přípojná rozhraní načítána z výjimky vyvolané klientem gRPC:

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

## <a name="configure-deadline"></a>Konfigurovat konečný termín

Doporučuje se nakonfigurovat konečný termín volání gRPC, protože poskytuje horní limit toho, jak dlouho může volání běžet. Zastaví se tak nesprávnému chování služeb, které neběží trvale a vyčerpání prostředků serveru. Termíny jsou užitečným nástrojem pro vytváření spolehlivých aplikací.

Nakonfigurujte `CallOptions.Deadline` , aby se nastavil konečný termín pro gRPC volání:

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

Další informace naleznete v tématu <xref:grpc/deadlines-cancellation#deadlines>.

## <a name="additional-resources"></a>Další materiály

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
