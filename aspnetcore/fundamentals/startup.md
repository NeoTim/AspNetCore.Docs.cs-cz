---
title: Spuštění aplikace v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak třída Startup v ASP.NET Core konfiguruje služby a kanál žádostí aplikace.
ms.author: riande
ms.custom: mvc
ms.date: 8/7/2019
uid: fundamentals/startup
ms.openlocfilehash: 8866ee9210a91754d8050d0b91ff52c3d3fe0836
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975441"
---
# <a name="app-startup-in-aspnet-core"></a><span data-ttu-id="d55b3-103">Spuštění aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d55b3-103">App startup in ASP.NET Core</span></span>

<span data-ttu-id="d55b3-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex)a [Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="d55b3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="d55b3-105">Třída `Startup` konfiguruje služby a kanál zpracování požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="d55b3-105">The `Startup` class configures services and the app's request pipeline.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="d55b3-106">Třída Startup</span><span class="sxs-lookup"><span data-stu-id="d55b3-106">The Startup class</span></span>

<span data-ttu-id="d55b3-107">Aplikace ASP.NET Core používají třídu `Startup`, která je konvenčně pojmenována `Startup`.</span><span class="sxs-lookup"><span data-stu-id="d55b3-107">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="d55b3-108">Třída `Startup`:</span><span class="sxs-lookup"><span data-stu-id="d55b3-108">The `Startup` class:</span></span>

* <span data-ttu-id="d55b3-109">Volitelně obsahuje metodu <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>, která slouží pro konfiguraci *služeb* aplikace.</span><span class="sxs-lookup"><span data-stu-id="d55b3-109">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="d55b3-110">Služba je znovupoužitelná komponenta, která poskytuje funkčnost aplikace.</span><span class="sxs-lookup"><span data-stu-id="d55b3-110">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="d55b3-111">Služby jsou nakonfigurovány &mdash; neboli takzvaně *zaregistrovány* &mdash; v metodě `ConfigureServices` a využívány napříč aplikací skrze [vkládání závislostí (DI)](xref:fundamentals/dependency-injection) nebo <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span><span class="sxs-lookup"><span data-stu-id="d55b3-111">Services are configured&mdash;also described as *registered*&mdash;in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="d55b3-112">Obsahuje metodu <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> pro vytvoření kanálu pro zpracování požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="d55b3-112">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="d55b3-113">`ConfigureServices`a `Configure` jsou volány modulem runtime ASP.NET Core při spuštění aplikace:</span><span class="sxs-lookup"><span data-stu-id="d55b3-113">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="d55b3-114">Předchozí ukázka je určena pro [Razor Pages](xref:razor-pages/index); verze MVC je podobná.</span><span class="sxs-lookup"><span data-stu-id="d55b3-114">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

::: moniker-end

<span data-ttu-id="d55b3-115">Třída je určena při sestavení hostitele aplikace. [](xref:fundamentals/index#host) `Startup`</span><span class="sxs-lookup"><span data-stu-id="d55b3-115">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="d55b3-116">Třída je obvykle určena [`WebHostBuilderExtensions.UseStartup<TStartup>`](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) voláním metody v Tvůrci hostitele: `Startup`</span><span class="sxs-lookup"><span data-stu-id="d55b3-116">The `Startup` class is typically specified by calling the [`WebHostBuilderExtensions.UseStartup<TStartup>`](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="d55b3-117">Hostitel poskytuje služby, které jsou k dispozici konstruktoru třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="d55b3-117">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="d55b3-118">Aplikace přidává další služby prostřednictvím metody `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d55b3-118">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="d55b3-119">Hostitelská i Aplikační služba jsou k dispozici `Configure` v i v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d55b3-119">Both the host and app services are available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="d55b3-120">Při použití `Startup` <xref:Microsoft.Extensions.Hosting.IHostBuilder>lze do konstruktoru vložit pouze následující typy služeb:</span><span class="sxs-lookup"><span data-stu-id="d55b3-120">Only the following service types can be injected into the `Startup` constructor when using <xref:Microsoft.Extensions.Hosting.IHostBuilder>:</span></span>

* `IWebHostEnvironment`
* `IHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

<span data-ttu-id="d55b3-121">Většina služeb není k dispozici, `Configure` dokud není volána metoda.</span><span class="sxs-lookup"><span data-stu-id="d55b3-121">Most services are not available until the `Configure` method is called.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d55b3-122">Hostitel poskytuje služby, které jsou k dispozici konstruktoru třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="d55b3-122">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="d55b3-123">Aplikace přidává další služby prostřednictvím metody `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d55b3-123">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="d55b3-124">Služby hostitele i aplikace jsou pak k dispozici v metodě `Configure` a v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="d55b3-124">Both the host and app services are then available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="d55b3-125">Ve třídě `Startup` se běžně používá [vkládání závislostí](xref:fundamentals/dependency-injection) pro vložení:</span><span class="sxs-lookup"><span data-stu-id="d55b3-125">A common use of [dependency injection](xref:fundamentals/dependency-injection) into the `Startup` class is to inject:</span></span>

* <span data-ttu-id="d55b3-126"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> pro konfiguraci služby pomocí prostředí.</span><span class="sxs-lookup"><span data-stu-id="d55b3-126"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> to configure services by environment.</span></span>
* <span data-ttu-id="d55b3-127"><xref:Microsoft.Extensions.Configuration.IConfiguration> pro načtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d55b3-127"><xref:Microsoft.Extensions.Configuration.IConfiguration> to read configuration.</span></span>
* <span data-ttu-id="d55b3-128"><xref:Microsoft.Extensions.Logging.ILoggerFactory> pro vytvoření protokolovacího nástroje v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d55b3-128"><xref:Microsoft.Extensions.Logging.ILoggerFactory> to create a logger in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

::: moniker-end
<span data-ttu-id="d55b3-129">Alternativou ke vložení `IWebHostEnvironment` je použití přístupu založeného na konvencích.</span><span class="sxs-lookup"><span data-stu-id="d55b3-129">An alternative to injecting `IWebHostEnvironment` is to use a conventions-based approach.</span></span>
::: moniker range=">= aspnetcore-3.0"

::: moniker-end

::: moniker range="< aspnetcore-3.0"
<span data-ttu-id="d55b3-130">Alternativou ke vložení `IHostingEnvironment` je použití přístupu založeného na konvencích.</span><span class="sxs-lookup"><span data-stu-id="d55b3-130">An alternative to injecting `IHostingEnvironment` is to use a conventions-based approach.</span></span>
::: moniker-end

<span data-ttu-id="d55b3-131">Pokud aplikace definuje samostatnou třídu `Startup` pro různá prostředí (například `StartupDevelopment`), odpovídající třída `Startup` je vybrána v době běhu.</span><span class="sxs-lookup"><span data-stu-id="d55b3-131">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="d55b3-132">Třída, jejíž název má příponu odpovídající aktuálnímu prostředí, je upřednostněna.</span><span class="sxs-lookup"><span data-stu-id="d55b3-132">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="d55b3-133">Pokud aplikace běží ve vývojovém prostředí a obsahuje třídu `Startup` i třídu `StartupDevelopment`, použije se třída `StartupDevelopment` .</span><span class="sxs-lookup"><span data-stu-id="d55b3-133">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="d55b3-134">Další informace naleznete v tématu [Používání více prostředí](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span><span class="sxs-lookup"><span data-stu-id="d55b3-134">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="d55b3-135">Další informace o hostiteli najdete v [hostiteli](xref:fundamentals/index#host) .</span><span class="sxs-lookup"><span data-stu-id="d55b3-135">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="d55b3-136">Informace o zpracování chyb během spuštění naleznete v tématu [Zpracování výjimek při spuštění](xref:fundamentals/error-handling#startup-exception-handling).</span><span class="sxs-lookup"><span data-stu-id="d55b3-136">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="d55b3-137">Metoda ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="d55b3-137">The ConfigureServices method</span></span>

<span data-ttu-id="d55b3-138">Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> je:</span><span class="sxs-lookup"><span data-stu-id="d55b3-138">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="d55b3-139">Volitelná.</span><span class="sxs-lookup"><span data-stu-id="d55b3-139">Optional.</span></span>
* <span data-ttu-id="d55b3-140">Je volána hostitelem před voláním metody `Configure` pro konfiguraci služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="d55b3-140">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="d55b3-141">metoda, ve které jsou [možnosti konfigurace](xref:fundamentals/configuration/index) nastaveny podle konvence.</span><span class="sxs-lookup"><span data-stu-id="d55b3-141">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="d55b3-142">Hostitel může nakonfigurovat některé služby před voláním metody `Startup`.</span><span class="sxs-lookup"><span data-stu-id="d55b3-142">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="d55b3-143">Další informace najdete v tématu [Hostitel](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="d55b3-143">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="d55b3-144">Pro funkce, které vyžadují rozsáhlou konfiguraci, existují rozšiřující metody `Add{Service}` nad <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span><span class="sxs-lookup"><span data-stu-id="d55b3-144">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="d55b3-145">**Přidejte**například DbContext, **přidejte**DefaultIdentity, **přidejte**EntityFrameworkStores a **přidejte**RazorPages:</span><span class="sxs-lookup"><span data-stu-id="d55b3-145">For example, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores, and **Add**RazorPages:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

::: moniker-end

<span data-ttu-id="d55b3-146">Po přidání služeb do kontejneru jsou tyto služby k dispozici v celé aplikaci a v rámci metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="d55b3-146">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="d55b3-147">Služby jsou řešeny prostřednictvím [vkládání závislostí](xref:fundamentals/dependency-injection) nebo z <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span><span class="sxs-lookup"><span data-stu-id="d55b3-147">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d55b3-148">Vizte [SetCompatibilityVersion](xref:mvc/compatibility-version) pro další informace o `SetCompatibilityVersion`.</span><span class="sxs-lookup"><span data-stu-id="d55b3-148">See [SetCompatibilityVersion](xref:mvc/compatibility-version) for more information on `SetCompatibilityVersion`.</span></span>

::: moniker-end

## <a name="the-configure-method"></a><span data-ttu-id="d55b3-149">Metoda Configure</span><span class="sxs-lookup"><span data-stu-id="d55b3-149">The Configure method</span></span>

<span data-ttu-id="d55b3-150">Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> se používá k určení toho, jak aplikace reaguje na HTTP požadavky.</span><span class="sxs-lookup"><span data-stu-id="d55b3-150">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="d55b3-151">Kanál požadavků se konfiguruje tak, že přidáte [middlewarové](xref:fundamentals/middleware/index) komponenty do instance <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="d55b3-151">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="d55b3-152">`IApplicationBuilder` je dostupný metodě `Configure`, není však registrován v kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="d55b3-152">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="d55b3-153">Hosting vytváří `IApplicationBuilder` a předává jej přímo metodě `Configure`.</span><span class="sxs-lookup"><span data-stu-id="d55b3-153">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="d55b3-154">[Šablony ASP.NET Core](/dotnet/core/tools/dotnet-new) konfigurují kanál s podporou pro:</span><span class="sxs-lookup"><span data-stu-id="d55b3-154">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="d55b3-155">Stránku výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="d55b3-155">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="d55b3-156">Obslužné rutiny výjimek</span><span class="sxs-lookup"><span data-stu-id="d55b3-156">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="d55b3-157">Zabezpečení striktního HTTP přenosu (HSTS)</span><span class="sxs-lookup"><span data-stu-id="d55b3-157">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="d55b3-158">Přesměrování HTTPS</span><span class="sxs-lookup"><span data-stu-id="d55b3-158">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="d55b3-159">Statické soubory</span><span class="sxs-lookup"><span data-stu-id="d55b3-159">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="d55b3-160">ASP.NET Core [MVC](xref:mvc/overview) a [Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="d55b3-160">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="d55b3-161">Obecné nařízení o ochraně osobních údajů (GDPR)</span><span class="sxs-lookup"><span data-stu-id="d55b3-161">General Data Protection Regulation (GDPR)</span></span>](xref:security/gdpr)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="d55b3-162">Předchozí ukázka je určena pro [Razor Pages](xref:razor-pages/index); verze MVC je podobná.</span><span class="sxs-lookup"><span data-stu-id="d55b3-162">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

::: moniker-end

<span data-ttu-id="d55b3-163">Každá rozšiřující metoda `Use` přidá jednu nebo více middlewarových komponent do kanálu zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="d55b3-163">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="d55b3-164">Například nakonfiguruje middleware na poskytování [statických souborů](xref:fundamentals/static-files). [](xref:fundamentals/middleware/index) <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*></span><span class="sxs-lookup"><span data-stu-id="d55b3-164">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="d55b3-165">Každá middlewarová komponenta v kanálu zpracování požadavků zodpovídá za vyvolání další komponenty v kanálu, případně může provést předčasné ukončení řetězce volání.</span><span class="sxs-lookup"><span data-stu-id="d55b3-165">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d55b3-166">Další `IWebHostEnvironment`služby, jako například, `ILoggerFactory`nebo cokoli definované v `ConfigureServices`, lze zadat v `Configure` signatuře metody.</span><span class="sxs-lookup"><span data-stu-id="d55b3-166">Additional services, such as `IWebHostEnvironment`, `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="d55b3-167">Tyto služby jsou vloženy, pokud jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="d55b3-167">These services are injected if they're available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d55b3-168">Další služby, `IHostingEnvironment` například a `ILoggerFactory`, nebo cokoli definované v `ConfigureServices`, lze zadat v `Configure` signatuře metody.</span><span class="sxs-lookup"><span data-stu-id="d55b3-168">Additional services, such as `IHostingEnvironment` and `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="d55b3-169">Tyto služby jsou vloženy, pokud jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="d55b3-169">These services are injected if they're available.</span></span>

::: moniker-end

<span data-ttu-id="d55b3-170">Další informace o tom, jak používat `IApplicationBuilder` a jaké je pořadí zpracování middlewarů, naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="d55b3-170">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="d55b3-171">Konfigurace služeb bez spuštění</span><span class="sxs-lookup"><span data-stu-id="d55b3-171">Configure services without Startup</span></span>

<span data-ttu-id="d55b3-172">Ke konfiguraci služeb a kanálu zpracování požadavků bez použití třídy `Startup` využijte volání usnadňujících metod `ConfigureServices` a `Configure` tvůrce (builderu) hostitele.</span><span class="sxs-lookup"><span data-stu-id="d55b3-172">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="d55b3-173">Při vícenásobném volání metody `ConfigureServices` se přidají služby ze všech volání.</span><span class="sxs-lookup"><span data-stu-id="d55b3-173">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="d55b3-174">Pokud existuje více volání metody `Configure`, využije se poslední volání `Configure`.</span><span class="sxs-lookup"><span data-stu-id="d55b3-174">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

::: moniker-end

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="d55b3-175">Rozšíření třídy Startup pomocí filtrů po spuštění</span><span class="sxs-lookup"><span data-stu-id="d55b3-175">Extend Startup with startup filters</span></span>

<span data-ttu-id="d55b3-176">Využijte <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> pro konfiguraci middlewaru na začátku a na konci [konfigurační](#the-configure-method) middlewarového kanálu vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="d55b3-176">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> to configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline.</span></span> <span data-ttu-id="d55b3-177">`IStartupFilter`slouží k vytvoření kanálu `Configure` metod.</span><span class="sxs-lookup"><span data-stu-id="d55b3-177">`IStartupFilter` is used to create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="d55b3-178">[IStartupFilter. Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) může nastavit middleware pro spuštění před nebo po middlewaru přidaném knihovnami.</span><span class="sxs-lookup"><span data-stu-id="d55b3-178">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="d55b3-179">`IStartupFilter`implementuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, které přijímá a `Action<IApplicationBuilder>`vrací.</span><span class="sxs-lookup"><span data-stu-id="d55b3-179">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="d55b3-180"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> definuje třídu pro konfiguraci kanálu zpracování požadavků vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="d55b3-180">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="d55b3-181">Další informace naleznete v tématu [Vytvoření kanálu middlewaru s IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span><span class="sxs-lookup"><span data-stu-id="d55b3-181">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="d55b3-182">Každý `IStartupFilter` z nich může do kanálu požadavků přidat jeden nebo více middlewarů.</span><span class="sxs-lookup"><span data-stu-id="d55b3-182">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="d55b3-183">Filtry jsou volány v pořadí, ve kterém byly přidány do kontejneru služeb.</span><span class="sxs-lookup"><span data-stu-id="d55b3-183">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="d55b3-184">Filtry mohou přidávat middleware před nebo po předání řízení dalšímu filtru, tedy připojují se na začátek nebo konec kanálu aplikace.</span><span class="sxs-lookup"><span data-stu-id="d55b3-184">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="d55b3-185">Následující příklad ukazuje, jak registrovat middleware pomocí `IStartupFilter`.</span><span class="sxs-lookup"><span data-stu-id="d55b3-185">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="d55b3-186">Middleware `RequestSetOptionsMiddleware` nastaví hodnoty voleb z parametrů řetězce dotazu (query string parameters):</span><span class="sxs-lookup"><span data-stu-id="d55b3-186">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

<span data-ttu-id="d55b3-187">`RequestSetOptionsMiddleware` je nakonfigurovaný ve třídě `RequestSetOptionsStartupFilter`:</span><span class="sxs-lookup"><span data-stu-id="d55b3-187">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

<span data-ttu-id="d55b3-188">`RequestSetOptionsMiddleware` je nakonfigurovaný ve třídě `RequestSetOptionsStartupFilter`:</span><span class="sxs-lookup"><span data-stu-id="d55b3-188">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

<span data-ttu-id="d55b3-189">Je zaregistrován v kontejneru služby v <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>. `IStartupFilter`</span><span class="sxs-lookup"><span data-stu-id="d55b3-189">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

::: moniker-end

<span data-ttu-id="d55b3-190">Pokud je k dispozici parametr `option` řetězce dotazu pro, middleware zpracovává přiřazení hodnoty před tím, než ASP.NET Core middleware vykreslí odpověď.</span><span class="sxs-lookup"><span data-stu-id="d55b3-190">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="d55b3-191">Pořadí spuštění middlewarů je nastaveno podle pořadí registrace `IStartupFilter`:</span><span class="sxs-lookup"><span data-stu-id="d55b3-191">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="d55b3-192">Několik různých implementací `IStartupFilter` může operovat se stejnými objekty.</span><span class="sxs-lookup"><span data-stu-id="d55b3-192">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="d55b3-193">Pokud je pro Vás důležité pořadí, seřaďte jednotlivé registrace služeb `IStartupFilter` tak, aby odpovídaly pořadí, ve kterém mají být jejich middlewary spuštěny.</span><span class="sxs-lookup"><span data-stu-id="d55b3-193">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="d55b3-194">Knihovny mohou přidávat middlewary s jednou nebo více implementacemi `IStartupFilter`, které se spuští před nebo po spuštění ostatních middlewarů aplikace zaregistrovaných pomocí `IStartupFilter`.</span><span class="sxs-lookup"><span data-stu-id="d55b3-194">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="d55b3-195">Vyvolání `IStartupFilter` middlewaru před middlewarem přidaným `IStartupFilter`knihovnou:</span><span class="sxs-lookup"><span data-stu-id="d55b3-195">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="d55b3-196">Před přidáním knihovny do kontejneru služby umístěte registraci služby.</span><span class="sxs-lookup"><span data-stu-id="d55b3-196">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="d55b3-197">Chcete-li provést vyvolání, umístěte registraci služby po přidání knihovny.</span><span class="sxs-lookup"><span data-stu-id="d55b3-197">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="d55b3-198">Přidání konfigurace při spuštění z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="d55b3-198">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="d55b3-199">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje do aplikace přidat různá vylepšení z externího sestavení při jejím spuštění, mimo třídu `Startup` aplikace.</span><span class="sxs-lookup"><span data-stu-id="d55b3-199">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="d55b3-200">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="d55b3-200">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d55b3-201">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d55b3-201">Additional resources</span></span>

* [<span data-ttu-id="d55b3-202">Hostitel</span><span class="sxs-lookup"><span data-stu-id="d55b3-202">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>
