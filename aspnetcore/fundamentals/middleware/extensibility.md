---
title: Aktivace middlewaru založená na továrně v ASP.NET Core
author: rick-anderson
description: Naučte se používat middleware silného typu s implementací aktivace na základě továrny v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
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
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: b45633baa804c8210ff00bd1bd8f8877c10581eb
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634732"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="71e38-103">Aktivace middlewaru založená na továrně v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71e38-103">Factory-based middleware activation in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71e38-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> je bod rozšiřitelnosti pro aktivaci [middlewaru](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="71e38-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="71e38-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> rozšiřující metody kontrolují, jestli implementuje registrovaný typ middlewaru <xref:Microsoft.AspNetCore.Http.IMiddleware> .</span><span class="sxs-lookup"><span data-stu-id="71e38-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="71e38-106">V takovém případě <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance zaregistrovaná v kontejneru slouží k vyřešení <xref:Microsoft.AspNetCore.Http.IMiddleware> implementace namísto použití logiky aktivace middleware založeného na konvencích.</span><span class="sxs-lookup"><span data-stu-id="71e38-106">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="71e38-107">Middleware je zaregistrován jako [služba v oboru nebo přechodné službě](xref:fundamentals/dependency-injection#service-lifetimes) v kontejneru služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="71e38-107">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="71e38-108">Výhody:</span><span class="sxs-lookup"><span data-stu-id="71e38-108">Benefits:</span></span>

* <span data-ttu-id="71e38-109">Aktivace na žádost klienta (vkládání oboru služeb)</span><span class="sxs-lookup"><span data-stu-id="71e38-109">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="71e38-110">Silné zadání middlewaru</span><span class="sxs-lookup"><span data-stu-id="71e38-110">Strong typing of middleware</span></span>

<span data-ttu-id="71e38-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> je aktivováno na žádost klienta (připojení), takže oborové služby lze vložit do konstruktoru middlewaru.</span><span class="sxs-lookup"><span data-stu-id="71e38-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="71e38-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71e38-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="71e38-113">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="71e38-113">IMiddleware</span></span>

<span data-ttu-id="71e38-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> definuje middleware pro kanál žádostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="71e38-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="71e38-115">Metoda [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) zpracovává požadavky a vrací <xref:System.Threading.Tasks.Task> , která představuje spuštění middlewaru.</span><span class="sxs-lookup"><span data-stu-id="71e38-115">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="71e38-116">Middleware aktivované konvencí:</span><span class="sxs-lookup"><span data-stu-id="71e38-116">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="71e38-117">Middleware aktivované <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> :</span><span class="sxs-lookup"><span data-stu-id="71e38-117">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="71e38-118">Pro middleware jsou vytvořena rozšíření:</span><span class="sxs-lookup"><span data-stu-id="71e38-118">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="71e38-119">Do middlewaru aktivovaného výrobou nelze předat objekty pomocí <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> :</span><span class="sxs-lookup"><span data-stu-id="71e38-119">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="71e38-120">Middleware aktivovaný v továrně se přidají do integrovaného kontejneru v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="71e38-120">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="71e38-121">V kanálu zpracování požadavků jsou zaregistrované middleware v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="71e38-121">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="71e38-122">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="71e38-122">IMiddlewareFactory</span></span>

<span data-ttu-id="71e38-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> poskytuje metody pro vytvoření middlewaru.</span><span class="sxs-lookup"><span data-stu-id="71e38-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="71e38-124">Implementace služby middleware middleware je registrována v kontejneru jako služba vymezená oborem.</span><span class="sxs-lookup"><span data-stu-id="71e38-124">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="71e38-125">Výchozí <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementace, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> , se nachází v balíčku [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .</span><span class="sxs-lookup"><span data-stu-id="71e38-125">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71e38-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> je bod rozšiřitelnosti pro aktivaci [middlewaru](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="71e38-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="71e38-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> rozšiřující metody kontrolují, jestli implementuje registrovaný typ middlewaru <xref:Microsoft.AspNetCore.Http.IMiddleware> .</span><span class="sxs-lookup"><span data-stu-id="71e38-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="71e38-128">V takovém případě <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance zaregistrovaná v kontejneru slouží k vyřešení <xref:Microsoft.AspNetCore.Http.IMiddleware> implementace namísto použití logiky aktivace middleware založeného na konvencích.</span><span class="sxs-lookup"><span data-stu-id="71e38-128">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="71e38-129">Middleware je zaregistrován jako [služba v oboru nebo přechodné službě](xref:fundamentals/dependency-injection#service-lifetimes) v kontejneru služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="71e38-129">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="71e38-130">Výhody:</span><span class="sxs-lookup"><span data-stu-id="71e38-130">Benefits:</span></span>

* <span data-ttu-id="71e38-131">Aktivace na žádost klienta (vkládání oboru služeb)</span><span class="sxs-lookup"><span data-stu-id="71e38-131">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="71e38-132">Silné zadání middlewaru</span><span class="sxs-lookup"><span data-stu-id="71e38-132">Strong typing of middleware</span></span>

<span data-ttu-id="71e38-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> je aktivováno na žádost klienta (připojení), takže oborové služby lze vložit do konstruktoru middlewaru.</span><span class="sxs-lookup"><span data-stu-id="71e38-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="71e38-134">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71e38-134">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="71e38-135">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="71e38-135">IMiddleware</span></span>

<span data-ttu-id="71e38-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> definuje middleware pro kanál žádostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="71e38-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="71e38-137">Metoda [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) zpracovává požadavky a vrací <xref:System.Threading.Tasks.Task> , která představuje spuštění middlewaru.</span><span class="sxs-lookup"><span data-stu-id="71e38-137">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="71e38-138">Middleware aktivované konvencí:</span><span class="sxs-lookup"><span data-stu-id="71e38-138">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="71e38-139">Middleware aktivované <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> :</span><span class="sxs-lookup"><span data-stu-id="71e38-139">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="71e38-140">Pro middleware jsou vytvořena rozšíření:</span><span class="sxs-lookup"><span data-stu-id="71e38-140">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="71e38-141">Do middlewaru aktivovaného výrobou nelze předat objekty pomocí <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> :</span><span class="sxs-lookup"><span data-stu-id="71e38-141">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="71e38-142">Middleware aktivovaný v továrně se přidají do integrovaného kontejneru v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="71e38-142">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="71e38-143">V kanálu zpracování požadavků jsou zaregistrované middleware v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="71e38-143">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="71e38-144">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="71e38-144">IMiddlewareFactory</span></span>

<span data-ttu-id="71e38-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> poskytuje metody pro vytvoření middlewaru.</span><span class="sxs-lookup"><span data-stu-id="71e38-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="71e38-146">Implementace služby middleware middleware je registrována v kontejneru jako služba vymezená oborem.</span><span class="sxs-lookup"><span data-stu-id="71e38-146">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="71e38-147">Výchozí <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementace, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> , se nachází v balíčku [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .</span><span class="sxs-lookup"><span data-stu-id="71e38-147">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="71e38-148">Další materiály</span><span class="sxs-lookup"><span data-stu-id="71e38-148">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
