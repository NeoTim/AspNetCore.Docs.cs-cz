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
# <a name="call-grpc-services-with-the-net-client"></a>Volání služeb gRPC s klientem .NET

Klientská knihovna .NET gRPC je k dispozici v balíčku [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet. Tento dokument vysvětluje, jak:

* Nakonfigurujte klienta gRPC pro volání služeb gRPC.
* Proveďte volání gRPC na unární, server streaming, streamování klientů a obousměrné metody streamování.

## <a name="configure-grpc-client"></a>Konfigurace klienta gRPC

gRPC klienti jsou konkrétní typy klientů, které jsou [generovány ze * \*souborů .proto* ](xref:grpc/basics#generated-c-assets). Konkrétní klient gRPC má metody, které se překládají do služby gRPC v souboru * \*.proto.*

Klient gRPC je vytvořen z kanálu. Začněte `GrpcChannel.ForAddress` pomocí k vytvoření kanálu a potom pomocí kanálu vytvořte klienta gRPC:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Kanál představuje dlouhodobé připojení ke službě gRPC. Když je kanál vytvořen, je nakonfigurován s možnostmi souvisejícími s voláním služby. Například `HttpClient` použití volání, maximální velikost odesílání a přijímání zpráv a protokolování lze zadat `GrpcChannelOptions` a použít s . `GrpcChannel.ForAddress` Úplný seznam možností naleznete v tématu [možnosti konfigurace klienta](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

Výkon a využití kanálu a klienta:

* Vytvoření kanálu může být nákladná operace. Opětovné použití kanálu pro volání gRPC poskytuje výhody výkonu.
* klienti gRPC jsou vytvářeny pomocí kanálů. gRPC klienti jsou lehké objekty a není nutné do mezipaměti nebo znovu použít.
* Z kanálu lze vytvořit více klientů gRPC, včetně různých typů klientů.
* Kanál a klienty vytvořené z kanálu lze bezpečně používat více vláken.
* Klienti vytvoření z kanálu mohou uskutečňovat více souběžných volání.

`GrpcChannel.ForAddress`není jedinou možností pro vytvoření gRPC klienta. Pokud voláte služby gRPC z aplikace ASP.NET Core, zvažte [integraci gRPC klienta .](xref:grpc/clientfactory) gRPC integrace `HttpClientFactory` s nabízí centralizovanou alternativu k vytváření gRPC klientů.

> [!NOTE]
> Další konfigurace je vyžadována pro [volání nezabezpečené služby gRPC s klientem .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).

> [!NOTE]
> Volání gRPC přes HTTP/2 s `Grpc.Net.Client` není aktuálně podporováno na Xamarin. Pracujeme na zlepšení podpory HTTP/2 v budoucí verzi Xamarinu. [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) a [gRPC-Web](xref:grpc/browser) jsou životaschopné alternativy, které fungují dnes.

## <a name="make-grpc-calls"></a>Volání gRPC

Volání gRPC je zahájeno voláním metody na straně klienta. Klient gRPC bude zpracovávat serializaci zpráv a adresování volání gRPC na správnou službu.

gRPC má různé typy metod. Způsob použití klienta k volání gRPC závisí na typu volané metody. Typy metod gRPC jsou:

* Unární
* Serverové vysílání
* Streamování klientů
* Obousměrné streamování

### <a name="unary-call"></a>Unární volání

Unární volání začíná klientem odesláním zprávy požadavku. Po dokončení služby je vrácena zpráva s odpovědí.

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

Každá metoda unární služby v souboru * \*.proto* bude mít za následek dvě metody .NET na konkrétním typu klienta gRPC pro volání metody: asynchronní metoda a metoda blokování. Například `GreeterClient` existují dva způsoby `SayHello`volání :

* `GreeterClient.SayHelloAsync`- `Greeter.SayHello` volání služby asynchronně. Dá se čekat.
* `GreeterClient.SayHello`- `Greeter.SayHello` volá službu a bloky až do dokončení. Nepoužívejte v asynchronním kódu.

### <a name="server-streaming-call"></a>Volání datového proudu serveru

Volání datového proudu serveru začíná odesláním zprávy požadavku klientem. `ResponseStream.MoveNext()`čte zprávy vysílané ze služby. Volání datového proudu `ResponseStream.MoveNext()` serveru `false`je dokončeno, když vrátí .

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

Pokud používáte C# 8 nebo `await foreach` novější, syntaxe lze číst zprávy. Metoda `IAsyncStreamReader<T>.ReadAllAsync()` rozšíření přečte všechny zprávy z datového proudu odpovědí:

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

### <a name="client-streaming-call"></a>Volání datového proudu klienta

Volání datového proudu klienta se spustí *bez* odeslání zprávy klientem. Klient se může rozhodnout `RequestStream.WriteAsync`odesílat zprávy pomocí aplikace . Po dokončení odesílání zpráv `RequestStream.CompleteAsync` klient by měl být volán upozornit službu. Volání je dokončeno, když služba vrátí zprávu odpovědi.

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

Obousměrné volání streamování se spustí *bez* odeslání zprávy klientem. Klient se může rozhodnout `RequestStream.WriteAsync`odesílat zprávy pomocí aplikace . Zprávy vysílané ze `ResponseStream.MoveNext()` služby jsou přístupné pomocí služby nebo `ResponseStream.ReadAllAsync()`. Obousměrné volání streamování je dokončeno, `ResponseStream` když nemá žádné další zprávy.

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

Během obousměrného volání datových proudů může klient a služba kdykoli vzájemně odesílat zprávy. Nejlepší logika klienta pro interakci s obousměrným voláním se liší v závislosti na logice služby.

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
