---
title: Použití gRPC v prohlížečových aplikacích
author: jamesnk
description: Naučte se konfigurovat gRPC služby na ASP.NET Core, které se mají volat z aplikací pro prohlížeč pomocí gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/16/2020
uid: grpc/browser
ms.openlocfilehash: 3beeffc26ffd3c2dc85bfc22a46d97d5fd78d3d0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78664197"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="af1aa-103">Použití gRPC v prohlížečových aplikacích</span><span class="sxs-lookup"><span data-stu-id="af1aa-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="af1aa-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="af1aa-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="af1aa-105">**gRPC – webová podpora v .NET je experimentální**</span><span class="sxs-lookup"><span data-stu-id="af1aa-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="af1aa-106">gRPC-web pro .NET je experimentální projekt, nikoli potvrzený produkt.</span><span class="sxs-lookup"><span data-stu-id="af1aa-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="af1aa-107">Chceme:</span><span class="sxs-lookup"><span data-stu-id="af1aa-107">We want to:</span></span>
>
> * <span data-ttu-id="af1aa-108">Otestujte, jak náš přístup k implementaci gRPC-web funguje.</span><span class="sxs-lookup"><span data-stu-id="af1aa-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="af1aa-109">Získejte zpětnou vazbu, pokud je tento přístup užitečný pro vývojáře na platformě .NET v porovnání s tradičním způsobem nastavení gRPC-web prostřednictvím proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="af1aa-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="af1aa-110">Zajistěte prosím svůj názor na [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) , abyste se ujistili, že jsme vytvořili něco, co vývojáři rádi a máte produktivní.</span><span class="sxs-lookup"><span data-stu-id="af1aa-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="af1aa-111">Nemůžete volat službu gRPC HTTP/2 z aplikace založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="af1aa-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="af1aa-112">[gRPC-web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) je protokol, který umožňuje aplikacím Javascript a Blazor v prohlížeči volat služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="af1aa-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="af1aa-113">Tento článek vysvětluje, jak používat gRPC-web v .NET Core.</span><span class="sxs-lookup"><span data-stu-id="af1aa-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="af1aa-114">Konfigurace gRPC-web v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af1aa-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="af1aa-115">služby gRPC hostované v ASP.NET Core můžou být nakonfigurované tak, aby podporovaly gRPC-web vedle HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="af1aa-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="af1aa-116">gRPC-web nevyžaduje žádné změny služeb.</span><span class="sxs-lookup"><span data-stu-id="af1aa-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="af1aa-117">Jedinou úpravou je spuštění konfigurace.</span><span class="sxs-lookup"><span data-stu-id="af1aa-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="af1aa-118">Povolení gRPC-web pomocí služby ASP.NET Core gRPC:</span><span class="sxs-lookup"><span data-stu-id="af1aa-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="af1aa-119">Přidejte odkaz na balíček [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="af1aa-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="af1aa-120">Nakonfigurujte aplikaci tak, aby používala gRPC-web přidáním `AddGrpcWeb` a `UseGrpcWeb` do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="af1aa-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="af1aa-121">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="af1aa-121">The preceding code:</span></span>

* <span data-ttu-id="af1aa-122">Přidá middleware gRPC-web middleware, `UseGrpcWeb`po směrování a před koncovými body.</span><span class="sxs-lookup"><span data-stu-id="af1aa-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="af1aa-123">Určuje metodu `endpoints.MapGrpcService<GreeterService>()` podporuje gRPC-web s `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="af1aa-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="af1aa-124">Případně můžete nakonfigurovat všechny služby tak, aby podporovaly gRPC-web přidáním `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` do ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="af1aa-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

### <a name="grpc-web-and-cors"></a><span data-ttu-id="af1aa-125">gRPC – web a CORS</span><span class="sxs-lookup"><span data-stu-id="af1aa-125">gRPC-Web and CORS</span></span>

<span data-ttu-id="af1aa-126">Zabezpečení prohlížeče brání webové stránce v tom, aby prováděla požadavky na jinou doménu než ta, která tuto webovou stránku obsluhoval.</span><span class="sxs-lookup"><span data-stu-id="af1aa-126">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="af1aa-127">Toto omezení se vztahuje k vytváření gRPC webových volání s aplikacemi prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="af1aa-127">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="af1aa-128">Například aplikace prohlížeče obsluhovaná `https://www.contoso.com` je blokována voláním gRPC webových služeb hostovaných na `https://services.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="af1aa-128">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="af1aa-129">Pro zmírnění tohoto omezení lze použít sdílení prostředků mezi zdroji (CORS).</span><span class="sxs-lookup"><span data-stu-id="af1aa-129">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="af1aa-130">Pokud chcete, aby aplikace v prohlížeči mohla dělat gRPC webová volání mezi zdroji, nastavte [CORS v ASP.NET Core](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="af1aa-130">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="af1aa-131">Využijte integrovanou podporu CORS a vystavte hlavičky specifické pro gRPC pomocí <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span><span class="sxs-lookup"><span data-stu-id="af1aa-131">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="af1aa-132">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="af1aa-132">The preceding code:</span></span>

* <span data-ttu-id="af1aa-133">Volá `AddCors`, aby se přidaly služby CORS a nakonfigurují zásady CORS, které zpřístupňují hlavičky specifické pro gRPC.</span><span class="sxs-lookup"><span data-stu-id="af1aa-133">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="af1aa-134">Volá `UseCors` pro přidání middlewaru CORS po směrování a před koncovými body.</span><span class="sxs-lookup"><span data-stu-id="af1aa-134">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="af1aa-135">Určuje, `endpoints.MapGrpcService<GreeterService>()` metoda podporuje CORS s `RequiresCors`.</span><span class="sxs-lookup"><span data-stu-id="af1aa-135">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="af1aa-136">Volání gRPC-web z prohlížeče</span><span class="sxs-lookup"><span data-stu-id="af1aa-136">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="af1aa-137">Aplikace prohlížeče můžou pomocí gRPC-web volat služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="af1aa-137">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="af1aa-138">Existují některé požadavky a omezení při volání služeb gRPC Services pomocí gRPC-web z prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="af1aa-138">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="af1aa-139">Server musí být nakonfigurovaný tak, aby podporoval gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="af1aa-139">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="af1aa-140">Volání streamování klientů a obousměrného streamování nejsou podporovaná.</span><span class="sxs-lookup"><span data-stu-id="af1aa-140">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="af1aa-141">Streamování serveru je podporované.</span><span class="sxs-lookup"><span data-stu-id="af1aa-141">Server streaming is supported.</span></span>
* <span data-ttu-id="af1aa-142">Volání služeb gRPC Services v jiné doméně vyžaduje, aby na serveru byla nakonfigurovaná [CORS](xref:security/cors) .</span><span class="sxs-lookup"><span data-stu-id="af1aa-142">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="af1aa-143">JavaScript gRPC – webový klient</span><span class="sxs-lookup"><span data-stu-id="af1aa-143">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="af1aa-144">K dispozici je gRPC JavaScript-Web Client.</span><span class="sxs-lookup"><span data-stu-id="af1aa-144">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="af1aa-145">Pokyny, jak používat gRPC-web z JavaScriptu, najdete v tématu [Zápis kódu klienta JavaScript pomocí gRPC-web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="af1aa-145">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="af1aa-146">Konfigurace gRPC-web pomocí klienta .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="af1aa-146">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="af1aa-147">Klient .NET gRPC se dá nakonfigurovat tak, aby gRPC webová volání.</span><span class="sxs-lookup"><span data-stu-id="af1aa-147">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="af1aa-148">To je užitečné pro [Blazor aplikace WebAssembly](xref:blazor/index#blazor-webassembly) , které jsou hostovány v prohlížeči a mají stejná omezení http kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="af1aa-148">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="af1aa-149">Volání gRPC-web s klientem .NET je [stejné jako http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="af1aa-149">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="af1aa-150">Jedinou úpravou je způsob vytvoření kanálu.</span><span class="sxs-lookup"><span data-stu-id="af1aa-150">The only modification is how the channel is created.</span></span>

<span data-ttu-id="af1aa-151">Použití gRPC-web:</span><span class="sxs-lookup"><span data-stu-id="af1aa-151">To use gRPC-Web:</span></span>

* <span data-ttu-id="af1aa-152">Přidejte odkaz na balíček [Grpc .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="af1aa-152">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="af1aa-153">Zajistěte, aby byl odkaz na balíček [Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) 2.27.0 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="af1aa-153">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="af1aa-154">Nakonfigurujte kanál tak, aby používal `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="af1aa-154">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="af1aa-155">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="af1aa-155">The preceding code:</span></span>

* <span data-ttu-id="af1aa-156">Nakonfiguruje kanál tak, aby používal gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="af1aa-156">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="af1aa-157">Vytvoří klienta a provede volání pomocí kanálu.</span><span class="sxs-lookup"><span data-stu-id="af1aa-157">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="af1aa-158">`GrpcWebHandler` má při vytvoření následující možnosti konfigurace:</span><span class="sxs-lookup"><span data-stu-id="af1aa-158">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="af1aa-159">**InnerHandler**: podkladové <xref:System.Net.Http.HttpMessageHandler>, které vytváří požadavek gRPC http, například `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="af1aa-159">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="af1aa-160">**Mode**: typ výčtu určující, zda `Content-Type` požadavek HTTP gRPC je `application/grpc-web` nebo `application/grpc-web-text`.</span><span class="sxs-lookup"><span data-stu-id="af1aa-160">**Mode**: An enumeration type that specifies whether the gRPC HTTP request request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="af1aa-161">`GrpcWebMode.GrpcWeb` konfiguruje obsah, který se má odeslat bez kódování.</span><span class="sxs-lookup"><span data-stu-id="af1aa-161">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="af1aa-162">Výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="af1aa-162">Default value.</span></span>
    * <span data-ttu-id="af1aa-163">`GrpcWebMode.GrpcWebText` konfiguruje obsah jako kódovaný v kódování Base64.</span><span class="sxs-lookup"><span data-stu-id="af1aa-163">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="af1aa-164">Vyžaduje se pro volání streamování serveru v prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="af1aa-164">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="af1aa-165">**HttpVersion**: `Version` protokolu HTTP se používá k nastavení [zprávy HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) na podkladové žádosti HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="af1aa-165">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="af1aa-166">gRPC-web nevyžaduje konkrétní verzi a nepřepisuje výchozí, pokud není zadaný.</span><span class="sxs-lookup"><span data-stu-id="af1aa-166">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="af1aa-167">Vygenerované klienty gRPC mají synchronizační a asynchronní metody pro volání unárních metod.</span><span class="sxs-lookup"><span data-stu-id="af1aa-167">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="af1aa-168">`SayHello` je například synchronizace a `SayHelloAsync` je asynchronní.</span><span class="sxs-lookup"><span data-stu-id="af1aa-168">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="af1aa-169">Volání metody synchronizace v aplikaci Blazor WebAssembly způsobí, že aplikace přestane reagovat.</span><span class="sxs-lookup"><span data-stu-id="af1aa-169">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="af1aa-170">Asynchronní metody musí být vždy použity v Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="af1aa-170">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af1aa-171">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="af1aa-171">Additional resources</span></span>

* [<span data-ttu-id="af1aa-172">gRPC pro webového klienta GitHub Project</span><span class="sxs-lookup"><span data-stu-id="af1aa-172">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
