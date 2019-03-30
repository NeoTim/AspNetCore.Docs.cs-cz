---
title: Middleware založený na objekt pro vytváření technologie aktivace v ASP.NET Core
author: guardrex
description: Další informace o použití middlewaru silného typu pomocí implementace aktivaci založenou na objekt pro vytváření v ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: d29c4d3d72ddd8ec3c2a726ee35ae1dc82774537
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750527"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="69ae7-103">Middleware založený na objekt pro vytváření technologie aktivace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="69ae7-103">Factory-based middleware activation in ASP.NET Core</span></span>

<span data-ttu-id="69ae7-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="69ae7-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="69ae7-105">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory)/[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) je bod rozšiřitelnosti pro [middleware](xref:fundamentals/middleware/index) aktivace.</span><span class="sxs-lookup"><span data-stu-id="69ae7-105">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory)/[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="69ae7-106">`UseMiddleware` rozšiřující metody zkontrolovat, pokud middlewarem registrovaného typu implementuje `IMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="69ae7-106">`UseMiddleware` extension methods check if a middleware's registered type implements `IMiddleware`.</span></span> <span data-ttu-id="69ae7-107">Pokud ano, `IMiddlewareFactory` instance zaregistrovaný v kontejneru se používá k překladu `IMiddleware` implementace namísto použití logiky aktivace založené na konvenci middlewaru.</span><span class="sxs-lookup"><span data-stu-id="69ae7-107">If it does, the `IMiddlewareFactory` instance registered in the container is used to resolve the `IMiddleware` implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="69ae7-108">Middleware je registrována jako služba s vymezeným oborem nebo přechodné v kontejneru aplikace služby.</span><span class="sxs-lookup"><span data-stu-id="69ae7-108">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="69ae7-109">Výhody:</span><span class="sxs-lookup"><span data-stu-id="69ae7-109">Benefits:</span></span>

* <span data-ttu-id="69ae7-110">Aktivace každý požadavek klienta (vkládání vymezené služby)</span><span class="sxs-lookup"><span data-stu-id="69ae7-110">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="69ae7-111">Silné typování middlewaru</span><span class="sxs-lookup"><span data-stu-id="69ae7-111">Strong typing of middleware</span></span>

<span data-ttu-id="69ae7-112">`IMiddleware` je aktivovaná, každý požadavek klienta (připojení), takže vymezené služby můžete vloženy do konstruktoru middlewaru.</span><span class="sxs-lookup"><span data-stu-id="69ae7-112">`IMiddleware` is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="69ae7-113">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="69ae7-113">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="69ae7-114">Ukázková aplikace předvádí middleware aktivoval:</span><span class="sxs-lookup"><span data-stu-id="69ae7-114">The sample app demonstrates middleware activated by:</span></span>

* <span data-ttu-id="69ae7-115">Konvence.</span><span class="sxs-lookup"><span data-stu-id="69ae7-115">Convention.</span></span> <span data-ttu-id="69ae7-116">Další informace o aktivaci konvenční middleware, najdete v článku [Middleware](xref:fundamentals/middleware/index) tématu.</span><span class="sxs-lookup"><span data-stu-id="69ae7-116">For more information on conventional middleware activation, see the [Middleware](xref:fundamentals/middleware/index) topic.</span></span>
* <span data-ttu-id="69ae7-117">[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) implementace.</span><span class="sxs-lookup"><span data-stu-id="69ae7-117">An [IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) implementation.</span></span> <span data-ttu-id="69ae7-118">Výchozí hodnota [MiddlewareFactory třídy](/dotnet/api/microsoft.aspnetcore.http.middlewarefactory) aktivuje middleware.</span><span class="sxs-lookup"><span data-stu-id="69ae7-118">The default [MiddlewareFactory class](/dotnet/api/microsoft.aspnetcore.http.middlewarefactory) activates the middleware.</span></span>

<span data-ttu-id="69ae7-119">Implementace middlewaru fungovat stejně jako a si poznamenejte hodnotu poskytovanou infrastrukturou parametru řetězce dotazu (`key`).</span><span class="sxs-lookup"><span data-stu-id="69ae7-119">The middleware implementations function identically and record the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="69ae7-120">Middlewares objekt context vloženého databáze (vymezené služby) slouží k zaznamenání hodnotu řetězce dotazu v databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="69ae7-120">The middlewares use an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

## <a name="imiddleware"></a><span data-ttu-id="69ae7-121">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="69ae7-121">IMiddleware</span></span>

<span data-ttu-id="69ae7-122">[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) definuje middleware pro kanál žádosti o aplikace.</span><span class="sxs-lookup"><span data-stu-id="69ae7-122">[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="69ae7-123">[InvokeAsync (HttpContext, RequestDelegate)](/dotnet/api/microsoft.aspnetcore.http.imiddleware.invokeasync#Microsoft_AspNetCore_Http_IMiddleware_InvokeAsync_Microsoft_AspNetCore_Http_HttpContext_Microsoft_AspNetCore_Http_RequestDelegate_) metoda zpracovává požadavky a vrátí `Task` , který představuje spuštění middlewaru.</span><span class="sxs-lookup"><span data-stu-id="69ae7-123">The [InvokeAsync(HttpContext, RequestDelegate)](/dotnet/api/microsoft.aspnetcore.http.imiddleware.invokeasync#Microsoft_AspNetCore_Http_IMiddleware_InvokeAsync_Microsoft_AspNetCore_Http_HttpContext_Microsoft_AspNetCore_Http_RequestDelegate_) method handles requests and returns a `Task` that represents the execution of the middleware.</span></span>

<span data-ttu-id="69ae7-124">Middleware aktivoval konvence:</span><span class="sxs-lookup"><span data-stu-id="69ae7-124">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/sample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="69ae7-125">Middleware aktivoval `MiddlewareFactory`:</span><span class="sxs-lookup"><span data-stu-id="69ae7-125">Middleware activated by `MiddlewareFactory`:</span></span>

[!code-csharp[](extensibility/sample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="69ae7-126">Rozšíření jsou vytvořeny pro middlewares:</span><span class="sxs-lookup"><span data-stu-id="69ae7-126">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/sample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="69ae7-127">Není možné předat objekty factory aktivuje middleware s `UseMiddleware`:</span><span class="sxs-lookup"><span data-stu-id="69ae7-127">It isn't possible to pass objects to the factory-activated middleware with `UseMiddleware`:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="69ae7-128">Middleware aktivovat objekt pro vytváření se přidá do kontejneru integrované v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="69ae7-128">The factory-activated middleware is added to the built-in container in *Startup.cs*:</span></span>

[!code-csharp[](extensibility/sample/Startup.cs?name=snippet1&highlight=12)]

<span data-ttu-id="69ae7-129">Obě middlewares zaregistrovaní v kanálu zpracování žádostí v `Configure`:</span><span class="sxs-lookup"><span data-stu-id="69ae7-129">Both middlewares are registered in the request processing pipeline in `Configure`:</span></span>

[!code-csharp[](extensibility/sample/Startup.cs?name=snippet2&highlight=14-15)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="69ae7-130">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="69ae7-130">IMiddlewareFactory</span></span>

<span data-ttu-id="69ae7-131">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) poskytuje metody pro vytvoření middlewaru.</span><span class="sxs-lookup"><span data-stu-id="69ae7-131">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) provides methods to create middleware.</span></span> <span data-ttu-id="69ae7-132">Implementace objektu factory middlewaru je zaregistrovaný v kontejneru jako služba s vymezeným oborem.</span><span class="sxs-lookup"><span data-stu-id="69ae7-132">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="69ae7-133">Výchozí hodnota `IMiddlewareFactory` implementaci [MiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.middlewarefactory), najdete v [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) balíčku.</span><span class="sxs-lookup"><span data-stu-id="69ae7-133">The default `IMiddlewareFactory` implementation, [MiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.middlewarefactory), is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="69ae7-134">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="69ae7-134">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
