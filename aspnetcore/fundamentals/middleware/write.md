---
title: Napište vlastní ASP.NET middleware Core
author: rick-anderson
description: Naučte se psát vlastní ASP.NET middleware Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: e74bba9e1bd826d4f493b0ee642a198f984daada
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663924"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="d5aed-103">Napište vlastní ASP.NET middleware Core</span><span class="sxs-lookup"><span data-stu-id="d5aed-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="d5aed-104">[Podle Rick Anderson](https://twitter.com/RickAndMSFT) a Steve [Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d5aed-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d5aed-105">Middleware je software, který je sestaven do kanálu aplikace pro zpracování požadavků a odpovědí.</span><span class="sxs-lookup"><span data-stu-id="d5aed-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="d5aed-106">ASP.NET Core poskytuje bohatou sadu vestavěných middleware komponent, ale v některých scénářích možná budete chtít napsat vlastní middleware.</span><span class="sxs-lookup"><span data-stu-id="d5aed-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="d5aed-107">Třída Middleware</span><span class="sxs-lookup"><span data-stu-id="d5aed-107">Middleware class</span></span>

<span data-ttu-id="d5aed-108">Middleware je obecně zapouzdřen ve třídě a vystavena s metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="d5aed-108">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="d5aed-109">Zvažte následující middleware, který nastaví jazykovou verzi pro aktuální požadavek z řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="d5aed-109">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="d5aed-110">Předchozí ukázkový kód se používá k předvedení vytvoření součásti middleware.</span><span class="sxs-lookup"><span data-stu-id="d5aed-110">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="d5aed-111">ASP.NET integrovanou podporu lokalizace jádra <xref:fundamentals/localization>najdete v tématu .</span><span class="sxs-lookup"><span data-stu-id="d5aed-111">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="d5aed-112">Otestujte middleware předáním v jazykové verzi.</span><span class="sxs-lookup"><span data-stu-id="d5aed-112">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="d5aed-113">Například požadavek `https://localhost:5001/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="d5aed-113">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="d5aed-114">Následující kód přesune delegáta middlewaru do třídy:</span><span class="sxs-lookup"><span data-stu-id="d5aed-114">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="d5aed-115">Třída middleware musí obsahovat:</span><span class="sxs-lookup"><span data-stu-id="d5aed-115">The middleware class must include:</span></span>

* <span data-ttu-id="d5aed-116">Veřejný konstruktor s parametrem <xref:Microsoft.AspNetCore.Http.RequestDelegate>typu .</span><span class="sxs-lookup"><span data-stu-id="d5aed-116">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="d5aed-117">Veřejná metoda `Invoke` `InvokeAsync`s názvem nebo .</span><span class="sxs-lookup"><span data-stu-id="d5aed-117">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="d5aed-118">Tato metoda musí:</span><span class="sxs-lookup"><span data-stu-id="d5aed-118">This method must:</span></span>
  * <span data-ttu-id="d5aed-119">Vrátí `Task`. .</span><span class="sxs-lookup"><span data-stu-id="d5aed-119">Return a `Task`.</span></span>
  * <span data-ttu-id="d5aed-120">Přijměte první <xref:Microsoft.AspNetCore.Http.HttpContext>parametr typu .</span><span class="sxs-lookup"><span data-stu-id="d5aed-120">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="d5aed-121">Další parametry pro konstruktor u `Invoke` / `InvokeAsync` jsou [naplněny vkládání závislostí (DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d5aed-121">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="d5aed-122">Závislosti middlewaru</span><span class="sxs-lookup"><span data-stu-id="d5aed-122">Middleware dependencies</span></span>

<span data-ttu-id="d5aed-123">Middleware by se měl řídit [zásadou explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) tím, že odhalí své závislosti v jeho konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="d5aed-123">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="d5aed-124">Middleware je konstruován jednou za *životnost aplikace*.</span><span class="sxs-lookup"><span data-stu-id="d5aed-124">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="d5aed-125">Pokud potřebujete sdílet služby s middlewarem v rámci žádosti, podívejte se do části [Závislosti middlewaru pro požadavek](#per-request-middleware-dependencies) na vyžádání.</span><span class="sxs-lookup"><span data-stu-id="d5aed-125">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="d5aed-126">Součásti middlewaru mohou vyřešit své závislosti z [vkládání závislostí (DI)](xref:fundamentals/dependency-injection) prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="d5aed-126">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="d5aed-127">[UseMiddleware&lt;&gt; T](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) může také přijímat další parametry přímo.</span><span class="sxs-lookup"><span data-stu-id="d5aed-127">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="d5aed-128">Závislosti middlewaru pro vyžádání</span><span class="sxs-lookup"><span data-stu-id="d5aed-128">Per-request middleware dependencies</span></span>

<span data-ttu-id="d5aed-129">Vzhledem k tomu, middleware je vytvořen při spuštění aplikace, nikoli na vyžádání, *rozsahem* životnost služby používané middleware konstruktory nejsou sdíleny s jinými typy vložené závislostmi během každého požadavku.</span><span class="sxs-lookup"><span data-stu-id="d5aed-129">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="d5aed-130">Pokud je nutné sdílet službu *s vymezeným oborem* mezi `Invoke` middlewarem a jinými typy, přidejte tyto služby do podpisu metody.</span><span class="sxs-lookup"><span data-stu-id="d5aed-130">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="d5aed-131">Metoda `Invoke` může přijmout další parametry, které jsou naplněny DI:</span><span class="sxs-lookup"><span data-stu-id="d5aed-131">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

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

## <a name="middleware-extension-method"></a><span data-ttu-id="d5aed-132">Middleware rozšiřující metoda</span><span class="sxs-lookup"><span data-stu-id="d5aed-132">Middleware extension method</span></span>

<span data-ttu-id="d5aed-133">Následující metoda rozšíření zveřejňuje middleware <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>prostřednictvím :</span><span class="sxs-lookup"><span data-stu-id="d5aed-133">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="d5aed-134">Následující kód volá middleware `Startup.Configure`z :</span><span class="sxs-lookup"><span data-stu-id="d5aed-134">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="d5aed-135">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d5aed-135">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
