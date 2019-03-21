---
title: Služby gRPC s ASP.NET Core
author: juntaoluo
description: Informace o základních konceptech při zápisu gRPC služby pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 387c3134efc04bec740fc66a5ca4b84715264d35
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209022"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="5a338-103">Služby gRPC s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a338-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="5a338-104">Tento dokument ukazuje, jak začít pracovat s gRPC služby pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5a338-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-all-3.0.md)]

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="5a338-105">Začínáme se službou gRPC v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a338-105">Get started with gRPC service in ASP.NET Core</span></span>

[!INCLUDE[View or download sample code](~/includes/grpc/download.md)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5a338-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a338-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5a338-107">Zobrazit [začít pracovat se službami gRPC](xref:tutorials/grpc/grpc-start) podrobné pokyny o tom, jak vytvořit projekt gRPC.</span><span class="sxs-lookup"><span data-stu-id="5a338-107">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="5a338-108">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5a338-108">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="5a338-109">Spustit `dotnet new grpc -o GrpcGreeter` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5a338-109">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="5a338-110">Přidání gRPC služby do aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a338-110">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="5a338-111">gRPC vyžaduje následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="5a338-111">gRPC requires the following packages:</span></span>

* [<span data-ttu-id="5a338-112">Grpc.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="5a338-112">Grpc.AspNetCore.Server</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server)
* <span data-ttu-id="5a338-113">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) protobuf zpráva rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="5a338-113">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) for protobuf message APIs.</span></span>
* [<span data-ttu-id="5a338-114">Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="5a338-114">Grpc.Tools</span></span>](https://www.nuget.org/packages/Grpc.Tools/)

### <a name="configure-grpc"></a><span data-ttu-id="5a338-115">Konfigurace gRPC</span><span class="sxs-lookup"><span data-stu-id="5a338-115">Configure gRPC</span></span>

<span data-ttu-id="5a338-116">gRPC je povolená s `AddGrpc` metody:</span><span class="sxs-lookup"><span data-stu-id="5a338-116">gRPC is enabled with the `AddGrpc` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="5a338-117">Každá služba gRPC se přidá do kanálu směrování prostřednictvím `MapGrpcService` metody:</span><span class="sxs-lookup"><span data-stu-id="5a338-117">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/Startup.cs?name=snippet&highlight=16-19)]

<span data-ttu-id="5a338-118">Funkce a ASP.NET Core middlewares sdílet směrování kanálu, proto aplikace může být nakonfigurován tak, aby obslužné rutiny další požadavek.</span><span class="sxs-lookup"><span data-stu-id="5a338-118">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="5a338-119">Obslužné rutiny dalších požadavků, jako jsou řadiče MVC pracovat souběžně s nakonfigurovanou gRPC služby.</span><span class="sxs-lookup"><span data-stu-id="5a338-119">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="5a338-120">Integrace s rozhraními API pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5a338-120">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="5a338-121">gRPC služby, jako mají plný přístup k funkcím ASP.NET Core [injektáž závislostí](xref:fundamentals/dependency-injection) (DI) a [protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="5a338-121">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="5a338-122">Implementace služby například můžete vyřešit služby protokoly z kontejnerů DI prostřednictvím konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="5a338-122">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="5a338-123">Ve výchozím nastavení lze vyřešit implementace služby gRPC dalším službám DI pomocí jakékoli životnost (Singleton, obor nebo přechodným).</span><span class="sxs-lookup"><span data-stu-id="5a338-123">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="5a338-124">Vyřešit HttpContext gRPC metod</span><span class="sxs-lookup"><span data-stu-id="5a338-124">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="5a338-125">GRPC rozhraní API poskytuje přístup k některým datům zprávy HTTP/2, jako je metoda, hostitele, záhlaví a přípojných.</span><span class="sxs-lookup"><span data-stu-id="5a338-125">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="5a338-126">Přístup je prostřednictvím `ServerCallContext` argument předaný metodě každou gRPC:</span><span class="sxs-lookup"><span data-stu-id="5a338-126">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/Services/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="5a338-127">`ServerCallContext` neposkytuje úplný přístup k `HttpContext` v všechna rozhraní API technologie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="5a338-127">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="5a338-128">`GetHttpContext` – Metoda rozšíření poskytuje úplný přístup ke `HttpContext` představující základní zprávy HTTP/2 v rozhraní API technologie ASP.NET:</span><span class="sxs-lookup"><span data-stu-id="5a338-128">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/samples/GrpcStart/GrpcGreeter.Server/Services/GreeterService.cs?name=snippet1)]

### <a name="request-body-data-rate-limit"></a><span data-ttu-id="5a338-129">Omezení četnosti data těla požadavku</span><span class="sxs-lookup"><span data-stu-id="5a338-129">Request body data rate limit</span></span>

<span data-ttu-id="5a338-130">Ve výchozím nastavení, Kestrel server ukládá [minimální požadavek tělo přenosová rychlost](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>).</span><span class="sxs-lookup"><span data-stu-id="5a338-130">By default, the Kestrel server imposes a [minimum request body data rate](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>).</span></span> <span data-ttu-id="5a338-131">Pro klienty streamování a streaming volání duplexní režim nemusí být splněny tato sazba a připojení může být vypršení časového limitu. Minimální požadavku limitu přenosové rychlosti dat musí být zakázáno, pokud obsahuje služba gRPC klienta streamování a streaming volání duplexní režim:</span><span class="sxs-lookup"><span data-stu-id="5a338-131">For client streaming and duplex streaming calls, this rate may not be satisfied and the connection may be timed out. The minimum request body data rate limit must be disabled when the gRPC service includes client streaming and duplex streaming calls:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
         Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
        webBuilder.ConfigureKestrel((context, options) =>
        {
            options.Limits.MinRequestBodyDataRate = null;
        });
    });
}
```

## <a name="additional-resources"></a><span data-ttu-id="5a338-132">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5a338-132">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
