---
title: Volání služeb gRPC Services pomocí klienta .NET
author: jamesnk
description: Naučte se volat služby gRPC Services pomocí klienta .NET gRPC.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 6a6a649f7194354b16f3d67160be02428cc01170
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667172"
---
# <a name="call-grpc-services-with-the-net-client"></a>Volání služeb gRPC Services pomocí klienta .NET

Klientská knihovna .NET gRPC je k dispozici v balíčku NuGet pro [gRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) . Tento dokument vysvětluje, jak:

* Nakonfigurujte klienta gRPC, který bude volat služby gRPC.
* GRPC volání unárního, streamování serveru, streamování klientů a metod obousměrného streamování.

## <a name="configure-grpc-client"></a>Konfigurace klienta gRPC

gRPC klienti jsou konkrétní typy klientů, které jsou [generovány z *\*.* soubory](xref:grpc/basics#generated-c-assets). Konkrétní klient gRPC má metody, které se převádějí do služby gRPC v souboru *\*..* .

Klient gRPC se vytvoří z kanálu. Začněte vytvořením kanálu pomocí `GrpcChannel.ForAddress` a pak pomocí kanálu vytvořte klienta gRPC:

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

Kanál představuje dlouhodobé připojení ke službě gRPC. Při vytvoření kanálu se nakonfiguruje s možnostmi, které se týkají volání služby. Například `HttpClient` použít k volání, maximální velikost zprávy Send a Receive a protokolování lze zadat v `GrpcChannelOptions` a použít u `GrpcChannel.ForAddress`. Úplný seznam možností najdete v tématu [Možnosti konfigurace klienta](xref:grpc/configuration#configure-client-options).

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

Výkon a využití kanálu a klienta:

* Vytvoření kanálu může být náročná operace. Použití kanálu pro volání gRPC poskytuje výhody týkající se výkonu.
* klienti gRPC se vytvářejí pomocí kanálů. gRPC klienti jsou prosté objekty a nemusíte je ukládat do mezipaměti ani je znovu používat.
* Z kanálu, včetně různých typů klientů, lze vytvořit více klientů gRPC.
* Kanál a klienti vytvořené z kanálu můžou být bezpečně využívány více vlákny.
* Klienti vytvoření z kanálu můžou provádět víc souběžných volání.

`GrpcChannel.ForAddress` není jedinou možností pro vytvoření klienta gRPC. Pokud voláte služby gRPC Services z aplikace ASP.NET Core, zvažte [integraci klientské továrny gRPC](xref:grpc/clientfactory). gRPC Integration with `HttpClientFactory` nabízí centralizovanou alternativu k vytváření klientů gRPC.

> [!NOTE]
> Pro [volání nezabezpečených služeb gRPC s klientem .NET](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)je vyžadována další konfigurace.

> [!NOTE]
> Volání gRPC přes HTTP/2 s `Grpc.Net.Client` v současnosti není v Xamarin podporováno. Pracujeme na vylepšení podpory protokolu HTTP/2 v budoucí verzi Xamarin. [Grpc. Core](https://www.nuget.org/packages/Grpc.Core) a [Grpc-web](xref:grpc/browser) představují životaschopné alternativy, které dnes fungují.

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

Každá unární metoda služby v *\*.* důvod bude mít za následek dvou metod .NET pro konkrétní typ klienta gRPC pro volání metody: asynchronní metodu a metodu blokování. Například na `GreeterClient` existují dva způsoby volání `SayHello`:

* `GreeterClient.SayHelloAsync` – volání služby `Greeter.SayHello` asynchronně. Může být očekáváno.
* `GreeterClient.SayHello` – zavolá `Greeter.SayHello` službu a zablokuje až do dokončení. Nepoužívejte v asynchronním kódu.

### <a name="server-streaming-call"></a>Volání streamování serveru

Volání streamování serveru začíná klientem, který odesílá zprávu požadavku. `ResponseStream.MoveNext()` čte zprávy streamované ze služby. Volání streamování serveru je dokončeno, když `ResponseStream.MoveNext()` vrátí `false`.

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

Pokud používáte C# 8 nebo novější, je možné použít syntaxi `await foreach` ke čtení zpráv. Metoda rozšíření `IAsyncStreamReader<T>.ReadAllAsync()` načte všechny zprávy z datového proudu odpovědí:

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

Volání streamování klienta se spustí *bez* odeslání zprávy klientem. Klient se může rozhodnout odeslat zprávy pomocí `RequestStream.WriteAsync`. Jakmile klient dokončí odesílání zpráv `RequestStream.CompleteAsync` by měla být volána pro upozornění služby. Volání je dokončeno, když služba vrátí zprávu odpovědi.

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

Obousměrné volání streamování se spustí *bez* odeslání zprávy klientem. Klient se může rozhodnout odeslat zprávy pomocí `RequestStream.WriteAsync`. Zprávy, které jsou streamované ze služby, jsou dostupné `ResponseStream.MoveNext()` nebo `ResponseStream.ReadAllAsync()`. Obousměrné volání streamování je dokončeno, když `ResponseStream` nemá žádné další zprávy.

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
