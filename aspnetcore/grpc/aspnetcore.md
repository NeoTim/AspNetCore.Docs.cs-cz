---
title: Služby gRPC s ASP.NET Core
author: juntaoluo
description: Seznamte se se základními pojmy při psaní služeb gRPC pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/07/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 26f0d7610151460967b97665ed61deab1ef56d68
ms.sourcegitcommit: 2719c70cd15a430479ab4007ff3e197fbf5dfee0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68862929"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="18ef5-103">Služby gRPC s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18ef5-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="18ef5-104">Tento dokument ukazuje, jak začít s gRPC službami pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="18ef5-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="18ef5-105">Požadavky</span><span class="sxs-lookup"><span data-stu-id="18ef5-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="18ef5-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18ef5-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="18ef5-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="18ef5-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="18ef5-108">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="18ef5-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="18ef5-109">Začínáme se službou gRPC v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18ef5-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="18ef5-110">[Zobrazit nebo stáhnout vzorový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([stažení](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="18ef5-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="18ef5-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="18ef5-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="18ef5-112">Podrobné pokyny k vytvoření projektu gRPC najdete v tématu Začínáme [se službou gRPC Services](xref:tutorials/grpc/grpc-start) .</span><span class="sxs-lookup"><span data-stu-id="18ef5-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="18ef5-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="18ef5-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="18ef5-114">Spusťte `dotnet new grpc -o GrpcGreeter` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="18ef5-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="18ef5-115">Přidání služeb gRPC Services do aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18ef5-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="18ef5-116">gRPC vyžaduje balíček [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="18ef5-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="18ef5-117">Konfigurace gRPC</span><span class="sxs-lookup"><span data-stu-id="18ef5-117">Configure gRPC</span></span>

<span data-ttu-id="18ef5-118">gRPC je povoleno s `AddGrpc` metodou:</span><span class="sxs-lookup"><span data-stu-id="18ef5-118">gRPC is enabled with the `AddGrpc` method:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

<span data-ttu-id="18ef5-119">Každá služba gRPC se přidá do kanálu směrování prostřednictvím `MapGrpcService` metody:</span><span class="sxs-lookup"><span data-stu-id="18ef5-119">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

<span data-ttu-id="18ef5-120">ASP.NET Core middlewarů a funkcí sdílí kanál směrování, proto je možné aplikaci nakonfigurovat tak, aby poskytovala další obslužné rutiny žádostí.</span><span class="sxs-lookup"><span data-stu-id="18ef5-120">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="18ef5-121">Další obslužné rutiny žádostí, jako jsou například řadiče MVC, pracují paralelně s nakonfigurovanými gRPC službami.</span><span class="sxs-lookup"><span data-stu-id="18ef5-121">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="18ef5-122">Integrace s rozhraními API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18ef5-122">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="18ef5-123">Služba gRPC Services má plný přístup k funkcím ASP.NET Core, jako je například [vkládání závislostí](xref:fundamentals/dependency-injection) (di) a [protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="18ef5-123">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="18ef5-124">Například implementace služby může přeložit službu protokolovacího nástroje z kontejneru DI prostřednictvím konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="18ef5-124">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="18ef5-125">Ve výchozím nastavení může implementace služby gRPC vyřešit jiné služby DI Services s jakoukoli životností (singleton, vymezený nebo přechodný).</span><span class="sxs-lookup"><span data-stu-id="18ef5-125">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="18ef5-126">Vyřešit HttpContext v metodách gRPC</span><span class="sxs-lookup"><span data-stu-id="18ef5-126">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="18ef5-127">Rozhraní gRPC API poskytuje přístup k některým datům zprávy HTTP/2, jako je například metoda, hostitel, hlavička a přípojná část.</span><span class="sxs-lookup"><span data-stu-id="18ef5-127">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="18ef5-128">Přístup je prostřednictvím `ServerCallContext` argumentu předaného pro každou metodu gRPC:</span><span class="sxs-lookup"><span data-stu-id="18ef5-128">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="18ef5-129">`ServerCallContext`neposkytuje úplný přístup ke `HttpContext` všem rozhraním API ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="18ef5-129">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="18ef5-130">Metoda rozšíření poskytuje úplný přístup `HttpContext` k reprezentující základní zprávu HTTP/2 v rozhraních API ASP.NET: `GetHttpContext`</span><span class="sxs-lookup"><span data-stu-id="18ef5-130">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="grpc-and-aspnet-core-on-macos"></a><span data-ttu-id="18ef5-131">gRPC a ASP.NET Core v macOS</span><span class="sxs-lookup"><span data-stu-id="18ef5-131">gRPC and ASP.NET Core on macOS</span></span>

<span data-ttu-id="18ef5-132">Kestrel nepodporuje protokol HTTP/2 se [zabezpečením TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246) na MacOS.</span><span class="sxs-lookup"><span data-stu-id="18ef5-132">Kestrel doesn't support HTTP/2 with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) on macOS.</span></span> <span data-ttu-id="18ef5-133">ASP.NET Core šablona a ukázky gRPC standardně používají protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="18ef5-133">The ASP.NET Core gRPC template and samples use TLS by default.</span></span> <span data-ttu-id="18ef5-134">Při pokusu o spuštění serveru gRPC se zobrazí následující chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="18ef5-134">You'll see the following error message when you attempt to start the gRPC server:</span></span>

> <span data-ttu-id="18ef5-135">Nedá se vytvořit vazba https://localhost:5001 na rozhraní zpětné smyčky IPv4: HTTP/2 přes TLS se v macOS nepodporuje kvůli chybějící podpoře ALPN.</span><span class="sxs-lookup"><span data-stu-id="18ef5-135">Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'.</span></span>

<span data-ttu-id="18ef5-136">Pokud chcete tento problém obejít, nakonfigurujte Kestrel a klienta gRPC na použití HTTP/2 **bez** TLS.</span><span class="sxs-lookup"><span data-stu-id="18ef5-136">To work around this issue, configure Kestrel and the gRPC client to use HTTP/2 **without** TLS.</span></span> <span data-ttu-id="18ef5-137">To byste měli udělat jenom během vývoje.</span><span class="sxs-lookup"><span data-stu-id="18ef5-137">You should only do this during development.</span></span> <span data-ttu-id="18ef5-138">Nepoužíváte-li protokol TLS, budou zasílány zprávy gRPC bez šifrování.</span><span class="sxs-lookup"><span data-stu-id="18ef5-138">Not using TLS will result in gRPC messages being sent without encryption.</span></span>

<span data-ttu-id="18ef5-139">Kestrel musí nakonfigurovat koncový bod HTTP/2 bez TLS v `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="18ef5-139">Kestrel must configure a HTTP/2 endpoint without TLS in `Program.cs`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="18ef5-140">Klient gRPC musí na adrese serveru `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` nastavit přepínač `true` a použít `http` ho:</span><span class="sxs-lookup"><span data-stu-id="18ef5-140">The gRPC client must set the `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` switch to `true` and use `http` in the server address:</span></span>

```csharp
// This switch must be set before creating the HttpClient.
AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

var httpClient = new HttpClient();
// The port number(5000) must match the port of the gRPC server.
httpClient.BaseAddress = new Uri("http://localhost:5000");
var client = GrpcClient.Create<Greeter.GreeterClient>(httpClient);
```

> [!WARNING]
> <span data-ttu-id="18ef5-141">HTTP/2 bez protokolu TLS by se mělo používat jenom při vývoji aplikací.</span><span class="sxs-lookup"><span data-stu-id="18ef5-141">HTTP/2 without TLS should only be used during app development.</span></span> <span data-ttu-id="18ef5-142">Provozní aplikace by měly vždy používat zabezpečení přenosu.</span><span class="sxs-lookup"><span data-stu-id="18ef5-142">Production applications should always use transport security.</span></span> <span data-ttu-id="18ef5-143">Další informace najdete v tématu věnovaném [hlediskům zabezpečení v gRPC pro ASP.NET Core](xref:grpc/security#transport-security).</span><span class="sxs-lookup"><span data-stu-id="18ef5-143">For more information, see [Security considerations in gRPC for ASP.NET Core](xref:grpc/security#transport-security).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="18ef5-144">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="18ef5-144">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
