---
title: Migrace služeb gRPC z C-core do ASP.NET Core
author: juntaoluo
description: Přečtěte si, jak přesunout existující aplikaci gRPC založenou na cjádru, která se má spouštět nad ASP.NET zásobníku Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 451171a041f7bbb3711babd73d2fa2e245aadd28
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664134"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="b9009-103">Migrace služeb gRPC z C-core do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b9009-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="b9009-104">Podle [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="b9009-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="b9009-105">Vzhledem k implementaci základního zásobníku ne fungují všechny funkce stejným způsobem mezi [aplikacemi gRPC založenými na jádru C](https://grpc.io/blog/grpc-stacks) a aplikacemi ASP.NET core.</span><span class="sxs-lookup"><span data-stu-id="b9009-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="b9009-106">Tento dokument zvýrazní klíčové rozdíly při migraci mezi dvěma zásobníky.</span><span class="sxs-lookup"><span data-stu-id="b9009-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="b9009-107">gRPC životnost implementace služby</span><span class="sxs-lookup"><span data-stu-id="b9009-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="b9009-108">V zásobníku ASP.NET Core jsou ve výchozím nastavení služby gRPC vytvořeny s [rozsahem životnosti](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="b9009-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="b9009-109">Naproti tomu gRPC C-core ve výchozím nastavení váže na službu s [životností singleton](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="b9009-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="b9009-110">Životnost oboru umožňuje implementaci služby vyřešit další služby s rozsahem životnosti.</span><span class="sxs-lookup"><span data-stu-id="b9009-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="b9009-111">Například životnost oboru můžete také `DbContext` vyřešit z kontejneru DI prostřednictvím vstřikování konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="b9009-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="b9009-112">Použití životnosti s vymezenou oborem:</span><span class="sxs-lookup"><span data-stu-id="b9009-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="b9009-113">Pro každý požadavek je vytvořena nová instance implementace služby.</span><span class="sxs-lookup"><span data-stu-id="b9009-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="b9009-114">Není možné sdílet stav mezi požadavky prostřednictvím členů instance na typu implementace.</span><span class="sxs-lookup"><span data-stu-id="b9009-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="b9009-115">Očekává se uložení sdílených stavů ve službě singleton v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="b9009-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="b9009-116">Uložené sdílené stavy jsou vyřešeny v konstruktoru implementace služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="b9009-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="b9009-117">Další informace o životnosti <xref:fundamentals/dependency-injection#service-lifetimes>naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="b9009-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="b9009-118">Přidání služby singleton</span><span class="sxs-lookup"><span data-stu-id="b9009-118">Add a singleton service</span></span>

<span data-ttu-id="b9009-119">Pro usnadnění přechodu z implementace jádra gRPC C na ASP.NET core je možné změnit životnost implementace služby z rozsahu na singleton.</span><span class="sxs-lookup"><span data-stu-id="b9009-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="b9009-120">To zahrnuje přidání instance implementace služby do kontejneru DI:</span><span class="sxs-lookup"><span data-stu-id="b9009-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="b9009-121">Implementace služby s životností singleton však již není schopna vyřešit služby s rozsahem prostřednictvím vkládání konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="b9009-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="b9009-122">Konfigurace možností služeb gRPC</span><span class="sxs-lookup"><span data-stu-id="b9009-122">Configure gRPC services options</span></span>

<span data-ttu-id="b9009-123">V aplikacích založených na jádru `grpc.max_receive_message_length` `grpc.max_send_message_length` C jsou `ChannelOption` nastavení, jako je například a jsou konfigurovány s [při vytváření instance Serveru](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span><span class="sxs-lookup"><span data-stu-id="b9009-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="b9009-124">V ASP.NET Core, gRPC `GrpcServiceOptions` poskytuje konfiguraci prostřednictvím typu.</span><span class="sxs-lookup"><span data-stu-id="b9009-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="b9009-125">Například maximální velikost příchozí zprávy služby gRPC lze `AddGrpc`konfigurovat pomocí aplikace .</span><span class="sxs-lookup"><span data-stu-id="b9009-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="b9009-126">Následující příklad změní `MaxReceiveMessageSize` výchozí hodnotu 4 MB na 16 MB:</span><span class="sxs-lookup"><span data-stu-id="b9009-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="b9009-127">Další informace o konfiguraci naleznete v tématu <xref:grpc/configuration>.</span><span class="sxs-lookup"><span data-stu-id="b9009-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="b9009-128">Protokolování</span><span class="sxs-lookup"><span data-stu-id="b9009-128">Logging</span></span>

<span data-ttu-id="b9009-129">C-core založené aplikace spoléhají `GrpcEnvironment` na [konfiguraci protokolování](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) pro účely ladění.</span><span class="sxs-lookup"><span data-stu-id="b9009-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="b9009-130">ASP.NET core zásobníku poskytuje tuto funkci prostřednictvím [rozhraní API protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="b9009-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b9009-131">Například logger lze přidat do služby gRPC prostřednictvím vstřikování konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="b9009-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="b9009-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="b9009-132">HTTPS</span></span>

<span data-ttu-id="b9009-133">Aplikace založené na jádru C konfigurují protokol HTTPS prostřednictvím [vlastnosti Server.Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="b9009-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="b9009-134">Podobný koncept se používá ke konfiguraci serverů v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b9009-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="b9009-135">Kestrel například používá [konfiguraci koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration) pro tuto funkci.</span><span class="sxs-lookup"><span data-stu-id="b9009-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="b9009-136">gRPC Interceptors vs Middleware</span><span class="sxs-lookup"><span data-stu-id="b9009-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="b9009-137">ASP.NET [Core middleware](xref:fundamentals/middleware/index) nabízí podobné funkce ve srovnání s interceptory v gRPC aplikacích založených na C-core.</span><span class="sxs-lookup"><span data-stu-id="b9009-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="b9009-138">ASP.NET Core middleware a interceptory jsou koncepčně podobné.</span><span class="sxs-lookup"><span data-stu-id="b9009-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="b9009-139">Oba:</span><span class="sxs-lookup"><span data-stu-id="b9009-139">Both:</span></span>

* <span data-ttu-id="b9009-140">Používají se k vytvoření kanálu, který zpracovává požadavek gRPC.</span><span class="sxs-lookup"><span data-stu-id="b9009-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="b9009-141">Povolit práci, která má být provedena před nebo po další součást v potrubí.</span><span class="sxs-lookup"><span data-stu-id="b9009-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="b9009-142">Poskytnout přístup `HttpContext`k :</span><span class="sxs-lookup"><span data-stu-id="b9009-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="b9009-143">V middleware `HttpContext` je parametr.</span><span class="sxs-lookup"><span data-stu-id="b9009-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="b9009-144">V zachycovačích `HttpContext` lze `ServerCallContext` přistupovat `ServerCallContext.GetHttpContext` pomocí parametru s metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="b9009-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="b9009-145">Všimněte si, že tato funkce je specifická pro zachycovače spuštěné v ASP.NET core.</span><span class="sxs-lookup"><span data-stu-id="b9009-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="b9009-146">gRPC Interceptor rozdíly od ASP.NET Core Middleware:</span><span class="sxs-lookup"><span data-stu-id="b9009-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="b9009-147">Zachycovače:</span><span class="sxs-lookup"><span data-stu-id="b9009-147">Interceptors:</span></span>
  * <span data-ttu-id="b9009-148">Pracujte na vrstvě abstrakce gRPC pomocí [služby ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="b9009-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="b9009-149">Poskytněte přístup k:</span><span class="sxs-lookup"><span data-stu-id="b9009-149">Provide access to:</span></span>
    * <span data-ttu-id="b9009-150">Deserializovaná zpráva odeslaná volání.</span><span class="sxs-lookup"><span data-stu-id="b9009-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="b9009-151">Zpráva, která je vrácena z volání před jeho serializace.</span><span class="sxs-lookup"><span data-stu-id="b9009-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="b9009-152">Může zachytit a zpracovat výjimky vyvoláné ze služeb gRPC.</span><span class="sxs-lookup"><span data-stu-id="b9009-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="b9009-153">Middleware:</span><span class="sxs-lookup"><span data-stu-id="b9009-153">Middleware:</span></span>
  * <span data-ttu-id="b9009-154">Běží před gRPC zachycovače.</span><span class="sxs-lookup"><span data-stu-id="b9009-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="b9009-155">Pracuje na podkladové zprávy HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b9009-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="b9009-156">Přístup k bajtům lze získat pouze z datových proudů požadavků a odpovědí.</span><span class="sxs-lookup"><span data-stu-id="b9009-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9009-157">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b9009-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
