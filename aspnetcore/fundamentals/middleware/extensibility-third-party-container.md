---
title: Aktivace middlewaru pomocí kontejneru třetí strany v ASP.NET Core
author: guardrex
description: Naučte se používat middleware silného typu s aktivací založenou na výrobě a kontejnerem třetí strany v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: e54a2bd366457fa2d898b7ee26e95021aec5389b
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187094"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="eeff9-103">Aktivace middlewaru pomocí kontejneru třetí strany v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eeff9-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

<span data-ttu-id="eeff9-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="eeff9-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eeff9-105">Tento článek ukazuje, jak používat <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> a <xref:Microsoft.AspNetCore.Http.IMiddleware> jako bod rozšiřitelnosti pro aktivaci [middlewaru](xref:fundamentals/middleware/index) pomocí kontejneru třetí strany.</span><span class="sxs-lookup"><span data-stu-id="eeff9-105">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="eeff9-106">Úvodní informace o systémech `IMiddlewareFactory` a `IMiddleware`najdete v <xref:fundamentals/middleware/extensibility>tématu.</span><span class="sxs-lookup"><span data-stu-id="eeff9-106">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="eeff9-107">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eeff9-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="eeff9-108">Ukázková aplikace předvádí aktivaci middlewaru `IMiddlewareFactory` pomocí `SimpleInjectorMiddlewareFactory`implementace.</span><span class="sxs-lookup"><span data-stu-id="eeff9-108">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="eeff9-109">Ukázka používá [jednoduchý](https://simpleinjector.org) kontejner vkládání závislostí (di).</span><span class="sxs-lookup"><span data-stu-id="eeff9-109">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="eeff9-110">Implementace middleware v ukázce zaznamenává hodnotu poskytnutou parametrem řetězce dotazu (`key`).</span><span class="sxs-lookup"><span data-stu-id="eeff9-110">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="eeff9-111">Middleware používá vložený kontext databáze (Oborová služba) k záznamu hodnoty řetězce dotazu do databáze v paměti.</span><span class="sxs-lookup"><span data-stu-id="eeff9-111">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="eeff9-112">Ukázková aplikace používá pro demonstrační účely čistě modul pro [vkládání](https://github.com/simpleinjector/SimpleInjector) .</span><span class="sxs-lookup"><span data-stu-id="eeff9-112">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="eeff9-113">Použití jednoduchého injektoru není potvrzením.</span><span class="sxs-lookup"><span data-stu-id="eeff9-113">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="eeff9-114">Přístupy k aktivaci middlewaru popsané v dokumentaci k funkci Simple injektor a problémy GitHubu doporučuje údržba jednoduchého injektoru.</span><span class="sxs-lookup"><span data-stu-id="eeff9-114">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="eeff9-115">Další informace najdete v [dokumentaci k jednoduchému vstřikovacímu](https://simpleinjector.readthedocs.io/en/latest/index.html) zařízení a v [úložišti GitHub pro jednoduché](https://github.com/simpleinjector/SimpleInjector)sady pro vkládání.</span><span class="sxs-lookup"><span data-stu-id="eeff9-115">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="eeff9-116">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="eeff9-116">IMiddlewareFactory</span></span>

<span data-ttu-id="eeff9-117"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middlewaru.</span><span class="sxs-lookup"><span data-stu-id="eeff9-117"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="eeff9-118">V ukázkové aplikaci je implementován objekt pro vytváření middlewaru pro vytvoření `SimpleInjectorActivatedMiddleware` instance.</span><span class="sxs-lookup"><span data-stu-id="eeff9-118">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="eeff9-119">Objekt pro vytváření middlewaru používá jednoduchý kontejner injektoru k překladu middlewaru:</span><span class="sxs-lookup"><span data-stu-id="eeff9-119">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="eeff9-120">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="eeff9-120">IMiddleware</span></span>

<span data-ttu-id="eeff9-121"><xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál žádostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="eeff9-121"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="eeff9-122">Middleware aktivovaný `IMiddlewareFactory` implementací (*middleware/SimpleInjectorActivatedMiddleware. cs*):</span><span class="sxs-lookup"><span data-stu-id="eeff9-122">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="eeff9-123">Pro middleware (*middleware/MiddlewareExtensions. cs*) se vytvoří rozšíření:</span><span class="sxs-lookup"><span data-stu-id="eeff9-123">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="eeff9-124">`Startup.ConfigureServices`musí provádět několik úloh:</span><span class="sxs-lookup"><span data-stu-id="eeff9-124">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="eeff9-125">Nastavení jednoduchého kontejneru pro nástřik.</span><span class="sxs-lookup"><span data-stu-id="eeff9-125">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="eeff9-126">Zaregistrujte objekt pro vytváření a middlewaru.</span><span class="sxs-lookup"><span data-stu-id="eeff9-126">Register the factory and middleware.</span></span>
* <span data-ttu-id="eeff9-127">Zpřístupněte kontext databáze aplikace z jednoduchého kontejneru injektoru.</span><span class="sxs-lookup"><span data-stu-id="eeff9-127">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="eeff9-128">Middleware je zaregistrován v kanálu zpracování žádosti v `Startup.Configure`nástroji:</span><span class="sxs-lookup"><span data-stu-id="eeff9-128">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eeff9-129">Tento článek ukazuje, jak používat <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> a <xref:Microsoft.AspNetCore.Http.IMiddleware> jako bod rozšiřitelnosti pro aktivaci [middlewaru](xref:fundamentals/middleware/index) pomocí kontejneru třetí strany.</span><span class="sxs-lookup"><span data-stu-id="eeff9-129">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="eeff9-130">Úvodní informace o systémech `IMiddlewareFactory` a `IMiddleware`najdete v <xref:fundamentals/middleware/extensibility>tématu.</span><span class="sxs-lookup"><span data-stu-id="eeff9-130">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="eeff9-131">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eeff9-131">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="eeff9-132">Ukázková aplikace předvádí aktivaci middlewaru `IMiddlewareFactory` pomocí `SimpleInjectorMiddlewareFactory`implementace.</span><span class="sxs-lookup"><span data-stu-id="eeff9-132">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="eeff9-133">Ukázka používá [jednoduchý](https://simpleinjector.org) kontejner vkládání závislostí (di).</span><span class="sxs-lookup"><span data-stu-id="eeff9-133">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="eeff9-134">Implementace middleware v ukázce zaznamenává hodnotu poskytnutou parametrem řetězce dotazu (`key`).</span><span class="sxs-lookup"><span data-stu-id="eeff9-134">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="eeff9-135">Middleware používá vložený kontext databáze (Oborová služba) k záznamu hodnoty řetězce dotazu do databáze v paměti.</span><span class="sxs-lookup"><span data-stu-id="eeff9-135">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="eeff9-136">Ukázková aplikace používá pro demonstrační účely čistě modul pro [vkládání](https://github.com/simpleinjector/SimpleInjector) .</span><span class="sxs-lookup"><span data-stu-id="eeff9-136">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="eeff9-137">Použití jednoduchého injektoru není potvrzením.</span><span class="sxs-lookup"><span data-stu-id="eeff9-137">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="eeff9-138">Přístupy k aktivaci middlewaru popsané v dokumentaci k funkci Simple injektor a problémy GitHubu doporučuje údržba jednoduchého injektoru.</span><span class="sxs-lookup"><span data-stu-id="eeff9-138">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="eeff9-139">Další informace najdete v [dokumentaci k jednoduchému vstřikovacímu](https://simpleinjector.readthedocs.io/en/latest/index.html) zařízení a v [úložišti GitHub pro jednoduché](https://github.com/simpleinjector/SimpleInjector)sady pro vkládání.</span><span class="sxs-lookup"><span data-stu-id="eeff9-139">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="eeff9-140">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="eeff9-140">IMiddlewareFactory</span></span>

<span data-ttu-id="eeff9-141"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middlewaru.</span><span class="sxs-lookup"><span data-stu-id="eeff9-141"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="eeff9-142">V ukázkové aplikaci je implementován objekt pro vytváření middlewaru pro vytvoření `SimpleInjectorActivatedMiddleware` instance.</span><span class="sxs-lookup"><span data-stu-id="eeff9-142">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="eeff9-143">Objekt pro vytváření middlewaru používá jednoduchý kontejner injektoru k překladu middlewaru:</span><span class="sxs-lookup"><span data-stu-id="eeff9-143">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="eeff9-144">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="eeff9-144">IMiddleware</span></span>

<span data-ttu-id="eeff9-145"><xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál žádostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="eeff9-145"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="eeff9-146">Middleware aktivovaný `IMiddlewareFactory` implementací (*middleware/SimpleInjectorActivatedMiddleware. cs*):</span><span class="sxs-lookup"><span data-stu-id="eeff9-146">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="eeff9-147">Pro middleware (*middleware/MiddlewareExtensions. cs*) se vytvoří rozšíření:</span><span class="sxs-lookup"><span data-stu-id="eeff9-147">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="eeff9-148">`Startup.ConfigureServices`musí provádět několik úloh:</span><span class="sxs-lookup"><span data-stu-id="eeff9-148">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="eeff9-149">Nastavení jednoduchého kontejneru pro nástřik.</span><span class="sxs-lookup"><span data-stu-id="eeff9-149">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="eeff9-150">Zaregistrujte objekt pro vytváření a middlewaru.</span><span class="sxs-lookup"><span data-stu-id="eeff9-150">Register the factory and middleware.</span></span>
* <span data-ttu-id="eeff9-151">Zpřístupněte kontext databáze aplikace z jednoduchého kontejneru injektoru.</span><span class="sxs-lookup"><span data-stu-id="eeff9-151">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="eeff9-152">Middleware je zaregistrován v kanálu zpracování žádosti v `Startup.Configure`nástroji:</span><span class="sxs-lookup"><span data-stu-id="eeff9-152">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="eeff9-153">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="eeff9-153">Additional resources</span></span>

* [<span data-ttu-id="eeff9-154">Middleware</span><span class="sxs-lookup"><span data-stu-id="eeff9-154">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="eeff9-155">Aktivace middlewaru založená na objektu factory</span><span class="sxs-lookup"><span data-stu-id="eeff9-155">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="eeff9-156">Úložiště GitHub pro jednoduché vložení</span><span class="sxs-lookup"><span data-stu-id="eeff9-156">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="eeff9-157">Dokumentace k jednoduchému vstřikovacímu zařízení</span><span class="sxs-lookup"><span data-stu-id="eeff9-157">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
