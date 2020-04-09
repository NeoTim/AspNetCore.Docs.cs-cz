---
title: gRPC integrace továrny klienta v rozhraní .NET Core
author: jamesnk
description: Naučte se vytvářet gRPC klienty pomocí klientské továrny.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: 3042bb61367f8b9a9f3142217ad329270ab2cca5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667165"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>gRPC integrace továrny klienta v rozhraní .NET Core

gRPC integrace `HttpClientFactory` s nabízí centralizovaný způsob, jak vytvořit gRPC klienty. Lze jej použít jako alternativu ke [konfiguraci samostatných instancí klienta gRPC](xref:grpc/client). Tovární integrace je k dispozici v balíčku [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet.

Továrna nabízí následující výhody:

* Poskytuje centrální umístění pro konfiguraci logických instancí klientů gRPC.
* Spravuje životnost podkladového`HttpClientMessageHandler`
* Automatické šíření termínu a zrušení ve službě ASP.NET Core gRPC

## <a name="register-grpc-clients"></a>Registrace klientů gRPC

Chcete-li zaregistrovat klienta `AddGrpcClient` gRPC, lze `Startup.ConfigureServices`použít metodu obecného rozšíření v rámci aplikace , která specifikuje třídu klienta typu gRPC a adresu služby:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

Typ klienta gRPC je registrován jako přechodný s vkládání závislostí (DI). Klient nyní může být vložen a spotřebován přímo v typech vytvořených DI. ASP.NET Core MVC SignalR řadiče, rozbočovače a gRPC služby jsou místa, kde gRPC klienti mohou být automaticky injektovány:

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

## <a name="configure-httpclient"></a>Konfigurace klienta Http

`HttpClientFactory`vytvoří `HttpClient` používá gRPC klienta. Standardní `HttpClientFactory` metody lze použít k přidání middleware odchozí `HttpClientHandler` požadavek `HttpClient`nebo ke konfiguraci základní :

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

Další informace naleznete v [tématu Vytváření požadavků HTTP pomocí aplikace IHttpClientFactory](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Konfigurace kanálů a stíhačů

metody specifické pro gRPC jsou k dispozici pro:

* Nakonfigurujte základní kanál klienta gRPC.
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

## <a name="deadline-and-cancellation-propagation"></a>Termín a šíření zrušení

gRPC klienti vytvořená továrnou ve službě gRPC mohou být nakonfigurováni tak, `EnableCallContextPropagation()` aby automaticky propagovali konečný termín a token zrušení pro podřízená volání. Metoda `EnableCallContextPropagation()` rozšíření je k dispozici v balíčku [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet.

Šíření kontextu volání funguje tak, že si přečtete konečný termín a token zrušení z aktuálního kontextu požadavku gRPC a automaticky je rozšíříte na odchozí volání provedená klientem gRPC. Šíření kontextu volání je vynikající způsob, jak zajistit, aby složité, vnořené gRPC scénáře vždy šířit konečný termín a zrušení.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Další informace o termínech a zrušení vzdáleného volání procedur naleznete [v tématu RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Další zdroje

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
