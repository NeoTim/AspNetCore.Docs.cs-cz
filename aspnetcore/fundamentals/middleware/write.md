---
title: Zápis vlastního middlewaru ASP.NET Core
author: rick-anderson
description: Naučte se psát vlastní middleware ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: e74bba9e1bd826d4f493b0ee642a198f984daada
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773720"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="c23d3-103">Zápis vlastního middlewaru ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c23d3-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="c23d3-104">Od [Rick Anderson](https://twitter.com/RickAndMSFT) a [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c23d3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c23d3-105">Middleware je software, který je včleněn do kanálu aplikace a zpracovává požadavky a odpovědi.</span><span class="sxs-lookup"><span data-stu-id="c23d3-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="c23d3-106">ASP.NET Core poskytuje bohatou sadu integrovaných komponent middlewaru, ale v některých případech můžete chtít napsat vlastní middleware.</span><span class="sxs-lookup"><span data-stu-id="c23d3-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="c23d3-107">Middleware – třída</span><span class="sxs-lookup"><span data-stu-id="c23d3-107">Middleware class</span></span>

<span data-ttu-id="c23d3-108">Middleware je obecně zapouzdřena ve třídě a vystavena s metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="c23d3-108">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="c23d3-109">Vezměte v úvahu následující middleware, který nastaví jazykovou verzi pro aktuální požadavek z řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="c23d3-109">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="c23d3-110">Předchozí vzorový kód slouží k předvedení vytváření komponenty middlewaru.</span><span class="sxs-lookup"><span data-stu-id="c23d3-110">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="c23d3-111">Integrovanou podporu lokalizace ASP.NET Core najdete v tématu <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="c23d3-111">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="c23d3-112">Otestujte middleware předáním v jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="c23d3-112">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="c23d3-113">Například Request `https://localhost:5001/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="c23d3-113">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="c23d3-114">Následující kód přesune delegáta middlewaru do třídy:</span><span class="sxs-lookup"><span data-stu-id="c23d3-114">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="c23d3-115">Třída middleware musí zahrnovat:</span><span class="sxs-lookup"><span data-stu-id="c23d3-115">The middleware class must include:</span></span>

* <span data-ttu-id="c23d3-116">Veřejný konstruktor s parametrem typu <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span><span class="sxs-lookup"><span data-stu-id="c23d3-116">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="c23d3-117">Veřejná metoda s názvem `Invoke` nebo `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="c23d3-117">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="c23d3-118">Tato metoda musí:</span><span class="sxs-lookup"><span data-stu-id="c23d3-118">This method must:</span></span>
  * <span data-ttu-id="c23d3-119">`Task`Vrátí.</span><span class="sxs-lookup"><span data-stu-id="c23d3-119">Return a `Task`.</span></span>
  * <span data-ttu-id="c23d3-120">Přijměte první parametr typu <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="c23d3-120">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="c23d3-121">Další parametry pro `Invoke` konstruktor a / `InvokeAsync` jsou vyplněny pomocí [Injektáže závislosti (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="c23d3-121">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="c23d3-122">Závislosti middlewaru</span><span class="sxs-lookup"><span data-stu-id="c23d3-122">Middleware dependencies</span></span>

<span data-ttu-id="c23d3-123">Middleware by měly následovat po [principu explicitní závislosti](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) tím, že vystaví jeho závislosti ve svém konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="c23d3-123">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="c23d3-124">Middleware je postaven jednou za *dobu života aplikace*.</span><span class="sxs-lookup"><span data-stu-id="c23d3-124">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="c23d3-125">Pokud potřebujete v rámci žádosti sdílet služby se middlewarem, podívejte se na část [závislosti middlewaru na žádost](#per-request-middleware-dependencies) .</span><span class="sxs-lookup"><span data-stu-id="c23d3-125">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="c23d3-126">Komponenty middlewaru mohou vyřešit své závislosti z [Injektáže závislosti (di)](xref:fundamentals/dependency-injection) prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="c23d3-126">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="c23d3-127">[UseMiddleware&lt;T&gt; ](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) může také přímo přijmout další parametry.</span><span class="sxs-lookup"><span data-stu-id="c23d3-127">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="c23d3-128">Závislosti middlewaru na požadavek</span><span class="sxs-lookup"><span data-stu-id="c23d3-128">Per-request middleware dependencies</span></span>

<span data-ttu-id="c23d3-129">Vzhledem k tomu, že middleware se vytvářejí při spuštění aplikace, ne na vyžádání, jsou *služby životnosti* , které jsou používány konstruktory middleware, sdíleny s jinými typy vloženými závislostmi během každé žádosti.</span><span class="sxs-lookup"><span data-stu-id="c23d3-129">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="c23d3-130">Pokud potřebujete sdílet *vymezenou* službu mezi middlewarem a jinými typy, přidejte tyto služby do `Invoke` signatury metody.</span><span class="sxs-lookup"><span data-stu-id="c23d3-130">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="c23d3-131">`Invoke` Metoda může přijmout další parametry, které jsou vyplněny parametrem di:</span><span class="sxs-lookup"><span data-stu-id="c23d3-131">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

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

## <a name="middleware-extension-method"></a><span data-ttu-id="c23d3-132">Metoda rozšíření middlewaru</span><span class="sxs-lookup"><span data-stu-id="c23d3-132">Middleware extension method</span></span>

<span data-ttu-id="c23d3-133">Následující rozšiřující metoda zpřístupňuje middleware prostřednictvím <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span><span class="sxs-lookup"><span data-stu-id="c23d3-133">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="c23d3-134">Následující kód volá middleware z `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="c23d3-134">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="c23d3-135">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c23d3-135">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
