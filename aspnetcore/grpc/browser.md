---
title: Použití gRPC v prohlížečových aplikacích
author: jamesnk
description: Naučte se konfigurovat gRPC služby na ASP.NET Core, které se mají volat z aplikací pro prohlížeč pomocí gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: 37932e755a0ef2149fb2336d2dcef87d3347d1a4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404753"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="4f160-103">Použití gRPC v prohlížečových aplikacích</span><span class="sxs-lookup"><span data-stu-id="4f160-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="4f160-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="4f160-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="4f160-105">Nemůžete volat službu gRPC HTTP/2 z aplikace založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="4f160-105">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="4f160-106">[gRPC-web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) je protokol, který umožňuje, aby JavaScript a aplikace v prohlížeči Blazor volaly služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="4f160-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="4f160-107">Tento článek vysvětluje, jak používat gRPC-web v .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4f160-107">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="4f160-108">gRPC – web ve ASP.NET Core vs. zástupné</span><span class="sxs-lookup"><span data-stu-id="4f160-108">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="4f160-109">Existují dvě možnosti, jak přidat gRPC-web do aplikace ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4f160-109">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="4f160-110">Podpora gRPC-web společně s gRPC HTTP/2 v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4f160-110">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="4f160-111">Tato možnost používá middleware poskytované `Grpc.AspNetCore.Web` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="4f160-111">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="4f160-112">Pomocí gRPC webu [zástupné proxy](https://www.envoyproxy.io/) můžete přeložit GRPC-web na gRPC http/2.</span><span class="sxs-lookup"><span data-stu-id="4f160-112">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="4f160-113">Přeložené volání je pak předáno do aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4f160-113">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="4f160-114">Existují odborníci a nevýhody pro každý přístup.</span><span class="sxs-lookup"><span data-stu-id="4f160-114">There are pros and cons to each approach.</span></span> <span data-ttu-id="4f160-115">Pokud už používáte zástupné jako proxy v prostředí vaší aplikace, může to mít smysl použít ho také k poskytování podpory gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="4f160-115">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="4f160-116">Pokud chcete jednoduché řešení pro gRPC web, které vyžaduje jenom ASP.NET Core, `Grpc.AspNetCore.Web` je vhodná volba.</span><span class="sxs-lookup"><span data-stu-id="4f160-116">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="4f160-117">Konfigurace gRPC-web v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4f160-117">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="4f160-118">služby gRPC hostované v ASP.NET Core můžou být nakonfigurované tak, aby podporovaly gRPC-web vedle HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="4f160-118">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="4f160-119">gRPC-web nevyžaduje žádné změny služeb.</span><span class="sxs-lookup"><span data-stu-id="4f160-119">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="4f160-120">Jedinou úpravou je spuštění konfigurace.</span><span class="sxs-lookup"><span data-stu-id="4f160-120">The only modification is startup configuration.</span></span>

<span data-ttu-id="4f160-121">Povolení gRPC-web pomocí služby ASP.NET Core gRPC:</span><span class="sxs-lookup"><span data-stu-id="4f160-121">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="4f160-122">Přidejte odkaz na balíček [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="4f160-122">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="4f160-123">Nakonfigurujte aplikaci tak, aby používala gRPC-web přidáním `UseGrpcWeb` a `EnableGrpcWeb` do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4f160-123">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="4f160-124">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="4f160-124">The preceding code:</span></span>

* <span data-ttu-id="4f160-125">Přidá gRPC-web middleware, `UseGrpcWeb` po směrování a před koncovými body.</span><span class="sxs-lookup"><span data-stu-id="4f160-125">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="4f160-126">Určuje metodu, která `endpoints.MapGrpcService<GreeterService>()` podporuje gRPC-web s `EnableGrpcWeb` .</span><span class="sxs-lookup"><span data-stu-id="4f160-126">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="4f160-127">Případně je možné nakonfigurovat middleware gRPC-web tak, aby všechny služby podporovaly gRPC-web ve výchozím nastavení a `EnableGrpcWeb` nejsou povinné.</span><span class="sxs-lookup"><span data-stu-id="4f160-127">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="4f160-128">Určete, `new GrpcWebOptions { DefaultEnabled = true }` kdy se má middleware přidat.</span><span class="sxs-lookup"><span data-stu-id="4f160-128">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="4f160-129">K dispozici je známý problém, který způsobí selhání gRPC-web při [hostování Http.sys](xref:fundamentals/servers/httpsys) v .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="4f160-129">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="4f160-130">[K dispozici je](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)alternativní řešení pro získání GRPC na webu na Http.sys.</span><span class="sxs-lookup"><span data-stu-id="4f160-130">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="4f160-131">gRPC – web a CORS</span><span class="sxs-lookup"><span data-stu-id="4f160-131">gRPC-Web and CORS</span></span>

<span data-ttu-id="4f160-132">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval.</span><span class="sxs-lookup"><span data-stu-id="4f160-132">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="4f160-133">Toto omezení se vztahuje k vytváření gRPC webových volání s aplikacemi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4f160-133">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="4f160-134">Například aplikace prohlížeče obsluhovaná aplikací `https://www.contoso.com` je blokována při volání gRPCch webových služeb hostovaných na `https://services.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="4f160-134">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="4f160-135">Pro zmírnění tohoto omezení lze použít sdílení prostředků mezi zdroji (CORS).</span><span class="sxs-lookup"><span data-stu-id="4f160-135">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="4f160-136">Pokud chcete, aby aplikace v prohlížeči mohla dělat gRPC webová volání mezi zdroji, nastavte [CORS v ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="4f160-136">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="4f160-137">Využijte integrovanou podporu CORS a vystavte hlavičky specifické pro gRPC <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="4f160-137">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="4f160-138">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="4f160-138">The preceding code:</span></span>

* <span data-ttu-id="4f160-139">Volání `AddCors` pro přidání služeb CORS a nakonfigurují zásady CORS, které zveřejňují gRPC konkrétní hlavičky.</span><span class="sxs-lookup"><span data-stu-id="4f160-139">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="4f160-140">Volání `UseCors` pro přidání middlewaru CORS po směrování a před koncovými body.</span><span class="sxs-lookup"><span data-stu-id="4f160-140">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="4f160-141">Určuje metodu, která `endpoints.MapGrpcService<GreeterService>()` podporuje CORS s `RequiresCors` .</span><span class="sxs-lookup"><span data-stu-id="4f160-141">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="4f160-142">Volání gRPC-web z prohlížeče</span><span class="sxs-lookup"><span data-stu-id="4f160-142">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="4f160-143">Aplikace prohlížeče můžou pomocí gRPC-web volat služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="4f160-143">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="4f160-144">Existují některé požadavky a omezení při volání služeb gRPC Services pomocí gRPC-web z prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="4f160-144">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="4f160-145">Server musí být nakonfigurovaný tak, aby podporoval gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="4f160-145">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="4f160-146">Volání streamování klientů a obousměrného streamování nejsou podporovaná.</span><span class="sxs-lookup"><span data-stu-id="4f160-146">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="4f160-147">Streamování serveru je podporované.</span><span class="sxs-lookup"><span data-stu-id="4f160-147">Server streaming is supported.</span></span>
* <span data-ttu-id="4f160-148">Volání služeb gRPC Services v jiné doméně vyžaduje, aby na serveru byla nakonfigurovaná [CORS](xref:security/cors) .</span><span class="sxs-lookup"><span data-stu-id="4f160-148">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="4f160-149">JavaScript gRPC – webový klient</span><span class="sxs-lookup"><span data-stu-id="4f160-149">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="4f160-150">K dispozici je gRPC JavaScript-Web Client.</span><span class="sxs-lookup"><span data-stu-id="4f160-150">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="4f160-151">Pokyny, jak používat gRPC-web z JavaScriptu, najdete v tématu [Zápis kódu klienta JavaScript pomocí gRPC-web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="4f160-151">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="4f160-152">Konfigurace gRPC-web pomocí klienta .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="4f160-152">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="4f160-153">Klient .NET gRPC se dá nakonfigurovat tak, aby gRPC webová volání.</span><span class="sxs-lookup"><span data-stu-id="4f160-153">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="4f160-154">To je užitečné pro [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) aplikace, které jsou hostované v prohlížeči a mají stejná omezení http kódu JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="4f160-154">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="4f160-155">Volání gRPC-web s klientem .NET je [stejné jako http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="4f160-155">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="4f160-156">Jedinou úpravou je způsob vytvoření kanálu.</span><span class="sxs-lookup"><span data-stu-id="4f160-156">The only modification is how the channel is created.</span></span>

<span data-ttu-id="4f160-157">Použití gRPC-web:</span><span class="sxs-lookup"><span data-stu-id="4f160-157">To use gRPC-Web:</span></span>

* <span data-ttu-id="4f160-158">Přidejte odkaz na balíček [Grpc .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="4f160-158">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="4f160-159">Zajistěte, aby byl odkaz na balíček [Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) 2.29.0 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="4f160-159">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="4f160-160">Nakonfigurujte kanál tak, aby používal `GrpcWebHandler` :</span><span class="sxs-lookup"><span data-stu-id="4f160-160">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="4f160-161">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="4f160-161">The preceding code:</span></span>

* <span data-ttu-id="4f160-162">Nakonfiguruje kanál tak, aby používal gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="4f160-162">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="4f160-163">Vytvoří klienta a provede volání pomocí kanálu.</span><span class="sxs-lookup"><span data-stu-id="4f160-163">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="4f160-164">`GrpcWebHandler`má následující možnosti konfigurace:</span><span class="sxs-lookup"><span data-stu-id="4f160-164">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="4f160-165">**InnerHandler**: základní <xref:System.Net.Http.HttpMessageHandler> , který vytváří požadavek gRPC http, například `HttpClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="4f160-165">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="4f160-166">**GrpcWebMode**: typ výčtu, který určuje, zda je GRPC požadavek `Content-Type` http `application/grpc-web` nebo `application/grpc-web-text` .</span><span class="sxs-lookup"><span data-stu-id="4f160-166">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="4f160-167">`GrpcWebMode.GrpcWeb`Konfiguruje obsah, který se má odeslat bez kódování.</span><span class="sxs-lookup"><span data-stu-id="4f160-167">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="4f160-168">Výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="4f160-168">Default value.</span></span>
    * <span data-ttu-id="4f160-169">`GrpcWebMode.GrpcWebText`Konfiguruje obsah tak, aby byl kódovaný v kódování Base64.</span><span class="sxs-lookup"><span data-stu-id="4f160-169">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="4f160-170">Vyžaduje se pro volání streamování serveru v prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="4f160-170">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="4f160-171">**HttpVersion**: protokol HTTP `Version` použitý k nastavení [zprávy HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) na podkladové žádosti HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="4f160-171">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="4f160-172">gRPC-web nevyžaduje konkrétní verzi a nepřepisuje výchozí, pokud není zadaný.</span><span class="sxs-lookup"><span data-stu-id="4f160-172">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4f160-173">Vygenerované klienty gRPC mají synchronizační a asynchronní metody pro volání unárních metod.</span><span class="sxs-lookup"><span data-stu-id="4f160-173">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="4f160-174">Například `SayHello` je synchronizován a `SayHelloAsync` je asynchronní.</span><span class="sxs-lookup"><span data-stu-id="4f160-174">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="4f160-175">Volání metody synchronizace v Blazor WebAssembly aplikaci způsobí, že aplikace přestane reagovat.</span><span class="sxs-lookup"><span data-stu-id="4f160-175">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="4f160-176">Asynchronní metody musí být vždy použity v Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="4f160-176">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4f160-177">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4f160-177">Additional resources</span></span>

* [<span data-ttu-id="4f160-178">gRPC pro webového klienta GitHub Project</span><span class="sxs-lookup"><span data-stu-id="4f160-178">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
