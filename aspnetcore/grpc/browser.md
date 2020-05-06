---
title: Použití gRPC v prohlížečových aplikacích
author: jamesnk
description: Naučte se konfigurovat gRPC služby na ASP.NET Core, které se mají volat z aplikací pro prohlížeč pomocí gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: a74f7acb54b4601a0c30ff1a39dc30231e2b5a78
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774740"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="482bc-103">Použití gRPC v prohlížečových aplikacích</span><span class="sxs-lookup"><span data-stu-id="482bc-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="482bc-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="482bc-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="482bc-105">**gRPC – webová podpora v .NET je experimentální**</span><span class="sxs-lookup"><span data-stu-id="482bc-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="482bc-106">gRPC-web pro .NET je experimentální projekt, nikoli potvrzený produkt.</span><span class="sxs-lookup"><span data-stu-id="482bc-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="482bc-107">Chceme:</span><span class="sxs-lookup"><span data-stu-id="482bc-107">We want to:</span></span>
>
> * <span data-ttu-id="482bc-108">Otestujte, jak náš přístup k implementaci gRPC-web funguje.</span><span class="sxs-lookup"><span data-stu-id="482bc-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="482bc-109">Získejte zpětnou vazbu, pokud je tento přístup užitečný pro vývojáře na platformě .NET v porovnání s tradičním způsobem nastavení gRPC-web prostřednictvím proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="482bc-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="482bc-110">Zajistěte prosím [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) svůj názor na, abyste se ujistili, že jsme vytvořili něco, co vývojáři rádi a máte produktivní.</span><span class="sxs-lookup"><span data-stu-id="482bc-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="482bc-111">Nemůžete volat službu gRPC HTTP/2 z aplikace založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="482bc-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="482bc-112">[gRPC-web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) je protokol, který umožňuje, aby JavaScript Blazor a aplikace v prohlížeči volaly služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="482bc-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="482bc-113">Tento článek vysvětluje, jak používat gRPC-web v .NET Core.</span><span class="sxs-lookup"><span data-stu-id="482bc-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="482bc-114">gRPC – web ve ASP.NET Core vs. zástupné</span><span class="sxs-lookup"><span data-stu-id="482bc-114">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="482bc-115">Existují dvě možnosti, jak přidat gRPC-web do aplikace ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="482bc-115">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="482bc-116">Podpora gRPC-web společně s gRPC HTTP/2 v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="482bc-116">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="482bc-117">Tato možnost používá middleware poskytované `Grpc.AspNetCore.Web` balíčkem.</span><span class="sxs-lookup"><span data-stu-id="482bc-117">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="482bc-118">Pomocí gRPC webu [zástupné proxy](https://www.envoyproxy.io/) můžete přeložit GRPC-web na gRPC http/2.</span><span class="sxs-lookup"><span data-stu-id="482bc-118">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="482bc-119">Přeložené volání je pak předáno do aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="482bc-119">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="482bc-120">Existují odborníci a nevýhody pro každý přístup.</span><span class="sxs-lookup"><span data-stu-id="482bc-120">There are pros and cons to each approach.</span></span> <span data-ttu-id="482bc-121">Pokud už používáte zástupné jako proxy v prostředí vaší aplikace, může to mít smysl použít ho také k poskytování podpory gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="482bc-121">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="482bc-122">Pokud chcete jednoduché řešení pro gRPC web, které vyžaduje jenom ASP.NET Core, `Grpc.AspNetCore.Web` je vhodná volba.</span><span class="sxs-lookup"><span data-stu-id="482bc-122">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="482bc-123">Konfigurace gRPC-web v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="482bc-123">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="482bc-124">služby gRPC hostované v ASP.NET Core můžou být nakonfigurované tak, aby podporovaly gRPC-web vedle HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="482bc-124">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="482bc-125">gRPC-web nevyžaduje žádné změny služeb.</span><span class="sxs-lookup"><span data-stu-id="482bc-125">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="482bc-126">Jedinou úpravou je spuštění konfigurace.</span><span class="sxs-lookup"><span data-stu-id="482bc-126">The only modification is startup configuration.</span></span>

<span data-ttu-id="482bc-127">Povolení gRPC-web pomocí služby ASP.NET Core gRPC:</span><span class="sxs-lookup"><span data-stu-id="482bc-127">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="482bc-128">Přidejte odkaz na balíček [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="482bc-128">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="482bc-129">Nakonfigurujte aplikaci tak, aby používala gRPC-web `AddGrpcWeb` přidáním `UseGrpcWeb` a do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="482bc-129">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="482bc-130">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="482bc-130">The preceding code:</span></span>

* <span data-ttu-id="482bc-131">Přidá gRPC-web middleware, `UseGrpcWeb`po směrování a před koncovými body.</span><span class="sxs-lookup"><span data-stu-id="482bc-131">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="482bc-132">Určuje metodu `endpoints.MapGrpcService<GreeterService>()` , která podporuje GRPC-web `EnableGrpcWeb`s.</span><span class="sxs-lookup"><span data-stu-id="482bc-132">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="482bc-133">Případně můžete nakonfigurovat všechny služby tak, aby podporovaly gRPC- `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` web přidáním do ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="482bc-133">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="482bc-134">K dispozici je známý problém, který způsobí selhání gRPC-web, pokud je [hostovaný souborem http. sys](xref:fundamentals/servers/httpsys) v rozhraní .NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="482bc-134">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="482bc-135">[K dispozici je](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)alternativní řešení pro získání gRPC webu na http. sys.</span><span class="sxs-lookup"><span data-stu-id="482bc-135">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="482bc-136">gRPC – web a CORS</span><span class="sxs-lookup"><span data-stu-id="482bc-136">gRPC-Web and CORS</span></span>

<span data-ttu-id="482bc-137">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval.</span><span class="sxs-lookup"><span data-stu-id="482bc-137">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="482bc-138">Toto omezení se vztahuje k vytváření gRPC webových volání s aplikacemi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="482bc-138">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="482bc-139">Například aplikace prohlížeče obsluhovaná aplikací `https://www.contoso.com` je blokována při volání gRPCch webových služeb hostovaných na `https://services.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="482bc-139">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="482bc-140">Pro zmírnění tohoto omezení lze použít sdílení prostředků mezi zdroji (CORS).</span><span class="sxs-lookup"><span data-stu-id="482bc-140">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="482bc-141">Pokud chcete, aby aplikace v prohlížeči mohla dělat gRPC webová volání mezi zdroji, nastavte [CORS v ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="482bc-141">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="482bc-142">Využijte integrovanou podporu CORS a vystavte hlavičky specifické pro gRPC <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="482bc-142">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="482bc-143">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="482bc-143">The preceding code:</span></span>

* <span data-ttu-id="482bc-144">Volání `AddCors` pro přidání služeb CORS a nakonfigurují zásady CORS, které zveřejňují gRPC konkrétní hlavičky.</span><span class="sxs-lookup"><span data-stu-id="482bc-144">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="482bc-145">Volání `UseCors` pro přidání middlewaru CORS po směrování a před koncovými body.</span><span class="sxs-lookup"><span data-stu-id="482bc-145">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="482bc-146">Určuje metodu `endpoints.MapGrpcService<GreeterService>()` , která podporuje CORS `RequiresCors`s.</span><span class="sxs-lookup"><span data-stu-id="482bc-146">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="482bc-147">Volání gRPC-web z prohlížeče</span><span class="sxs-lookup"><span data-stu-id="482bc-147">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="482bc-148">Aplikace prohlížeče můžou pomocí gRPC-web volat služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="482bc-148">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="482bc-149">Existují některé požadavky a omezení při volání služeb gRPC Services pomocí gRPC-web z prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="482bc-149">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="482bc-150">Server musí být nakonfigurovaný tak, aby podporoval gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="482bc-150">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="482bc-151">Volání streamování klientů a obousměrného streamování nejsou podporovaná.</span><span class="sxs-lookup"><span data-stu-id="482bc-151">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="482bc-152">Streamování serveru je podporované.</span><span class="sxs-lookup"><span data-stu-id="482bc-152">Server streaming is supported.</span></span>
* <span data-ttu-id="482bc-153">Volání služeb gRPC Services v jiné doméně vyžaduje, aby na serveru byla nakonfigurovaná [CORS](xref:security/cors) .</span><span class="sxs-lookup"><span data-stu-id="482bc-153">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="482bc-154">JavaScript gRPC – webový klient</span><span class="sxs-lookup"><span data-stu-id="482bc-154">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="482bc-155">K dispozici je gRPC JavaScript-Web Client.</span><span class="sxs-lookup"><span data-stu-id="482bc-155">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="482bc-156">Pokyny, jak používat gRPC-web z JavaScriptu, najdete v tématu [Zápis kódu klienta JavaScript pomocí gRPC-web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="482bc-156">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="482bc-157">Konfigurace gRPC-web pomocí klienta .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="482bc-157">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="482bc-158">Klient .NET gRPC se dá nakonfigurovat tak, aby gRPC webová volání.</span><span class="sxs-lookup"><span data-stu-id="482bc-158">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="482bc-159">To je užitečné pro [ Blazor aplikace WebAssembly](xref:blazor/index#blazor-webassembly) , které jsou hostovány v prohlížeči a mají stejná omezení http kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="482bc-159">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="482bc-160">Volání gRPC-web s klientem .NET je [stejné jako http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="482bc-160">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="482bc-161">Jedinou úpravou je způsob vytvoření kanálu.</span><span class="sxs-lookup"><span data-stu-id="482bc-161">The only modification is how the channel is created.</span></span>

<span data-ttu-id="482bc-162">Použití gRPC-web:</span><span class="sxs-lookup"><span data-stu-id="482bc-162">To use gRPC-Web:</span></span>

* <span data-ttu-id="482bc-163">Přidejte odkaz na balíček [Grpc .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="482bc-163">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="482bc-164">Zajistěte, aby byl odkaz na balíček [Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) 2.27.0 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="482bc-164">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="482bc-165">Nakonfigurujte kanál tak, aby používal `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="482bc-165">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="482bc-166">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="482bc-166">The preceding code:</span></span>

* <span data-ttu-id="482bc-167">Nakonfiguruje kanál tak, aby používal gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="482bc-167">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="482bc-168">Vytvoří klienta a provede volání pomocí kanálu.</span><span class="sxs-lookup"><span data-stu-id="482bc-168">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="482bc-169">Při `GrpcWebHandler` vytvoření má následující možnosti konfigurace:</span><span class="sxs-lookup"><span data-stu-id="482bc-169">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="482bc-170">**InnerHandler**: základní <xref:System.Net.Http.HttpMessageHandler> , který vytváří požadavek gRPC http, například `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="482bc-170">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="482bc-171">**Mode**: typ výčtu, který určuje, zda je `Content-Type` `application/grpc-web` požadavek HTTP gRPC nebo `application/grpc-web-text`.</span><span class="sxs-lookup"><span data-stu-id="482bc-171">**Mode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="482bc-172">`GrpcWebMode.GrpcWeb`Konfiguruje obsah, který se má odeslat bez kódování.</span><span class="sxs-lookup"><span data-stu-id="482bc-172">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="482bc-173">Výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="482bc-173">Default value.</span></span>
    * <span data-ttu-id="482bc-174">`GrpcWebMode.GrpcWebText`Konfiguruje obsah tak, aby byl kódovaný v kódování Base64.</span><span class="sxs-lookup"><span data-stu-id="482bc-174">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="482bc-175">Vyžaduje se pro volání streamování serveru v prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="482bc-175">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="482bc-176">**HttpVersion**: protokol `Version` http použitý k nastavení [zprávy HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) na podkladové žádosti HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="482bc-176">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="482bc-177">gRPC-web nevyžaduje konkrétní verzi a nepřepisuje výchozí, pokud není zadaný.</span><span class="sxs-lookup"><span data-stu-id="482bc-177">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="482bc-178">Vygenerované klienty gRPC mají synchronizační a asynchronní metody pro volání unárních metod.</span><span class="sxs-lookup"><span data-stu-id="482bc-178">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="482bc-179">Například `SayHello` je synchronizován a `SayHelloAsync` je asynchronní.</span><span class="sxs-lookup"><span data-stu-id="482bc-179">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="482bc-180">Volání metody synchronizace v Blazor aplikaci WebAssembly způsobí, že aplikace přestane reagovat.</span><span class="sxs-lookup"><span data-stu-id="482bc-180">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="482bc-181">Asynchronní metody musí být vždy použity v Blazor sestavení WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="482bc-181">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="482bc-182">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="482bc-182">Additional resources</span></span>

* [<span data-ttu-id="482bc-183">gRPC pro webového klienta GitHub Project</span><span class="sxs-lookup"><span data-stu-id="482bc-183">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
