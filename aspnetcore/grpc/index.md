---
title: Úvod do gRPC na .NET Core
author: juntaoluo
description: Seznamte se se službami gRPC se serverem Kestrel a zásobníkem ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
uid: grpc/index
ms.openlocfilehash: d97eea1da28424680a3cfa38102637b1e20ff661
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667312"
---
# <a name="introduction-to-grpc-on-net-core"></a>Úvod do gRPC na .NET Core

[John Luo](https://github.com/juntaoluo) a [James Newton-King](https://twitter.com/jamesnk)

[gRPC](https://grpc.io/docs/guides/) je jazyk agnostik, vysoce výkonné vzdálené procedury volání (RPC) framework.

Hlavní výhody gRPC jsou:
* Moderní, vysoce výkonný a lehký rám RPC.
* Vývoj rozhraní API první smlouvy, pomocí vyrovnávacích pamětí protokolu ve výchozím nastavení, umožňující implementace agnostik jazyka.
* Nástroje dostupné pro mnoho jazyků pro generování serverů a klientů silného typu.
* Podporuje volání klienta, serveru a obousměrného streamování.
* Snížené využití sítě díky binární serializaci Protobuf.

Díky těmto výhodám je gRPC ideální pro:
* Zjednodušené mikroslužby, kde je důležitá efektivita.
* Polyglot systémy, kde více jazyků jsou potřebné pro vývoj.
* Služby point-to-point v reálném čase, které potřebují zpracovávat požadavky nebo odpovědi streamování.

## <a name="c-tooling-support-for-proto-files"></a>Podpora nástrojů jazyka C# pro soubory .proto

gRPC používá přístup k vývoji rozhraní API založený na smlouvě. Služby a zprávy * \** jsou definovány v souborech .proto:

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

Typy .NET pro služby, klienty a * \** zprávy jsou automaticky generovány zahrnutím souborů .proto do projektu:

* Přidejte odkaz na balíček do balíčku [Grpc.Tools.](https://www.nuget.org/packages/Grpc.Tools/)
* Přidejte * \** soubory .proto do skupiny `<Protobuf>` položek.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

Další informace o podpoře nástrojů gRPC naleznete v tématu <xref:grpc/basics>.

## <a name="grpc-services-on-aspnet-core"></a>gRPC služby na ASP.NET Core

služby gRPC mohou být hostovány na ASP.NET Core. Služby mají plnou integraci s oblíbenými funkcemi ASP.NET Core, jako je protokolování, vkládání závislostí (DI), ověřování a autorizace.

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

`GreeterService`dědí `GreeterBase` z typu, který je `Greeter` generován ze služby v souboru * \*.proto.* Služba je klientům zpřístupněna v *Startup.cs*:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Další informace o službách gRPC <xref:grpc/aspnetcore>na ASP.NET Core najdete v tématu .

## <a name="call-grpc-services-with-a-net-client"></a>Volání služeb gRPC s klientem .NET

gRPC klienti jsou konkrétní typy klientů, které jsou [generovány ze * \*souborů .proto* ](xref:grpc/basics#generated-c-assets). Konkrétní klient gRPC má metody, které se překládají do služby gRPC v souboru * \*.proto.*

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

Klient gRPC je vytvořen pomocí kanálu, který představuje dlouhodobé připojení ke službě gRPC. Kanál lze vytvořit `GrpcChannel.ForAddress`pomocí aplikace .

Další informace o vytváření klientů a volání <xref:grpc/client>různých metod služby naleznete v tématu .

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
