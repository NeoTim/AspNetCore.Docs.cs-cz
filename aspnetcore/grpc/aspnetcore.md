---
title: Služby gRPC s ASP.NET Core
author: juntaoluo
description: Informace o základních konceptech při zápisu gRPC služby pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 5937ca9f2a783c4dabe324dae828b97953782938
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555863"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="a065a-103">Služby gRPC s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a065a-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="a065a-104">Tento dokument ukazuje, jak začít pracovat s gRPC služby pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a065a-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a065a-105">Požadavky</span><span class="sxs-lookup"><span data-stu-id="a065a-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a065a-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a065a-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a065a-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a065a-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="a065a-108">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="a065a-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="a065a-109">Začínáme se službou gRPC v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a065a-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="a065a-110">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="a065a-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a065a-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a065a-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a065a-112">Zobrazit [začít pracovat se službami gRPC](xref:tutorials/grpc/grpc-start) podrobné pokyny o tom, jak vytvořit projekt gRPC.</span><span class="sxs-lookup"><span data-stu-id="a065a-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="a065a-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a065a-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="a065a-114">Spustit `dotnet new grpc -o GrpcGreeter` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="a065a-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="a065a-115">Přidání gRPC služby do aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a065a-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="a065a-116">gRPC vyžaduje následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="a065a-116">gRPC requires the following packages:</span></span>

* [<span data-ttu-id="a065a-117">Grpc.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="a065a-117">Grpc.AspNetCore.Server</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server)
* <span data-ttu-id="a065a-118">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) protobuf zpráva rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="a065a-118">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) for protobuf message APIs.</span></span>
* [<span data-ttu-id="a065a-119">Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="a065a-119">Grpc.Tools</span></span>](https://www.nuget.org/packages/Grpc.Tools/)

### <a name="configure-grpc"></a><span data-ttu-id="a065a-120">Konfigurace gRPC</span><span class="sxs-lookup"><span data-stu-id="a065a-120">Configure gRPC</span></span>

<span data-ttu-id="a065a-121">gRPC je povolená s `AddGrpc` metody:</span><span class="sxs-lookup"><span data-stu-id="a065a-121">gRPC is enabled with the `AddGrpc` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

<span data-ttu-id="a065a-122">Každá služba gRPC se přidá do kanálu směrování prostřednictvím `MapGrpcService` metody:</span><span class="sxs-lookup"><span data-stu-id="a065a-122">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

<span data-ttu-id="a065a-123">Funkce a ASP.NET Core middlewares sdílet směrování kanálu, proto aplikace může být nakonfigurován tak, aby obslužné rutiny další požadavek.</span><span class="sxs-lookup"><span data-stu-id="a065a-123">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="a065a-124">Obslužné rutiny dalších požadavků, jako jsou řadiče MVC pracovat souběžně s nakonfigurovanou gRPC služby.</span><span class="sxs-lookup"><span data-stu-id="a065a-124">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="a065a-125">Integrace s rozhraními API pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a065a-125">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="a065a-126">gRPC služby, jako mají plný přístup k funkcím ASP.NET Core [injektáž závislostí](xref:fundamentals/dependency-injection) (DI) a [protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a065a-126">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a065a-127">Implementace služby například můžete vyřešit služby protokoly z kontejnerů DI prostřednictvím konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="a065a-127">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="a065a-128">Ve výchozím nastavení lze vyřešit implementace služby gRPC dalším službám DI pomocí jakékoli životnost (Singleton, obor nebo přechodným).</span><span class="sxs-lookup"><span data-stu-id="a065a-128">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="a065a-129">Vyřešit HttpContext gRPC metod</span><span class="sxs-lookup"><span data-stu-id="a065a-129">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="a065a-130">GRPC rozhraní API poskytuje přístup k některým datům zprávy HTTP/2, jako je metoda, hostitele, záhlaví a přípojných.</span><span class="sxs-lookup"><span data-stu-id="a065a-130">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="a065a-131">Přístup je prostřednictvím `ServerCallContext` argument předaný metodě každou gRPC:</span><span class="sxs-lookup"><span data-stu-id="a065a-131">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="a065a-132">`ServerCallContext` neposkytuje úplný přístup k `HttpContext` v všechna rozhraní API technologie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="a065a-132">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="a065a-133">`GetHttpContext` – Metoda rozšíření poskytuje úplný přístup ke `HttpContext` představující základní zprávy HTTP/2 v rozhraní API technologie ASP.NET:</span><span class="sxs-lookup"><span data-stu-id="a065a-133">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="a065a-134">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a065a-134">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
