---
title: integrace klientské továrny gRPC v .NET Core
author: jamesnk
description: Naučte se vytvářet klienty gRPC pomocí klientské továrny.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: dfdcb8d73017c0c1ccb4cf2aaffdbe7c49030179
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633731"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="cc264-103">integrace klientské továrny gRPC v .NET Core</span><span class="sxs-lookup"><span data-stu-id="cc264-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="cc264-104">integrace gRPC s `HttpClientFactory` nabízí centralizovaný způsob vytváření klientů gRPC.</span><span class="sxs-lookup"><span data-stu-id="cc264-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="cc264-105">Dá se použít jako alternativa ke [konfiguraci samostatných klientských gRPC instancí](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="cc264-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="cc264-106">Integration Factory je k dispozici v balíčku NuGet [Grpc .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) .</span><span class="sxs-lookup"><span data-stu-id="cc264-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="cc264-107">Továrna nabízí následující výhody:</span><span class="sxs-lookup"><span data-stu-id="cc264-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="cc264-108">Poskytuje centrální umístění pro konfiguraci logických instancí klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="cc264-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="cc264-109">Spravuje životnost základního `HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="cc264-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="cc264-110">Automatické šíření konečného termínu a zrušení ve službě ASP.NET Core gRPC</span><span class="sxs-lookup"><span data-stu-id="cc264-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="cc264-111">Registrace klientů gRPC</span><span class="sxs-lookup"><span data-stu-id="cc264-111">Register gRPC clients</span></span>

<span data-ttu-id="cc264-112">Pokud chcete zaregistrovat klienta gRPC, `AddGrpcClient` můžete použít obecnou metodu rozšíření v rámci `Startup.ConfigureServices` , zadáním klientské třídy gRPC typu a adresy služby:</span><span class="sxs-lookup"><span data-stu-id="cc264-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="cc264-113">Typ klienta gRPC je zaregistrován jako přechodný s vkládáním závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="cc264-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="cc264-114">Klienta se teď dá vložit a spotřebovat přímo v typech vytvořených pomocí DI.</span><span class="sxs-lookup"><span data-stu-id="cc264-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="cc264-115">ASP.NET Core řadiče MVC, SignalR centra a gRPC služby jsou místa, kde můžou být automaticky vloženi klienti gRPC:</span><span class="sxs-lookup"><span data-stu-id="cc264-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

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

## <a name="configure-httpclient"></a><span data-ttu-id="cc264-116">Konfigurace HttpClient</span><span class="sxs-lookup"><span data-stu-id="cc264-116">Configure HttpClient</span></span>

<span data-ttu-id="cc264-117">`HttpClientFactory` vytvoří klienta, který `HttpClient` používá klient gRPC.</span><span class="sxs-lookup"><span data-stu-id="cc264-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="cc264-118">Standardní `HttpClientFactory` metody lze použít pro přidání middlewaru pro odchozí žádosti nebo pro konfiguraci podkladu `HttpClientHandler` `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="cc264-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

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

<span data-ttu-id="cc264-119">Další informace najdete v tématu [Vytváření požadavků HTTP pomocí IHttpClientFactory](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="cc264-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="cc264-120">Konfigurace kanálů a zachycení</span><span class="sxs-lookup"><span data-stu-id="cc264-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="cc264-121">k dispozici jsou metody specifické pro gRPC:</span><span class="sxs-lookup"><span data-stu-id="cc264-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="cc264-122">Konfigurace základního kanálu klienta gRPC.</span><span class="sxs-lookup"><span data-stu-id="cc264-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="cc264-123">Přidejte `Interceptor` instance, které klient použije při volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="cc264-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

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

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="cc264-124">Termín a zrušení šíření</span><span class="sxs-lookup"><span data-stu-id="cc264-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="cc264-125">gRPC klienti, které vytvořila továrna ve službě gRPC, se dají nakonfigurovat `EnableCallContextPropagation()` tak, aby automaticky rozšířily konečný termín a token zrušení do podřízených volání.</span><span class="sxs-lookup"><span data-stu-id="cc264-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="cc264-126">`EnableCallContextPropagation()`Metoda rozšíření je k dispozici v balíčku NuGet [Grpc. AspNetCore. Server. ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) .</span><span class="sxs-lookup"><span data-stu-id="cc264-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="cc264-127">Šíření kontextu volání funguje tak, že si přečtete konečný termín a token zrušení z aktuálního kontextu požadavku gRPC a automaticky je šíříte do odchozích volání prováděných klientem gRPC.</span><span class="sxs-lookup"><span data-stu-id="cc264-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="cc264-128">Šíření kontextu volání je vynikající způsob, jak zajistit, aby komplexní a vnořené gRPC scénáře vždy rozšířily konečný termín a zrušení.</span><span class="sxs-lookup"><span data-stu-id="cc264-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="cc264-129">Ve výchozím nastavení `EnableCallContextPropagation` vyvolá chybu, pokud se klient používá mimo kontext volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="cc264-129">By default, `EnableCallContextPropagation` raises an error if the client is used outside the context of a gRPC call.</span></span> <span data-ttu-id="cc264-130">Tato chyba je navržena tak, aby vás upozornila, že není k dispozici kontext volání pro rozšíření.</span><span class="sxs-lookup"><span data-stu-id="cc264-130">The error is designed to alert you that there isn't a call context to propagate.</span></span> <span data-ttu-id="cc264-131">Pokud chcete použít klienta mimo kontext volání, potlačit chybu, pokud je klient konfigurován pomocí `SuppressContextNotFoundErrors` :</span><span class="sxs-lookup"><span data-stu-id="cc264-131">If you want to use the client outside of a call context, suppress the error when the client is configured with `SuppressContextNotFoundErrors`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

<span data-ttu-id="cc264-132">Další informace o termínech a zrušení vzdáleného volání procedur najdete v tématu [životní cyklus RPC](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="cc264-132">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc264-133">Další materiály</span><span class="sxs-lookup"><span data-stu-id="cc264-133">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
