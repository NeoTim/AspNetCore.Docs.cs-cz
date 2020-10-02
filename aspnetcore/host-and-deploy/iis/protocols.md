---
title: Použití ASP.NET Core s HTTP/2 na IIS
author: rick-anderson
description: Naučte se používat funkce HTTP/2 ve službě IIS.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 1c3748eeead1b8cccdb6112150cf90fb5597b689
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654367"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a><span data-ttu-id="4cbf7-103">Použití ASP.NET Core s HTTP/2 na IIS</span><span class="sxs-lookup"><span data-stu-id="4cbf7-103">Use ASP.NET Core with HTTP/2 on IIS</span></span>

<span data-ttu-id="4cbf7-104">Od [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="4cbf7-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="4cbf7-105">[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení služby IIS:</span><span class="sxs-lookup"><span data-stu-id="4cbf7-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="4cbf7-106">Windows Server 2016 nebo novější/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="4cbf7-106">Windows Server 2016 or later / Windows 10 or later</span></span>
* <span data-ttu-id="4cbf7-107">IIS 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="4cbf7-107">IIS 10 or later</span></span>
* <span data-ttu-id="4cbf7-108">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="4cbf7-108">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="4cbf7-109">Při [hostování mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model): připojení hraničního serveru s přístupem k serveru používá protokol HTTP/2, ale připojení reverzního proxy [serveru Kestrel](xref:fundamentals/servers/kestrel) používá protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="4cbf7-109">When [hosting out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model): Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>

<span data-ttu-id="4cbf7-110">Pro nasazení v rámci procesu, když je vytvořeno připojení HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="4cbf7-110">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="4cbf7-111">Pro nasazení mimo procesy, když je navázáno připojení HTTP/2, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="4cbf7-111">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="4cbf7-112">Další informace o modelech hostování v procesu a mimoprocesové procesy naleznete v tématu <xref:host-and-deploy/aspnet-core-module> .</span><span class="sxs-lookup"><span data-stu-id="4cbf7-112">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="4cbf7-113">Protokol HTTP/2 je ve výchozím nastavení povolen pro připojení pomocí protokolu HTTPS/TLS.</span><span class="sxs-lookup"><span data-stu-id="4cbf7-113">HTTP/2 is enabled by default for HTTPS/TLS connections.</span></span> <span data-ttu-id="4cbf7-114">Pokud není navázáno připojení HTTP/2, připojení se vrátí k HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="4cbf7-114">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="4cbf7-115">Další informace o konfiguraci HTTP/2 v nasazeních služby IIS najdete v tématu [http/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="4cbf7-115">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="4cbf7-116">Rozšířené funkce protokolu HTTP/2 pro podporu gRPC</span><span class="sxs-lookup"><span data-stu-id="4cbf7-116">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="4cbf7-117">Další funkce HTTP/2 ve službě IIS podporují gRPC, včetně podpory pro přívěsy odpovědí a odesílání snímků pro resetování.</span><span class="sxs-lookup"><span data-stu-id="4cbf7-117">Additional HTTP/2 features in IIS support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="4cbf7-118">Požadavky na spuštění gRPC ve službě IIS:</span><span class="sxs-lookup"><span data-stu-id="4cbf7-118">Requirements to run gRPC on IIS:</span></span>

* <span data-ttu-id="4cbf7-119">Hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="4cbf7-119">In-process hosting.</span></span>
* <span data-ttu-id="4cbf7-120">Windows 10, Build operačního systému 20300,1000 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="4cbf7-120">Windows 10, OS Build 20300.1000 or later.</span></span> <span data-ttu-id="4cbf7-121">Může vyžadovat použití buildů Windows Insider.</span><span class="sxs-lookup"><span data-stu-id="4cbf7-121">May require use of Windows Insider Builds.</span></span>
* <span data-ttu-id="4cbf7-122">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="4cbf7-122">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="4cbf7-123">Karoseri</span><span class="sxs-lookup"><span data-stu-id="4cbf7-123">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="4cbf7-124">Resetovat</span><span class="sxs-lookup"><span data-stu-id="4cbf7-124">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
