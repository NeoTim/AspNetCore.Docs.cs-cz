---
title: Použití gRPC v prohlížečových aplikacích
author: jamesnk
description: Naučte se konfigurovat gRPC služby na ASP.NET Core, které se mají volat z aplikací pro prohlížeč pomocí gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 5c9501b3e7cbdcbb02e3d78d67185a0a75ccba7c
ms.sourcegitcommit: c9b03d8a6a4dcc59e4aacb30a691f349235a74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379403"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="dd4d7-103">Použití gRPC v prohlížečových aplikacích</span><span class="sxs-lookup"><span data-stu-id="dd4d7-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="dd4d7-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="dd4d7-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

 <span data-ttu-id="dd4d7-105">Naučte se konfigurovat existující službu ASP.NET Core gRPC, která se má volat z aplikací v prohlížeči, pomocí [GRPC webového](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protokolu.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-105">Learn how to configure an existing ASP.NET Core gRPC service to be callable from browser apps, using the [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protocol.</span></span> <span data-ttu-id="dd4d7-106">gRPC-web umožňuje prohlížeči JavaScript a Blazor aplikacím volat služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-106">gRPC-Web allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="dd4d7-107">Nemůžete volat službu gRPC HTTP/2 z aplikace založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-107">It's not possible to call an HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="dd4d7-108">služby gRPC hostované v ASP.NET Core můžou být nakonfigurované tak, aby podporovaly gRPC-web vedle HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-108">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span>


<span data-ttu-id="dd4d7-109">Pokyny k přidání služby gRPC do existující aplikace ASP.NET Core najdete v tématu [Přidání gRPC Services do aplikace ASP.NET Core](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span><span class="sxs-lookup"><span data-stu-id="dd4d7-109">For instructions on adding a gRPC service to an existing ASP.NET Core app, see [Add gRPC services to an ASP.NET Core app](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span></span>

<span data-ttu-id="dd4d7-110">Pokyny k vytvoření projektu gRPC naleznete v tématu <xref:tutorials/grpc/grpc-start> .</span><span class="sxs-lookup"><span data-stu-id="dd4d7-110">For instructions on creating a gRPC project, see <xref:tutorials/grpc/grpc-start>.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="dd4d7-111">gRPC – web ve ASP.NET Core vs. zástupné</span><span class="sxs-lookup"><span data-stu-id="dd4d7-111">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="dd4d7-112">Existují dvě možnosti, jak přidat gRPC-web do aplikace ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="dd4d7-112">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="dd4d7-113">Podpora gRPC-web společně s gRPC HTTP/2 v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-113">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="dd4d7-114">Tato možnost používá middleware poskytované `Grpc.AspNetCore.Web` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-114">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="dd4d7-115">Pomocí gRPC webu [zástupné proxy](https://www.envoyproxy.io/) můžete přeložit GRPC-web na gRPC http/2.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-115">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="dd4d7-116">Přeložené volání je pak předáno do aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-116">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="dd4d7-117">Existují odborníci a nevýhody pro každý přístup.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-117">There are pros and cons to each approach.</span></span> <span data-ttu-id="dd4d7-118">Pokud prostředí aplikace už používá zástupné jako proxy, může to mít smysl také použít zástupné k poskytování gRPC-web support.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-118">If an app's environment is already using Envoy as a proxy, it might make sense to also use Envoy to provide gRPC-Web support.</span></span> <span data-ttu-id="dd4d7-119">Pro základní řešení pro gRPC-web, které vyžaduje jenom ASP.NET Core, `Grpc.AspNetCore.Web` je vhodná volba.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-119">For a basic solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="dd4d7-120">Konfigurace gRPC-web v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dd4d7-120">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="dd4d7-121">služby gRPC hostované v ASP.NET Core můžou být nakonfigurované tak, aby podporovaly gRPC-web vedle HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-121">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="dd4d7-122">gRPC-web nevyžaduje žádné změny služeb.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-122">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="dd4d7-123">Jedinou úpravou je spuštění konfigurace.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-123">The only modification is startup configuration.</span></span>

<span data-ttu-id="dd4d7-124">Povolení gRPC-web pomocí služby ASP.NET Core gRPC:</span><span class="sxs-lookup"><span data-stu-id="dd4d7-124">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="dd4d7-125">Přidejte odkaz na balíček [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="dd4d7-125">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="dd4d7-126">Nakonfigurujte aplikaci tak, aby používala gRPC-web přidáním `UseGrpcWeb` a `EnableGrpcWeb` do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="dd4d7-126">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="dd4d7-127">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="dd4d7-127">The preceding code:</span></span>

* <span data-ttu-id="dd4d7-128">Přidá gRPC-web middleware, `UseGrpcWeb` po směrování a před koncovými body.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-128">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="dd4d7-129">Určuje metodu, která `endpoints.MapGrpcService<GreeterService>()` podporuje gRPC-web s `EnableGrpcWeb` .</span><span class="sxs-lookup"><span data-stu-id="dd4d7-129">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="dd4d7-130">Případně je možné nakonfigurovat middleware gRPC-web tak, aby všechny služby podporovaly gRPC-web ve výchozím nastavení a `EnableGrpcWeb` nejsou povinné.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-130">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="dd4d7-131">Určete, `new GrpcWebOptions { DefaultEnabled = true }` kdy se má middleware přidat.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-131">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="dd4d7-132">K dispozici je známý problém, který způsobí selhání gRPC-web při [hostování Http.sys](xref:fundamentals/servers/httpsys) v .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-132">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="dd4d7-133">[K dispozici je](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)alternativní řešení pro získání GRPC na webu na Http.sys.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-133">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="dd4d7-134">gRPC – web a CORS</span><span class="sxs-lookup"><span data-stu-id="dd4d7-134">gRPC-Web and CORS</span></span>

<span data-ttu-id="dd4d7-135">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-135">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="dd4d7-136">Toto omezení se vztahuje k vytváření gRPC webových volání s aplikacemi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-136">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="dd4d7-137">Například aplikace prohlížeče obsluhovaná aplikací `https://www.contoso.com` je blokována při volání gRPCch webových služeb hostovaných na `https://services.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="dd4d7-137">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="dd4d7-138">Pro zmírnění tohoto omezení lze použít sdílení prostředků mezi zdroji (CORS).</span><span class="sxs-lookup"><span data-stu-id="dd4d7-138">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="dd4d7-139">Aby mohla aplikace v prohlížeči dělat gRPC webová volání mezi zdroji, nastavte [CORS v ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="dd4d7-139">To allow a browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="dd4d7-140">Využijte integrovanou podporu CORS a vystavte hlavičky specifické pro gRPC <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> .</span><span class="sxs-lookup"><span data-stu-id="dd4d7-140">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="dd4d7-141">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="dd4d7-141">The preceding code:</span></span>

* <span data-ttu-id="dd4d7-142">Volání `AddCors` pro přidání služeb CORS a nakonfigurují zásady CORS, které zveřejňují gRPC konkrétní hlavičky.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-142">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="dd4d7-143">Volání `UseCors` pro přidání middlewaru CORS po směrování a před koncovými body.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-143">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="dd4d7-144">Určuje metodu, která `endpoints.MapGrpcService<GreeterService>()` podporuje CORS s `RequiresCors` .</span><span class="sxs-lookup"><span data-stu-id="dd4d7-144">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

### <a name="grpc-web-and-streaming"></a><span data-ttu-id="dd4d7-145">gRPC – web a streamování</span><span class="sxs-lookup"><span data-stu-id="dd4d7-145">gRPC-Web and streaming</span></span>

<span data-ttu-id="dd4d7-146">Tradiční gRPC přes HTTP/2 podporuje streamování ve všech směrech.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-146">Traditional gRPC over HTTP/2 supports streaming in all directions.</span></span> <span data-ttu-id="dd4d7-147">gRPC – web nabízí omezené podpory pro streamování:</span><span class="sxs-lookup"><span data-stu-id="dd4d7-147">gRPC-Web offers limited support for streaming:</span></span>

* <span data-ttu-id="dd4d7-148">gRPC – klienti webového prohlížeče nepodporují volání metod streamování klientů a obousměrného streamování.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-148">gRPC-Web browser clients don't support calling client streaming and bidirectional streaming methods.</span></span>
* <span data-ttu-id="dd4d7-149">ASP.NET Core služby gRPC hostované v Azure App Service a IIS nepodporují obousměrný streamování.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-149">ASP.NET Core gRPC services hosted on Azure App Service and IIS don't support bidirectional streaming.</span></span>

<span data-ttu-id="dd4d7-150">Při použití gRPC-web doporučujeme použít jenom unární metody a metody streamování serveru.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-150">When using gRPC-Web, we only recommend the use of unary methods and server streaming methods.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="dd4d7-151">Volání gRPC-web z prohlížeče</span><span class="sxs-lookup"><span data-stu-id="dd4d7-151">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="dd4d7-152">Aplikace prohlížeče můžou pomocí gRPC-web volat služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-152">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="dd4d7-153">Existují některé požadavky a omezení při volání služeb gRPC Services pomocí gRPC-web z prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="dd4d7-153">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="dd4d7-154">Server musí být nakonfigurovaný tak, aby podporoval gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-154">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="dd4d7-155">Volání streamování klientů a obousměrného streamování nejsou podporovaná.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-155">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="dd4d7-156">Streamování serveru je podporované.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-156">Server streaming is supported.</span></span>
* <span data-ttu-id="dd4d7-157">Volání služeb gRPC Services v jiné doméně vyžaduje, aby na serveru byla nakonfigurovaná [CORS](xref:security/cors) .</span><span class="sxs-lookup"><span data-stu-id="dd4d7-157">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="dd4d7-158">JavaScript gRPC – webový klient</span><span class="sxs-lookup"><span data-stu-id="dd4d7-158">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="dd4d7-159">K dispozici je gRPC JavaScript-Web Client.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-159">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="dd4d7-160">Pokyny, jak používat gRPC-web z JavaScriptu, najdete v tématu [Zápis kódu klienta JavaScript pomocí gRPC-web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="dd4d7-160">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="dd4d7-161">Konfigurace gRPC-web pomocí klienta .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="dd4d7-161">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="dd4d7-162">Klient .NET gRPC se dá nakonfigurovat tak, aby gRPC webová volání.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-162">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="dd4d7-163">To je užitečné pro [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) aplikace, které jsou hostované v prohlížeči a mají stejná omezení http kódu JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-163">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="dd4d7-164">Volání gRPC-web s klientem .NET je [stejné jako http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="dd4d7-164">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="dd4d7-165">Jedinou úpravou je způsob vytvoření kanálu.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-165">The only modification is how the channel is created.</span></span>

<span data-ttu-id="dd4d7-166">Použití gRPC-web:</span><span class="sxs-lookup"><span data-stu-id="dd4d7-166">To use gRPC-Web:</span></span>

* <span data-ttu-id="dd4d7-167">Přidejte odkaz na balíček [Grpc .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="dd4d7-167">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="dd4d7-168">Zajistěte, aby byl odkaz na balíček [Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) 2.29.0 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-168">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="dd4d7-169">Nakonfigurujte kanál tak, aby používal `GrpcWebHandler` :</span><span class="sxs-lookup"><span data-stu-id="dd4d7-169">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="dd4d7-170">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="dd4d7-170">The preceding code:</span></span>

* <span data-ttu-id="dd4d7-171">Nakonfiguruje kanál tak, aby používal gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-171">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="dd4d7-172">Vytvoří klienta a provede volání pomocí kanálu.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-172">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="dd4d7-173">`GrpcWebHandler` má následující možnosti konfigurace:</span><span class="sxs-lookup"><span data-stu-id="dd4d7-173">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="dd4d7-174">**InnerHandler**: základní <xref:System.Net.Http.HttpMessageHandler> , který vytváří požadavek gRPC http, například `HttpClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="dd4d7-174">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="dd4d7-175">**GrpcWebMode**: typ výčtu, který určuje, zda je GRPC požadavek `Content-Type` http `application/grpc-web` nebo `application/grpc-web-text` .</span><span class="sxs-lookup"><span data-stu-id="dd4d7-175">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="dd4d7-176">`GrpcWebMode.GrpcWeb` Konfiguruje obsah, který se má odeslat bez kódování.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-176">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="dd4d7-177">Výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-177">Default value.</span></span>
    * <span data-ttu-id="dd4d7-178">`GrpcWebMode.GrpcWebText` Konfiguruje obsah tak, aby byl kódovaný v kódování Base64.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-178">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="dd4d7-179">Vyžaduje se pro volání streamování serveru v prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-179">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="dd4d7-180">**HttpVersion**: protokol HTTP `Version` použitý k nastavení [zprávy HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) na podkladové žádosti HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-180">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="dd4d7-181">gRPC-web nevyžaduje konkrétní verzi a nepřepisuje výchozí, pokud není zadaný.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-181">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="dd4d7-182">Vygenerované klienty gRPC mají synchronizační a asynchronní metody pro volání unárních metod.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-182">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="dd4d7-183">Například `SayHello` je synchronizován a `SayHelloAsync` je asynchronní.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-183">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="dd4d7-184">Volání metody synchronizace v Blazor WebAssembly aplikaci způsobí, že aplikace přestane reagovat.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-184">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="dd4d7-185">Asynchronní metody musí být vždy použity v Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="dd4d7-185">Async methods must always be used in Blazor WebAssembly.</span></span>

### <a name="use-grpc-client-factory-with-grpc-web"></a><span data-ttu-id="dd4d7-186">Použití klienta gRPC Client Factory s gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="dd4d7-186">Use gRPC client factory with gRPC-Web</span></span>

<span data-ttu-id="dd4d7-187">.NET Client kompatibilní s gRPC se dají vytvořit pomocí integrace gRPC s [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="dd4d7-187">A gRPC-Web compatible .NET client can be created using gRPC's integration with [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory).</span></span>

<span data-ttu-id="dd4d7-188">Použití gRPC-web s klientskou továrnou:</span><span class="sxs-lookup"><span data-stu-id="dd4d7-188">To use gRPC-Web with client factory:</span></span>

* <span data-ttu-id="dd4d7-189">Přidejte do souboru projektu odkazy balíčků pro následující balíčky:</span><span class="sxs-lookup"><span data-stu-id="dd4d7-189">Add package references to the project file for the following packages:</span></span>
  * [<span data-ttu-id="dd4d7-190">Grpc .NET. Client. Web</span><span class="sxs-lookup"><span data-stu-id="dd4d7-190">Grpc.Net.Client.Web</span></span>](https://www.nuget.org/packages/Grpc.Net.Client.Web)
  * [<span data-ttu-id="dd4d7-191">Grpc .NET. ClientFactory</span><span class="sxs-lookup"><span data-stu-id="dd4d7-191">Grpc.Net.ClientFactory</span></span>](https://www.nuget.org/packages/Grpc.Net.ClientFactory)
* <span data-ttu-id="dd4d7-192">Zaregistrujte klienta gRPC se vkládáním závislostí (DI) pomocí obecné `AddGrpcClient` metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-192">Register a gRPC client with dependency injection (DI) using the generic `AddGrpcClient` extension method.</span></span> <span data-ttu-id="dd4d7-193">V Blazor WebAssembly aplikaci jsou služby zaregistrované v di v `Program.cs` .</span><span class="sxs-lookup"><span data-stu-id="dd4d7-193">In a Blazor WebAssembly app, services are registered with DI in `Program.cs`.</span></span>
* <span data-ttu-id="dd4d7-194">Nakonfigurujte `GrpcWebHandler` pomocí <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-194">Configure `GrpcWebHandler` using the <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> extension method.</span></span>

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>((services, options) =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(GrpcWebMode.GrpcWebText, new HttpClientHandler()));
```

<span data-ttu-id="dd4d7-195">Další informace naleznete v tématu <xref:grpc/clientfactory>.</span><span class="sxs-lookup"><span data-stu-id="dd4d7-195">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dd4d7-196">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="dd4d7-196">Additional resources</span></span>

* [<span data-ttu-id="dd4d7-197">gRPC pro webového klienta GitHub Project</span><span class="sxs-lookup"><span data-stu-id="dd4d7-197">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
* <xref:grpc/httpapi>
