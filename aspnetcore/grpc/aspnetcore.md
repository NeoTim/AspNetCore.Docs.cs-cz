---
title: Služby gRPC s ASP.NET Core
author: juntaoluo
description: Naučte se základní pojmy při psaní služeb gRPC s ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 6107704a4b4d9c629a7abe907efd5b1932019130
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667627"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="ab5a5-103">Služby gRPC s ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab5a5-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="ab5a5-104">Tento dokument ukazuje, jak začít se službami gRPC pomocí ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ab5a5-105">Požadavky</span><span class="sxs-lookup"><span data-stu-id="ab5a5-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ab5a5-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ab5a5-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ab5a5-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ab5a5-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ab5a5-108">Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ab5a5-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="ab5a5-109">Začínáme se službou gRPC v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab5a5-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="ab5a5-110">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([jak stáhnout](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ab5a5-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ab5a5-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ab5a5-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ab5a5-112">Podrobné pokyny k vytvoření projektu gRPC najdete v tématu [Začínáme se službami gRPC.](xref:tutorials/grpc/grpc-start)</span><span class="sxs-lookup"><span data-stu-id="ab5a5-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ab5a5-113">Visual Studio Code / Visual Studio pro Mac</span><span class="sxs-lookup"><span data-stu-id="ab5a5-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ab5a5-114">Spusťte `dotnet new grpc -o GrpcGreeter` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="ab5a5-115">Přidání služeb gRPC do aplikace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ab5a5-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="ab5a5-116">gRPC vyžaduje balíček [Grpc.AspNetCore.](https://www.nuget.org/packages/Grpc.AspNetCore)</span><span class="sxs-lookup"><span data-stu-id="ab5a5-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="ab5a5-117">Konfigurace gRPC</span><span class="sxs-lookup"><span data-stu-id="ab5a5-117">Configure gRPC</span></span>

<span data-ttu-id="ab5a5-118">V *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ab5a5-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="ab5a5-119">gRPC je s `AddGrpc` metodou povolena.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="ab5a5-120">Každá služba gRPC je přidána `MapGrpcService` do kanálu směrování prostřednictvím metody.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="ab5a5-121">ASP.NET základní middlewares a funkce sdílet směrování kanálu, proto aplikace může být nakonfigurován tak, aby sloužit další obslužné rutiny požadavku.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="ab5a5-122">Další obslužné rutiny požadavků, jako jsou řadiče MVC, pracují paralelně s nakonfigurovanými službami gRPC.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="ab5a5-123">Konfigurovat poštolky</span><span class="sxs-lookup"><span data-stu-id="ab5a5-123">Configure Kestrel</span></span>

<span data-ttu-id="ab5a5-124">Kestrel gRPC koncové body:</span><span class="sxs-lookup"><span data-stu-id="ab5a5-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="ab5a5-125">Vyžadovat HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-125">Require HTTP/2.</span></span>
* <span data-ttu-id="ab5a5-126">By měla být zabezpečena [pomocí zabezpečení transportní vrstvy (TLS).](https://tools.ietf.org/html/rfc5246)</span><span class="sxs-lookup"><span data-stu-id="ab5a5-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="ab5a5-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="ab5a5-127">HTTP/2</span></span>

<span data-ttu-id="ab5a5-128">gRPC vyžaduje HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="ab5a5-129">gRPC pro ASP.NET Core ověřuje [httprequest.protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) je `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="ab5a5-130">Kestrel [podporuje HTTP/2](xref:fundamentals/servers/kestrel#http2-support) na většině moderních operačních systémů.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="ab5a5-131">Koncové body Kestrel jsou ve výchozím nastavení nakonfigurovány tak, aby podporovaly připojení HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="ab5a5-132">TLS</span><span class="sxs-lookup"><span data-stu-id="ab5a5-132">TLS</span></span>

<span data-ttu-id="ab5a5-133">Kestrelové koncové body používané pro gRPC by měly být zajištěny TLS.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="ab5a5-134">Ve vývoji koncový bod zabezpečený tls je automaticky vytvořen při `https://localhost:5001` ASP.NET je k dispozici certifikát vývoje jádra.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="ab5a5-135">Není nutná žádná konfigurace.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-135">No configuration is required.</span></span> <span data-ttu-id="ab5a5-136">Předpona `https` ověří koncový bod Kestrel používá TLS.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="ab5a5-137">V produkčním prostředí musí být tls explicitně nakonfigurován.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="ab5a5-138">V následujícím příkladu *appsettings.json* je k dispozici koncový bod HTTP/2 zabezpečený pomocí tls:</span><span class="sxs-lookup"><span data-stu-id="ab5a5-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="ab5a5-139">Případně kestrel koncové body lze nakonfigurovat v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ab5a5-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="ab5a5-140">Vyjednávání protokolu</span><span class="sxs-lookup"><span data-stu-id="ab5a5-140">Protocol negotiation</span></span>

<span data-ttu-id="ab5a5-141">TLS se používá pro více než zajištění komunikace.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="ab5a5-142">Handshake protokolu [tls aplikační vrstvy (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) se používá k vyjednání protokolu připojení mezi klientem a serverem, pokud koncový bod podporuje více protokolů.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="ab5a5-143">Toto vyjednávání určuje, zda připojení používá HTTP/1.1 nebo HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="ab5a5-144">Pokud je koncový bod HTTP/2 nakonfigurován bez protokolu TLS, musí být `HttpProtocols.Http2`protokol [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) koncového bodu nastaven na .</span><span class="sxs-lookup"><span data-stu-id="ab5a5-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="ab5a5-145">Koncový bod s více protokoly `HttpProtocols.Http1AndHttp2`(například) nelze použít bez TLS, protože neexistuje žádné vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there is no negotiation.</span></span> <span data-ttu-id="ab5a5-146">Všechna připojení k nezabezpečenému koncovému bodu výchozí HTTP/1.1 a gRPC volání nezdaří.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="ab5a5-147">Další informace o povolení HTTP/2 a TLS s Kestrel, viz [Kestrel konfigurace koncového bodu](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="ab5a5-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="ab5a5-148">macOS nepodporuje ASP.NET Core gRPC s TLS.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="ab5a5-149">K úspěšnému spuštění služeb gRPC v systému macOS je nutná další konfigurace.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="ab5a5-150">Další informace najdete [v tématu Nelze spustit ASP.NET aplikaci Core gRPC v systému macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="ab5a5-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="ab5a5-151">Integrace s ASP.NET základními API</span><span class="sxs-lookup"><span data-stu-id="ab5a5-151">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="ab5a5-152">služby gRPC mají plný přístup k ASP.NET core funkce, jako je [vkládání závislostí](xref:fundamentals/dependency-injection) (DI) a [protokolování](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="ab5a5-152">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="ab5a5-153">Implementace služby může například vyřešit službu protokolování z kontejneru DI prostřednictvím konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="ab5a5-153">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="ab5a5-154">Ve výchozím nastavení může implementace služby gRPC vyřešit další služby DI s libovolnou životností (Singleton, Scoped nebo Transient).</span><span class="sxs-lookup"><span data-stu-id="ab5a5-154">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="ab5a5-155">Vyřešit httpcontext v metodách gRPC</span><span class="sxs-lookup"><span data-stu-id="ab5a5-155">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="ab5a5-156">Rozhraní gRPC API poskytuje přístup k některým datům zpráv HTTP/2, jako je metoda, hostitel, záhlaví a přívěsy.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-156">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="ab5a5-157">Přístup je `ServerCallContext` prostřednictvím argumentu předaný každé metodě gRPC:</span><span class="sxs-lookup"><span data-stu-id="ab5a5-157">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="ab5a5-158">`ServerCallContext`neposkytuje úplný přístup `HttpContext` ke všem ASP.NET api.</span><span class="sxs-lookup"><span data-stu-id="ab5a5-158">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="ab5a5-159">Metoda `GetHttpContext` rozšíření poskytuje úplný `HttpContext` přístup k představující základní zprávu HTTP/2 v ASP.NET API:</span><span class="sxs-lookup"><span data-stu-id="ab5a5-159">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="ab5a5-160">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ab5a5-160">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
