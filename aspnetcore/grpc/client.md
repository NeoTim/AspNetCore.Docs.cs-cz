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
# <a name="call-grpc-services-with-the-net-client"></a>Volání služeb gRPC Services pomocí klienta .NET

Klientská knihovna .NET gRPC je k dispozici v balíčku NuGet pro [gRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) . Tento dokument vysvětluje, jak:

* Nakonfigurujte klienta gRPC, který bude volat služby gRPC.
* GRPC volání unárního, streamování serveru, streamování klientů a metod obousměrného streamování.

## <a name="configure-grpc-client"></a>Konfigurace klienta gRPC

gRPC klienti jsou konkrétní typy klientů, které jsou [vygenerovány ze  *\*souborů. proto* ](xref:grpc/basics#generated-c-assets). Konkrétní klient gRPC má metody, které se převádějí do služby gRPC v  *\*souboru..* .

Klient gRPC se vytvoří z kanálu. Začněte tím `GrpcChannel.ForAddress` , že použijete k vytvoření kanálu a pak pomocí kanálu vytvoříte klienta gRPC:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Kanál představuje dlouhodobé připojení ke službě gRPC. Při vytvoření kanálu se nakonfiguruje s možnostmi, které se týkají volání služby. Například, který `HttpClient` se používá k volání, maximální velikost zprávy Send a Receive a protokolování lze zadat v `GrpcChannelOptions` a použít s `GrpcChannel.ForAddress`. Úplný seznam možností najdete v tématu [Možnosti konfigurace klienta](xref:grpc/configuration#configure-client-options).

Vytvoření kanálu může být náročná operace a opakované použití kanálu pro volání gRPC nabízí výhody týkající se výkonu. Z kanálu, včetně různých typů klientů, lze vytvořit více konkrétních gRPC klientů. Konkrétní typy klientů gRPC jsou prosté objekty a v případě potřeby je lze vytvořit.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

`GrpcChannel.ForAddress`není jedinou možností pro vytvoření klienta gRPC. Pokud voláte služby gRPC Services z aplikace ASP.NET Core, zvažte [integraci klientské továrny gRPC](xref:grpc/clientfactory). integrace gRPC s `HttpClientFactory` nabízí centralizovanou alternativu k vytváření klientů gRPC.

> [!NOTE]
> Pro [volání nezabezpečených služeb gRPC s klientem .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)je vyžadována další konfigurace.

## <a name="make-grpc-calls"></a>Provést volání gRPC

Volání gRPC je zahájeno voláním metody na klientovi. Klient gRPC zpracuje serializaci zprávy a adresování volání gRPC ke správné službě.

gRPC má různé typy metod. Způsob, jakým se používá klient pro volání gRPC, závisí na typu volané metody. Typy metod gRPC jsou:

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

Každá unární metoda služby v  *\*souboru.* proč má za následek dvě metody .NET pro konkrétní typ klienta gRPC pro volání metody: asynchronní metodu a metodu blokování. Například `GreeterClient` existují dva způsoby volání `SayHello`:

* `GreeterClient.SayHelloAsync`-volá `Greeter.SayHello` službu asynchronně. Může být očekáváno.
* `GreeterClient.SayHello`-volá `Greeter.SayHello` službu a zablokuje se do dokončení. Nepoužívejte v asynchronním kódu.

### <a name="server-streaming-call"></a>Volání streamování serveru

Volání streamování serveru začíná klientem, který odesílá zprávu požadavku. `ResponseStream.MoveNext()`přečte zprávy streamované ze služby. Volání streamování serveru je po `ResponseStream.MoveNext()` návratu `false`dokončeno.

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

Pokud používáte C# 8 nebo novější `await foreach` , můžete syntaxi použít ke čtení zpráv. Metoda `IAsyncStreamReader<T>.ReadAllAsync()` rozšíření načte všechny zprávy z datového proudu odpovědí:

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

### <a name="client-streaming-call"></a>Volání streamování klientů

Volání streamování klienta se spustí *bez* odeslání zprávy klientem. Klient se může rozhodnout, že odešle zprávy `RequestStream.WriteAsync`Send. Až klient dokončí odesílání zpráv `RequestStream.CompleteAsync` , měla by se volat oznámení služby. Volání je dokončeno, když služba vrátí zprávu odpovědi.

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

### <a name="bi-directional-streaming-call"></a>Obousměrné volání streamování

Obousměrné volání streamování se spustí *bez* odeslání zprávy klientem. Klient nástroje může odeslat zprávy pomocí `RequestStream.WriteAsync`příkazu. Zprávy streamované ze služby jsou přístupné pomocí `ResponseStream.MoveNext()` nástroje nebo. `ResponseStream.ReadAllAsync()` Obousměrné volání streamování je dokončeno, když `ResponseStream` nemá žádné další zprávy.

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

Během volání obousměrného streamování může klient a služba kdykoli odesílat zprávy. Nejlepší klientská logika pro interakci s obousměrným voláním se liší v závislosti na logice služby.

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
