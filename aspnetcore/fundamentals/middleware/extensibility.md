---
title: Aktivace middlewaru na bázi továrny v ASP.NET Core
author: rick-anderson
description: Naučte se používat middleware silného typu s implementací aktivace z výroby v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: abc6268584d12fe43d972c79a99316b94e8bee4b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663091"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="4c301-103">Aktivace middlewaru na bázi továrny v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c301-103">Factory-based middleware activation in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4c301-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>je rozšiřitelnost bod pro [middleware](xref:fundamentals/middleware/index) aktivace.</span><span class="sxs-lookup"><span data-stu-id="4c301-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="4c301-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>metody rozšíření zkontrolujte, zda middleware <xref:Microsoft.AspNetCore.Http.IMiddleware>registrovaný typ implementuje .</span><span class="sxs-lookup"><span data-stu-id="4c301-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="4c301-106">Pokud ano, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registrovaná v kontejneru <xref:Microsoft.AspNetCore.Http.IMiddleware> se používá k vyřešení implementace namísto použití logiky aktivace middlewaru založené na konvencích.</span><span class="sxs-lookup"><span data-stu-id="4c301-106">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="4c301-107">Middleware je registrován jako [oborová nebo přechodná služba](xref:fundamentals/dependency-injection#service-lifetimes) v kontejneru služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c301-107">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="4c301-108">Výhody:</span><span class="sxs-lookup"><span data-stu-id="4c301-108">Benefits:</span></span>

* <span data-ttu-id="4c301-109">Aktivace na požadavek klienta (vkládání služeb s rozsahem)</span><span class="sxs-lookup"><span data-stu-id="4c301-109">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="4c301-110">Silné psaní middleware</span><span class="sxs-lookup"><span data-stu-id="4c301-110">Strong typing of middleware</span></span>

<span data-ttu-id="4c301-111"><xref:Microsoft.AspNetCore.Http.IMiddleware>je aktivován na požadavek klienta (připojení), takže oborové služby mohou být vloženy do konstruktoru middleware.</span><span class="sxs-lookup"><span data-stu-id="4c301-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="4c301-112">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="4c301-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="4c301-113">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="4c301-113">IMiddleware</span></span>

<span data-ttu-id="4c301-114"><xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c301-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="4c301-115">Metoda [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) zpracovává požadavky a <xref:System.Threading.Tasks.Task> vrací, která představuje spuštění middlewaru.</span><span class="sxs-lookup"><span data-stu-id="4c301-115">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="4c301-116">Middleware aktivován konvencí:</span><span class="sxs-lookup"><span data-stu-id="4c301-116">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="4c301-117">Middleware aktivován: <xref:Microsoft.AspNetCore.Http.MiddlewareFactory></span><span class="sxs-lookup"><span data-stu-id="4c301-117">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="4c301-118">Rozšíření jsou vytvořeny pro middlewares:</span><span class="sxs-lookup"><span data-stu-id="4c301-118">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="4c301-119">Není možné předat objekty do továrně aktivovaného <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>middlewaru s:</span><span class="sxs-lookup"><span data-stu-id="4c301-119">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="4c301-120">Middleware aktivovaný z výroby se přidá do `Startup.ConfigureServices`vestavěného kontejneru v:</span><span class="sxs-lookup"><span data-stu-id="4c301-120">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="4c301-121">Oba middlewares jsou registrovány v `Startup.Configure`kanálu zpracování požadavků v :</span><span class="sxs-lookup"><span data-stu-id="4c301-121">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="4c301-122">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="4c301-122">IMiddlewareFactory</span></span>

<span data-ttu-id="4c301-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middleware.</span><span class="sxs-lookup"><span data-stu-id="4c301-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="4c301-124">Middleware factory implementace je registrována v kontejneru jako oborové služby.</span><span class="sxs-lookup"><span data-stu-id="4c301-124">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="4c301-125">Výchozí <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementace <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, je nalezena v balíčku [Microsoft.AspNetCore.Http.](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/)</span><span class="sxs-lookup"><span data-stu-id="4c301-125">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4c301-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>je rozšiřitelnost bod pro [middleware](xref:fundamentals/middleware/index) aktivace.</span><span class="sxs-lookup"><span data-stu-id="4c301-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="4c301-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>metody rozšíření zkontrolujte, zda middleware <xref:Microsoft.AspNetCore.Http.IMiddleware>registrovaný typ implementuje .</span><span class="sxs-lookup"><span data-stu-id="4c301-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="4c301-128">Pokud ano, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registrovaná v kontejneru <xref:Microsoft.AspNetCore.Http.IMiddleware> se používá k vyřešení implementace namísto použití logiky aktivace middlewaru založené na konvencích.</span><span class="sxs-lookup"><span data-stu-id="4c301-128">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="4c301-129">Middleware je registrován jako [oborová nebo přechodná služba](xref:fundamentals/dependency-injection#service-lifetimes) v kontejneru služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c301-129">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="4c301-130">Výhody:</span><span class="sxs-lookup"><span data-stu-id="4c301-130">Benefits:</span></span>

* <span data-ttu-id="4c301-131">Aktivace na požadavek klienta (vkládání služeb s rozsahem)</span><span class="sxs-lookup"><span data-stu-id="4c301-131">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="4c301-132">Silné psaní middleware</span><span class="sxs-lookup"><span data-stu-id="4c301-132">Strong typing of middleware</span></span>

<span data-ttu-id="4c301-133"><xref:Microsoft.AspNetCore.Http.IMiddleware>je aktivován na požadavek klienta (připojení), takže oborové služby mohou být vloženy do konstruktoru middleware.</span><span class="sxs-lookup"><span data-stu-id="4c301-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="4c301-134">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="4c301-134">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="4c301-135">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="4c301-135">IMiddleware</span></span>

<span data-ttu-id="4c301-136"><xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="4c301-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="4c301-137">Metoda [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) zpracovává požadavky a <xref:System.Threading.Tasks.Task> vrací, která představuje spuštění middlewaru.</span><span class="sxs-lookup"><span data-stu-id="4c301-137">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="4c301-138">Middleware aktivován konvencí:</span><span class="sxs-lookup"><span data-stu-id="4c301-138">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="4c301-139">Middleware aktivován: <xref:Microsoft.AspNetCore.Http.MiddlewareFactory></span><span class="sxs-lookup"><span data-stu-id="4c301-139">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="4c301-140">Rozšíření jsou vytvořeny pro middlewares:</span><span class="sxs-lookup"><span data-stu-id="4c301-140">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="4c301-141">Není možné předat objekty do továrně aktivovaného <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>middlewaru s:</span><span class="sxs-lookup"><span data-stu-id="4c301-141">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="4c301-142">Middleware aktivovaný z výroby se přidá do `Startup.ConfigureServices`vestavěného kontejneru v:</span><span class="sxs-lookup"><span data-stu-id="4c301-142">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="4c301-143">Oba middlewares jsou registrovány v `Startup.Configure`kanálu zpracování požadavků v :</span><span class="sxs-lookup"><span data-stu-id="4c301-143">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="4c301-144">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="4c301-144">IMiddlewareFactory</span></span>

<span data-ttu-id="4c301-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middleware.</span><span class="sxs-lookup"><span data-stu-id="4c301-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="4c301-146">Middleware factory implementace je registrována v kontejneru jako oborové služby.</span><span class="sxs-lookup"><span data-stu-id="4c301-146">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="4c301-147">Výchozí <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementace <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, je nalezena v balíčku [Microsoft.AspNetCore.Http.](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/)</span><span class="sxs-lookup"><span data-stu-id="4c301-147">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4c301-148">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4c301-148">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
