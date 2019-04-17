---
title: Migrace služeb gRPC z C jádra ASP.NET Core
author: juntaoluo
description: Zjistěte, jak přesunout existující aplikaci na základě gRPC C jádra pro spuštění nad rámec zásobníku ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/migration
ms.openlocfilehash: 4d489b5aecf2e15fbbe3ac472b991a4365cd47c1
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59672616"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="50c10-103">Migrace služeb gRPC z C jádra ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="50c10-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="50c10-104">Podle [Luo Jan](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="50c10-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="50c10-105">Kvůli implementaci základní zásobníku, některé funkce fungovat stejně jako mezi [C-podle počtu jader gRPC](https://grpc.io/blog/grpc-stacks) aplikací a aplikací pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="50c10-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="50c10-106">Tento dokument popisuje klíčové rozdíly pro migraci mezi dvěma zásobníků.</span><span class="sxs-lookup"><span data-stu-id="50c10-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="50c10-107">Doba platnosti implementace gRPC služby</span><span class="sxs-lookup"><span data-stu-id="50c10-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="50c10-108">V zásobníku ASP.NET Core gRPC services ve výchozím nastavení, se vytvoří s [s vymezeným oborem životnost](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="50c10-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="50c10-109">Naproti tomu gRPC C core ve výchozím nastavení vytvoří vazbu ke službě s [singleton životnost](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="50c10-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="50c10-110">S vymezeným oborem životnost umožňuje implementaci služby k vyřešení dalších služeb pomocí s vymezeným oborem platnosti.</span><span class="sxs-lookup"><span data-stu-id="50c10-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="50c10-111">Například můžete také vyřešit s vymezeným oborem životnost `DBContext` z kontejnerů DI prostřednictvím konstruktoru vkládání.</span><span class="sxs-lookup"><span data-stu-id="50c10-111">For example, a scoped lifetime can also resolve `DBContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="50c10-112">Pomocí vymezených životnost:</span><span class="sxs-lookup"><span data-stu-id="50c10-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="50c10-113">Nová instance implementace služby je vytvořen pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="50c10-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="50c10-114">Není možné sdílení stavu mezi žádostí prostřednictvím členy instance na typu implementace.</span><span class="sxs-lookup"><span data-stu-id="50c10-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="50c10-115">Očekává se, k ukládání sdílené stavy do služby jednotlivý prvek v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="50c10-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="50c10-116">Uložené sdílené stavy jsou vyřešeny v konstruktoru gRPC implementaci služby.</span><span class="sxs-lookup"><span data-stu-id="50c10-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="50c10-117">Další informace o životnosti služby najdete v tématu <xref:fundamentals/dependency-injection#service-lifetimes>.</span><span class="sxs-lookup"><span data-stu-id="50c10-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="50c10-118">Přidání služby typu singleton</span><span class="sxs-lookup"><span data-stu-id="50c10-118">Add a singleton service</span></span>

<span data-ttu-id="50c10-119">Pro usnadnění přechodu od implementace C core gRPC k ASP.NET Core, je možné změnit životnost služby implementaci služby z oboru na jednotlivý prvek.</span><span class="sxs-lookup"><span data-stu-id="50c10-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="50c10-120">To zahrnuje přidání do kontejnerů DI instance implementace služby:</span><span class="sxs-lookup"><span data-stu-id="50c10-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="50c10-121">Implementace služby s životností jednotlivý prvek je však již nemůže přeložit vymezené služby prostřednictvím konstruktoru vkládání.</span><span class="sxs-lookup"><span data-stu-id="50c10-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="50c10-122">Nakonfigurujte možnosti služby gRPC</span><span class="sxs-lookup"><span data-stu-id="50c10-122">Configure gRPC services options</span></span>

<span data-ttu-id="50c10-123">V aplikacích jazyka C-podle počtu jader, nastavení, jako `grpc.max_receive_message_length` a `grpc.max_send_message_length` jsou nakonfigurovány s `ChannelOption` při [vytváření instance serveru](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span><span class="sxs-lookup"><span data-stu-id="50c10-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="50c10-124">V ASP.NET Core `GrpcServiceOptions` poskytuje způsob, jak nakonfigurovat tato nastavení.</span><span class="sxs-lookup"><span data-stu-id="50c10-124">In ASP.NET Core, `GrpcServiceOptions` provides a way to configure these settings.</span></span> <span data-ttu-id="50c10-125">Nastavení můžete použít globálně ke všem službám gRPC nebo typ implementace jednotlivých služeb.</span><span class="sxs-lookup"><span data-stu-id="50c10-125">The settings can be applied globally to all gRPC services or to an individual service implementation type.</span></span> <span data-ttu-id="50c10-126">Možnosti zadané pro tyto typy implementace jednotlivých služeb přepsat globální nastavení, pokud je nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="50c10-126">Options specified for individual service implementation types override global settings when configured.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services
        .AddGrpc(globalOptions =>
        {
            // Global settings
            globalOptions.SendMaxMessageSize = 4096
            globalOptions.ReceiveMaxMessageSize = 4096
        })
        .AddServiceOptions<GreeterService>(greeterOptions =>
        {
            // GreeterService settings. These will override global settings
            globalOptions.SendMaxMessageSize = 2048
            globalOptions.ReceiveMaxMessageSize = 2048
        })
}
```

## <a name="logging"></a><span data-ttu-id="50c10-127">Protokolování</span><span class="sxs-lookup"><span data-stu-id="50c10-127">Logging</span></span>

<span data-ttu-id="50c10-128">Využívají aplikace s C-podle počtu jader `GrpcEnvironment` k [konfiguraci protokolovacího nástroje](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) pro účely ladění.</span><span class="sxs-lookup"><span data-stu-id="50c10-128">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="50c10-129">Tuto funkci prostřednictvím poskytuje zásobníku ASP.NET Core [protokolování rozhraní API](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="50c10-129">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="50c10-130">Protokolovací nástroj lze například přidat ke službě gRPC prostřednictvím konstruktoru vkládání:</span><span class="sxs-lookup"><span data-stu-id="50c10-130">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="50c10-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="50c10-131">HTTPS</span></span>

<span data-ttu-id="50c10-132">Aplikace s C-podle počtu jader konfigurace HTTPS prostřednictvím [Server.Ports vlastnost](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="50c10-132">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="50c10-133">Podobný koncept se používá ke konfiguraci serverů v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="50c10-133">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="50c10-134">Například používá Kestrel [konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration) pro tuto funkci.</span><span class="sxs-lookup"><span data-stu-id="50c10-134">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="interceptors-and-middleware"></a><span data-ttu-id="50c10-135">Sběrače a Middleware</span><span class="sxs-lookup"><span data-stu-id="50c10-135">Interceptors and Middleware</span></span>

<span data-ttu-id="50c10-136">ASP.NET Core [middleware](xref:fundamentals/middleware/index) nabízí podobné funkce, které jsou ve srovnání s sběrače v aplikacích jazyka C-podle počtu jader gRPC.</span><span class="sxs-lookup"><span data-stu-id="50c10-136">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="50c10-137">Middleware a sběrače jsou koncepčně stejné jako se používá k vytvoření kanálu, který zpracovává požadavek gRPC.</span><span class="sxs-lookup"><span data-stu-id="50c10-137">Middleware and interceptors are conceptually the same as both are used to construct a pipeline that handles a gRPC request.</span></span> <span data-ttu-id="50c10-138">Oba umožňují práci prováděnou před nebo po další komponenta v kanálu.</span><span class="sxs-lookup"><span data-stu-id="50c10-138">They both allow work to be performed before or after the next component in the pipeline.</span></span> <span data-ttu-id="50c10-139">Však middleware ASP.NET Core funguje v základní zprávy HTTP/2, zatímco sběrače provádět gRPC vrstva abstrakce pomocí [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="50c10-139">However, ASP.NET Core middleware operates on the underlying HTTP/2 messages, while interceptors operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="50c10-140">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="50c10-140">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
