---
title: Migrace služeb gRPC z C-Core na ASP.NET Core
author: juntaoluo
description: Naučte se, jak přesunout existující aplikaci gRPC založenou na jazyce C a spustit ji nad ASP.NET Core Stack.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
uid: grpc/migration
ms.openlocfilehash: 451171a041f7bbb3711babd73d2fa2e245aadd28
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664134"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="4754e-103">Migrace služeb gRPC z C-Core na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4754e-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="4754e-104">Od [Jan Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="4754e-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="4754e-105">Z důvodu implementace podkladového zásobníku ne všechny funkce fungují stejným způsobem mezi aplikacemi [gRPC založenými na jazyce C](https://grpc.io/blog/grpc-stacks) a aplikacemi založenými na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4754e-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="4754e-106">Tento dokument popisuje klíčové rozdíly mezi těmito dvěma zásobníky.</span><span class="sxs-lookup"><span data-stu-id="4754e-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="4754e-107">doba implementace služby gRPC</span><span class="sxs-lookup"><span data-stu-id="4754e-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="4754e-108">Ve výchozím nastavení jsou ve ASP.NET Core stacku služby gRPC vytvořeny s [vymezenou životností](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="4754e-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="4754e-109">Naproti tomu gRPC C-Core se ve výchozím nastavení váže ke službě s [životností singleton](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="4754e-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="4754e-110">Vymezená doba života umožňuje implementaci služby vyřešit další služby s vymezenými životnostmi.</span><span class="sxs-lookup"><span data-stu-id="4754e-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="4754e-111">Například vymezená doba života může také vyřešit `DbContext` z kontejneru DI prostřednictvím injektáže konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="4754e-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="4754e-112">Použití oboru platnosti:</span><span class="sxs-lookup"><span data-stu-id="4754e-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="4754e-113">Nová instance implementace služby je vytvořena pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="4754e-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="4754e-114">Není možné sdílet stav mezi požadavky prostřednictvím členů instance v typu implementace.</span><span class="sxs-lookup"><span data-stu-id="4754e-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="4754e-115">Očekává se, že se sdílené stavy ukládají do služby s jedním prvkem v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="4754e-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="4754e-116">Uložené sdílené stavy jsou vyřešeny v konstruktoru implementace služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="4754e-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="4754e-117">Další informace o životních cyklech služby najdete v tématu <xref:fundamentals/dependency-injection#service-lifetimes>.</span><span class="sxs-lookup"><span data-stu-id="4754e-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="4754e-118">Přidání služby s jedním prvkem</span><span class="sxs-lookup"><span data-stu-id="4754e-118">Add a singleton service</span></span>

<span data-ttu-id="4754e-119">Pro usnadnění přechodu z gRPC implementace C-Core do ASP.NET Core je možné změnit dobu života služby implementace služby z oboru na singleton.</span><span class="sxs-lookup"><span data-stu-id="4754e-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="4754e-120">To zahrnuje přidání instance implementace služby do kontejneru DI:</span><span class="sxs-lookup"><span data-stu-id="4754e-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="4754e-121">Nicméně implementace služby s životností singleton již není schopna překládat oborové služby prostřednictvím injektáže konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="4754e-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="4754e-122">Konfigurace možností služeb gRPC Services</span><span class="sxs-lookup"><span data-stu-id="4754e-122">Configure gRPC services options</span></span>

<span data-ttu-id="4754e-123">V aplikacích založených na jazyce C jsou nastavení, jako je například `grpc.max_receive_message_length` a `grpc.max_send_message_length`, konfigurována s `ChannelOption` při [vytváření instance serveru](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span><span class="sxs-lookup"><span data-stu-id="4754e-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="4754e-124">V ASP.NET Core poskytuje gRPC konfiguraci prostřednictvím `GrpcServiceOptions` typu.</span><span class="sxs-lookup"><span data-stu-id="4754e-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="4754e-125">Například maximální velikost příchozích zpráv služby gRPC je možné nakonfigurovat prostřednictvím `AddGrpc`.</span><span class="sxs-lookup"><span data-stu-id="4754e-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="4754e-126">Následující příklad změní výchozí `MaxReceiveMessageSize` 4 MB na 16 MB:</span><span class="sxs-lookup"><span data-stu-id="4754e-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="4754e-127">Další informace o konfiguraci najdete v tématu <xref:grpc/configuration>.</span><span class="sxs-lookup"><span data-stu-id="4754e-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="4754e-128">Protokolování</span><span class="sxs-lookup"><span data-stu-id="4754e-128">Logging</span></span>

<span data-ttu-id="4754e-129">Aplikace založené na základních jazycích v jazyce C spoléhají na `GrpcEnvironment` ke [konfiguraci protokolovacího](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) nástroje pro účely ladění.</span><span class="sxs-lookup"><span data-stu-id="4754e-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="4754e-130">ASP.NET Core Stack tuto funkci poskytuje prostřednictvím [rozhraní API protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="4754e-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="4754e-131">Například protokolovací nástroj může být přidán do služby gRPC prostřednictvím injektáže konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="4754e-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="4754e-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="4754e-132">HTTPS</span></span>

<span data-ttu-id="4754e-133">Aplikace založené na základních jazycích konfigurují HTTPS prostřednictvím [vlastnosti Server. Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="4754e-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="4754e-134">Podobný koncept se používá ke konfiguraci serverů v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4754e-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="4754e-135">Kestrel například pro tuto funkci používá [konfiguraci koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration) .</span><span class="sxs-lookup"><span data-stu-id="4754e-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="4754e-136">gRPC zachycení vs middleware</span><span class="sxs-lookup"><span data-stu-id="4754e-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="4754e-137">[Middleware](xref:fundamentals/middleware/index) ASP.NET Core nabízí podobné funkce v porovnání s zachycením v aplikacích gRPC založených na jazyce C.</span><span class="sxs-lookup"><span data-stu-id="4754e-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="4754e-138">ASP.NET Core middlewarů a zachycení jsou koncepčně podobné.</span><span class="sxs-lookup"><span data-stu-id="4754e-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="4754e-139">Protokoly</span><span class="sxs-lookup"><span data-stu-id="4754e-139">Both:</span></span>

* <span data-ttu-id="4754e-140">Slouží k vytvoření kanálu, který zpracovává gRPC požadavek.</span><span class="sxs-lookup"><span data-stu-id="4754e-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="4754e-141">Povolí provedení práce před nebo po další komponentě v kanálu.</span><span class="sxs-lookup"><span data-stu-id="4754e-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="4754e-142">Poskytněte přístup k `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="4754e-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="4754e-143">V middleware je `HttpContext` parametr.</span><span class="sxs-lookup"><span data-stu-id="4754e-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="4754e-144">Ve zachycených modulech lze k `HttpContext` přistupovat pomocí parametru `ServerCallContext` s metodou rozšíření `ServerCallContext.GetHttpContext`.</span><span class="sxs-lookup"><span data-stu-id="4754e-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="4754e-145">Všimněte si, že tato funkce je specifická pro zachycení běžící v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4754e-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="4754e-146">rozdíly v gRPC zachytávací z ASP.NET Core middlewaru:</span><span class="sxs-lookup"><span data-stu-id="4754e-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="4754e-147">Sběrače</span><span class="sxs-lookup"><span data-stu-id="4754e-147">Interceptors:</span></span>
  * <span data-ttu-id="4754e-148">Pracovat na gRPC vrstvě abstrakce s využitím [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="4754e-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="4754e-149">Poskytněte přístup k:</span><span class="sxs-lookup"><span data-stu-id="4754e-149">Provide access to:</span></span>
    * <span data-ttu-id="4754e-150">Deserializovaná zpráva odeslaná volání.</span><span class="sxs-lookup"><span data-stu-id="4754e-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="4754e-151">Zpráva vracená voláním před tím, než je serializována.</span><span class="sxs-lookup"><span data-stu-id="4754e-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="4754e-152">Dokáže zachytit a zpracovat výjimky vyvolané službami gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="4754e-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="4754e-153">Jiné</span><span class="sxs-lookup"><span data-stu-id="4754e-153">Middleware:</span></span>
  * <span data-ttu-id="4754e-154">Spustí se před zachycením gRPC.</span><span class="sxs-lookup"><span data-stu-id="4754e-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="4754e-155">Funguje se základními zprávami HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="4754e-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="4754e-156">Může přistupovat jenom k bajtům z datových proudů požadavků a odpovědí.</span><span class="sxs-lookup"><span data-stu-id="4754e-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4754e-157">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4754e-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
