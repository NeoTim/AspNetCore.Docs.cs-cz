---
title: Použití gRPC v prohlížečových aplikacích
author: jamesnk
description: Přečtěte si, jak nakonfigurovat služby gRPC na ASP.NET Core tak, aby byly volatelné z aplikací prohlížeče pomocí gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/16/2020
uid: grpc/browser
ms.openlocfilehash: 0bb8157525ccd32991d8925816c1b599c3d21a92
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977142"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="a3343-103">Použití gRPC v prohlížečových aplikacích</span><span class="sxs-lookup"><span data-stu-id="a3343-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="a3343-104">Podle [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="a3343-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a3343-105">**gRPC-Web support in .NET je experimentální**</span><span class="sxs-lookup"><span data-stu-id="a3343-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="a3343-106">gRPC-Web pro .NET je experimentální projekt, nikoli potvrzený produkt.</span><span class="sxs-lookup"><span data-stu-id="a3343-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="a3343-107">Chceme:</span><span class="sxs-lookup"><span data-stu-id="a3343-107">We want to:</span></span>
>
> * <span data-ttu-id="a3343-108">Otestujte, že náš přístup k implementaci gRPC-Web funguje.</span><span class="sxs-lookup"><span data-stu-id="a3343-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="a3343-109">Získejte zpětnou vazbu o tom, zda je tento přístup užitečný pro vývojáře rozhraní .NET ve srovnání s tradičním způsobem nastavení gRPC-Web prostřednictvím proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="a3343-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="a3343-110">Prosím, zanechte zpětnou vazbu na [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) zajistit, abychom vytvořit něco, co vývojáři rádi a jsou produktivní s.</span><span class="sxs-lookup"><span data-stu-id="a3343-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="a3343-111">Není možné volat službu HTTP/2 gRPC z aplikace založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="a3343-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="a3343-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) je protokol, který umožňuje prohlížeči JavaScript a Blazor aplikace volat gRPC služby.</span><span class="sxs-lookup"><span data-stu-id="a3343-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="a3343-113">Tento článek vysvětluje, jak používat gRPC-Web v .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a3343-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="a3343-114">Konfigurace gRPC-Web v ASP.NET jádru</span><span class="sxs-lookup"><span data-stu-id="a3343-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="a3343-115">gRPC služby hostované v ASP.NET Core lze nakonfigurovat tak, aby podporovaly gRPC-Web vedle HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="a3343-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="a3343-116">gRPC-Web nevyžaduje žádné změny služeb.</span><span class="sxs-lookup"><span data-stu-id="a3343-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="a3343-117">Jedinou úpravou je konfigurace spuštění.</span><span class="sxs-lookup"><span data-stu-id="a3343-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="a3343-118">Povolení gRPC-Web se službou ASP.NET Core gRPC:</span><span class="sxs-lookup"><span data-stu-id="a3343-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="a3343-119">Přidejte odkaz na balíček [Grpc.AspNetCore.Web.](https://www.nuget.org/packages/Grpc.AspNetCore.Web)</span><span class="sxs-lookup"><span data-stu-id="a3343-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="a3343-120">Nakonfigurujte aplikaci tak, `AddGrpcWeb` aby `UseGrpcWeb` používala gRPC-Web přidáním a *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a3343-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="a3343-121">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="a3343-121">The preceding code:</span></span>

* <span data-ttu-id="a3343-122">Přidá middleware gRPC-Web `UseGrpcWeb`, po směrování a před koncové body.</span><span class="sxs-lookup"><span data-stu-id="a3343-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="a3343-123">Určuje metodu `endpoints.MapGrpcService<GreeterService>()` podporuje gRPC-Web s `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="a3343-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="a3343-124">Případně nakonfigurujte všechny služby pro `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` podporu gRPC-Web přidáním configureservices.</span><span class="sxs-lookup"><span data-stu-id="a3343-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="a3343-125">Existuje známý problém, který způsobuje selhání gRPC-Web při [hostování souborem Http.sys](xref:fundamentals/servers/httpsys) v rozhraní .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="a3343-125">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="a3343-126">Řešení pro získání gRPC-Web pracuje na Http.sys je k dispozici [zde](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="a3343-126">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="a3343-127">gRPC-Web a CORS</span><span class="sxs-lookup"><span data-stu-id="a3343-127">gRPC-Web and CORS</span></span>

<span data-ttu-id="a3343-128">Zabezpečení prohlížeče zabraňuje webové stránce podávat žádosti do jiné domény, než je doména, která webovou stránku obsluhovala.</span><span class="sxs-lookup"><span data-stu-id="a3343-128">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="a3343-129">Toto omezení se vztahuje na provádění volání gRPC-Web s aplikacemi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="a3343-129">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="a3343-130">Například aplikace prohlížeče obsluhovaná `https://www.contoso.com` aplikací je blokována voláním služeb `https://services.contoso.com`gRPC-Web hostovaných na .</span><span class="sxs-lookup"><span data-stu-id="a3343-130">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="a3343-131">Cross Origin Resource Sharing (CORS) lze použít k uvolnění tohoto omezení.</span><span class="sxs-lookup"><span data-stu-id="a3343-131">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="a3343-132">Chcete-li aplikaci prohlížeče povolit volání gRPC-Web napříč počátkem, nastavte [cors v ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="a3343-132">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="a3343-133">Použijte integrovanou podporu CORS a zpřístupní záhlaví specifická pro gRPC pomocí <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>aplikace .</span><span class="sxs-lookup"><span data-stu-id="a3343-133">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="a3343-134">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="a3343-134">The preceding code:</span></span>

* <span data-ttu-id="a3343-135">Volání `AddCors` přidat služby CORS a konfiguruje zásady CORS, která zveřejňuje hlavičky specifické pro gRPC.</span><span class="sxs-lookup"><span data-stu-id="a3343-135">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="a3343-136">Volání `UseCors` přidat middleware CORS po směrování a před koncové body.</span><span class="sxs-lookup"><span data-stu-id="a3343-136">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="a3343-137">Určuje metodu, která `endpoints.MapGrpcService<GreeterService>()` `RequiresCors`podporuje CORS s .</span><span class="sxs-lookup"><span data-stu-id="a3343-137">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="a3343-138">Volání gRPC-Web z prohlížeče</span><span class="sxs-lookup"><span data-stu-id="a3343-138">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="a3343-139">Aplikace prohlížeče mohou používat gRPC-Web k volání služeb gRPC.</span><span class="sxs-lookup"><span data-stu-id="a3343-139">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="a3343-140">Existují určité požadavky a omezení při volání služeb gRPC s gRPC-Web z prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="a3343-140">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="a3343-141">Server musí být nakonfigurován tak, aby podporoval gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="a3343-141">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="a3343-142">Volání datových proudů klientů a obousměrné streamování nejsou podporovány.</span><span class="sxs-lookup"><span data-stu-id="a3343-142">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="a3343-143">Streamování serveru je podporováno.</span><span class="sxs-lookup"><span data-stu-id="a3343-143">Server streaming is supported.</span></span>
* <span data-ttu-id="a3343-144">Volání služeb gRPC v jiné doméně vyžaduje [konfiguraci CORS](xref:security/cors) na serveru.</span><span class="sxs-lookup"><span data-stu-id="a3343-144">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="a3343-145">JavaScript gRPC-Webový klient</span><span class="sxs-lookup"><span data-stu-id="a3343-145">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="a3343-146">K dispozici je JavaScript gRPC-Web klienta.</span><span class="sxs-lookup"><span data-stu-id="a3343-146">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="a3343-147">Pokyny k používání gRPC-Web z JavaScriptu najdete v [tématu napsání klientského kódu JavaScriptu pomocí gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="a3343-147">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="a3343-148">Konfigurace gRPC-Web s klientem .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="a3343-148">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="a3343-149">Klienta .NET gRPC lze nakonfigurovat tak, aby uskutečňovat volání gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="a3343-149">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="a3343-150">To je užitečné pro aplikace [Blazor WebAssembly,](xref:blazor/index#blazor-webassembly) které jsou hostovány v prohlížeči a mají stejná http omezení kódu JavaScript.</span><span class="sxs-lookup"><span data-stu-id="a3343-150">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="a3343-151">Volání gRPC-Web s klientem .NET je [stejné jako HTTP/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="a3343-151">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="a3343-152">Jedinou úpravou je způsob vytvoření kanálu.</span><span class="sxs-lookup"><span data-stu-id="a3343-152">The only modification is how the channel is created.</span></span>

<span data-ttu-id="a3343-153">Použití gRPC-Web:</span><span class="sxs-lookup"><span data-stu-id="a3343-153">To use gRPC-Web:</span></span>

* <span data-ttu-id="a3343-154">Přidejte odkaz na balíček [Grpc.Net.Client.Web.](https://www.nuget.org/packages/Grpc.Net.Client.Web)</span><span class="sxs-lookup"><span data-stu-id="a3343-154">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="a3343-155">Ujistěte se, že odkaz na balíček [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) je 2.27.0 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="a3343-155">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="a3343-156">Nakonfigurujte `GrpcWebHandler`kanál tak, aby používal :</span><span class="sxs-lookup"><span data-stu-id="a3343-156">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="a3343-157">Předcházející kód:</span><span class="sxs-lookup"><span data-stu-id="a3343-157">The preceding code:</span></span>

* <span data-ttu-id="a3343-158">Konfiguruje kanál pro použití gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="a3343-158">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="a3343-159">Vytvoří klienta a provede volání pomocí kanálu.</span><span class="sxs-lookup"><span data-stu-id="a3343-159">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="a3343-160">Při `GrpcWebHandler` vytvoření má následující možnosti konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a3343-160">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="a3343-161">**InnerHandler**: <xref:System.Net.Http.HttpMessageHandler> Podklad, který dělá gRPC HTTP `HttpClientHandler`požadavek, například .</span><span class="sxs-lookup"><span data-stu-id="a3343-161">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="a3343-162">**Režim**: Typ výčtu, který určuje, zda `Content-Type` je `application/grpc-web` `application/grpc-web-text`požadavek požadavku http gRPC nebo .</span><span class="sxs-lookup"><span data-stu-id="a3343-162">**Mode**: An enumeration type that specifies whether the gRPC HTTP request request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="a3343-163">`GrpcWebMode.GrpcWeb`konfiguruje obsah, který má být odeslán bez kódování.</span><span class="sxs-lookup"><span data-stu-id="a3343-163">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="a3343-164">Výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="a3343-164">Default value.</span></span>
    * <span data-ttu-id="a3343-165">`GrpcWebMode.GrpcWebText`konfiguruje obsah tak, aby byl kódován base64.</span><span class="sxs-lookup"><span data-stu-id="a3343-165">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="a3343-166">Vyžadováno pro volání datových proudů serveru v prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="a3343-166">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="a3343-167">**HttpVersion**: `Version` HTTP protokol používaný k nastavení [httprequestmessage.version](xref:System.Net.Http.HttpRequestMessage.Version) na podkladovém gRPC HTTP požadavku.</span><span class="sxs-lookup"><span data-stu-id="a3343-167">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="a3343-168">gRPC-Web nevyžaduje konkrétní verzi a nepřepisuje výchozí hodnotu, pokud není zadána.</span><span class="sxs-lookup"><span data-stu-id="a3343-168">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a3343-169">Generovaní klienti gRPC mají synchronizační a asynchronní metody pro volání unárních metod.</span><span class="sxs-lookup"><span data-stu-id="a3343-169">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="a3343-170">Například `SayHello` je synchronizace a `SayHelloAsync` je asynchronní.</span><span class="sxs-lookup"><span data-stu-id="a3343-170">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="a3343-171">Volání metody synchronizace v aplikaci Blazor WebAssembly způsobí, že aplikace přestane reagovat.</span><span class="sxs-lookup"><span data-stu-id="a3343-171">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="a3343-172">Asynchronní metody musí být vždy použity v Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="a3343-172">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a3343-173">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a3343-173">Additional resources</span></span>

* [<span data-ttu-id="a3343-174">gRPC pro webové klienty projekt GitHub</span><span class="sxs-lookup"><span data-stu-id="a3343-174">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
