---
title: Middleware aktivace s kontejnerem jiného výrobce v ASP.NET Core
author: guardrex
description: Zjistěte, jak používat middleware silného typu pomocí aktivace založené na objekt pro vytváření a kontejnerem jiného výrobce v ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2018
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: 9e4d4c6c0232ebc51ad08923e10164262b652280
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64901182"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="29a67-103">Middleware aktivace s kontejnerem jiného výrobce v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29a67-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

<span data-ttu-id="29a67-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="29a67-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="29a67-105">Tento článek ukazuje, jak používat [IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) a [IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) jako bod rozšiřitelnosti pro [middleware](xref:fundamentals/middleware/index) aktivace s kontejnerem jiného výrobce.</span><span class="sxs-lookup"><span data-stu-id="29a67-105">This article demonstrates how to use [IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) and [IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="29a67-106">Úvodní informace o `IMiddlewareFactory` a `IMiddleware`, najdete v článku [middleware založený na objekt pro vytváření aktivace](xref:fundamentals/middleware/extensibility) tématu.</span><span class="sxs-lookup"><span data-stu-id="29a67-106">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see the [Factory-based middleware activation](xref:fundamentals/middleware/extensibility) topic.</span></span>

<span data-ttu-id="29a67-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/sample) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="29a67-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="29a67-108">Ukázková aplikace předvádí middleware aktivace pomocí `IMiddlewareFactory` implementaci `SimpleInjectorMiddlewareFactory`.</span><span class="sxs-lookup"><span data-stu-id="29a67-108">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="29a67-109">Ukázka používá [jednoduché Injector](https://simpleinjector.org) kontejneru pro vkládání (DI) závislosti.</span><span class="sxs-lookup"><span data-stu-id="29a67-109">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="29a67-110">Ukázková implementace middlewaru zaznamenává hodnota poskytnutá parametru řetězce dotazu (`key`).</span><span class="sxs-lookup"><span data-stu-id="29a67-110">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="29a67-111">Middleware použije objekt context vloženého databáze (s vymezeným oborem service) k zaznamenání hodnotu řetězce dotazu v databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="29a67-111">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="29a67-112">Tato ukázková aplikace používá [jednoduché Injector](https://github.com/simpleinjector/SimpleInjector) čistě pro demonstrační účely.</span><span class="sxs-lookup"><span data-stu-id="29a67-112">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="29a67-113">Využití jednoduché Injector není o potvrzení.</span><span class="sxs-lookup"><span data-stu-id="29a67-113">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="29a67-114">Přístupy k aktivaci middleware popsaného v dokumentaci k jednoduché Injector a programu jednoduché Injector jsou doporučené problémy Githubu.</span><span class="sxs-lookup"><span data-stu-id="29a67-114">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="29a67-115">Další informace najdete v tématu [jednoduché Injector dokumentaci](https://simpleinjector.readthedocs.io/en/latest/index.html) a [úložiště GitHub jednoduché Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="29a67-115">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="29a67-116">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="29a67-116">IMiddlewareFactory</span></span>

<span data-ttu-id="29a67-117">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) poskytuje metody pro vytvoření middlewaru.</span><span class="sxs-lookup"><span data-stu-id="29a67-117">[IMiddlewareFactory](/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory) provides methods to create middleware.</span></span>

<span data-ttu-id="29a67-118">V ukázkové aplikaci se implementuje objekt pro vytváření middleware k vytvoření `SimpleInjectorActivatedMiddleware` instance.</span><span class="sxs-lookup"><span data-stu-id="29a67-118">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="29a67-119">Objekt pro vytváření middleware používá jednoduché Injector kontejneru pro middleware:</span><span class="sxs-lookup"><span data-stu-id="29a67-119">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="29a67-120">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="29a67-120">IMiddleware</span></span>

<span data-ttu-id="29a67-121">[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) definuje middleware pro kanál žádosti o aplikace.</span><span class="sxs-lookup"><span data-stu-id="29a67-121">[IMiddleware](/dotnet/api/microsoft.aspnetcore.http.imiddleware) defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="29a67-122">Middleware aktivoval `IMiddlewareFactory` implementace (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="29a67-122">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="29a67-123">Vytvoření rozšíření pro middleware (*Middleware/MiddlewareExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="29a67-123">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="29a67-124">`Startup.ConfigureServices` třeba provést několik úloh:</span><span class="sxs-lookup"><span data-stu-id="29a67-124">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="29a67-125">Nastavte jednoduché Injector kontejner.</span><span class="sxs-lookup"><span data-stu-id="29a67-125">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="29a67-126">Zaregistrujte objekt pro vytváření a middlewaru.</span><span class="sxs-lookup"><span data-stu-id="29a67-126">Register the factory and middleware.</span></span>
* <span data-ttu-id="29a67-127">Zpřístupnit kontext databáze aplikace z jednoduchého Injector kontejneru pro stránky Razor.</span><span class="sxs-lookup"><span data-stu-id="29a67-127">Make the app's database context available from the Simple Injector container for a Razor Page.</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="29a67-128">Middleware je registrován v kanálu zpracování žádostí v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="29a67-128">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/sample/Startup.cs?name=snippet2&highlight=13)]

## <a name="additional-resources"></a><span data-ttu-id="29a67-129">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="29a67-129">Additional resources</span></span>

* [<span data-ttu-id="29a67-130">Middleware</span><span class="sxs-lookup"><span data-stu-id="29a67-130">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="29a67-131">Middleware založený na objekt pro vytváření aktivace</span><span class="sxs-lookup"><span data-stu-id="29a67-131">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="29a67-132">Jednoduché úložiště Injector GitHub</span><span class="sxs-lookup"><span data-stu-id="29a67-132">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="29a67-133">Dokumentace k jednoduché Injector</span><span class="sxs-lookup"><span data-stu-id="29a67-133">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
