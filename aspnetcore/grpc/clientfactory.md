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
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="b982c-103">gRPC integrace továrny klienta v rozhraní .NET Core</span><span class="sxs-lookup"><span data-stu-id="b982c-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="b982c-104">gRPC integrace `HttpClientFactory` s nabízí centralizovaný způsob, jak vytvořit gRPC klienty.</span><span class="sxs-lookup"><span data-stu-id="b982c-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="b982c-105">Lze jej použít jako alternativu ke [konfiguraci samostatných instancí klienta gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="b982c-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="b982c-106">Tovární integrace je k dispozici v balíčku [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet.</span><span class="sxs-lookup"><span data-stu-id="b982c-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="b982c-107">Továrna nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="b982c-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="b982c-108">Poskytuje centrální umístění pro konfiguraci logických instancí klientů gRPC.</span><span class="sxs-lookup"><span data-stu-id="b982c-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="b982c-109">Spravuje životnost podkladového`HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="b982c-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="b982c-110">Automatické šíření termínu a zrušení ve službě ASP.NET Core gRPC</span><span class="sxs-lookup"><span data-stu-id="b982c-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="b982c-111">Registrace klientů gRPC</span><span class="sxs-lookup"><span data-stu-id="b982c-111">Register gRPC clients</span></span>

<span data-ttu-id="b982c-112">Chcete-li zaregistrovat klienta `AddGrpcClient` gRPC, lze `Startup.ConfigureServices`použít metodu obecného rozšíření v rámci aplikace , která specifikuje třídu klienta typu gRPC a adresu služby:</span><span class="sxs-lookup"><span data-stu-id="b982c-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="b982c-113">Typ klienta gRPC je registrován jako přechodný s vkládání závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="b982c-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="b982c-114">Klient nyní může být vložen a spotřebován přímo v typech vytvořených DI.</span><span class="sxs-lookup"><span data-stu-id="b982c-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="b982c-115">ASP.NET Core MVC SignalR řadiče, rozbočovače a gRPC služby jsou místa, kde gRPC klienti mohou být automaticky injektovány:</span><span class="sxs-lookup"><span data-stu-id="b982c-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

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

## <a name="configure-httpclient"></a><span data-ttu-id="b982c-116">Konfigurace klienta Http</span><span class="sxs-lookup"><span data-stu-id="b982c-116">Configure HttpClient</span></span>

<span data-ttu-id="b982c-117">`HttpClientFactory`vytvoří `HttpClient` používá gRPC klienta.</span><span class="sxs-lookup"><span data-stu-id="b982c-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="b982c-118">Standardní `HttpClientFactory` metody lze použít k přidání middleware odchozí `HttpClientHandler` požadavek `HttpClient`nebo ke konfiguraci základní :</span><span class="sxs-lookup"><span data-stu-id="b982c-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

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

<span data-ttu-id="b982c-119">Další informace naleznete v [tématu Vytváření požadavků HTTP pomocí aplikace IHttpClientFactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="b982c-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="b982c-120">Konfigurace kanálů a stíhačů</span><span class="sxs-lookup"><span data-stu-id="b982c-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="b982c-121">metody specifické pro gRPC jsou k dispozici pro:</span><span class="sxs-lookup"><span data-stu-id="b982c-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="b982c-122">Nakonfigurujte základní kanál klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="b982c-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="b982c-123">Přidejte `Interceptor` instance, které klient použije při volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="b982c-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

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

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="b982c-124">Termín a šíření zrušení</span><span class="sxs-lookup"><span data-stu-id="b982c-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="b982c-125">gRPC klienti vytvořená továrnou ve službě gRPC mohou být nakonfigurováni tak, `EnableCallContextPropagation()` aby automaticky propagovali konečný termín a token zrušení pro podřízená volání.</span><span class="sxs-lookup"><span data-stu-id="b982c-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="b982c-126">Metoda `EnableCallContextPropagation()` rozšíření je k dispozici v balíčku [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet.</span><span class="sxs-lookup"><span data-stu-id="b982c-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="b982c-127">Šíření kontextu volání funguje tak, že si přečtete konečný termín a token zrušení z aktuálního kontextu požadavku gRPC a automaticky je rozšíříte na odchozí volání provedená klientem gRPC.</span><span class="sxs-lookup"><span data-stu-id="b982c-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="b982c-128">Šíření kontextu volání je vynikající způsob, jak zajistit, aby složité, vnořené gRPC scénáře vždy šířit konečný termín a zrušení.</span><span class="sxs-lookup"><span data-stu-id="b982c-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="b982c-129">Další informace o termínech a zrušení vzdáleného volání procedur naleznete [v tématu RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="b982c-129">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b982c-130">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b982c-130">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
