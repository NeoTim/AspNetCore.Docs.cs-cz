---
title: Middleware založený na objekt pro vytváření technologie aktivace v ASP.NET Core
author: guardrex
description: Další informace o použití middlewaru silného typu pomocí implementace aktivaci založenou na objekt pro vytváření v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2019
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 9305616ce3f2ef49cf9dfcab719f673c0fb4b51e
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "58809163"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="59317-103">Middleware založený na objekt pro vytváření technologie aktivace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59317-103">Factory-based middleware activation in ASP.NET Core</span></span>

<span data-ttu-id="59317-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="59317-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="59317-105"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> představuje rozšíření bod pro [middleware](xref:fundamentals/middleware/index) aktivace.</span><span class="sxs-lookup"><span data-stu-id="59317-105"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="59317-106"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> rozšiřující metody zkontrolovat, pokud middlewarem registrovaného typu implementuje <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span><span class="sxs-lookup"><span data-stu-id="59317-106"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="59317-107">Pokud ano, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance zaregistrovaný v kontejneru se používá k překladu <xref:Microsoft.AspNetCore.Http.IMiddleware> implementace namísto použití logiky aktivace založené na konvenci middlewaru.</span><span class="sxs-lookup"><span data-stu-id="59317-107">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="59317-108">Middleware je zaregistrovaný jako [služby s vymezeným oborem nebo přechodné](xref:fundamentals/dependency-injection#service-lifetimes) v kontejneru aplikace služby.</span><span class="sxs-lookup"><span data-stu-id="59317-108">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="59317-109">Výhody:</span><span class="sxs-lookup"><span data-stu-id="59317-109">Benefits:</span></span>

* <span data-ttu-id="59317-110">Aktivace každý požadavek klienta (vkládání vymezené služby)</span><span class="sxs-lookup"><span data-stu-id="59317-110">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="59317-111">Silné typování middlewaru</span><span class="sxs-lookup"><span data-stu-id="59317-111">Strong typing of middleware</span></span>

<span data-ttu-id="59317-112"><xref:Microsoft.AspNetCore.Http.IMiddleware> je aktivovaná, každý požadavek klienta (připojení), takže vymezené služby můžete vloženy do konstruktoru middlewaru.</span><span class="sxs-lookup"><span data-stu-id="59317-112"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="59317-113">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="59317-113">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="59317-114">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="59317-114">IMiddleware</span></span>

<span data-ttu-id="59317-115"><xref:Microsoft.AspNetCore.Http.IMiddleware> Definuje middleware pro kanál žádosti o aplikace.</span><span class="sxs-lookup"><span data-stu-id="59317-115"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="59317-116">[InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) metoda zpracovává požadavky a vrátí <xref:System.Threading.Tasks.Task> , který představuje spuštění middlewaru.</span><span class="sxs-lookup"><span data-stu-id="59317-116">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="59317-117">Middleware aktivoval konvence:</span><span class="sxs-lookup"><span data-stu-id="59317-117">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="59317-118">Middleware aktivoval <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span><span class="sxs-lookup"><span data-stu-id="59317-118">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="59317-119">Rozšíření jsou vytvořeny pro middlewares:</span><span class="sxs-lookup"><span data-stu-id="59317-119">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="59317-120">Není možné předat objekty factory aktivuje middleware s <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span><span class="sxs-lookup"><span data-stu-id="59317-120">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="59317-121">Middleware aktivovat objekt pro vytváření se přidá do kontejneru integrované v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="59317-121">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="59317-122">Obě middlewares zaregistrovaní v kanálu zpracování žádostí v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="59317-122">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="59317-123">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="59317-123">IMiddlewareFactory</span></span>

<span data-ttu-id="59317-124"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> poskytuje metody pro vytvoření middlewaru.</span><span class="sxs-lookup"><span data-stu-id="59317-124"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="59317-125">Implementace objektu factory middlewaru je zaregistrovaný v kontejneru jako služba s vymezeným oborem.</span><span class="sxs-lookup"><span data-stu-id="59317-125">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="59317-126">Výchozí hodnota <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementaci <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, najdete v [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="59317-126">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="59317-127">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="59317-127">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
