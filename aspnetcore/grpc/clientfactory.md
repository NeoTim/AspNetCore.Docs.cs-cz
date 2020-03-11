---
title: integrace klientské továrny gRPC v .NET Core
author: jamesnk
description: Naučte se vytvářet klienty gRPC pomocí klientské továrny.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: 3042bb61367f8b9a9f3142217ad329270ab2cca5
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78667165"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>integrace klientské továrny gRPC v .NET Core

gRPC Integration with `HttpClientFactory` nabízí centralizovaný způsob vytváření klientů gRPC. Dá se použít jako alternativa ke [konfiguraci samostatných klientských gRPC instancí](xref:grpc/client). Integration Factory je k dispozici v balíčku NuGet [Grpc .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .

Továrna nabízí následující výhody:

* Poskytuje centrální umístění pro konfiguraci logických instancí klienta gRPC.
* Spravuje dobu života podkladové `HttpClientMessageHandler`.
* Automatické šíření konečného termínu a zrušení ve službě ASP.NET Core gRPC

## <a name="register-grpc-clients"></a>Registrace klientů gRPC

Chcete-li zaregistrovat klienta gRPC, lze použít metodu rozšíření Generic `AddGrpcClient` v rámci `Startup.ConfigureServices`, přičemž zadáte třídu klienta typu gRPC a adresu služby:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

Typ klienta gRPC je zaregistrován jako přechodný s vkládáním závislostí (DI). Klienta se teď dá vložit a spotřebovat přímo v typech vytvořených pomocí DI. ASP.NET Core řadiče MVC, centra SignalR a služby gRPC jsou místa, kde se můžou automaticky vkládat klienti gRPC:

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

`HttpClientFactory` vytvoří `HttpClient` používané klientem gRPC. Metody Standard `HttpClientFactory` lze použít k přidání middleware pro odchozí žádosti nebo ke konfiguraci podkladových `HttpClientHandler` `HttpClient`:

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

gRPC klienti, které vytvořila továrna ve službě gRPC, se dají nakonfigurovat s `EnableCallContextPropagation()`, aby automaticky rozšířily konečný termín a token zrušení do podřízených volání. Metoda rozšíření `EnableCallContextPropagation()` je k dispozici v balíčku NuGet [Grpc. AspNetCore. Server. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) .

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

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
