---
title: Úvod do gRPC v .NET Core
author: juntaoluo
description: Přečtěte si o službách gRPC Services pomocí serveru Kestrel a ASP.NET Core stacku.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
uid: grpc/index
ms.openlocfilehash: d97eea1da28424680a3cfa38102637b1e20ff661
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77446954"
---
# <a name="introduction-to-grpc-on-net-core"></a>Úvod do gRPC v .NET Core

Od [Jan Luo](https://github.com/juntaoluo) a [James Newton – král](https://twitter.com/jamesnk)

[gRPC](https://grpc.io/docs/guides/) je jazyk nezávislá, vysoce výkonné rozhraní vzdáleného volání procedur (RPC).

Hlavní výhody gRPC jsou:
* Moderní, vysoce výkonné a odlehčené rozhraní RPC.
* Vývoj rozhraní API kontraktu, ve výchozím nastavení používá vyrovnávací paměti protokolů, což umožňuje implementaci nezávislá jazyka.
* Nástroj, který je k dispozici pro mnoho jazyků pro generování serverů a klientů se silným typem.
* Podporuje volání klientů, serverů a obousměrného streamování.
* Omezené využití sítě pomocí binární serializace Protobuf

Díky těmto výhodám je gRPC ideální pro:
* Odlehčené mikroslužby, ve kterých je efektivita nejdůležitější.
* Polyglot systémy, ve kterých se pro vývoj vyžaduje více jazyků.
* Služby v reálném čase Point-to-Point, které potřebují zpracovávat žádosti o streamování nebo odpovědi.

## <a name="c-tooling-support-for-proto-files"></a>C#Podpora nástrojů pro soubory.

gRPC využívá přístup ke vývoji rozhraní API, který je prvním kontraktem. Služby a zprávy jsou definované v *\*. proto* soubory:

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

Typy .NET pro služby, klienti a zprávy jsou automaticky vygenerováni zahrnutím *\*.* soubory v projektu:

* Přidejte odkaz na balíček do balíčku [Grpc. Tools](https://www.nuget.org/packages/Grpc.Tools/) .
* Přidejte *\*...* soubory do skupiny položek `<Protobuf>`.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

Další informace o podpoře nástrojů gRPC naleznete v tématu <xref:grpc/basics>.

## <a name="grpc-services-on-aspnet-core"></a>gRPC Services na ASP.NET Core

služby gRPC lze hostovat na ASP.NET Core. Služby mají úplnou integraci s oblíbenými ASP.NET Core funkcemi, jako je protokolování, vkládání závislostí (DI), ověřování a autorizace.

Šablona projektu služby gRPC poskytuje počáteční službu:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

`GreeterService` dědí z typu `GreeterBase`, který je vygenerován ze služby `Greeter` v souboru *\*.* . Služba je zpřístupněna klientům v *Startup.cs*:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Další informace o službách gRPC Services v ASP.NET Core najdete v tématu <xref:grpc/aspnetcore>.

## <a name="call-grpc-services-with-a-net-client"></a>Volání služeb gRPC Services pomocí klienta .NET

gRPC klienti jsou konkrétní typy klientů, které jsou [generovány z *\*.* soubory](xref:grpc/basics#generated-c-assets). Konkrétní klient gRPC má metody, které se převádějí do služby gRPC v souboru *\*.* .

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

Klient gRPC se vytvoří pomocí kanálu, který představuje dlouhodobé připojení ke službě gRPC. Kanál se dá vytvořit pomocí `GrpcChannel.ForAddress`.

Další informace o vytváření klientů a volání různých metod služby najdete v tématu <xref:grpc/client>.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
