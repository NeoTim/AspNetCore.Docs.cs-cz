---
title: Spuštění aplikace v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak třída Startup v ASP.NET Core konfiguruje služby a kanál žádostí aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/02/2019
uid: fundamentals/startup
ms.openlocfilehash: 081eaa772d136477a37a3392877886327e0cda7c
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634041"
---
# <a name="app-startup-in-aspnet-core"></a><span data-ttu-id="39de9-103">Spuštění aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="39de9-103">App startup in ASP.NET Core</span></span>

<span data-ttu-id="39de9-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex)a [Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="39de9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="39de9-105">Třída `Startup` konfiguruje služby a kanál žádostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="39de9-105">The `Startup` class configures services and the app's request pipeline.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="39de9-106">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="39de9-106">The Startup class</span></span>

<span data-ttu-id="39de9-107">Aplikace ASP.NET Core používají `Startup` třídu, která je pojmenována `Startup` podle konvence.</span><span class="sxs-lookup"><span data-stu-id="39de9-107">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="39de9-108">Třída `Startup`:</span><span class="sxs-lookup"><span data-stu-id="39de9-108">The `Startup` class:</span></span>

* <span data-ttu-id="39de9-109">Volitelně zahrnuje metodu <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> pro konfiguraci *služeb*aplikace.</span><span class="sxs-lookup"><span data-stu-id="39de9-109">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="39de9-110">Služba je opakovaně použitelná součást, která poskytuje funkce aplikace.</span><span class="sxs-lookup"><span data-stu-id="39de9-110">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="39de9-111">Služby jsou *registrovány* v `ConfigureServices` a spotřebovány v rámci aplikace přes [vkládání závislostí (DI)](xref:fundamentals/dependency-injection) nebo <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span><span class="sxs-lookup"><span data-stu-id="39de9-111">Services are *registered* in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="39de9-112">Obsahuje metodu <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> k vytvoření kanálu zpracování požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="39de9-112">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="39de9-113">`ConfigureServices` a `Configure` jsou volány modulem runtime ASP.NET Core při spuštění aplikace:</span><span class="sxs-lookup"><span data-stu-id="39de9-113">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="39de9-114">Předchozí ukázka je určena pro [Razor Pages](xref:razor-pages/index); verze MVC je podobná.</span><span class="sxs-lookup"><span data-stu-id="39de9-114">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

::: moniker-end

<span data-ttu-id="39de9-115">Třída `Startup` je určena při sestavení [hostitele](xref:fundamentals/index#host) aplikace.</span><span class="sxs-lookup"><span data-stu-id="39de9-115">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="39de9-116">Třída `Startup` je obvykle určena voláním metody [`WebHostBuilderExtensions.UseStartup<TStartup>`](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) na tvůrci hostitele:</span><span class="sxs-lookup"><span data-stu-id="39de9-116">The `Startup` class is typically specified by calling the [`WebHostBuilderExtensions.UseStartup<TStartup>`](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="39de9-117">Hostitel poskytuje služby, které jsou k dispozici pro konstruktor `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="39de9-117">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="39de9-118">Aplikace přidá další služby prostřednictvím `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="39de9-118">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="39de9-119">Hostitelská i Aplikační služba jsou k dispozici v `Configure` a v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="39de9-119">Both the host and app services are available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="39de9-120">Při použití [obecného hostitele](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) lze do konstruktoru `Startup` vložit pouze následující typy služeb:</span><span class="sxs-lookup"><span data-stu-id="39de9-120">Only the following service types can be injected into the `Startup` constructor when using the [Generic Host](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

<span data-ttu-id="39de9-121">Většina služeb není k dispozici, dokud není volána metoda `Configure`.</span><span class="sxs-lookup"><span data-stu-id="39de9-121">Most services are not available until the `Configure` method is called.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="39de9-122">Hostitel poskytuje služby, které jsou k dispozici pro konstruktor `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="39de9-122">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="39de9-123">Aplikace přidá další služby prostřednictvím `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="39de9-123">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="39de9-124">Hostitelská i Aplikační služba jsou pak k dispozici v `Configure` a v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="39de9-124">Both the host and app services are then available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="39de9-125">Běžné použití [Injektáže závislosti](xref:fundamentals/dependency-injection) do `Startup` třídy je vložení:</span><span class="sxs-lookup"><span data-stu-id="39de9-125">A common use of [dependency injection](xref:fundamentals/dependency-injection) into the `Startup` class is to inject:</span></span>

* <span data-ttu-id="39de9-126"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> ke konfiguraci služeb podle prostředí.</span><span class="sxs-lookup"><span data-stu-id="39de9-126"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> to configure services by environment.</span></span>
* <span data-ttu-id="39de9-127"><xref:Microsoft.Extensions.Configuration.IConfiguration> ke čtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="39de9-127"><xref:Microsoft.Extensions.Configuration.IConfiguration> to read configuration.</span></span>
* <span data-ttu-id="39de9-128"><xref:Microsoft.Extensions.Logging.ILoggerFactory> vytvoření protokolovacího nástroje v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="39de9-128"><xref:Microsoft.Extensions.Logging.ILoggerFactory> to create a logger in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

<span data-ttu-id="39de9-129">Většina služeb není k dispozici, dokud není volána metoda `Configure`.</span><span class="sxs-lookup"><span data-stu-id="39de9-129">Most services are not available until the `Configure` method is called.</span></span>

::: moniker-end

### <a name="multiple-startup"></a><span data-ttu-id="39de9-130">Vícenásobné spuštění</span><span class="sxs-lookup"><span data-stu-id="39de9-130">Multiple Startup</span></span>

<span data-ttu-id="39de9-131">Když aplikace definuje samostatné třídy `Startup` pro různá prostředí (například `StartupDevelopment`), je při běhu vybrána odpovídající třída `Startup`.</span><span class="sxs-lookup"><span data-stu-id="39de9-131">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="39de9-132">Určuje prioritu třídy, jejíž přípona názvu odpovídá aktuálnímu prostředí.</span><span class="sxs-lookup"><span data-stu-id="39de9-132">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="39de9-133">Pokud je aplikace spuštěna ve vývojovém prostředí a obsahuje třídu `Startup` a třídu `StartupDevelopment`, je použita třída `StartupDevelopment`.</span><span class="sxs-lookup"><span data-stu-id="39de9-133">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="39de9-134">Další informace najdete v tématu [použití více prostředí](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span><span class="sxs-lookup"><span data-stu-id="39de9-134">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="39de9-135">Další informace o hostiteli najdete v [hostiteli](xref:fundamentals/index#host) .</span><span class="sxs-lookup"><span data-stu-id="39de9-135">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="39de9-136">Informace o zpracování chyb během spuštění najdete v tématu [zpracování výjimek při spuštění](xref:fundamentals/error-handling#startup-exception-handling).</span><span class="sxs-lookup"><span data-stu-id="39de9-136">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="39de9-137">Metoda ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="39de9-137">The ConfigureServices method</span></span>

<span data-ttu-id="39de9-138">Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> je:</span><span class="sxs-lookup"><span data-stu-id="39de9-138">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="39de9-139">Volitelné.</span><span class="sxs-lookup"><span data-stu-id="39de9-139">Optional.</span></span>
* <span data-ttu-id="39de9-140">Volá se hostitelem před metodou `Configure` ke konfiguraci služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="39de9-140">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="39de9-141">Kde jsou [Možnosti konfigurace](xref:fundamentals/configuration/index) nastaveny podle konvence.</span><span class="sxs-lookup"><span data-stu-id="39de9-141">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="39de9-142">Hostitel může nakonfigurovat některé služby před voláním `Startup` metody.</span><span class="sxs-lookup"><span data-stu-id="39de9-142">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="39de9-143">Další informace najdete v tématu [hostitel](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="39de9-143">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="39de9-144">Pro funkce, které vyžadují zásadní instalaci, existují `Add{Service}` metody rozšíření na <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span><span class="sxs-lookup"><span data-stu-id="39de9-144">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="39de9-145">**Přidejte**například DbContext, **přidejte**DefaultIdentity, **přidejte**EntityFrameworkStores a **přidejte**RazorPages:</span><span class="sxs-lookup"><span data-stu-id="39de9-145">For example, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores, and **Add**RazorPages:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

::: moniker-end

<span data-ttu-id="39de9-146">Přidání služeb do kontejneru služby je zpřístupní v rámci aplikace a v metodě `Configure`.</span><span class="sxs-lookup"><span data-stu-id="39de9-146">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="39de9-147">Služby jsou vyřešeny prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection) nebo z <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span><span class="sxs-lookup"><span data-stu-id="39de9-147">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="39de9-148">Další informace o `SetCompatibilityVersion`najdete v tématu [SetCompatibilityVersion](xref:mvc/compatibility-version) .</span><span class="sxs-lookup"><span data-stu-id="39de9-148">See [SetCompatibilityVersion](xref:mvc/compatibility-version) for more information on `SetCompatibilityVersion`.</span></span>

::: moniker-end

## <a name="the-configure-method"></a><span data-ttu-id="39de9-149">Metoda Configure</span><span class="sxs-lookup"><span data-stu-id="39de9-149">The Configure method</span></span>

<span data-ttu-id="39de9-150">Metoda <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> slouží k určení způsobu, jakým aplikace reaguje na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="39de9-150">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="39de9-151">Kanál požadavků je nakonfigurován přidáním součástí [middlewaru](xref:fundamentals/middleware/index) do instance <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>.</span><span class="sxs-lookup"><span data-stu-id="39de9-151">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="39de9-152">`IApplicationBuilder` je k dispozici pro metodu `Configure`, ale není zaregistrována v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="39de9-152">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="39de9-153">Hostování vytvoří `IApplicationBuilder` a předá ho přímo do `Configure`.</span><span class="sxs-lookup"><span data-stu-id="39de9-153">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="39de9-154">[Šablony ASP.NET Core](/dotnet/core/tools/dotnet-new) konfigurují kanál s podporou pro:</span><span class="sxs-lookup"><span data-stu-id="39de9-154">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="39de9-155">Stránka s výjimkou pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="39de9-155">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="39de9-156">Obslužná rutina výjimky</span><span class="sxs-lookup"><span data-stu-id="39de9-156">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="39de9-157">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="39de9-157">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="39de9-158">Přesměrování HTTPS</span><span class="sxs-lookup"><span data-stu-id="39de9-158">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="39de9-159">Statické soubory</span><span class="sxs-lookup"><span data-stu-id="39de9-159">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="39de9-160">ASP.NET Core [MVC](xref:mvc/overview) a [Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="39de9-160">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="39de9-161">Obecné nařízení o ochraně osobních údajů (GDPR)</span><span class="sxs-lookup"><span data-stu-id="39de9-161">General Data Protection Regulation (GDPR)</span></span>](xref:security/gdpr)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="39de9-162">Předchozí ukázka je určena pro [Razor Pages](xref:razor-pages/index); verze MVC je podobná.</span><span class="sxs-lookup"><span data-stu-id="39de9-162">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

::: moniker-end

<span data-ttu-id="39de9-163">Každá metoda rozšíření `Use` přidá do kanálu žádosti jednu nebo více součástí middlewaru.</span><span class="sxs-lookup"><span data-stu-id="39de9-163">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="39de9-164"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> například nakonfiguruje [middleware](xref:fundamentals/middleware/index) pro poskytování [statických souborů](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="39de9-164">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="39de9-165">Každá součást middlewaru v kanálu požadavků zodpovídá za vyvolání další komponenty v kanálu nebo při krátkém okruhu řetězce, pokud je to vhodné.</span><span class="sxs-lookup"><span data-stu-id="39de9-165">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="39de9-166">Další služby, například `IWebHostEnvironment`, `ILoggerFactory`nebo cokoli definované v `ConfigureServices`, lze zadat v signatuře metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="39de9-166">Additional services, such as `IWebHostEnvironment`, `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="39de9-167">Tyto služby jsou vloženy, pokud jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="39de9-167">These services are injected if they're available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="39de9-168">Další služby, například `IHostingEnvironment` a `ILoggerFactory`nebo cokoli definované v `ConfigureServices`, lze zadat v signatuře metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="39de9-168">Additional services, such as `IHostingEnvironment` and `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="39de9-169">Tyto služby jsou vloženy, pokud jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="39de9-169">These services are injected if they're available.</span></span>

::: moniker-end

<span data-ttu-id="39de9-170">Další informace o tom, jak používat `IApplicationBuilder` a pořadí zpracování middlewaru, najdete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="39de9-170">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="39de9-171">Konfigurace služeb bez spuštění</span><span class="sxs-lookup"><span data-stu-id="39de9-171">Configure services without Startup</span></span>

<span data-ttu-id="39de9-172">Chcete-li nakonfigurovat služby a kanál zpracování požadavků bez použití třídy `Startup`, zavolejte `ConfigureServices` a `Configure` metody usnadnění v Tvůrci hostitele.</span><span class="sxs-lookup"><span data-stu-id="39de9-172">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="39de9-173">Několik volání, která se `ConfigureServices` připojit k druhému.</span><span class="sxs-lookup"><span data-stu-id="39de9-173">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="39de9-174">Pokud existuje více volání metody `Configure`, je použita poslední volání `Configure`.</span><span class="sxs-lookup"><span data-stu-id="39de9-174">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

::: moniker-end

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="39de9-175">Rozšířené spuštění s filtry po spuštění</span><span class="sxs-lookup"><span data-stu-id="39de9-175">Extend Startup with startup filters</span></span>

<span data-ttu-id="39de9-176">Použít <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span><span class="sxs-lookup"><span data-stu-id="39de9-176">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span></span>

* <span data-ttu-id="39de9-177">Konfigurace middlewaru na začátku nebo na konci kanálu [Konfigurace](#the-configure-method) middlewaru aplikace bez explicitního volání `Use{Middleware}`.</span><span class="sxs-lookup"><span data-stu-id="39de9-177">To configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline without an explicit call to `Use{Middleware}`.</span></span> <span data-ttu-id="39de9-178">`IStartupFilter` používá ASP.NET Core k přidání výchozích hodnot na začátek kanálu, aniž by bylo nutné, aby autor aplikace explicitně zaregistroval výchozí middleware.</span><span class="sxs-lookup"><span data-stu-id="39de9-178">`IStartupFilter` is used by ASP.NET Core to add defaults to the beginning of the pipeline without having to make the app author explicitly register the default middleware.</span></span> <span data-ttu-id="39de9-179">`IStartupFilter` umožňuje jinému volání komponenty `Use{Middleware}` jménem autora aplikace.</span><span class="sxs-lookup"><span data-stu-id="39de9-179">`IStartupFilter` allows a different component call `Use{Middleware}` on behalf of the app author.</span></span>
* <span data-ttu-id="39de9-180">Chcete-li vytvořit kanál `Configure`ch metod.</span><span class="sxs-lookup"><span data-stu-id="39de9-180">To create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="39de9-181">[IStartupFilter. Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) může nastavit middleware pro spuštění před nebo po middlewaru přidaném knihovnami.</span><span class="sxs-lookup"><span data-stu-id="39de9-181">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="39de9-182">`IStartupFilter` implementuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, které obdrží a vrátí `Action<IApplicationBuilder>`.</span><span class="sxs-lookup"><span data-stu-id="39de9-182">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="39de9-183"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> definuje třídu pro konfiguraci kanálu požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="39de9-183">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="39de9-184">Další informace najdete v tématu [vytvoření kanálu middlewaru pomocí IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span><span class="sxs-lookup"><span data-stu-id="39de9-184">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="39de9-185">Každý `IStartupFilter` může do kanálu požadavků přidat jeden nebo více middlewarů.</span><span class="sxs-lookup"><span data-stu-id="39de9-185">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="39de9-186">Filtry jsou vyvolány v pořadí, v jakém byly přidány do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="39de9-186">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="39de9-187">Filtry můžou přidat middleware před nebo po předání řízení k dalšímu filtru, takže se připojí k začátku nebo konci kanálu aplikace.</span><span class="sxs-lookup"><span data-stu-id="39de9-187">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="39de9-188">Následující příklad ukazuje, jak zaregistrovat middleware pomocí `IStartupFilter`.</span><span class="sxs-lookup"><span data-stu-id="39de9-188">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="39de9-189">Middleware `RequestSetOptionsMiddleware` nastaví hodnotu možností z parametru řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="39de9-189">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

<span data-ttu-id="39de9-190">`RequestSetOptionsMiddleware` je nakonfigurována v `RequestSetOptionsStartupFilter` třídě:</span><span class="sxs-lookup"><span data-stu-id="39de9-190">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

<span data-ttu-id="39de9-191">`RequestSetOptionsMiddleware` je nakonfigurována v `RequestSetOptionsStartupFilter` třídě:</span><span class="sxs-lookup"><span data-stu-id="39de9-191">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

<span data-ttu-id="39de9-192">`IStartupFilter` je zaregistrován v kontejneru služby v <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span><span class="sxs-lookup"><span data-stu-id="39de9-192">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

::: moniker-end

<span data-ttu-id="39de9-193">Pokud je k dispozici parametr řetězce dotazu pro `option`, middleware zpracovává přiřazení hodnoty před tím, než ASP.NET Core middleware vykreslí odpověď.</span><span class="sxs-lookup"><span data-stu-id="39de9-193">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="39de9-194">Pořadí spouštění middlewaru je nastaveno podle pořadí `IStartupFilter` registrace:</span><span class="sxs-lookup"><span data-stu-id="39de9-194">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="39de9-195">Více implementací `IStartupFilter` může komunikovat se stejnými objekty.</span><span class="sxs-lookup"><span data-stu-id="39de9-195">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="39de9-196">Pokud je řazení důležité, porovnejte své `IStartupFilter` registraci služeb, aby odpovídaly pořadí, ve kterém by měly být své middleware spuštěny.</span><span class="sxs-lookup"><span data-stu-id="39de9-196">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="39de9-197">Knihovny můžou přidat middlewaru s jednou nebo více `IStartupFilter` implementacemi, které se spouštějí před nebo po jiných middlewarech aplikací registrovaných pomocí `IStartupFilter`.</span><span class="sxs-lookup"><span data-stu-id="39de9-197">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="39de9-198">Vyvolání middlewaru `IStartupFilter` před přidáním middlewaru `IStartupFilter`knihovny:</span><span class="sxs-lookup"><span data-stu-id="39de9-198">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="39de9-199">Před přidáním knihovny do kontejneru služby umístěte registraci služby.</span><span class="sxs-lookup"><span data-stu-id="39de9-199">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="39de9-200">Chcete-li provést vyvolání, umístěte registraci služby po přidání knihovny.</span><span class="sxs-lookup"><span data-stu-id="39de9-200">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="39de9-201">Přidat konfiguraci při spuštění z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="39de9-201">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="39de9-202">Implementace <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídy aplikace.</span><span class="sxs-lookup"><span data-stu-id="39de9-202">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="39de9-203">Další informace najdete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="39de9-203">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39de9-204">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="39de9-204">Additional resources</span></span>

* [<span data-ttu-id="39de9-205">Hostitel</span><span class="sxs-lookup"><span data-stu-id="39de9-205">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>
