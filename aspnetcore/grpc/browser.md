---
title: gRPC v prohlížečových aplikacích
author: jamesnk
description: Naučte se konfigurovat gRPC služby na ASP.NET Core, které se mají volat z aplikací pro prohlížeč pomocí gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/24/2020
uid: grpc/browser
ms.openlocfilehash: 6359c3b76b3cb1ba2b6d9f9a989f64cbf4c4379d
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76830658"
---
# <a name="grpc-in-browser-apps"></a><span data-ttu-id="b0bb0-103">gRPC v prohlížečových aplikacích</span><span class="sxs-lookup"><span data-stu-id="b0bb0-103">gRPC in browser apps</span></span>

<span data-ttu-id="b0bb0-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="b0bb0-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b0bb0-105">**gRPC – webová podpora v .NET je experimentální**</span><span class="sxs-lookup"><span data-stu-id="b0bb0-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="b0bb0-106">gRPC-web pro .NET je experimentální projekt, nikoli potvrzený produkt.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="b0bb0-107">Chceme:</span><span class="sxs-lookup"><span data-stu-id="b0bb0-107">We want to:</span></span>
>
> * <span data-ttu-id="b0bb0-108">Otestujte, jak náš přístup k implementaci gRPC-web funguje.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="b0bb0-109">Získejte zpětnou vazbu, pokud je tento přístup užitečný pro vývojáře na platformě .NET v porovnání s tradičním způsobem nastavení gRPC-web prostřednictvím proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="b0bb0-110">Zajistěte prosím svůj názor na [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) , abyste se ujistili, že jsme vytvořili něco, co vývojáři rádi a máte produktivní.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="b0bb0-111">Nemůžete volat službu gRPC HTTP/2 z aplikace založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="b0bb0-112">[gRPC-web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) je protokol, který umožňuje aplikacím Javascript a Blazor v prohlížeči volat služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="b0bb0-113">Tento článek vysvětluje, jak používat gRPC-web v .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="b0bb0-114">Konfigurace gRPC-web v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b0bb0-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="b0bb0-115">služby gRPC hostované v ASP.NET Core můžou být nakonfigurované tak, aby podporovaly gRPC-web vedle HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="b0bb0-116">gRPC-web nevyžaduje žádné změny služeb.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="b0bb0-117">Jedinou úpravou je spuštění konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="b0bb0-118">Povolení gRPC-web pomocí služby ASP.NET Core gRPC:</span><span class="sxs-lookup"><span data-stu-id="b0bb0-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="b0bb0-119">Přidejte odkaz na balíček [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="b0bb0-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="b0bb0-120">Nakonfigurujte aplikaci tak, aby používala gRPC-web přidáním `AddGrpcWeb` a `UseGrpcWeb` do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b0bb0-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=3,10,14)]

<span data-ttu-id="b0bb0-121">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="b0bb0-121">The preceding code:</span></span>

* <span data-ttu-id="b0bb0-122">Přidá middleware gRPC-web middleware, `UseGrpcWeb`po směrování a před koncovými body.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="b0bb0-123">Určuje metodu `endpoints.MapGrpcService<GreeterService>()` podporuje gRPC-web s `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="b0bb0-124">Případně můžete nakonfigurovat všechny služby tak, aby podporovaly gRPC-web přidáním `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` do ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=5,12,16)]

<span data-ttu-id="b0bb0-125">K volání gRPC-web z prohlížeče může být potřeba některá další konfigurace, jako je například konfigurace ASP.NET Core pro podporu CORS.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-125">Some additional configuration may be required to call gRPC-Web from the browser, such as configuring ASP.NET Core to support CORS.</span></span> <span data-ttu-id="b0bb0-126">Další informace najdete v tématu [Podpora CORS](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="b0bb0-126">For more information, see [support CORS](xref:security/cors).</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="b0bb0-127">Volání gRPC-web z prohlížeče</span><span class="sxs-lookup"><span data-stu-id="b0bb0-127">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="b0bb0-128">Aplikace prohlížeče můžou pomocí gRPC-web volat služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-128">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="b0bb0-129">Existují některé požadavky a omezení při volání služeb gRPC Services pomocí gRPC-web z prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="b0bb0-129">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="b0bb0-130">Server musí být nakonfigurovaný tak, aby podporoval gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-130">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="b0bb0-131">Volání streamování klientů a obousměrného streamování nejsou podporovaná.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-131">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="b0bb0-132">Streamování serveru je podporované.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-132">Server streaming is supported.</span></span>
* <span data-ttu-id="b0bb0-133">Volání služeb gRPC Services v jiné doméně vyžaduje, aby na serveru byla nakonfigurovaná [CORS](xref:security/cors) .</span><span class="sxs-lookup"><span data-stu-id="b0bb0-133">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="b0bb0-134">JavaScript gRPC – webový klient</span><span class="sxs-lookup"><span data-stu-id="b0bb0-134">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="b0bb0-135">K dispozici je gRPC JavaScript-Web Client.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-135">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="b0bb0-136">Pokyny, jak používat gRPC-web z JavaScriptu, najdete v tématu [Zápis kódu klienta JavaScript pomocí gRPC-web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="b0bb0-136">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="b0bb0-137">Konfigurace gRPC-web pomocí klienta .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="b0bb0-137">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="b0bb0-138">Klient .NET gRPC se dá nakonfigurovat tak, aby gRPC webová volání.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-138">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="b0bb0-139">To je užitečné pro [Blazor aplikace WebAssembly](xref:blazor/index#blazor-webassembly) , které jsou hostovány v prohlížeči a mají stejná omezení http kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-139">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="b0bb0-140">Volání gRPC-web s klientem .NET je [stejné jako http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="b0bb0-140">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="b0bb0-141">Jedinou úpravou je způsob vytvoření kanálu.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-141">The only modification is how the channel is created.</span></span>

<span data-ttu-id="b0bb0-142">Použití gRPC-web:</span><span class="sxs-lookup"><span data-stu-id="b0bb0-142">To use gRPC-Web:</span></span>

* <span data-ttu-id="b0bb0-143">Přidejte odkaz na balíček [Grpc .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="b0bb0-143">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="b0bb0-144">Nakonfigurujte kanál tak, aby používal `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="b0bb0-144">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="b0bb0-145">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="b0bb0-145">The preceding code:</span></span>

* <span data-ttu-id="b0bb0-146">Nakonfiguruje kanál tak, aby používal gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-146">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="b0bb0-147">Vytvoří klienta a provede volání pomocí kanálu.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-147">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="b0bb0-148">`GrpcWebHandler` má při vytvoření následující možnosti konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b0bb0-148">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="b0bb0-149">**InnerHandler**: podkladové <xref:System.Net.Http.HttpMessageHandler>, které provádí volání http, například `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-149">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the HTTP call, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="b0bb0-150">**Režim**: `GrpcWebMode` Enum.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-150">**Mode**: `GrpcWebMode` enum.</span></span> <span data-ttu-id="b0bb0-151">`GrpcWebMode.GrpcWebText` konfiguruje obsah tak, aby byl kódovaný v kódování Base64, který je nutný k podpoře volání streamování serveru.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-151">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded, which is required to support server streaming calls.</span></span>
* <span data-ttu-id="b0bb0-152">**HttpVersion**: `Version`protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-152">**HttpVersion**: HTTP protocol `Version`.</span></span> <span data-ttu-id="b0bb0-153">gRPC-web nevyžaduje konkrétní protokol a při vytváření požadavku ho nezadá, pokud není nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="b0bb0-153">gRPC-Web doesn't require a specific protocol and won't specify one when making a request unless configured.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b0bb0-154">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="b0bb0-154">Additional resources</span></span>

* [<span data-ttu-id="b0bb0-155">gRPC pro webového klienta GitHub Project</span><span class="sxs-lookup"><span data-stu-id="b0bb0-155">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
