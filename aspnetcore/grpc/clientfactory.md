---
title: integrace klientské továrny gRPC v .NET Core
author: jamesnk
description: Naučte se vytvářet klienty gRPC pomocí klientské továrny.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: 42b786b9a4d9b422ccf92d7a329979894a35b275
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774714"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>integrace klientské továrny gRPC v .NET Core

integrace gRPC s `HttpClientFactory` nabízí centralizovaný způsob vytváření klientů gRPC. Dá se použít jako alternativa ke [konfiguraci samostatných klientských gRPC instancí](xref:grpc/client). Integration Factory je k dispozici v balíčku NuGet [Grpc .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .

Továrna nabízí následující výhody:

* Poskytuje centrální umístění pro konfiguraci logických instancí klienta gRPC.
* Spravuje životnost základního`HttpClientMessageHandler`
* Automatické šíření konečného termínu a zrušení ve službě ASP.NET Core gRPC

## <a name="register-grpc-clients"></a>Registrace klientů gRPC

Pokud chcete zaregistrovat klienta gRPC, můžete použít `AddGrpcClient` obecnou metodu rozšíření v rámci `Startup.ConfigureServices`, zadáním klientské třídy gRPC typu a adresy služby:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

Typ klienta gRPC je zaregistrován jako přechodný s vkládáním závislostí (DI). Klienta se teď dá vložit a spotřebovat přímo v typech vytvořených pomocí DI. ASP.NET Core řadiče MVC, SignalR centra a gRPC služby jsou místa, kde můžou být automaticky vloženi klienti gRPC:

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a>Konfigurace HttpClient

`HttpClientFactory`vytvoří klienta `HttpClient` , který používá klient gRPC. Standardní `HttpClientFactory` metody lze použít pro přidání middlewaru pro odchozí žádosti nebo pro konfiguraci `HttpClientHandler` podkladu `HttpClient`:

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

Další informace najdete v tématu [Vytváření požadavků HTTP pomocí IHttpClientFactory](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Konfigurace kanálů a zachycení

k dispozici jsou metody specifické pro gRPC:

* Konfigurace základního kanálu klienta gRPC.
* Přidejte `Interceptor` instance, které klient použije při volání gRPC.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a>Termín a zrušení šíření

gRPC klienti, které vytvořila továrna ve službě gRPC, se `EnableCallContextPropagation()` dají nakonfigurovat tak, aby automaticky rozšířily konečný termín a token zrušení do podřízených volání. Metoda `EnableCallContextPropagation()` rozšíření je k dispozici v balíčku NuGet [Grpc. AspNetCore. Server. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) .

Šíření kontextu volání funguje tak, že si přečtete konečný termín a token zrušení z aktuálního kontextu požadavku gRPC a automaticky je šíříte do odchozích volání prováděných klientem gRPC. Šíření kontextu volání je vynikající způsob, jak zajistit, aby komplexní a vnořené gRPC scénáře vždy rozšířily konečný termín a zrušení.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Další informace o termínech a zrušení vzdáleného volání procedur najdete v tématu [životní cyklus RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
