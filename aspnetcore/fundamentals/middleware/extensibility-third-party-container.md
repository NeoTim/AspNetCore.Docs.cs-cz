---
title: Middleware aktivace s kontejnerem jiného výrobce v ASP.NET Core
author: guardrex
description: Zjistěte, jak používat middleware silného typu pomocí aktivace založené na objekt pro vytváření a kontejnerem jiného výrobce v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/03/2019
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: 4bc99b4c336aba611287c9fbe03d4252f8abee5b
ms.sourcegitcommit: f6e6730872a7d6f039f97d1df762f0d0bd5e34cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561652"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="5fc6a-103">Middleware aktivace s kontejnerem jiného výrobce v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5fc6a-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

<span data-ttu-id="5fc6a-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5fc6a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5fc6a-105">Tento článek ukazuje, jak používat <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> a <xref:Microsoft.AspNetCore.Http.IMiddleware> jako bod rozšiřitelnosti pro [middleware](xref:fundamentals/middleware/index) aktivace s kontejnerem jiného výrobce.</span><span class="sxs-lookup"><span data-stu-id="5fc6a-105">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="5fc6a-106">Úvodní informace o `IMiddlewareFactory` a `IMiddleware`, naleznete v tématu <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="5fc6a-106">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="5fc6a-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5fc6a-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5fc6a-108">Ukázková aplikace předvádí middleware aktivace pomocí `IMiddlewareFactory` implementaci `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="5fc6a-108">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="5fc6a-109">Ukázka používá [jednoduché Injector](https://simpleinjector.org) kontejneru pro vkládání (DI) závislosti.</span><span class="sxs-lookup"><span data-stu-id="5fc6a-109">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="5fc6a-110">Ukázková implementace middlewaru zaznamenává hodnota poskytnutá parametru řetězce dotazu (`key`).</span><span class="sxs-lookup"><span data-stu-id="5fc6a-110">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="5fc6a-111">Middleware použije objekt context vloženého databáze (s vymezeným oborem service) k zaznamenání hodnotu řetězce dotazu v databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="5fc6a-111">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="5fc6a-112">Tato ukázková aplikace používá [jednoduché Injector](https://github.com/simpleinjector/SimpleInjector) čistě pro demonstrační účely.</span><span class="sxs-lookup"><span data-stu-id="5fc6a-112">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="5fc6a-113">Využití jednoduché Injector není o potvrzení.</span><span class="sxs-lookup"><span data-stu-id="5fc6a-113">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="5fc6a-114">Přístupy k aktivaci middleware popsaného v dokumentaci k jednoduché Injector a programu jednoduché Injector jsou doporučené problémy Githubu.</span><span class="sxs-lookup"><span data-stu-id="5fc6a-114">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="5fc6a-115">Další informace najdete v tématu [jednoduché Injector dokumentaci](https://simpleinjector.readthedocs.io/en/latest/index.html) a [úložiště GitHub jednoduché Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="5fc6a-115">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="5fc6a-116">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="5fc6a-116">IMiddlewareFactory</span></span>

<span data-ttu-id="5fc6a-117"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> poskytuje metody pro vytvoření middlewaru.</span><span class="sxs-lookup"><span data-stu-id="5fc6a-117"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="5fc6a-118">V ukázkové aplikaci se implementuje objekt pro vytváření middleware k vytvoření `SimpleInjectorActivatedMiddleware` instance.</span><span class="sxs-lookup"><span data-stu-id="5fc6a-118">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="5fc6a-119">Objekt pro vytváření middleware používá jednoduché Injector kontejneru pro middleware:</span><span class="sxs-lookup"><span data-stu-id="5fc6a-119">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="5fc6a-120">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="5fc6a-120">IMiddleware</span></span>

<span data-ttu-id="5fc6a-121"><xref:Microsoft.AspNetCore.Http.IMiddleware> Definuje middleware pro kanál žádosti o aplikace.</span><span class="sxs-lookup"><span data-stu-id="5fc6a-121"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="5fc6a-122">Middleware aktivoval `IMiddlewareFactory` implementace (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="5fc6a-122">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="5fc6a-123">Vytvoření rozšíření pro middleware (*Middleware/MiddlewareExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="5fc6a-123">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="5fc6a-124">`Startup.ConfigureServices` třeba provést několik úloh:</span><span class="sxs-lookup"><span data-stu-id="5fc6a-124">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="5fc6a-125">Nastavte jednoduché Injector kontejner.</span><span class="sxs-lookup"><span data-stu-id="5fc6a-125">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="5fc6a-126">Zaregistrujte objekt pro vytváření a middlewaru.</span><span class="sxs-lookup"><span data-stu-id="5fc6a-126">Register the factory and middleware.</span></span>
* <span data-ttu-id="5fc6a-127">Kontext databáze aplikace zpřístupníte z jednoduchého Injector kontejneru.</span><span class="sxs-lookup"><span data-stu-id="5fc6a-127">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="5fc6a-128">Middleware je registrován v kanálu zpracování žádostí v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="5fc6a-128">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=13)]

## <a name="additional-resources"></a><span data-ttu-id="5fc6a-129">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5fc6a-129">Additional resources</span></span>

* [<span data-ttu-id="5fc6a-130">Middleware</span><span class="sxs-lookup"><span data-stu-id="5fc6a-130">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="5fc6a-131">Middleware založený na objekt pro vytváření aktivace</span><span class="sxs-lookup"><span data-stu-id="5fc6a-131">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="5fc6a-132">Jednoduché úložiště Injector GitHub</span><span class="sxs-lookup"><span data-stu-id="5fc6a-132">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="5fc6a-133">Dokumentace k jednoduché Injector</span><span class="sxs-lookup"><span data-stu-id="5fc6a-133">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
