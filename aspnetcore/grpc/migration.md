---
title: Migrace služeb gRPC z C jádra ASP.NET Core
author: juntaoluo
description: Zjistěte, jak přesunout existující aplikaci na základě gRPC C jádra pro spuštění nad rámec zásobníku ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/migration
ms.openlocfilehash: 47d74edd821124f0c8390d704ca7931b7eb6c4cd
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901674"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="ecfea-103">Migrace služeb gRPC z C jádra ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ecfea-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="ecfea-104">Podle [Luo Jan](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="ecfea-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="ecfea-105">Kvůli implementaci základní zásobníku, některé funkce fungovat stejně jako mezi [C-podle počtu jader gRPC](https://grpc.io/blog/grpc-stacks) aplikací a aplikací pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ecfea-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="ecfea-106">Tento dokument popisuje klíčové rozdíly pro migraci mezi dvěma zásobníků.</span><span class="sxs-lookup"><span data-stu-id="ecfea-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="ecfea-107">Doba platnosti implementace gRPC služby</span><span class="sxs-lookup"><span data-stu-id="ecfea-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="ecfea-108">V zásobníku ASP.NET Core gRPC services ve výchozím nastavení, se vytvoří s [s vymezeným oborem životnost](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="ecfea-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="ecfea-109">Naproti tomu gRPC C core ve výchozím nastavení vytvoří vazbu ke službě s [singleton životnost](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="ecfea-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="ecfea-110">S vymezeným oborem životnost umožňuje implementaci služby k vyřešení dalších služeb pomocí s vymezeným oborem platnosti.</span><span class="sxs-lookup"><span data-stu-id="ecfea-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="ecfea-111">Například můžete také vyřešit s vymezeným oborem životnost `DBContext` z kontejnerů DI prostřednictvím konstruktoru vkládání.</span><span class="sxs-lookup"><span data-stu-id="ecfea-111">For example, a scoped lifetime can also resolve `DBContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="ecfea-112">Pomocí vymezených životnost:</span><span class="sxs-lookup"><span data-stu-id="ecfea-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="ecfea-113">Nová instance implementace služby je vytvořen pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="ecfea-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="ecfea-114">Není možné sdílení stavu mezi žádostí prostřednictvím členy instance na typu implementace.</span><span class="sxs-lookup"><span data-stu-id="ecfea-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="ecfea-115">Očekává se, k ukládání sdílené stavy do služby jednotlivý prvek v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="ecfea-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="ecfea-116">Uložené sdílené stavy jsou vyřešeny v konstruktoru gRPC implementaci služby.</span><span class="sxs-lookup"><span data-stu-id="ecfea-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="ecfea-117">Další informace o životnosti služby najdete v tématu <xref:fundamentals/dependency-injection#service-lifetimes>.</span><span class="sxs-lookup"><span data-stu-id="ecfea-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="ecfea-118">Přidání služby typu singleton</span><span class="sxs-lookup"><span data-stu-id="ecfea-118">Add a singleton service</span></span>

<span data-ttu-id="ecfea-119">Pro usnadnění přechodu od implementace C core gRPC k ASP.NET Core, je možné změnit životnost služby implementaci služby z oboru na jednotlivý prvek.</span><span class="sxs-lookup"><span data-stu-id="ecfea-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="ecfea-120">To zahrnuje přidání do kontejnerů DI instance implementace služby:</span><span class="sxs-lookup"><span data-stu-id="ecfea-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="ecfea-121">Implementace služby s životností jednotlivý prvek je však již nemůže přeložit vymezené služby prostřednictvím konstruktoru vkládání.</span><span class="sxs-lookup"><span data-stu-id="ecfea-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="ecfea-122">Nakonfigurujte možnosti služby gRPC</span><span class="sxs-lookup"><span data-stu-id="ecfea-122">Configure gRPC services options</span></span>

<span data-ttu-id="ecfea-123">V aplikacích jazyka C-podle počtu jader, nastavení, jako `grpc.max_receive_message_length` a `grpc.max_send_message_length` jsou nakonfigurovány s `ChannelOption` při [vytváření instance serveru](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span><span class="sxs-lookup"><span data-stu-id="ecfea-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="ecfea-124">GRPC v ASP.NET Core, poskytuje konfiguraci prostřednictvím `GrpcServiceOptions` typu.</span><span class="sxs-lookup"><span data-stu-id="ecfea-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="ecfea-125">Například gRPC služby maximální velikost příchozí zprávy můžete nakonfigurovat přes `AddGrpc`:</span><span class="sxs-lookup"><span data-stu-id="ecfea-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 16384; // 16 MB
    });
}
```

<span data-ttu-id="ecfea-126">Další informace o konfiguraci najdete v tématu <xref:grpc/configuration>.</span><span class="sxs-lookup"><span data-stu-id="ecfea-126">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="ecfea-127">Protokolování</span><span class="sxs-lookup"><span data-stu-id="ecfea-127">Logging</span></span>

<span data-ttu-id="ecfea-128">Využívají aplikace s C-podle počtu jader `GrpcEnvironment` k [konfiguraci protokolovacího nástroje](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) pro účely ladění.</span><span class="sxs-lookup"><span data-stu-id="ecfea-128">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="ecfea-129">Tuto funkci prostřednictvím poskytuje zásobníku ASP.NET Core [protokolování rozhraní API](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="ecfea-129">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="ecfea-130">Protokolovací nástroj lze například přidat ke službě gRPC prostřednictvím konstruktoru vkládání:</span><span class="sxs-lookup"><span data-stu-id="ecfea-130">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="ecfea-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="ecfea-131">HTTPS</span></span>

<span data-ttu-id="ecfea-132">Aplikace s C-podle počtu jader konfigurace HTTPS prostřednictvím [Server.Ports vlastnost](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="ecfea-132">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="ecfea-133">Podobný koncept se používá ke konfiguraci serverů v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ecfea-133">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="ecfea-134">Například používá Kestrel [konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration) pro tuto funkci.</span><span class="sxs-lookup"><span data-stu-id="ecfea-134">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="interceptors-and-middleware"></a><span data-ttu-id="ecfea-135">Sběrače a Middleware</span><span class="sxs-lookup"><span data-stu-id="ecfea-135">Interceptors and Middleware</span></span>

<span data-ttu-id="ecfea-136">ASP.NET Core [middleware](xref:fundamentals/middleware/index) nabízí podobné funkce, které jsou ve srovnání s sběrače v aplikacích jazyka C-podle počtu jader gRPC.</span><span class="sxs-lookup"><span data-stu-id="ecfea-136">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="ecfea-137">Middleware a sběrače jsou koncepčně stejné jako se používá k vytvoření kanálu, který zpracovává požadavek gRPC.</span><span class="sxs-lookup"><span data-stu-id="ecfea-137">Middleware and interceptors are conceptually the same as both are used to construct a pipeline that handles a gRPC request.</span></span> <span data-ttu-id="ecfea-138">Oba umožňují práci prováděnou před nebo po další komponenta v kanálu.</span><span class="sxs-lookup"><span data-stu-id="ecfea-138">They both allow work to be performed before or after the next component in the pipeline.</span></span> <span data-ttu-id="ecfea-139">Však middleware ASP.NET Core funguje v základní zprávy HTTP/2, zatímco sběrače provádět gRPC vrstva abstrakce pomocí [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="ecfea-139">However, ASP.NET Core middleware operates on the underlying HTTP/2 messages, while interceptors operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ecfea-140">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ecfea-140">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
