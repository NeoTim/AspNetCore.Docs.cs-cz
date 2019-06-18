---
title: Zápis vlastního middlewaru ASP.NET Core
author: rick-anderson
description: Informace o psaní vlastního middlewaru ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/17/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: 352db93dd7061070c76e34f6c03883f68e2041ee
ms.sourcegitcommit: 28a2874765cefe9eaa068dceb989a978ba2096aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67167100"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="058e5-103">Zápis vlastního middlewaru ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="058e5-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="058e5-104">Podle [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="058e5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="058e5-105">Middleware je software, který je sestaven do kanálu služby aplikace pro zpracování požadavků a odpovědí.</span><span class="sxs-lookup"><span data-stu-id="058e5-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="058e5-106">ASP.NET Core nabízí bohaté nastavit integrované middlewarových komponent, ale v některých případech můžete chtít napsat vlastního middlewaru.</span><span class="sxs-lookup"><span data-stu-id="058e5-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="058e5-107">Třída middlewaru</span><span class="sxs-lookup"><span data-stu-id="058e5-107">Middleware class</span></span>

<span data-ttu-id="058e5-108">Middleware je obecně zapouzdřené v třídě a vystavený s metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="058e5-108">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="058e5-109">Vezměte v úvahu následující middlewaru, který nastaví jazykovou verzi pro aktuální požadavek z řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="058e5-109">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](index/snapshot/Culture/StartupCulture.cs?name=snippet1)]

<span data-ttu-id="058e5-110">Předchozí kód ukázkové slouží k předvedení vytváření komponenta middlewaru.</span><span class="sxs-lookup"><span data-stu-id="058e5-110">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="058e5-111">ASP.NET Core lokalizace integrovanou podporu najdete na webu <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="058e5-111">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="058e5-112">Otestujte middleware předáním jazykovou verzi.</span><span class="sxs-lookup"><span data-stu-id="058e5-112">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="058e5-113">Například požadovat `https://localhost:5001/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="058e5-113">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="058e5-114">Následující kód přesune delegáta middleware pro třídu:</span><span class="sxs-lookup"><span data-stu-id="058e5-114">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddleware.cs)]

<span data-ttu-id="058e5-115">Třída middlewaru, musí obsahovat:</span><span class="sxs-lookup"><span data-stu-id="058e5-115">The middleware class must include:</span></span>

* <span data-ttu-id="058e5-116">Veřejný konstruktor s parametrem typu <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span><span class="sxs-lookup"><span data-stu-id="058e5-116">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="058e5-117">Veřejná metoda s názvem `Invoke` nebo `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="058e5-117">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="058e5-118">Tato metoda musí:</span><span class="sxs-lookup"><span data-stu-id="058e5-118">This method must:</span></span>
  * <span data-ttu-id="058e5-119">Vrátit `Task`.</span><span class="sxs-lookup"><span data-stu-id="058e5-119">Return a `Task`.</span></span>
  * <span data-ttu-id="058e5-120">První parametr typu přijmout <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="058e5-120">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="058e5-121">Další parametry pro konstruktor a `Invoke` / `InvokeAsync` jsou vyplněn [injektáž závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="058e5-121">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="058e5-122">Middleware závislosti</span><span class="sxs-lookup"><span data-stu-id="058e5-122">Middleware dependencies</span></span>

<span data-ttu-id="058e5-123">Postupujte podle middleware [explicitní závislosti Princip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) zveřejněním závislých 've svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="058e5-123">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="058e5-124">Middleware je vytvořen jednou za *dobu životnosti aplikace*.</span><span class="sxs-lookup"><span data-stu-id="058e5-124">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="058e5-125">Najdete v článku [middleware závislosti na požadavku](#per-request-middleware-dependencies) části, pokud je potřeba sdílet s middlewaru v rámci žádost o služby.</span><span class="sxs-lookup"><span data-stu-id="058e5-125">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="058e5-126">Middlewarových komponent lze vyřešit jejich závislosti z [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) prostřednictvím parametry konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="058e5-126">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="058e5-127">[UseMiddleware&lt;T&gt; ](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) můžete také přijmout přímo další parametry.</span><span class="sxs-lookup"><span data-stu-id="058e5-127">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="058e5-128">Middleware závislosti na základě žádosti</span><span class="sxs-lookup"><span data-stu-id="058e5-128">Per-request middleware dependencies</span></span>

<span data-ttu-id="058e5-129">Protože middlewaru je vytvořen při spuštění aplikace, nikoli jednotlivých žádostí, *obor* služby životního cyklu používat middleware konstruktory nejsou sdíleny s jinými typy vložený závislostí při každém požadavku.</span><span class="sxs-lookup"><span data-stu-id="058e5-129">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="058e5-130">Pokud musíte sdílet *obor* služby mezi middlewaru a ostatními typy, přidejte tyto služby `Invoke` podpis metody.</span><span class="sxs-lookup"><span data-stu-id="058e5-130">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="058e5-131">`Invoke` Metoda může přijímat další parametry, které se vyplní podle DI:</span><span class="sxs-lookup"><span data-stu-id="058e5-131">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a><span data-ttu-id="058e5-132">Metoda rozšíření middlewaru</span><span class="sxs-lookup"><span data-stu-id="058e5-132">Middleware extension method</span></span>

<span data-ttu-id="058e5-133">Poskytuje následující metody rozšíření middleware prostřednictvím <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span><span class="sxs-lookup"><span data-stu-id="058e5-133">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="058e5-134">Následující kód volá middleware z `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="058e5-134">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](index/snapshot/Culture/Startup.cs?name=snippet1&highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="058e5-135">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="058e5-135">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
