---
title: Aktivace middlewaru založená na továrně v ASP.NET Core
author: guardrex
description: Naučte se používat middleware silného typu s implementací aktivace na základě továrny v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 17018d2dd20ed7b26bd0aa1095fa720a73f77261
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71186941"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="20e0d-103">Aktivace middlewaru založená na továrně v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="20e0d-103">Factory-based middleware activation in ASP.NET Core</span></span>

<span data-ttu-id="20e0d-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="20e0d-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="20e0d-105"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>je bod rozšiřitelnosti pro aktivaci [middlewaru](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="20e0d-105"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="20e0d-106"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>rozšiřující metody kontrolují, jestli implementuje <xref:Microsoft.AspNetCore.Http.IMiddleware>registrovaný typ middlewaru.</span><span class="sxs-lookup"><span data-stu-id="20e0d-106"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="20e0d-107">V takovém <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> případě instance zaregistrovaná v kontejneru slouží k <xref:Microsoft.AspNetCore.Http.IMiddleware> vyřešení implementace namísto použití logiky aktivace middleware založeného na konvencích.</span><span class="sxs-lookup"><span data-stu-id="20e0d-107">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="20e0d-108">Middleware je zaregistrován jako [služba v oboru nebo přechodné službě](xref:fundamentals/dependency-injection#service-lifetimes) v kontejneru služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="20e0d-108">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="20e0d-109">Výhodnější</span><span class="sxs-lookup"><span data-stu-id="20e0d-109">Benefits:</span></span>

* <span data-ttu-id="20e0d-110">Aktivace na žádost klienta (vkládání oboru služeb)</span><span class="sxs-lookup"><span data-stu-id="20e0d-110">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="20e0d-111">Silné zadání middlewaru</span><span class="sxs-lookup"><span data-stu-id="20e0d-111">Strong typing of middleware</span></span>

<span data-ttu-id="20e0d-112"><xref:Microsoft.AspNetCore.Http.IMiddleware>je aktivováno na žádost klienta (připojení), takže oborové služby lze vložit do konstruktoru middlewaru.</span><span class="sxs-lookup"><span data-stu-id="20e0d-112"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="20e0d-113">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="20e0d-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="20e0d-114">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="20e0d-114">IMiddleware</span></span>

<span data-ttu-id="20e0d-115"><xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál žádostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="20e0d-115"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="20e0d-116">Metoda [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) zpracovává požadavky a vrací <xref:System.Threading.Tasks.Task> , která představuje spuštění middlewaru.</span><span class="sxs-lookup"><span data-stu-id="20e0d-116">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="20e0d-117">Middleware aktivované konvencí:</span><span class="sxs-lookup"><span data-stu-id="20e0d-117">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="20e0d-118">Middleware aktivované <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span><span class="sxs-lookup"><span data-stu-id="20e0d-118">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="20e0d-119">Pro middleware jsou vytvořena rozšíření:</span><span class="sxs-lookup"><span data-stu-id="20e0d-119">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="20e0d-120">Do middlewaru aktivovaného výrobou nelze předat objekty pomocí <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span><span class="sxs-lookup"><span data-stu-id="20e0d-120">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="20e0d-121">Middleware aktivovaný v továrně se přidají do integrovaného kontejneru v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="20e0d-121">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="20e0d-122">V kanálu zpracování požadavků jsou zaregistrované middleware v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="20e0d-122">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="20e0d-123">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="20e0d-123">IMiddlewareFactory</span></span>

<span data-ttu-id="20e0d-124"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middlewaru.</span><span class="sxs-lookup"><span data-stu-id="20e0d-124"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="20e0d-125">Implementace služby middleware middleware je registrována v kontejneru jako služba vymezená oborem.</span><span class="sxs-lookup"><span data-stu-id="20e0d-125">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="20e0d-126">Výchozí <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementace, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, se nachází v balíčku [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .</span><span class="sxs-lookup"><span data-stu-id="20e0d-126">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="20e0d-127"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>je bod rozšiřitelnosti pro aktivaci [middlewaru](xref:fundamentals/middleware/index) .</span><span class="sxs-lookup"><span data-stu-id="20e0d-127"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="20e0d-128"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>rozšiřující metody kontrolují, jestli implementuje <xref:Microsoft.AspNetCore.Http.IMiddleware>registrovaný typ middlewaru.</span><span class="sxs-lookup"><span data-stu-id="20e0d-128"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="20e0d-129">V takovém <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> případě instance zaregistrovaná v kontejneru slouží k <xref:Microsoft.AspNetCore.Http.IMiddleware> vyřešení implementace namísto použití logiky aktivace middleware založeného na konvencích.</span><span class="sxs-lookup"><span data-stu-id="20e0d-129">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="20e0d-130">Middleware je zaregistrován jako [služba v oboru nebo přechodné službě](xref:fundamentals/dependency-injection#service-lifetimes) v kontejneru služby aplikace.</span><span class="sxs-lookup"><span data-stu-id="20e0d-130">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="20e0d-131">Výhodnější</span><span class="sxs-lookup"><span data-stu-id="20e0d-131">Benefits:</span></span>

* <span data-ttu-id="20e0d-132">Aktivace na žádost klienta (vkládání oboru služeb)</span><span class="sxs-lookup"><span data-stu-id="20e0d-132">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="20e0d-133">Silné zadání middlewaru</span><span class="sxs-lookup"><span data-stu-id="20e0d-133">Strong typing of middleware</span></span>

<span data-ttu-id="20e0d-134"><xref:Microsoft.AspNetCore.Http.IMiddleware>je aktivováno na žádost klienta (připojení), takže oborové služby lze vložit do konstruktoru middlewaru.</span><span class="sxs-lookup"><span data-stu-id="20e0d-134"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="20e0d-135">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="20e0d-135">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="20e0d-136">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="20e0d-136">IMiddleware</span></span>

<span data-ttu-id="20e0d-137"><xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál žádostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="20e0d-137"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="20e0d-138">Metoda [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) zpracovává požadavky a vrací <xref:System.Threading.Tasks.Task> , která představuje spuštění middlewaru.</span><span class="sxs-lookup"><span data-stu-id="20e0d-138">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="20e0d-139">Middleware aktivované konvencí:</span><span class="sxs-lookup"><span data-stu-id="20e0d-139">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="20e0d-140">Middleware aktivované <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span><span class="sxs-lookup"><span data-stu-id="20e0d-140">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="20e0d-141">Pro middleware jsou vytvořena rozšíření:</span><span class="sxs-lookup"><span data-stu-id="20e0d-141">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="20e0d-142">Do middlewaru aktivovaného výrobou nelze předat objekty pomocí <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span><span class="sxs-lookup"><span data-stu-id="20e0d-142">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="20e0d-143">Middleware aktivovaný v továrně se přidají do integrovaného kontejneru v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="20e0d-143">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="20e0d-144">V kanálu zpracování požadavků jsou zaregistrované middleware v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="20e0d-144">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="20e0d-145">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="20e0d-145">IMiddlewareFactory</span></span>

<span data-ttu-id="20e0d-146"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middlewaru.</span><span class="sxs-lookup"><span data-stu-id="20e0d-146"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="20e0d-147">Implementace služby middleware middleware je registrována v kontejneru jako služba vymezená oborem.</span><span class="sxs-lookup"><span data-stu-id="20e0d-147">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="20e0d-148">Výchozí <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementace, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, se nachází v balíčku [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .</span><span class="sxs-lookup"><span data-stu-id="20e0d-148">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="20e0d-149">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="20e0d-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
