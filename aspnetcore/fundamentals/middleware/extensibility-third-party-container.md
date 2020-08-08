---
title: Aktivace middlewaru pomocí kontejneru třetí strany v ASP.NET Core
author: rick-anderson
description: Naučte se používat middleware silného typu s aktivací založenou na výrobě a kontejnerem třetí strany v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
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
ms.openlocfilehash: 5d453de26b265b795768befeaa4071e7b0e1ec08
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017026"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="3bd0c-103">Aktivace middlewaru pomocí kontejneru třetí strany v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3bd0c-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3bd0c-104">Tento článek ukazuje, jak používat <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> a <xref:Microsoft.AspNetCore.Http.IMiddleware> jako bod rozšiřitelnosti pro aktivaci [middlewaru](xref:fundamentals/middleware/index) pomocí kontejneru třetí strany.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="3bd0c-105">Úvodní informace o systémech `IMiddlewareFactory` a `IMiddleware` najdete v tématu <xref:fundamentals/middleware/extensibility> .</span><span class="sxs-lookup"><span data-stu-id="3bd0c-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="3bd0c-106">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3bd0c-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3bd0c-107">Ukázková aplikace předvádí aktivaci middlewaru pomocí `IMiddlewareFactory` implementace `SimpleInjectorMiddlewareFactory` .</span><span class="sxs-lookup"><span data-stu-id="3bd0c-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="3bd0c-108">Ukázka používá [jednoduchý](https://simpleinjector.org) kontejner vkládání závislostí (di).</span><span class="sxs-lookup"><span data-stu-id="3bd0c-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="3bd0c-109">Implementace middleware v ukázce zaznamenává hodnotu poskytnutou parametrem řetězce dotazu ( `key` ).</span><span class="sxs-lookup"><span data-stu-id="3bd0c-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="3bd0c-110">Middleware používá vložený kontext databáze (Oborová služba) k záznamu hodnoty řetězce dotazu do databáze v paměti.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="3bd0c-111">Ukázková aplikace používá pro demonstrační účely čistě modul pro [vkládání](https://github.com/simpleinjector/SimpleInjector) .</span><span class="sxs-lookup"><span data-stu-id="3bd0c-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="3bd0c-112">Použití jednoduchého injektoru není potvrzením.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="3bd0c-113">Přístupy k aktivaci middlewaru popsané v dokumentaci k funkci Simple injektor a problémy GitHubu doporučuje údržba jednoduchého injektoru.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="3bd0c-114">Další informace najdete v [dokumentaci k jednoduchému vstřikovacímu](https://simpleinjector.readthedocs.io/en/latest/index.html) zařízení a v [úložišti GitHub pro jednoduché](https://github.com/simpleinjector/SimpleInjector)sady pro vkládání.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="3bd0c-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="3bd0c-115">IMiddlewareFactory</span></span>

<span data-ttu-id="3bd0c-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middlewaru.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="3bd0c-117">V ukázkové aplikaci je implementován objekt pro vytváření middlewaru pro vytvoření `SimpleInjectorActivatedMiddleware` instance.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="3bd0c-118">Objekt pro vytváření middlewaru používá jednoduchý kontejner injektoru k překladu middlewaru:</span><span class="sxs-lookup"><span data-stu-id="3bd0c-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="3bd0c-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="3bd0c-119">IMiddleware</span></span>

<span data-ttu-id="3bd0c-120"><xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál žádostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="3bd0c-121">Middleware aktivovaný `IMiddlewareFactory` implementací (*middleware/SimpleInjectorActivatedMiddleware. cs*):</span><span class="sxs-lookup"><span data-stu-id="3bd0c-121">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="3bd0c-122">Pro middleware (*middleware/MiddlewareExtensions. cs*) se vytvoří rozšíření:</span><span class="sxs-lookup"><span data-stu-id="3bd0c-122">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="3bd0c-123">`Startup.ConfigureServices`musí provádět několik úloh:</span><span class="sxs-lookup"><span data-stu-id="3bd0c-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="3bd0c-124">Nastavení jednoduchého kontejneru pro nástřik.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="3bd0c-125">Zaregistrujte objekt pro vytváření a middlewaru.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="3bd0c-126">Zpřístupněte kontext databáze aplikace z jednoduchého kontejneru injektoru.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="3bd0c-127">Middleware je zaregistrován v kanálu zpracování žádosti v nástroji `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="3bd0c-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3bd0c-128">Tento článek ukazuje, jak používat <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> a <xref:Microsoft.AspNetCore.Http.IMiddleware> jako bod rozšiřitelnosti pro aktivaci [middlewaru](xref:fundamentals/middleware/index) pomocí kontejneru třetí strany.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="3bd0c-129">Úvodní informace o systémech `IMiddlewareFactory` a `IMiddleware` najdete v tématu <xref:fundamentals/middleware/extensibility> .</span><span class="sxs-lookup"><span data-stu-id="3bd0c-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="3bd0c-130">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3bd0c-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3bd0c-131">Ukázková aplikace předvádí aktivaci middlewaru pomocí `IMiddlewareFactory` implementace `SimpleInjectorMiddlewareFactory` .</span><span class="sxs-lookup"><span data-stu-id="3bd0c-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="3bd0c-132">Ukázka používá [jednoduchý](https://simpleinjector.org) kontejner vkládání závislostí (di).</span><span class="sxs-lookup"><span data-stu-id="3bd0c-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="3bd0c-133">Implementace middleware v ukázce zaznamenává hodnotu poskytnutou parametrem řetězce dotazu ( `key` ).</span><span class="sxs-lookup"><span data-stu-id="3bd0c-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="3bd0c-134">Middleware používá vložený kontext databáze (Oborová služba) k záznamu hodnoty řetězce dotazu do databáze v paměti.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="3bd0c-135">Ukázková aplikace používá pro demonstrační účely čistě modul pro [vkládání](https://github.com/simpleinjector/SimpleInjector) .</span><span class="sxs-lookup"><span data-stu-id="3bd0c-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="3bd0c-136">Použití jednoduchého injektoru není potvrzením.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="3bd0c-137">Přístupy k aktivaci middlewaru popsané v dokumentaci k funkci Simple injektor a problémy GitHubu doporučuje údržba jednoduchého injektoru.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="3bd0c-138">Další informace najdete v [dokumentaci k jednoduchému vstřikovacímu](https://simpleinjector.readthedocs.io/en/latest/index.html) zařízení a v [úložišti GitHub pro jednoduché](https://github.com/simpleinjector/SimpleInjector)sady pro vkládání.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="3bd0c-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="3bd0c-139">IMiddlewareFactory</span></span>

<span data-ttu-id="3bd0c-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>poskytuje metody pro vytvoření middlewaru.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="3bd0c-141">V ukázkové aplikaci je implementován objekt pro vytváření middlewaru pro vytvoření `SimpleInjectorActivatedMiddleware` instance.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="3bd0c-142">Objekt pro vytváření middlewaru používá jednoduchý kontejner injektoru k překladu middlewaru:</span><span class="sxs-lookup"><span data-stu-id="3bd0c-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="3bd0c-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="3bd0c-143">IMiddleware</span></span>

<span data-ttu-id="3bd0c-144"><xref:Microsoft.AspNetCore.Http.IMiddleware>definuje middleware pro kanál žádostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="3bd0c-145">Middleware aktivovaný `IMiddlewareFactory` implementací (*middleware/SimpleInjectorActivatedMiddleware. cs*):</span><span class="sxs-lookup"><span data-stu-id="3bd0c-145">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="3bd0c-146">Pro middleware (*middleware/MiddlewareExtensions. cs*) se vytvoří rozšíření:</span><span class="sxs-lookup"><span data-stu-id="3bd0c-146">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="3bd0c-147">`Startup.ConfigureServices`musí provádět několik úloh:</span><span class="sxs-lookup"><span data-stu-id="3bd0c-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="3bd0c-148">Nastavení jednoduchého kontejneru pro nástřik.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="3bd0c-149">Zaregistrujte objekt pro vytváření a middlewaru.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="3bd0c-150">Zpřístupněte kontext databáze aplikace z jednoduchého kontejneru injektoru.</span><span class="sxs-lookup"><span data-stu-id="3bd0c-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="3bd0c-151">Middleware je zaregistrován v kanálu zpracování žádosti v nástroji `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="3bd0c-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3bd0c-152">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3bd0c-152">Additional resources</span></span>

* [<span data-ttu-id="3bd0c-153">Middleware</span><span class="sxs-lookup"><span data-stu-id="3bd0c-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="3bd0c-154">Aktivace middlewaru založená na objektu factory</span><span class="sxs-lookup"><span data-stu-id="3bd0c-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="3bd0c-155">Úložiště GitHub pro jednoduché vložení</span><span class="sxs-lookup"><span data-stu-id="3bd0c-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="3bd0c-156">Dokumentace k jednoduchému vstřikovacímu zařízení</span><span class="sxs-lookup"><span data-stu-id="3bd0c-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
