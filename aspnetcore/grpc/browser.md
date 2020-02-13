---
title: Použití gRPC v prohlížečových aplikacích
author: jamesnk
description: Naučte se konfigurovat gRPC služby na ASP.NET Core, které se mají volat z aplikací pro prohlížeč pomocí gRPC-Web.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/10/2020
uid: grpc/browser
ms.openlocfilehash: 333fc8c4277bbac47042d4904c276e963186914a
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172277"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="01185-103">Použití gRPC v prohlížečových aplikacích</span><span class="sxs-lookup"><span data-stu-id="01185-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="01185-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="01185-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="01185-105">**gRPC – webová podpora v .NET je experimentální**</span><span class="sxs-lookup"><span data-stu-id="01185-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="01185-106">gRPC-web pro .NET je experimentální projekt, nikoli potvrzený produkt.</span><span class="sxs-lookup"><span data-stu-id="01185-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="01185-107">Chceme:</span><span class="sxs-lookup"><span data-stu-id="01185-107">We want to:</span></span>
>
> * <span data-ttu-id="01185-108">Otestujte, jak náš přístup k implementaci gRPC-web funguje.</span><span class="sxs-lookup"><span data-stu-id="01185-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="01185-109">Získejte zpětnou vazbu, pokud je tento přístup užitečný pro vývojáře na platformě .NET v porovnání s tradičním způsobem nastavení gRPC-web prostřednictvím proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="01185-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="01185-110">Zajistěte prosím svůj názor na [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) , abyste se ujistili, že jsme vytvořili něco, co vývojáři rádi a máte produktivní.</span><span class="sxs-lookup"><span data-stu-id="01185-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="01185-111">Nemůžete volat službu gRPC HTTP/2 z aplikace založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="01185-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="01185-112">[gRPC-web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) je protokol, který umožňuje aplikacím Javascript a Blazor v prohlížeči volat služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="01185-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="01185-113">Tento článek vysvětluje, jak používat gRPC-web v .NET Core.</span><span class="sxs-lookup"><span data-stu-id="01185-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="01185-114">Konfigurace gRPC-web v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="01185-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="01185-115">služby gRPC hostované v ASP.NET Core můžou být nakonfigurované tak, aby podporovaly gRPC-web vedle HTTP/2 gRPC.</span><span class="sxs-lookup"><span data-stu-id="01185-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="01185-116">gRPC-web nevyžaduje žádné změny služeb.</span><span class="sxs-lookup"><span data-stu-id="01185-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="01185-117">Jedinou úpravou je spuštění konfigurace.</span><span class="sxs-lookup"><span data-stu-id="01185-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="01185-118">Povolení gRPC-web pomocí služby ASP.NET Core gRPC:</span><span class="sxs-lookup"><span data-stu-id="01185-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="01185-119">Přidejte odkaz na balíček [Grpc. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) .</span><span class="sxs-lookup"><span data-stu-id="01185-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="01185-120">Nakonfigurujte aplikaci tak, aby používala gRPC-web přidáním `AddGrpcWeb` a `UseGrpcWeb` do *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="01185-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="01185-121">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="01185-121">The preceding code:</span></span>

* <span data-ttu-id="01185-122">Přidá middleware gRPC-web middleware, `UseGrpcWeb`po směrování a před koncovými body.</span><span class="sxs-lookup"><span data-stu-id="01185-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="01185-123">Určuje metodu `endpoints.MapGrpcService<GreeterService>()` podporuje gRPC-web s `EnableGrpcWeb`.</span><span class="sxs-lookup"><span data-stu-id="01185-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="01185-124">Případně můžete nakonfigurovat všechny služby tak, aby podporovaly gRPC-web přidáním `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` do ConfigureServices.</span><span class="sxs-lookup"><span data-stu-id="01185-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

<span data-ttu-id="01185-125">K volání gRPC-web z prohlížeče může být potřeba některá další konfigurace, jako je například konfigurace ASP.NET Core pro podporu CORS.</span><span class="sxs-lookup"><span data-stu-id="01185-125">Some additional configuration may be required to call gRPC-Web from the browser, such as configuring ASP.NET Core to support CORS.</span></span> <span data-ttu-id="01185-126">Další informace najdete v tématu [Podpora CORS](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="01185-126">For more information, see [support CORS](xref:security/cors).</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="01185-127">Volání gRPC-web z prohlížeče</span><span class="sxs-lookup"><span data-stu-id="01185-127">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="01185-128">Aplikace prohlížeče můžou pomocí gRPC-web volat služby gRPC.</span><span class="sxs-lookup"><span data-stu-id="01185-128">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="01185-129">Existují některé požadavky a omezení při volání služeb gRPC Services pomocí gRPC-web z prohlížeče:</span><span class="sxs-lookup"><span data-stu-id="01185-129">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="01185-130">Server musí být nakonfigurovaný tak, aby podporoval gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="01185-130">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="01185-131">Volání streamování klientů a obousměrného streamování nejsou podporovaná.</span><span class="sxs-lookup"><span data-stu-id="01185-131">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="01185-132">Streamování serveru je podporované.</span><span class="sxs-lookup"><span data-stu-id="01185-132">Server streaming is supported.</span></span>
* <span data-ttu-id="01185-133">Volání služeb gRPC Services v jiné doméně vyžaduje, aby na serveru byla nakonfigurovaná [CORS](xref:security/cors) .</span><span class="sxs-lookup"><span data-stu-id="01185-133">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="01185-134">JavaScript gRPC – webový klient</span><span class="sxs-lookup"><span data-stu-id="01185-134">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="01185-135">K dispozici je gRPC JavaScript-Web Client.</span><span class="sxs-lookup"><span data-stu-id="01185-135">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="01185-136">Pokyny, jak používat gRPC-web z JavaScriptu, najdete v tématu [Zápis kódu klienta JavaScript pomocí gRPC-web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="01185-136">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="01185-137">Konfigurace gRPC-web pomocí klienta .NET gRPC</span><span class="sxs-lookup"><span data-stu-id="01185-137">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="01185-138">Klient .NET gRPC se dá nakonfigurovat tak, aby gRPC webová volání.</span><span class="sxs-lookup"><span data-stu-id="01185-138">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="01185-139">To je užitečné pro [Blazor aplikace WebAssembly](xref:blazor/index#blazor-webassembly) , které jsou hostovány v prohlížeči a mají stejná omezení http kódu jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="01185-139">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="01185-140">Volání gRPC-web s klientem .NET je [stejné jako http/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="01185-140">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="01185-141">Jedinou úpravou je způsob vytvoření kanálu.</span><span class="sxs-lookup"><span data-stu-id="01185-141">The only modification is how the channel is created.</span></span>

<span data-ttu-id="01185-142">Použití gRPC-web:</span><span class="sxs-lookup"><span data-stu-id="01185-142">To use gRPC-Web:</span></span>

* <span data-ttu-id="01185-143">Přidejte odkaz na balíček [Grpc .NET. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) .</span><span class="sxs-lookup"><span data-stu-id="01185-143">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="01185-144">Zajistěte, aby byl odkaz na balíček [Grpc .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) 2.27.0 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="01185-144">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="01185-145">Nakonfigurujte kanál tak, aby používal `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="01185-145">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="01185-146">Předchozí kód:</span><span class="sxs-lookup"><span data-stu-id="01185-146">The preceding code:</span></span>

* <span data-ttu-id="01185-147">Nakonfiguruje kanál tak, aby používal gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="01185-147">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="01185-148">Vytvoří klienta a provede volání pomocí kanálu.</span><span class="sxs-lookup"><span data-stu-id="01185-148">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="01185-149">`GrpcWebHandler` má při vytvoření následující možnosti konfigurace:</span><span class="sxs-lookup"><span data-stu-id="01185-149">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="01185-150">**InnerHandler**: podkladové <xref:System.Net.Http.HttpMessageHandler>, které vytváří požadavek gRPC http, například `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="01185-150">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="01185-151">**Mode**: typ výčtu určující, zda `Content-Type` požadavek HTTP gRPC je `application/grpc-web` nebo `application/grpc-web-text`.</span><span class="sxs-lookup"><span data-stu-id="01185-151">**Mode**: An enumeration type that specifies whether the gRPC HTTP request request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="01185-152">`GrpcWebMode.GrpcWeb` konfiguruje obsah, který se má odeslat bez kódování.</span><span class="sxs-lookup"><span data-stu-id="01185-152">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="01185-153">Výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="01185-153">Default value.</span></span>
    * <span data-ttu-id="01185-154">`GrpcWebMode.GrpcWebText` konfiguruje obsah jako kódovaný v kódování Base64.</span><span class="sxs-lookup"><span data-stu-id="01185-154">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="01185-155">Vyžaduje se pro volání streamování serveru v prohlížečích.</span><span class="sxs-lookup"><span data-stu-id="01185-155">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="01185-156">**HttpVersion**: `Version` protokolu HTTP se používá k nastavení [zprávy HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) na podkladové žádosti HTTP gRPC.</span><span class="sxs-lookup"><span data-stu-id="01185-156">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="01185-157">gRPC-web nevyžaduje konkrétní verzi a nepřepisuje výchozí, pokud není zadaný.</span><span class="sxs-lookup"><span data-stu-id="01185-157">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="01185-158">Vygenerované klienty gRPC mají synchronizační a asynchronní metody pro volání unárních metod.</span><span class="sxs-lookup"><span data-stu-id="01185-158">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="01185-159">`SayHello` je například synchronizace a `SayHelloAsync` je asynchronní.</span><span class="sxs-lookup"><span data-stu-id="01185-159">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="01185-160">Volání metody synchronizace v aplikaci Blazor WebAssembly způsobí, že aplikace přestane reagovat.</span><span class="sxs-lookup"><span data-stu-id="01185-160">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="01185-161">Asynchronní metody musí být vždy použity v Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="01185-161">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="01185-162">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="01185-162">Additional resources</span></span>

* [<span data-ttu-id="01185-163">gRPC pro webového klienta GitHub Project</span><span class="sxs-lookup"><span data-stu-id="01185-163">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
