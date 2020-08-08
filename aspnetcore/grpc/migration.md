---
title: Migrace služeb gRPC z C-Core na ASP.NET Core
author: juntaoluo
description: Naučte se, jak přesunout existující aplikaci gRPC založenou na jazyce C a spustit ji nad ASP.NET Core Stack.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/migration
ms.openlocfilehash: 8098d3a629eb7e5902c5379c00ea7090900a87d2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016021"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="12b67-103">Migrace služeb gRPC z C-Core na ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="12b67-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="12b67-104">Od [Jan Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="12b67-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="12b67-105">Z důvodu implementace podkladového zásobníku ne všechny funkce fungují stejným způsobem mezi aplikacemi [gRPC založenými na jazyce C](https://grpc.io/blog/grpc-stacks) a aplikacemi založenými na ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="12b67-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="12b67-106">Tento dokument popisuje klíčové rozdíly mezi těmito dvěma zásobníky.</span><span class="sxs-lookup"><span data-stu-id="12b67-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="12b67-107">doba implementace služby gRPC</span><span class="sxs-lookup"><span data-stu-id="12b67-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="12b67-108">Ve výchozím nastavení jsou ve ASP.NET Core stacku služby gRPC vytvořeny s [vymezenou životností](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="12b67-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="12b67-109">Naproti tomu gRPC C-Core se ve výchozím nastavení váže ke službě s [životností singleton](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="12b67-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="12b67-110">Vymezená doba života umožňuje implementaci služby vyřešit další služby s vymezenými životnostmi.</span><span class="sxs-lookup"><span data-stu-id="12b67-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="12b67-111">Například rozsah životnosti lze také vyřešit `DbContext` z kontejneru di prostřednictvím injektáže konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="12b67-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="12b67-112">Použití oboru platnosti:</span><span class="sxs-lookup"><span data-stu-id="12b67-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="12b67-113">Nová instance implementace služby je vytvořena pro každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="12b67-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="12b67-114">Není možné sdílet stav mezi požadavky prostřednictvím členů instance v typu implementace.</span><span class="sxs-lookup"><span data-stu-id="12b67-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="12b67-115">Očekává se, že se sdílené stavy ukládají do služby s jedním prvkem v kontejneru DI.</span><span class="sxs-lookup"><span data-stu-id="12b67-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="12b67-116">Uložené sdílené stavy jsou vyřešeny v konstruktoru implementace služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="12b67-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="12b67-117">Další informace o životních cyklech služby najdete v tématu <xref:fundamentals/dependency-injection#service-lifetimes> .</span><span class="sxs-lookup"><span data-stu-id="12b67-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="12b67-118">Přidání služby s jedním prvkem</span><span class="sxs-lookup"><span data-stu-id="12b67-118">Add a singleton service</span></span>

<span data-ttu-id="12b67-119">Pro usnadnění přechodu z gRPC implementace C-Core do ASP.NET Core je možné změnit dobu života služby implementace služby z oboru na singleton.</span><span class="sxs-lookup"><span data-stu-id="12b67-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="12b67-120">To zahrnuje přidání instance implementace služby do kontejneru DI:</span><span class="sxs-lookup"><span data-stu-id="12b67-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="12b67-121">Nicméně implementace služby s životností singleton již není schopna překládat oborové služby prostřednictvím injektáže konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="12b67-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="12b67-122">Konfigurace možností služeb gRPC Services</span><span class="sxs-lookup"><span data-stu-id="12b67-122">Configure gRPC services options</span></span>

<span data-ttu-id="12b67-123">V aplikacích založených na jazyce C jsou nastavení, jako `grpc.max_receive_message_length` jsou a, `grpc.max_send_message_length` nakonfigurována `ChannelOption` při [vytváření instance serveru](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span><span class="sxs-lookup"><span data-stu-id="12b67-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="12b67-124">V ASP.NET Core poskytuje gRPC konfiguraci prostřednictvím `GrpcServiceOptions` typu.</span><span class="sxs-lookup"><span data-stu-id="12b67-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="12b67-125">Například maximální velikost příchozích zpráv služby gRPC lze nakonfigurovat prostřednictvím `AddGrpc` .</span><span class="sxs-lookup"><span data-stu-id="12b67-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="12b67-126">Následující příklad změní výchozí nastavení `MaxReceiveMessageSize` 4 MB na 16 MB:</span><span class="sxs-lookup"><span data-stu-id="12b67-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="12b67-127">Další informace o konfiguraci najdete v tématu <xref:grpc/configuration> .</span><span class="sxs-lookup"><span data-stu-id="12b67-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="12b67-128">protokolování</span><span class="sxs-lookup"><span data-stu-id="12b67-128">Logging</span></span>

<span data-ttu-id="12b67-129">Základní aplikace založené na jazyce C jsou závislé na `GrpcEnvironment` [konfiguraci protokolovacího](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) nástroje pro účely ladění.</span><span class="sxs-lookup"><span data-stu-id="12b67-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="12b67-130">ASP.NET Core Stack tuto funkci poskytuje prostřednictvím [rozhraní API protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="12b67-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="12b67-131">Například protokolovací nástroj může být přidán do služby gRPC prostřednictvím injektáže konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="12b67-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="12b67-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="12b67-132">HTTPS</span></span>

<span data-ttu-id="12b67-133">Aplikace založené na základních jazycích konfigurují HTTPS prostřednictvím [vlastnosti Server. Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span><span class="sxs-lookup"><span data-stu-id="12b67-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="12b67-134">Podobný koncept se používá ke konfiguraci serverů v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="12b67-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="12b67-135">Kestrel například pro tuto funkci používá [konfiguraci koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration) .</span><span class="sxs-lookup"><span data-stu-id="12b67-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="12b67-136">gRPC zachycení vs middleware</span><span class="sxs-lookup"><span data-stu-id="12b67-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="12b67-137">[Middleware](xref:fundamentals/middleware/index) ASP.NET Core nabízí podobné funkce v porovnání s zachycením v aplikacích gRPC založených na jazyce C.</span><span class="sxs-lookup"><span data-stu-id="12b67-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="12b67-138">ASP.NET Core middlewarů a zachycení jsou koncepčně podobné.</span><span class="sxs-lookup"><span data-stu-id="12b67-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="12b67-139">Protokoly</span><span class="sxs-lookup"><span data-stu-id="12b67-139">Both:</span></span>

* <span data-ttu-id="12b67-140">Slouží k vytvoření kanálu, který zpracovává gRPC požadavek.</span><span class="sxs-lookup"><span data-stu-id="12b67-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="12b67-141">Povolí provedení práce před nebo po další komponentě v kanálu.</span><span class="sxs-lookup"><span data-stu-id="12b67-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="12b67-142">Poskytněte přístup k `HttpContext` :</span><span class="sxs-lookup"><span data-stu-id="12b67-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="12b67-143">V middleware `HttpContext` je parametr.</span><span class="sxs-lookup"><span data-stu-id="12b67-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="12b67-144">V modulech zachycení `HttpContext` lze k němu přistup pomocí `ServerCallContext` parametru s `ServerCallContext.GetHttpContext` metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="12b67-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="12b67-145">Všimněte si, že tato funkce je specifická pro zachycení běžící v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="12b67-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="12b67-146">rozdíly v gRPC zachytávací z ASP.NET Core middlewaru:</span><span class="sxs-lookup"><span data-stu-id="12b67-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="12b67-147">Sběrače</span><span class="sxs-lookup"><span data-stu-id="12b67-147">Interceptors:</span></span>
  * <span data-ttu-id="12b67-148">Pracovat na gRPC vrstvě abstrakce s využitím [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="12b67-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="12b67-149">Poskytněte přístup k:</span><span class="sxs-lookup"><span data-stu-id="12b67-149">Provide access to:</span></span>
    * <span data-ttu-id="12b67-150">Deserializovaná zpráva odeslaná volání.</span><span class="sxs-lookup"><span data-stu-id="12b67-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="12b67-151">Zpráva vracená voláním před tím, než je serializována.</span><span class="sxs-lookup"><span data-stu-id="12b67-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="12b67-152">Dokáže zachytit a zpracovat výjimky vyvolané službami gRPC Services.</span><span class="sxs-lookup"><span data-stu-id="12b67-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="12b67-153">Jiné</span><span class="sxs-lookup"><span data-stu-id="12b67-153">Middleware:</span></span>
  * <span data-ttu-id="12b67-154">Spustí se před zachycením gRPC.</span><span class="sxs-lookup"><span data-stu-id="12b67-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="12b67-155">Funguje se základními zprávami HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="12b67-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="12b67-156">Může přistupovat jenom k bajtům z datových proudů požadavků a odpovědí.</span><span class="sxs-lookup"><span data-stu-id="12b67-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="12b67-157">Další materiály</span><span class="sxs-lookup"><span data-stu-id="12b67-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
