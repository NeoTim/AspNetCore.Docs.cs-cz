---
title: Aktivace middlewaru s kontejnerem jiného výrobce v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak používat middleware silného typu s aktivací z výroby a kontejnerem jiného výrobce v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: a5c5bf6dff6ef795add075df932dd625129ef793
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663133"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="04623-103">Aktivace middlewaru s kontejnerem jiného výrobce v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="04623-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="04623-104">Tento článek ukazuje, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> jak <xref:Microsoft.AspNetCore.Http.IMiddleware> používat a jako bod rozšiřitelnosti pro aktivaci [middleware](xref:fundamentals/middleware/index) s kontejnerem jiného výrobce.</span><span class="sxs-lookup"><span data-stu-id="04623-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="04623-105">Úvodní informace o písmenu `IMiddlewareFactory` `IMiddleware`a) <xref:fundamentals/middleware/extensibility>a d), viz .</span><span class="sxs-lookup"><span data-stu-id="04623-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="04623-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="04623-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="04623-107">Ukázková aplikace demonstruje aktivaci middlewaru implementací `IMiddlewareFactory` . `SimpleInjectorMiddlewareFactory`</span><span class="sxs-lookup"><span data-stu-id="04623-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="04623-108">Ukázka používá kontejner vkládání závislostí [jednoduchého injektu](https://simpleinjector.org) (DI).</span><span class="sxs-lookup"><span data-stu-id="04623-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="04623-109">Implementace middlewaru ukázky zaznamenává hodnotu poskytovo parametrem řetězce dotazu (`key`).</span><span class="sxs-lookup"><span data-stu-id="04623-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="04623-110">Middleware používá vložený kontext databáze (oborová služba) k zaznamenání hodnoty řetězce dotazu v databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="04623-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="04623-111">Ukázková aplikace používá [simple injector](https://github.com/simpleinjector/SimpleInjector) čistě pro demonstrační účely.</span><span class="sxs-lookup"><span data-stu-id="04623-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="04623-112">Použití jednoduchého injektoru není potvrzení.</span><span class="sxs-lookup"><span data-stu-id="04623-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="04623-113">Aktivační přístupy middlewaru popsané v dokumentaci Simple Injector a problémy GitHubu doporučují správci jednoduchého injektoru.</span><span class="sxs-lookup"><span data-stu-id="04623-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="04623-114">Další informace naleznete v [dokumentaci simple injector](https://simpleinjector.readthedocs.io/en/latest/index.html) a [simple injector github repozitář .](https://github.com/simpleinjector/SimpleInjector)</span><span class="sxs-lookup"><span data-stu-id="04623-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="04623-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="04623-115">IMiddlewareFactory</span></span>

<span data-ttu-id="04623-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middleware.</span><span class="sxs-lookup"><span data-stu-id="04623-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="04623-117">V ukázkové aplikaci je implementována `SimpleInjectorActivatedMiddleware` middleware továrna k vytvoření instance.</span><span class="sxs-lookup"><span data-stu-id="04623-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="04623-118">Middleware továrna používá jednoduchý vstřikovací kontejner k vyřešení middleware:</span><span class="sxs-lookup"><span data-stu-id="04623-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="04623-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="04623-119">IMiddleware</span></span>

<span data-ttu-id="04623-120"><xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="04623-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="04623-121">Middleware aktivován `IMiddlewareFactory` implementací (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="04623-121">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="04623-122">Rozšíření je vytvořen pro middleware (*Middleware/MiddlewareExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="04623-122">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="04623-123">`Startup.ConfigureServices`musí provést několik úkolů:</span><span class="sxs-lookup"><span data-stu-id="04623-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="04623-124">Nastavte jednoduchý zásobník vstřikovače.</span><span class="sxs-lookup"><span data-stu-id="04623-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="04623-125">Zaregistrujte tovární a middleware.</span><span class="sxs-lookup"><span data-stu-id="04623-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="04623-126">Zpřístupněte kontext databáze aplikace z kontejneru Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="04623-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="04623-127">Middleware je registrován v kanálu `Startup.Configure`zpracování požadavků v :</span><span class="sxs-lookup"><span data-stu-id="04623-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="04623-128">Tento článek ukazuje, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> jak <xref:Microsoft.AspNetCore.Http.IMiddleware> používat a jako bod rozšiřitelnosti pro aktivaci [middleware](xref:fundamentals/middleware/index) s kontejnerem jiného výrobce.</span><span class="sxs-lookup"><span data-stu-id="04623-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="04623-129">Úvodní informace o písmenu `IMiddlewareFactory` `IMiddleware`a) <xref:fundamentals/middleware/extensibility>a d), viz .</span><span class="sxs-lookup"><span data-stu-id="04623-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="04623-130">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="04623-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="04623-131">Ukázková aplikace demonstruje aktivaci middlewaru implementací `IMiddlewareFactory` . `SimpleInjectorMiddlewareFactory`</span><span class="sxs-lookup"><span data-stu-id="04623-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="04623-132">Ukázka používá kontejner vkládání závislostí [jednoduchého injektu](https://simpleinjector.org) (DI).</span><span class="sxs-lookup"><span data-stu-id="04623-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="04623-133">Implementace middlewaru ukázky zaznamenává hodnotu poskytovo parametrem řetězce dotazu (`key`).</span><span class="sxs-lookup"><span data-stu-id="04623-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="04623-134">Middleware používá vložený kontext databáze (oborová služba) k zaznamenání hodnoty řetězce dotazu v databázi v paměti.</span><span class="sxs-lookup"><span data-stu-id="04623-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="04623-135">Ukázková aplikace používá [simple injector](https://github.com/simpleinjector/SimpleInjector) čistě pro demonstrační účely.</span><span class="sxs-lookup"><span data-stu-id="04623-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="04623-136">Použití jednoduchého injektoru není potvrzení.</span><span class="sxs-lookup"><span data-stu-id="04623-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="04623-137">Aktivační přístupy middlewaru popsané v dokumentaci Simple Injector a problémy GitHubu doporučují správci jednoduchého injektoru.</span><span class="sxs-lookup"><span data-stu-id="04623-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="04623-138">Další informace naleznete v [dokumentaci simple injector](https://simpleinjector.readthedocs.io/en/latest/index.html) a [simple injector github repozitář .](https://github.com/simpleinjector/SimpleInjector)</span><span class="sxs-lookup"><span data-stu-id="04623-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="04623-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="04623-139">IMiddlewareFactory</span></span>

<span data-ttu-id="04623-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middleware.</span><span class="sxs-lookup"><span data-stu-id="04623-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="04623-141">V ukázkové aplikaci je implementována `SimpleInjectorActivatedMiddleware` middleware továrna k vytvoření instance.</span><span class="sxs-lookup"><span data-stu-id="04623-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="04623-142">Middleware továrna používá jednoduchý vstřikovací kontejner k vyřešení middleware:</span><span class="sxs-lookup"><span data-stu-id="04623-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="04623-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="04623-143">IMiddleware</span></span>

<span data-ttu-id="04623-144"><xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="04623-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="04623-145">Middleware aktivován `IMiddlewareFactory` implementací (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span><span class="sxs-lookup"><span data-stu-id="04623-145">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="04623-146">Rozšíření je vytvořen pro middleware (*Middleware/MiddlewareExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="04623-146">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="04623-147">`Startup.ConfigureServices`musí provést několik úkolů:</span><span class="sxs-lookup"><span data-stu-id="04623-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="04623-148">Nastavte jednoduchý zásobník vstřikovače.</span><span class="sxs-lookup"><span data-stu-id="04623-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="04623-149">Zaregistrujte tovární a middleware.</span><span class="sxs-lookup"><span data-stu-id="04623-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="04623-150">Zpřístupněte kontext databáze aplikace z kontejneru Simple Injector.</span><span class="sxs-lookup"><span data-stu-id="04623-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="04623-151">Middleware je registrován v kanálu `Startup.Configure`zpracování požadavků v :</span><span class="sxs-lookup"><span data-stu-id="04623-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="04623-152">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="04623-152">Additional resources</span></span>

* [<span data-ttu-id="04623-153">Middleware</span><span class="sxs-lookup"><span data-stu-id="04623-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="04623-154">Aktivace middlewaru na základě výroby</span><span class="sxs-lookup"><span data-stu-id="04623-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="04623-155">Jednoduché úložiště Injector GitHub</span><span class="sxs-lookup"><span data-stu-id="04623-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="04623-156">Jednoduchá dokumentace injektoru</span><span class="sxs-lookup"><span data-stu-id="04623-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
