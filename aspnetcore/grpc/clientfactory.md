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
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="1dedb-103">integrace klientské továrny gRPC v .NET Core</span><span class="sxs-lookup"><span data-stu-id="1dedb-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="1dedb-104">gRPC Integration with `HttpClientFactory` nabízí centralizovaný způsob vytváření klientů gRPC.</span><span class="sxs-lookup"><span data-stu-id="1dedb-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="1dedb-105">Dá se použít jako alternativa ke [konfiguraci samostatných klientských gRPC instancí](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="1dedb-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="1dedb-106">Integration Factory je k dispozici v balíčku NuGet [Grpc .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .</span><span class="sxs-lookup"><span data-stu-id="1dedb-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="1dedb-107">Továrna nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="1dedb-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="1dedb-108">Poskytuje centrální umístění pro konfiguraci logických instancí klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="1dedb-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="1dedb-109">Spravuje dobu života podkladové `HttpClientMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="1dedb-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="1dedb-110">Automatické šíření konečného termínu a zrušení ve službě ASP.NET Core gRPC</span><span class="sxs-lookup"><span data-stu-id="1dedb-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="1dedb-111">Registrace klientů gRPC</span><span class="sxs-lookup"><span data-stu-id="1dedb-111">Register gRPC clients</span></span>

<span data-ttu-id="1dedb-112">Chcete-li zaregistrovat klienta gRPC, lze použít metodu rozšíření Generic `AddGrpcClient` v rámci `Startup.ConfigureServices`, přičemž zadáte třídu klienta typu gRPC a adresu služby:</span><span class="sxs-lookup"><span data-stu-id="1dedb-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="1dedb-113">Typ klienta gRPC je zaregistrován jako přechodný s vkládáním závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="1dedb-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="1dedb-114">Klienta se teď dá vložit a spotřebovat přímo v typech vytvořených pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="1dedb-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="1dedb-115">ASP.NET Core řadiče MVC, centra SignalR a služby gRPC jsou místa, kde se můžou automaticky vkládat klienti gRPC:</span><span class="sxs-lookup"><span data-stu-id="1dedb-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

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

## <a name="configure-httpclient"></a><span data-ttu-id="1dedb-116">Konfigurace HttpClient</span><span class="sxs-lookup"><span data-stu-id="1dedb-116">Configure HttpClient</span></span>

<span data-ttu-id="1dedb-117">`HttpClientFactory` vytvoří `HttpClient` používané klientem gRPC.</span><span class="sxs-lookup"><span data-stu-id="1dedb-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="1dedb-118">Metody Standard `HttpClientFactory` lze použít k přidání middleware pro odchozí žádosti nebo ke konfiguraci podkladových `HttpClientHandler` `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="1dedb-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

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

<span data-ttu-id="1dedb-119">Další informace najdete v tématu [Vytváření požadavků HTTP pomocí IHttpClientFactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="1dedb-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="1dedb-120">Konfigurace kanálů a zachycení</span><span class="sxs-lookup"><span data-stu-id="1dedb-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="1dedb-121">k dispozici jsou metody specifické pro gRPC:</span><span class="sxs-lookup"><span data-stu-id="1dedb-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="1dedb-122">Konfigurace základního kanálu klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="1dedb-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="1dedb-123">Přidejte `Interceptor` instance, které klient použije při volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="1dedb-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

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

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="1dedb-124">Termín a zrušení šíření</span><span class="sxs-lookup"><span data-stu-id="1dedb-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="1dedb-125">gRPC klienti, které vytvořila továrna ve službě gRPC, se dají nakonfigurovat s `EnableCallContextPropagation()`, aby automaticky rozšířily konečný termín a token zrušení do podřízených volání.</span><span class="sxs-lookup"><span data-stu-id="1dedb-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="1dedb-126">Metoda rozšíření `EnableCallContextPropagation()` je k dispozici v balíčku NuGet [Grpc. AspNetCore. Server. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) .</span><span class="sxs-lookup"><span data-stu-id="1dedb-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="1dedb-127">Šíření kontextu volání funguje tak, že si přečtete konečný termín a token zrušení z aktuálního kontextu požadavku gRPC a automaticky je šíříte do odchozích volání prováděných klientem gRPC.</span><span class="sxs-lookup"><span data-stu-id="1dedb-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="1dedb-128">Šíření kontextu volání je vynikající způsob, jak zajistit, aby komplexní a vnořené gRPC scénáře vždy rozšířily konečný termín a zrušení.</span><span class="sxs-lookup"><span data-stu-id="1dedb-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="1dedb-129">Další informace o termínech a zrušení vzdáleného volání procedur najdete v tématu [životní cyklus RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="1dedb-129">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1dedb-130">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1dedb-130">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
