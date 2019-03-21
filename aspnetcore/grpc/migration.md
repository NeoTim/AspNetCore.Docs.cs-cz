---
title: Migrace služeb gRPC z C jádra ASP.NET Core
author: juntaoluo
description: Zjistěte, jak přesunout existující aplikaci na základě gRPC C jádra pro spuštění nad rámec zásobníku ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/migration
ms.openlocfilehash: 3c6e04694a33e953f6e1575f5ee9b0699cf1cdd3
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58207955"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="5f09d-103">Migrace služeb gRPC z C jádra ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5f09d-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="5f09d-104">Podle [Luo Jan](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="5f09d-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="5f09d-105">Kvůli implementaci základní zásobníku, některé funkce fungovat stejně jako mezi [C jádra na základě gRPC](https://grpc.io/blog/grpc-stacks) aplikací založených na aplikace a ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5f09d-105">Due to implementation of the underlying stack, not all features work in the same way between [C-core based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core based apps.</span></span> <span data-ttu-id="5f09d-106">Tento dokument popisuje klíčové rozdíly si při migraci mezi dvěma zásobníků.</span><span class="sxs-lookup"><span data-stu-id="5f09d-106">This document highlights the key differences to note when migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="5f09d-107">Doba platnosti implementace gRPC služby</span><span class="sxs-lookup"><span data-stu-id="5f09d-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="5f09d-108">V zásobníku ASP.NET Core gRPC services ve výchozím nastavení, bude vytvořena s [obor životnost](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5f09d-108">In the ASP.NET Core stack, gRPC services, by default, will be created with a [Scoped lifetime](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5f09d-109">Naproti tomu gRPC C core ve výchozím nastavení vytvoří vazbu na službu s životností typu Singleton.</span><span class="sxs-lookup"><span data-stu-id="5f09d-109">In contrast, gRPC C-core by default binds to a service with a Singleton lifetime.</span></span>

<span data-ttu-id="5f09d-110">Doba života obor umožňuje implementace služby k vyřešení dalším službám pomocí životnosti obor.</span><span class="sxs-lookup"><span data-stu-id="5f09d-110">A Scoped lifetime allows the service implementation to resolve other services with Scoped lifetimes.</span></span> <span data-ttu-id="5f09d-111">Například můžete také vyřešit obor životnost `DBContext`, od kontejnerů DI prostřednictvím konstruktoru vkládání.</span><span class="sxs-lookup"><span data-stu-id="5f09d-111">For example Scoped lifetime can also resolve `DBContext`, from the DI container through constructor injection.</span></span> <span data-ttu-id="5f09d-112">Pomocí obor životnost:</span><span class="sxs-lookup"><span data-stu-id="5f09d-112">Using Scoped lifetime:</span></span>

* <span data-ttu-id="5f09d-113">Nová instance implementace služby je vytvořen pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="5f09d-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="5f09d-114">Není možné sdílení stavu mezi žádostí prostřednictvím členy instance na typu implementace.</span><span class="sxs-lookup"><span data-stu-id="5f09d-114">It's not possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="5f09d-115">Očekává se, k ukládání sdílené stavy do služby jednotlivý prvek v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="5f09d-115">The expectation is to store shared states in a Singleton service in the DI container.</span></span> <span data-ttu-id="5f09d-116">Uložené sdílené stavy jsou vyřešeny v konstruktoru gRPC implementaci služby.</span><span class="sxs-lookup"><span data-stu-id="5f09d-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span> 

<span data-ttu-id="5f09d-117">Další informace o životnost obor a Singleton, naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="5f09d-117">For more information on Scoped and Singleton lifetime, see <xref:fundamentals/dependency-injection>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="5f09d-118">Přidání služby typu singleton</span><span class="sxs-lookup"><span data-stu-id="5f09d-118">Add a singleton service</span></span>

<span data-ttu-id="5f09d-119">Pro usnadnění přechodu od gRPC C základní implementace pro ASP.NET Core, je možné změnit životnost služby implementaci služby z obor na jednotlivý prvek.</span><span class="sxs-lookup"><span data-stu-id="5f09d-119">To facilitate the transition from gRPC C-core implementation to ASP.NET Core, it is possible to change the service lifetime of the service implementation from Scoped to Singleton.</span></span> <span data-ttu-id="5f09d-120">To zahrnuje přidání do kontejnerů DI instance implementace služby:</span><span class="sxs-lookup"><span data-stu-id="5f09d-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="5f09d-121">Však implementace služby s dobou života typu Singleton již bude schopen převést obor služeb prostřednictvím konstruktoru vkládání.</span><span class="sxs-lookup"><span data-stu-id="5f09d-121">However, service implementation with Singleton lifetime will no longer be able to resolve Scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="5f09d-122">Nakonfigurujte možnosti služby gRPC</span><span class="sxs-lookup"><span data-stu-id="5f09d-122">Configure gRPC services options</span></span>

<span data-ttu-id="5f09d-123">V jazyce C core na základě aplikace, nastavení, jako `grpc.max_receive_message_length` a `grpc.max_send_message_length` jsou nakonfigurovány s `ChannelOption` při [vytváření `Server` instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span><span class="sxs-lookup"><span data-stu-id="5f09d-123">In C-core based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the `Server` instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="5f09d-124">V ASP.NET Core `GrpcServiceOptions` poskytuje způsob, jak nakonfigurovat tato nastavení.</span><span class="sxs-lookup"><span data-stu-id="5f09d-124">In ASP.NET Core, `GrpcServiceOptions` provides a way to configure these settings.</span></span> <span data-ttu-id="5f09d-125">Nastavení můžete použít globálně ke všem službám gRPC nebo typ implementace jednotlivých služeb.</span><span class="sxs-lookup"><span data-stu-id="5f09d-125">The settings can be applied globally to all gRPC services or to an individual service implementation type.</span></span> <span data-ttu-id="5f09d-126">Možnosti zadané pro tyto typy implementace jednotlivých služeb se přepsat globální nastavení, pokud je nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="5f09d-126">Options specified for individual service implementation types will override global settings when configured.</span></span>

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

## <a name="logging"></a><span data-ttu-id="5f09d-127">Protokolování</span><span class="sxs-lookup"><span data-stu-id="5f09d-127">Logging</span></span>

<span data-ttu-id="5f09d-128">Na základě aplikací C core závisí na `GrpcEnvironment` k [konfiguraci protokolovacího nástroje](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) pro účely ladění.</span><span class="sxs-lookup"><span data-stu-id="5f09d-128">C-core based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="5f09d-129">Tuto funkci prostřednictvím poskytuje zásobníku ASP.NET Core [protokolování rozhraní API](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="5f09d-129">The ASP.NET Core stack provides this functionality through the [logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="5f09d-130">Protokolovací nástroj například mohou být přidány do služby gRPC prostřednictvím konstruktoru vkládání:</span><span class="sxs-lookup"><span data-stu-id="5f09d-130">For example a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="5f09d-131">HTTPS</span><span class="sxs-lookup"><span data-stu-id="5f09d-131">HTTPS</span></span>

<span data-ttu-id="5f09d-132">Na základě aplikací C – core konfigurace HTTPS prostřednictvím [ `Server.Ports` vlastnost](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="5f09d-132">C-core based apps configure HTTPS through the [`Server.Ports` property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="5f09d-133">Podobný koncept se používá ke konfiguraci serverů v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5f09d-133">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="5f09d-134">Například používá Kestrel [konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration) pro tuto funkci.</span><span class="sxs-lookup"><span data-stu-id="5f09d-134">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="interceptors-and-middlewares"></a><span data-ttu-id="5f09d-135">Sběrače a Middlewares</span><span class="sxs-lookup"><span data-stu-id="5f09d-135">Interceptors and Middlewares</span></span>

<span data-ttu-id="5f09d-136">ASP.NET Core [middlewares](xref:fundamentals/middleware/index) gRPC aplikací založených na nabízí podobné funkce ve srovnání s zachycovacích dotazů v C core.</span><span class="sxs-lookup"><span data-stu-id="5f09d-136">ASP.NET Core [middlewares](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core based gRPC apps.</span></span> <span data-ttu-id="5f09d-137">Middlewares a sběrače jsou koncepčně stejné jako se používá k vytvoření kanálu, který zpracovává požadavek gRPC.</span><span class="sxs-lookup"><span data-stu-id="5f09d-137">Middlewares and interceptors are conceptually the same as both are used to construct a pipeline that handles a gRPC request.</span></span> <span data-ttu-id="5f09d-138">Oba umožňují práci prováděnou před nebo po další komponenta v kanálu.</span><span class="sxs-lookup"><span data-stu-id="5f09d-138">They both allow work to be performed before or after the next component in the pipeline.</span></span> <span data-ttu-id="5f09d-139">Ale middlewares ASP.NET Core pracovat s základní zprávy HTTP/2, zatímco sběrače provádět gRPC vrstva abstrakce pomocí [ `ServerCallContext` ](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="5f09d-139">However, ASP.NET Core middlewares operate on the underlying HTTP/2 messages whereas interceptors operate on the gRPC layer of abstraction using the [`ServerCallContext`](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5f09d-140">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5f09d-140">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
