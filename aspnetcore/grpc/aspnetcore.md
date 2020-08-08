---
title: Služby gRPC s ASP.NET Core
author: juntaoluo
description: Seznamte se se základními pojmy při psaní služeb gRPC pomocí ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
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
uid: grpc/aspnetcore
ms.openlocfilehash: bd97ece1d42b1b90d0b8740e33924d80b91bf49a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016437"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="10bb7-103">Služby gRPC s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10bb7-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="10bb7-104">Tento dokument ukazuje, jak začít s gRPC službami pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="10bb7-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="10bb7-105">Požadavky</span><span class="sxs-lookup"><span data-stu-id="10bb7-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="10bb7-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10bb7-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="10bb7-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="10bb7-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="10bb7-108">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="10bb7-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="10bb7-109">Začínáme se službou gRPC v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10bb7-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="10bb7-110">[Zobrazit nebo stáhnout vzorový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="10bb7-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="10bb7-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="10bb7-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="10bb7-112">Podrobné pokyny k vytvoření projektu gRPC najdete v tématu Začínáme [se službou gRPC Services](xref:tutorials/grpc/grpc-start) .</span><span class="sxs-lookup"><span data-stu-id="10bb7-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="10bb7-113">Visual Studio Code/Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="10bb7-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="10bb7-114">Spusťte `dotnet new grpc -o GrpcGreeter` příkaz z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="10bb7-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="10bb7-115">Přidání služeb gRPC Services do aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10bb7-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="10bb7-116">gRPC vyžaduje balíček [gRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) .</span><span class="sxs-lookup"><span data-stu-id="10bb7-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="10bb7-117">Konfigurace gRPC</span><span class="sxs-lookup"><span data-stu-id="10bb7-117">Configure gRPC</span></span>

<span data-ttu-id="10bb7-118">V *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="10bb7-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="10bb7-119">gRPC je povoleno s `AddGrpc` metodou.</span><span class="sxs-lookup"><span data-stu-id="10bb7-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="10bb7-120">Každá služba gRPC je prostřednictvím metody přidána do kanálu směrování `MapGrpcService` .</span><span class="sxs-lookup"><span data-stu-id="10bb7-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="10bb7-121">ASP.NET Core middlewarů a funkcí sdílí kanál směrování, proto je možné aplikaci nakonfigurovat tak, aby poskytovala další obslužné rutiny žádostí.</span><span class="sxs-lookup"><span data-stu-id="10bb7-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="10bb7-122">Další obslužné rutiny žádostí, jako jsou například řadiče MVC, pracují paralelně s nakonfigurovanými gRPC službami.</span><span class="sxs-lookup"><span data-stu-id="10bb7-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="10bb7-123">Konfigurace Kestrel</span><span class="sxs-lookup"><span data-stu-id="10bb7-123">Configure Kestrel</span></span>

<span data-ttu-id="10bb7-124">Koncové body Kestrel gRPC:</span><span class="sxs-lookup"><span data-stu-id="10bb7-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="10bb7-125">Vyžadovat HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="10bb7-125">Require HTTP/2.</span></span>
* <span data-ttu-id="10bb7-126">By měl být zabezpečený pomocí [protokolu TLS (Transport Layer Security)](https://tools.ietf.org/html/rfc5246).</span><span class="sxs-lookup"><span data-stu-id="10bb7-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="10bb7-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="10bb7-127">HTTP/2</span></span>

<span data-ttu-id="10bb7-128">gRPC vyžaduje HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="10bb7-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="10bb7-129">gRPC pro ASP.NET Core ověří [HttpRequest. protokol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) je `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="10bb7-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="10bb7-130">Kestrel [podporuje HTTP/2](xref:fundamentals/servers/kestrel#http2-support) na většině moderních operačních systémů.</span><span class="sxs-lookup"><span data-stu-id="10bb7-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="10bb7-131">Ve výchozím nastavení jsou Kestrel koncové body konfigurovány pro podporu připojení HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="10bb7-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="10bb7-132">TLS</span><span class="sxs-lookup"><span data-stu-id="10bb7-132">TLS</span></span>

<span data-ttu-id="10bb7-133">Koncové body Kestrel použité pro gRPC by měly být zabezpečené pomocí protokolu TLS.</span><span class="sxs-lookup"><span data-stu-id="10bb7-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="10bb7-134">Ve vývojovém prostředí je koncový bod zabezpečený pomocí protokolu TLS automaticky vytvořen v době `https://localhost:5001` , kdy je k dispozici ASP.NET Core vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="10bb7-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="10bb7-135">Není nutná žádná konfigurace.</span><span class="sxs-lookup"><span data-stu-id="10bb7-135">No configuration is required.</span></span> <span data-ttu-id="10bb7-136">`https`Předpona ověří koncový bod Kestrel pomocí protokolu TLS.</span><span class="sxs-lookup"><span data-stu-id="10bb7-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="10bb7-137">V produkčním prostředí musí být protokol TLS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="10bb7-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="10bb7-138">V následujících *appsettings.jsv* tomto příkladu je k dispozici koncový bod HTTP/2 zabezpečený pomocí protokolu TLS:</span><span class="sxs-lookup"><span data-stu-id="10bb7-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="10bb7-139">Alternativně lze v *program.cs*nakonfigurovat koncové body Kestrel:</span><span class="sxs-lookup"><span data-stu-id="10bb7-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="10bb7-140">Vyjednávání protokolu</span><span class="sxs-lookup"><span data-stu-id="10bb7-140">Protocol negotiation</span></span>

<span data-ttu-id="10bb7-141">Protokol TLS se používá pro více než zabezpečení komunikace.</span><span class="sxs-lookup"><span data-stu-id="10bb7-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="10bb7-142">Metoda handshake [protokolu TLS (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) se používá k vyjednání protokolu připojení mezi klientem a serverem, když koncový bod podporuje více protokolů.</span><span class="sxs-lookup"><span data-stu-id="10bb7-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="10bb7-143">Toto vyjednávání určuje, zda připojení používá protokol HTTP/1.1 nebo HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="10bb7-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="10bb7-144">Pokud je koncový bod HTTP/2 nakonfigurovaný bez TLS, musí být [ListenOptions. Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) koncového bodu nastaven na `HttpProtocols.Http2` .</span><span class="sxs-lookup"><span data-stu-id="10bb7-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="10bb7-145">Koncový bod s více protokoly (například `HttpProtocols.Http1AndHttp2` ) nelze použít bez TLS, protože neexistuje žádné vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="10bb7-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there is no negotiation.</span></span> <span data-ttu-id="10bb7-146">Všechna připojení k nezabezpečenému koncovému bodu ve výchozím nastavení HTTP/1.1 a volání gRPC selžou.</span><span class="sxs-lookup"><span data-stu-id="10bb7-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="10bb7-147">Další informace o povolení HTTP/2 a TLS s Kestrel najdete v tématu [Konfigurace koncového bodu Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="10bb7-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="10bb7-148">macOS nepodporuje ASP.NET Core gRPC s protokolem TLS.</span><span class="sxs-lookup"><span data-stu-id="10bb7-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="10bb7-149">K úspěšnému spuštění gRPC služeb na macOS se vyžaduje další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="10bb7-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="10bb7-150">Další informace najdete v tématu [nepovedlo se spustit aplikaci ASP.NET Core gRPC v MacOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="10bb7-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="10bb7-151">Integrace s rozhraními API ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10bb7-151">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="10bb7-152">Služba gRPC Services má plný přístup k funkcím ASP.NET Core, jako je například [vkládání závislostí](xref:fundamentals/dependency-injection) (di) a [protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="10bb7-152">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="10bb7-153">Například implementace služby může přeložit službu protokolovacího nástroje z kontejneru DI prostřednictvím konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="10bb7-153">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="10bb7-154">Ve výchozím nastavení může implementace služby gRPC vyřešit jiné služby DI Services s jakoukoli životností (singleton, vymezený nebo přechodný).</span><span class="sxs-lookup"><span data-stu-id="10bb7-154">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="10bb7-155">Vyřešit HttpContext v metodách gRPC</span><span class="sxs-lookup"><span data-stu-id="10bb7-155">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="10bb7-156">Rozhraní gRPC API poskytuje přístup k některým datům zprávy HTTP/2, jako je například metoda, hostitel, hlavička a přípojná část.</span><span class="sxs-lookup"><span data-stu-id="10bb7-156">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="10bb7-157">Přístup je prostřednictvím `ServerCallContext` argumentu předaného pro každou metodu gRPC:</span><span class="sxs-lookup"><span data-stu-id="10bb7-157">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="10bb7-158">`ServerCallContext`neposkytuje úplný přístup ke `HttpContext` všem rozhraním api ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="10bb7-158">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="10bb7-159">`GetHttpContext`Metoda rozšíření poskytuje úplný přístup k `HttpContext` reprezentující základní zprávu HTTP/2 v rozhraních API ASP.NET:</span><span class="sxs-lookup"><span data-stu-id="10bb7-159">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="10bb7-160">Další materiály</span><span class="sxs-lookup"><span data-stu-id="10bb7-160">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
