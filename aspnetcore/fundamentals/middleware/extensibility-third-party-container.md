---
title: Aktivace middlewaru pomocí kontejneru třetí strany v ASP.NET Core
author: rick-anderson
description: Naučte se používat middleware silného typu s aktivací založenou na výrobě a kontejnerem třetí strany v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: e0ebcd46e6b506b7b2172f087f12773910ea4d9c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634862"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="d7df8-103">Aktivace middlewaru pomocí kontejneru třetí strany v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7df8-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d7df8-104">Tento článek ukazuje, jak používat <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> a <xref:Microsoft.AspNetCore.Http.IMiddleware> jako bod rozšiřitelnosti pro aktivaci [middlewaru](xref:fundamentals/middleware/index) pomocí kontejneru třetí strany.</span><span class="sxs-lookup"><span data-stu-id="d7df8-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="d7df8-105">Úvodní informace o systémech `IMiddlewareFactory` a `IMiddleware` najdete v tématu <xref:fundamentals/middleware/extensibility> .</span><span class="sxs-lookup"><span data-stu-id="d7df8-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="d7df8-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d7df8-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d7df8-107">Ukázková aplikace předvádí aktivaci middlewaru pomocí `IMiddlewareFactory` implementace `SimpleInjectorMiddlewareFactory` .</span><span class="sxs-lookup"><span data-stu-id="d7df8-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="d7df8-108">Ukázka používá [jednoduchý](https://simpleinjector.org) kontejner vkládání závislostí (di).</span><span class="sxs-lookup"><span data-stu-id="d7df8-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="d7df8-109">Implementace middleware v ukázce zaznamenává hodnotu poskytnutou parametrem řetězce dotazu ( `key` ).</span><span class="sxs-lookup"><span data-stu-id="d7df8-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="d7df8-110">Middleware používá vložený kontext databáze (Oborová služba) k záznamu hodnoty řetězce dotazu do databáze v paměti.</span><span class="sxs-lookup"><span data-stu-id="d7df8-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="d7df8-111">Ukázková aplikace používá pro demonstrační účely čistě modul pro [vkládání](https://github.com/simpleinjector/SimpleInjector) .</span><span class="sxs-lookup"><span data-stu-id="d7df8-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="d7df8-112">Použití jednoduchého injektoru není potvrzením.</span><span class="sxs-lookup"><span data-stu-id="d7df8-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="d7df8-113">Přístupy k aktivaci middlewaru popsané v dokumentaci k funkci Simple injektor a problémy GitHubu doporučuje údržba jednoduchého injektoru.</span><span class="sxs-lookup"><span data-stu-id="d7df8-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="d7df8-114">Další informace najdete v [dokumentaci k jednoduchému vstřikovacímu](https://simpleinjector.readthedocs.io/en/latest/index.html) zařízení a v [úložišti GitHub pro jednoduché](https://github.com/simpleinjector/SimpleInjector)sady pro vkládání.</span><span class="sxs-lookup"><span data-stu-id="d7df8-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="d7df8-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="d7df8-115">IMiddlewareFactory</span></span>

<span data-ttu-id="d7df8-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> poskytuje metody pro vytvoření middlewaru.</span><span class="sxs-lookup"><span data-stu-id="d7df8-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="d7df8-117">V ukázkové aplikaci je implementován objekt pro vytváření middlewaru pro vytvoření `SimpleInjectorActivatedMiddleware` instance.</span><span class="sxs-lookup"><span data-stu-id="d7df8-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="d7df8-118">Objekt pro vytváření middlewaru používá jednoduchý kontejner injektoru k překladu middlewaru:</span><span class="sxs-lookup"><span data-stu-id="d7df8-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="d7df8-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="d7df8-119">IMiddleware</span></span>

<span data-ttu-id="d7df8-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> definuje middleware pro kanál žádostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="d7df8-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="d7df8-121">Middleware aktivovaný `IMiddlewareFactory` implementací (*middleware/SimpleInjectorActivatedMiddleware. cs*):</span><span class="sxs-lookup"><span data-stu-id="d7df8-121">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="d7df8-122">Pro middleware (*middleware/MiddlewareExtensions. cs*) se vytvoří rozšíření:</span><span class="sxs-lookup"><span data-stu-id="d7df8-122">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="d7df8-123">`Startup.ConfigureServices` musí provádět několik úloh:</span><span class="sxs-lookup"><span data-stu-id="d7df8-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="d7df8-124">Nastavení jednoduchého kontejneru pro nástřik.</span><span class="sxs-lookup"><span data-stu-id="d7df8-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="d7df8-125">Zaregistrujte objekt pro vytváření a middlewaru.</span><span class="sxs-lookup"><span data-stu-id="d7df8-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="d7df8-126">Zpřístupněte kontext databáze aplikace z jednoduchého kontejneru injektoru.</span><span class="sxs-lookup"><span data-stu-id="d7df8-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="d7df8-127">Middleware je zaregistrován v kanálu zpracování žádosti v nástroji `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d7df8-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d7df8-128">Tento článek ukazuje, jak používat <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> a <xref:Microsoft.AspNetCore.Http.IMiddleware> jako bod rozšiřitelnosti pro aktivaci [middlewaru](xref:fundamentals/middleware/index) pomocí kontejneru třetí strany.</span><span class="sxs-lookup"><span data-stu-id="d7df8-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="d7df8-129">Úvodní informace o systémech `IMiddlewareFactory` a `IMiddleware` najdete v tématu <xref:fundamentals/middleware/extensibility> .</span><span class="sxs-lookup"><span data-stu-id="d7df8-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="d7df8-130">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d7df8-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d7df8-131">Ukázková aplikace předvádí aktivaci middlewaru pomocí `IMiddlewareFactory` implementace `SimpleInjectorMiddlewareFactory` .</span><span class="sxs-lookup"><span data-stu-id="d7df8-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="d7df8-132">Ukázka používá [jednoduchý](https://simpleinjector.org) kontejner vkládání závislostí (di).</span><span class="sxs-lookup"><span data-stu-id="d7df8-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="d7df8-133">Implementace middleware v ukázce zaznamenává hodnotu poskytnutou parametrem řetězce dotazu ( `key` ).</span><span class="sxs-lookup"><span data-stu-id="d7df8-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="d7df8-134">Middleware používá vložený kontext databáze (Oborová služba) k záznamu hodnoty řetězce dotazu do databáze v paměti.</span><span class="sxs-lookup"><span data-stu-id="d7df8-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="d7df8-135">Ukázková aplikace používá pro demonstrační účely čistě modul pro [vkládání](https://github.com/simpleinjector/SimpleInjector) .</span><span class="sxs-lookup"><span data-stu-id="d7df8-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="d7df8-136">Použití jednoduchého injektoru není potvrzením.</span><span class="sxs-lookup"><span data-stu-id="d7df8-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="d7df8-137">Přístupy k aktivaci middlewaru popsané v dokumentaci k funkci Simple injektor a problémy GitHubu doporučuje údržba jednoduchého injektoru.</span><span class="sxs-lookup"><span data-stu-id="d7df8-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="d7df8-138">Další informace najdete v [dokumentaci k jednoduchému vstřikovacímu](https://simpleinjector.readthedocs.io/en/latest/index.html) zařízení a v [úložišti GitHub pro jednoduché](https://github.com/simpleinjector/SimpleInjector)sady pro vkládání.</span><span class="sxs-lookup"><span data-stu-id="d7df8-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="d7df8-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="d7df8-139">IMiddlewareFactory</span></span>

<span data-ttu-id="d7df8-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> poskytuje metody pro vytvoření middlewaru.</span><span class="sxs-lookup"><span data-stu-id="d7df8-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="d7df8-141">V ukázkové aplikaci je implementován objekt pro vytváření middlewaru pro vytvoření `SimpleInjectorActivatedMiddleware` instance.</span><span class="sxs-lookup"><span data-stu-id="d7df8-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="d7df8-142">Objekt pro vytváření middlewaru používá jednoduchý kontejner injektoru k překladu middlewaru:</span><span class="sxs-lookup"><span data-stu-id="d7df8-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="d7df8-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="d7df8-143">IMiddleware</span></span>

<span data-ttu-id="d7df8-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> definuje middleware pro kanál žádostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="d7df8-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="d7df8-145">Middleware aktivovaný `IMiddlewareFactory` implementací (*middleware/SimpleInjectorActivatedMiddleware. cs*):</span><span class="sxs-lookup"><span data-stu-id="d7df8-145">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="d7df8-146">Pro middleware (*middleware/MiddlewareExtensions. cs*) se vytvoří rozšíření:</span><span class="sxs-lookup"><span data-stu-id="d7df8-146">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="d7df8-147">`Startup.ConfigureServices` musí provádět několik úloh:</span><span class="sxs-lookup"><span data-stu-id="d7df8-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="d7df8-148">Nastavení jednoduchého kontejneru pro nástřik.</span><span class="sxs-lookup"><span data-stu-id="d7df8-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="d7df8-149">Zaregistrujte objekt pro vytváření a middlewaru.</span><span class="sxs-lookup"><span data-stu-id="d7df8-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="d7df8-150">Zpřístupněte kontext databáze aplikace z jednoduchého kontejneru injektoru.</span><span class="sxs-lookup"><span data-stu-id="d7df8-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="d7df8-151">Middleware je zaregistrován v kanálu zpracování žádosti v nástroji `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="d7df8-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d7df8-152">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="d7df8-152">Additional resources</span></span>

* [<span data-ttu-id="d7df8-153">Middleware</span><span class="sxs-lookup"><span data-stu-id="d7df8-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="d7df8-154">Aktivace middlewaru založená na objektu factory</span><span class="sxs-lookup"><span data-stu-id="d7df8-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="d7df8-155">Úložiště GitHub pro jednoduché vložení</span><span class="sxs-lookup"><span data-stu-id="d7df8-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="d7df8-156">Dokumentace k jednoduchému vstřikovacímu zařízení</span><span class="sxs-lookup"><span data-stu-id="d7df8-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
