---
title: Spuštění aplikace v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak třída Startup v ASP.NET Core konfiguruje služby a kanál žádostí aplikace.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/startup
ms.openlocfilehash: 0d1bf0dd23b7f3b99d6e9383ffc72291c1b138ec
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400710"
---
# <a name="app-startup-in-aspnet-core"></a><span data-ttu-id="ad727-103">Spuštění aplikace v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ad727-103">App startup in ASP.NET Core</span></span>

<span data-ttu-id="ad727-104">[Rick Anderson](https://twitter.com/RickAndMSFT), [Dykstra](https://github.com/tdykstra)a [Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="ad727-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="ad727-105">`Startup`Třída konfiguruje služby a kanál žádostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-105">The `Startup` class configures services and the app's request pipeline.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a><span data-ttu-id="ad727-106">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="ad727-106">The Startup class</span></span>

<span data-ttu-id="ad727-107">Aplikace ASP.NET Core používají `Startup` třídu, která je pojmenována `Startup` podle konvence.</span><span class="sxs-lookup"><span data-stu-id="ad727-107">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="ad727-108">Třída `Startup`:</span><span class="sxs-lookup"><span data-stu-id="ad727-108">The `Startup` class:</span></span>

* <span data-ttu-id="ad727-109">Volitelně zahrnuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> metodu konfigurace *služeb*aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-109">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="ad727-110">Služba je opakovaně použitelná součást, která poskytuje funkce aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-110">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="ad727-111">Služby jsou v rámci aplikace *registrovány* `ConfigureServices` a spotřebovány prostřednictvím [Injektáže závislosti (di)](xref:fundamentals/dependency-injection) nebo <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .</span><span class="sxs-lookup"><span data-stu-id="ad727-111">Services are *registered* in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="ad727-112">Obsahuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> metodu pro vytvoření kanálu zpracování požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-112">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="ad727-113">`ConfigureServices`a `Configure` jsou volány modulem runtime ASP.NET Core při spuštění aplikace:</span><span class="sxs-lookup"><span data-stu-id="ad727-113">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="ad727-114">Předchozí vzorek je pro [ Razor stránky](xref:razor-pages/index). verze MVC je podobná.</span><span class="sxs-lookup"><span data-stu-id="ad727-114">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>


<span data-ttu-id="ad727-115">`Startup`Třída je určena při sestavení [hostitele](xref:fundamentals/index#host) aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-115">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="ad727-116">`Startup`Třída je obvykle určena voláním metody [WebHostBuilderExtensions. UseStartup \<TStartup> ](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) v Tvůrci hostitele:</span><span class="sxs-lookup"><span data-stu-id="ad727-116">The `Startup` class is typically specified by calling the [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="ad727-117">Hostitel poskytuje služby, které jsou k dispozici pro `Startup` konstruktor třídy.</span><span class="sxs-lookup"><span data-stu-id="ad727-117">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="ad727-118">Aplikace přidá další služby prostřednictvím `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ad727-118">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="ad727-119">Hostitelská i Aplikační služba jsou k dispozici v `Configure` i v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ad727-119">Both the host and app services are available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="ad727-120">`Startup`Při použití [obecného hostitele](xref:fundamentals/host/generic-host) () mohou být do konstruktoru vloženy pouze následující typy služeb <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="ad727-120">Only the following service types can be injected into the `Startup` constructor when using the [Generic Host](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

<span data-ttu-id="ad727-121">Většina služeb není k dispozici, dokud `Configure` není volána metoda.</span><span class="sxs-lookup"><span data-stu-id="ad727-121">Most services are not available until the `Configure` method is called.</span></span>

### <a name="multiple-startup"></a><span data-ttu-id="ad727-122">Vícenásobné spuštění</span><span class="sxs-lookup"><span data-stu-id="ad727-122">Multiple Startup</span></span>

<span data-ttu-id="ad727-123">Když aplikace definuje samostatné `Startup` třídy pro různá prostředí (například `StartupDevelopment` ), příslušná `Startup` Třída se vybere za běhu.</span><span class="sxs-lookup"><span data-stu-id="ad727-123">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="ad727-124">Určuje prioritu třídy, jejíž přípona názvu odpovídá aktuálnímu prostředí.</span><span class="sxs-lookup"><span data-stu-id="ad727-124">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="ad727-125">Pokud je aplikace spuštěna ve vývojovém prostředí a obsahuje třídu i `Startup` `StartupDevelopment` třídu, `StartupDevelopment` je použita třída.</span><span class="sxs-lookup"><span data-stu-id="ad727-125">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="ad727-126">Další informace najdete v tématu [použití více prostředí](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span><span class="sxs-lookup"><span data-stu-id="ad727-126">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="ad727-127">Další informace o hostiteli najdete v [hostiteli](xref:fundamentals/index#host) .</span><span class="sxs-lookup"><span data-stu-id="ad727-127">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="ad727-128">Informace o zpracování chyb během spuštění najdete v tématu [zpracování výjimek při spuštění](xref:fundamentals/error-handling#startup-exception-handling).</span><span class="sxs-lookup"><span data-stu-id="ad727-128">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="ad727-129">Metoda ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="ad727-129">The ConfigureServices method</span></span>

<span data-ttu-id="ad727-130"><xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>Metoda je:</span><span class="sxs-lookup"><span data-stu-id="ad727-130">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="ad727-131">Nepovinný parametr.</span><span class="sxs-lookup"><span data-stu-id="ad727-131">Optional.</span></span>
* <span data-ttu-id="ad727-132">Volá se hostitelem před `Configure` metodou konfigurace služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-132">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="ad727-133">Kde jsou [Možnosti konfigurace](xref:fundamentals/configuration/index) nastaveny podle konvence.</span><span class="sxs-lookup"><span data-stu-id="ad727-133">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="ad727-134">Hostitel může nakonfigurovat některé služby před `Startup` voláním metod.</span><span class="sxs-lookup"><span data-stu-id="ad727-134">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="ad727-135">Další informace najdete v tématu [hostitel](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="ad727-135">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="ad727-136">Pro funkce, které vyžadují podstatnou instalaci, jsou k dispozici `Add{Service}` metody rozšíření <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> .</span><span class="sxs-lookup"><span data-stu-id="ad727-136">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="ad727-137">**Přidejte**například DbContext, **přidejte**DefaultIdentity, **přidejte**EntityFrameworkStores a **přidejte**RazorPages:</span><span class="sxs-lookup"><span data-stu-id="ad727-137">For example, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores, and **Add**RazorPages:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

<span data-ttu-id="ad727-138">Přidání služeb do kontejneru služby je zpřístupní v rámci aplikace a v `Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="ad727-138">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="ad727-139">Služby jsou vyřešeny prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection) nebo z <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .</span><span class="sxs-lookup"><span data-stu-id="ad727-139">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

## <a name="the-configure-method"></a><span data-ttu-id="ad727-140">Metoda Configure</span><span class="sxs-lookup"><span data-stu-id="ad727-140">The Configure method</span></span>

<span data-ttu-id="ad727-141"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>Metoda slouží k určení způsobu, jakým aplikace reaguje na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="ad727-141">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="ad727-142">Kanál požadavků je nakonfigurován přidáním součástí [middlewaru](xref:fundamentals/middleware/index) do <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span><span class="sxs-lookup"><span data-stu-id="ad727-142">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="ad727-143">`IApplicationBuilder`je k dispozici pro `Configure` metodu, ale není zaregistrována v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="ad727-143">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="ad727-144">Hostování vytvoří `IApplicationBuilder` a předá ho přímo do `Configure` .</span><span class="sxs-lookup"><span data-stu-id="ad727-144">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="ad727-145">[Šablony ASP.NET Core](/dotnet/core/tools/dotnet-new) konfigurují kanál s podporou pro:</span><span class="sxs-lookup"><span data-stu-id="ad727-145">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="ad727-146">Stránka s výjimkou pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="ad727-146">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="ad727-147">Obslužná rutina výjimky</span><span class="sxs-lookup"><span data-stu-id="ad727-147">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="ad727-148">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="ad727-148">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="ad727-149">Přesměrování HTTPS</span><span class="sxs-lookup"><span data-stu-id="ad727-149">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="ad727-150">Statické soubory</span><span class="sxs-lookup"><span data-stu-id="ad727-150">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="ad727-151">ASP.NET Core [MVC](xref:mvc/overview) a [ Razor stránky](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="ad727-151">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="ad727-152">Předchozí vzorek je pro [ Razor stránky](xref:razor-pages/index). verze MVC je podobná.</span><span class="sxs-lookup"><span data-stu-id="ad727-152">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

<span data-ttu-id="ad727-153">Každá `Use` metoda rozšíření přidá do kanálu žádosti jednu nebo více součástí middlewaru.</span><span class="sxs-lookup"><span data-stu-id="ad727-153">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="ad727-154">Například <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> nakonfiguruje [middleware](xref:fundamentals/middleware/index) na poskytování [statických souborů](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="ad727-154">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="ad727-155">Každá součást middlewaru v kanálu požadavků zodpovídá za vyvolání další komponenty v kanálu nebo při krátkém okruhu řetězce, pokud je to vhodné.</span><span class="sxs-lookup"><span data-stu-id="ad727-155">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

<span data-ttu-id="ad727-156">Další služby, jako například `IWebHostEnvironment` , `ILoggerFactory` nebo cokoli definované v `ConfigureServices` , lze zadat v `Configure` signatuře metody.</span><span class="sxs-lookup"><span data-stu-id="ad727-156">Additional services, such as `IWebHostEnvironment`, `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="ad727-157">Tyto služby jsou vloženy, pokud jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="ad727-157">These services are injected if they're available.</span></span>

<span data-ttu-id="ad727-158">Další informace o tom, jak používat `IApplicationBuilder` a pořadí zpracování middlewaru, najdete v tématu <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="ad727-158">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="ad727-159">Konfigurace služeb bez spuštění</span><span class="sxs-lookup"><span data-stu-id="ad727-159">Configure services without Startup</span></span>

<span data-ttu-id="ad727-160">Ke konfiguraci služeb a kanálu zpracování požadavků bez použití `Startup` třídy, volání `ConfigureServices` a `Configure` praktických metod v Tvůrci hostitele.</span><span class="sxs-lookup"><span data-stu-id="ad727-160">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="ad727-161">Několik volání, která se mají `ConfigureServices` navzájem připojit.</span><span class="sxs-lookup"><span data-stu-id="ad727-161">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="ad727-162">Pokud `Configure` existuje více volání metody, `Configure` je použito poslední volání.</span><span class="sxs-lookup"><span data-stu-id="ad727-162">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="ad727-163">Rozšířené spuštění s filtry po spuštění</span><span class="sxs-lookup"><span data-stu-id="ad727-163">Extend Startup with startup filters</span></span>

<span data-ttu-id="ad727-164">Použijte <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> :</span><span class="sxs-lookup"><span data-stu-id="ad727-164">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span></span>

* <span data-ttu-id="ad727-165">Konfigurace middlewaru na začátku nebo na konci kanálu [Konfigurace](#the-configure-method) middlewaru aplikace bez explicitního volání `Use{Middleware}` .</span><span class="sxs-lookup"><span data-stu-id="ad727-165">To configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline without an explicit call to `Use{Middleware}`.</span></span> <span data-ttu-id="ad727-166">`IStartupFilter`se používá ASP.NET Core k přidání výchozích hodnot na začátek kanálu, aniž by bylo nutné, aby autor aplikace explicitně zaregistroval výchozí middleware.</span><span class="sxs-lookup"><span data-stu-id="ad727-166">`IStartupFilter` is used by ASP.NET Core to add defaults to the beginning of the pipeline without having to make the app author explicitly register the default middleware.</span></span> <span data-ttu-id="ad727-167">`IStartupFilter`umožňuje jinému volání komponenty `Use{Middleware}` jménem autora aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-167">`IStartupFilter` allows a different component call `Use{Middleware}` on behalf of the app author.</span></span>
* <span data-ttu-id="ad727-168">Pro vytvoření kanálu `Configure` metod.</span><span class="sxs-lookup"><span data-stu-id="ad727-168">To create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="ad727-169">[IStartupFilter.Configurovat](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) může nastavit middlewaru tak, aby běžel před nebo po middlewaru, který přidaly knihovny.</span><span class="sxs-lookup"><span data-stu-id="ad727-169">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="ad727-170">`IStartupFilter`implementuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> , které přijímá a vrací `Action<IApplicationBuilder>` .</span><span class="sxs-lookup"><span data-stu-id="ad727-170">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="ad727-171"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>Definuje třídu pro konfiguraci kanálu požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-171">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="ad727-172">Další informace najdete v tématu [vytvoření kanálu middlewaru pomocí IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span><span class="sxs-lookup"><span data-stu-id="ad727-172">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="ad727-173">Každý `IStartupFilter` z nich může do kanálu požadavků přidat jeden nebo více middlewarů.</span><span class="sxs-lookup"><span data-stu-id="ad727-173">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="ad727-174">Filtry jsou vyvolány v pořadí, v jakém byly přidány do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="ad727-174">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="ad727-175">Filtry můžou přidat middleware před nebo po předání řízení k dalšímu filtru, takže se připojí k začátku nebo konci kanálu aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-175">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="ad727-176">Následující příklad ukazuje, jak registrovat middleware pomocí `IStartupFilter` .</span><span class="sxs-lookup"><span data-stu-id="ad727-176">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="ad727-177">`RequestSetOptionsMiddleware`Middleware nastaví hodnotu možností z parametru řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="ad727-177">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

<span data-ttu-id="ad727-178">`RequestSetOptionsMiddleware`Je nakonfigurován ve `RequestSetOptionsStartupFilter` třídě:</span><span class="sxs-lookup"><span data-stu-id="ad727-178">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

<span data-ttu-id="ad727-179">`IStartupFilter`Je zaregistrován v kontejneru služby v <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="ad727-179">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

<span data-ttu-id="ad727-180">Pokud je k dispozici parametr řetězce dotazu pro `option` , middleware zpracovává přiřazení hodnoty před tím, než ASP.NET Core middleware vykreslí odpověď.</span><span class="sxs-lookup"><span data-stu-id="ad727-180">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="ad727-181">Pořadí spouštění middlewaru je nastaveno podle pořadí `IStartupFilter` registrací:</span><span class="sxs-lookup"><span data-stu-id="ad727-181">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="ad727-182">Více `IStartupFilter` implementací může komunikovat se stejnými objekty.</span><span class="sxs-lookup"><span data-stu-id="ad727-182">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="ad727-183">Pokud je řazení důležité, `IStartupFilter` zajistěte, aby registrace služeb odpovídaly pořadí, ve kterém by měly být spuštěny middleware.</span><span class="sxs-lookup"><span data-stu-id="ad727-183">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="ad727-184">Knihovny můžou přidat middlewaru s jednou nebo více `IStartupFilter` implementacemi, které se spouštějí před nebo po jiných middlewarech aplikace registrované `IStartupFilter` .</span><span class="sxs-lookup"><span data-stu-id="ad727-184">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="ad727-185">Vyvolání `IStartupFilter` middlewaru před middlewarem přidaným knihovnou `IStartupFilter` :</span><span class="sxs-lookup"><span data-stu-id="ad727-185">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="ad727-186">Před přidáním knihovny do kontejneru služby umístěte registraci služby.</span><span class="sxs-lookup"><span data-stu-id="ad727-186">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="ad727-187">Chcete-li provést vyvolání, umístěte registraci služby po přidání knihovny.</span><span class="sxs-lookup"><span data-stu-id="ad727-187">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="ad727-188">Přidat konfiguraci při spuštění z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="ad727-188">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="ad727-189"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-189">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="ad727-190">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ad727-190">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ad727-191">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ad727-191">Additional resources</span></span>

* [<span data-ttu-id="ad727-192">Hostitel</span><span class="sxs-lookup"><span data-stu-id="ad727-192">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a><span data-ttu-id="ad727-193">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="ad727-193">The Startup class</span></span>

<span data-ttu-id="ad727-194">Aplikace ASP.NET Core používají `Startup` třídu, která je pojmenována `Startup` podle konvence.</span><span class="sxs-lookup"><span data-stu-id="ad727-194">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="ad727-195">Třída `Startup`:</span><span class="sxs-lookup"><span data-stu-id="ad727-195">The `Startup` class:</span></span>

* <span data-ttu-id="ad727-196">Volitelně zahrnuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> metodu konfigurace *služeb*aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-196">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="ad727-197">Služba je opakovaně použitelná součást, která poskytuje funkce aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-197">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="ad727-198">Služby jsou v rámci aplikace *registrovány* `ConfigureServices` a spotřebovány prostřednictvím [Injektáže závislosti (di)](xref:fundamentals/dependency-injection) nebo <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .</span><span class="sxs-lookup"><span data-stu-id="ad727-198">Services are *registered* in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="ad727-199">Obsahuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> metodu pro vytvoření kanálu zpracování požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-199">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="ad727-200">`ConfigureServices`a `Configure` jsou volány modulem runtime ASP.NET Core při spuštění aplikace:</span><span class="sxs-lookup"><span data-stu-id="ad727-200">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

<span data-ttu-id="ad727-201">`Startup`Třída je určena při sestavení [hostitele](xref:fundamentals/index#host) aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-201">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="ad727-202">`Startup`Třída je obvykle určena voláním metody [WebHostBuilderExtensions. UseStartup \<TStartup> ](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) v Tvůrci hostitele:</span><span class="sxs-lookup"><span data-stu-id="ad727-202">The `Startup` class is typically specified by calling the [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="ad727-203">Hostitel poskytuje služby, které jsou k dispozici pro `Startup` konstruktor třídy.</span><span class="sxs-lookup"><span data-stu-id="ad727-203">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="ad727-204">Aplikace přidá další služby prostřednictvím `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ad727-204">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="ad727-205">Hostitelská i Aplikační služba jsou pak k dispozici v `Configure` a v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ad727-205">Both the host and app services are then available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="ad727-206">Běžné použití [Injektáže závislostí](xref:fundamentals/dependency-injection) do `Startup` třídy je vložení:</span><span class="sxs-lookup"><span data-stu-id="ad727-206">A common use of [dependency injection](xref:fundamentals/dependency-injection) into the `Startup` class is to inject:</span></span>

* <span data-ttu-id="ad727-207"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>ke konfiguraci služeb podle prostředí.</span><span class="sxs-lookup"><span data-stu-id="ad727-207"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> to configure services by environment.</span></span>
* <span data-ttu-id="ad727-208"><xref:Microsoft.Extensions.Configuration.IConfiguration>pro čtení konfigurace.</span><span class="sxs-lookup"><span data-stu-id="ad727-208"><xref:Microsoft.Extensions.Configuration.IConfiguration> to read configuration.</span></span>
* <span data-ttu-id="ad727-209"><xref:Microsoft.Extensions.Logging.ILoggerFactory>Vytvoření protokolovacího nástroje v nástroji `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ad727-209"><xref:Microsoft.Extensions.Logging.ILoggerFactory> to create a logger in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

<span data-ttu-id="ad727-210">Většina služeb není k dispozici, dokud `Configure` není volána metoda.</span><span class="sxs-lookup"><span data-stu-id="ad727-210">Most services are not available until the `Configure` method is called.</span></span>

### <a name="multiple-startup"></a><span data-ttu-id="ad727-211">Vícenásobné spuštění</span><span class="sxs-lookup"><span data-stu-id="ad727-211">Multiple Startup</span></span>

<span data-ttu-id="ad727-212">Když aplikace definuje samostatné `Startup` třídy pro různá prostředí (například `StartupDevelopment` ), příslušná `Startup` Třída se vybere za běhu.</span><span class="sxs-lookup"><span data-stu-id="ad727-212">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="ad727-213">Určuje prioritu třídy, jejíž přípona názvu odpovídá aktuálnímu prostředí.</span><span class="sxs-lookup"><span data-stu-id="ad727-213">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="ad727-214">Pokud je aplikace spuštěna ve vývojovém prostředí a obsahuje třídu i `Startup` `StartupDevelopment` třídu, `StartupDevelopment` je použita třída.</span><span class="sxs-lookup"><span data-stu-id="ad727-214">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="ad727-215">Další informace najdete v tématu [použití více prostředí](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span><span class="sxs-lookup"><span data-stu-id="ad727-215">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="ad727-216">Další informace o hostiteli najdete v [hostiteli](xref:fundamentals/index#host) .</span><span class="sxs-lookup"><span data-stu-id="ad727-216">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="ad727-217">Informace o zpracování chyb během spuštění najdete v tématu [zpracování výjimek při spuštění](xref:fundamentals/error-handling#startup-exception-handling).</span><span class="sxs-lookup"><span data-stu-id="ad727-217">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="ad727-218">Metoda ConfigureServices</span><span class="sxs-lookup"><span data-stu-id="ad727-218">The ConfigureServices method</span></span>

<span data-ttu-id="ad727-219"><xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>Metoda je:</span><span class="sxs-lookup"><span data-stu-id="ad727-219">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="ad727-220">Nepovinný parametr.</span><span class="sxs-lookup"><span data-stu-id="ad727-220">Optional.</span></span>
* <span data-ttu-id="ad727-221">Volá se hostitelem před `Configure` metodou konfigurace služeb aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-221">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="ad727-222">Kde jsou [Možnosti konfigurace](xref:fundamentals/configuration/index) nastaveny podle konvence.</span><span class="sxs-lookup"><span data-stu-id="ad727-222">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="ad727-223">Hostitel může nakonfigurovat některé služby před `Startup` voláním metod.</span><span class="sxs-lookup"><span data-stu-id="ad727-223">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="ad727-224">Další informace najdete v tématu [hostitel](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="ad727-224">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="ad727-225">Pro funkce, které vyžadují podstatnou instalaci, jsou k dispozici `Add{Service}` metody rozšíření <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> .</span><span class="sxs-lookup"><span data-stu-id="ad727-225">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="ad727-226">**Přidejte**například DbContext, **přidejte**DefaultIdentity, **přidejte**EntityFrameworkStores a **přidejte**RazorPages:</span><span class="sxs-lookup"><span data-stu-id="ad727-226">For example, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores, and **Add**RazorPages:</span></span>

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

<span data-ttu-id="ad727-227">Přidání služeb do kontejneru služby je zpřístupní v rámci aplikace a v `Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="ad727-227">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="ad727-228">Služby jsou vyřešeny prostřednictvím [Injektáže závislosti](xref:fundamentals/dependency-injection) nebo z <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .</span><span class="sxs-lookup"><span data-stu-id="ad727-228">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

<span data-ttu-id="ad727-229">Další [SetCompatibilityVersion](xref:mvc/compatibility-version) informace najdete v tématu SetCompatibilityVersion `SetCompatibilityVersion` .</span><span class="sxs-lookup"><span data-stu-id="ad727-229">See [SetCompatibilityVersion](xref:mvc/compatibility-version) for more information on `SetCompatibilityVersion`.</span></span>

## <a name="the-configure-method"></a><span data-ttu-id="ad727-230">Metoda Configure</span><span class="sxs-lookup"><span data-stu-id="ad727-230">The Configure method</span></span>

<span data-ttu-id="ad727-231"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>Metoda slouží k určení způsobu, jakým aplikace reaguje na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="ad727-231">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="ad727-232">Kanál požadavků je nakonfigurován přidáním součástí [middlewaru](xref:fundamentals/middleware/index) do <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span><span class="sxs-lookup"><span data-stu-id="ad727-232">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="ad727-233">`IApplicationBuilder`je k dispozici pro `Configure` metodu, ale není zaregistrována v kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="ad727-233">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="ad727-234">Hostování vytvoří `IApplicationBuilder` a předá ho přímo do `Configure` .</span><span class="sxs-lookup"><span data-stu-id="ad727-234">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="ad727-235">[Šablony ASP.NET Core](/dotnet/core/tools/dotnet-new) konfigurují kanál s podporou pro:</span><span class="sxs-lookup"><span data-stu-id="ad727-235">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="ad727-236">Stránka s výjimkou pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="ad727-236">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="ad727-237">Obslužná rutina výjimky</span><span class="sxs-lookup"><span data-stu-id="ad727-237">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="ad727-238">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="ad727-238">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="ad727-239">Přesměrování HTTPS</span><span class="sxs-lookup"><span data-stu-id="ad727-239">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="ad727-240">Statické soubory</span><span class="sxs-lookup"><span data-stu-id="ad727-240">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="ad727-241">ASP.NET Core [MVC](xref:mvc/overview) a [ Razor stránky](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="ad727-241">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>
* [<span data-ttu-id="ad727-242">Obecné nařízení o ochraně osobních údajů (GDPR)</span><span class="sxs-lookup"><span data-stu-id="ad727-242">General Data Protection Regulation (GDPR)</span></span>](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

<span data-ttu-id="ad727-243">Každá `Use` metoda rozšíření přidá do kanálu žádosti jednu nebo více součástí middlewaru.</span><span class="sxs-lookup"><span data-stu-id="ad727-243">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="ad727-244">Například <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> nakonfiguruje [middleware](xref:fundamentals/middleware/index) na poskytování [statických souborů](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="ad727-244">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="ad727-245">Každá součást middlewaru v kanálu požadavků zodpovídá za vyvolání další komponenty v kanálu nebo při krátkém okruhu řetězce, pokud je to vhodné.</span><span class="sxs-lookup"><span data-stu-id="ad727-245">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

<span data-ttu-id="ad727-246">Další služby, například `IHostingEnvironment` a `ILoggerFactory` , nebo cokoli definované v `ConfigureServices` , lze zadat v `Configure` signatuře metody.</span><span class="sxs-lookup"><span data-stu-id="ad727-246">Additional services, such as `IHostingEnvironment` and `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="ad727-247">Tyto služby jsou vloženy, pokud jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="ad727-247">These services are injected if they're available.</span></span>

<span data-ttu-id="ad727-248">Další informace o tom, jak používat `IApplicationBuilder` a pořadí zpracování middlewaru, najdete v tématu <xref:fundamentals/middleware/index> .</span><span class="sxs-lookup"><span data-stu-id="ad727-248">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="ad727-249">Konfigurace služeb bez spuštění</span><span class="sxs-lookup"><span data-stu-id="ad727-249">Configure services without Startup</span></span>

<span data-ttu-id="ad727-250">Ke konfiguraci služeb a kanálu zpracování požadavků bez použití `Startup` třídy, volání `ConfigureServices` a `Configure` praktických metod v Tvůrci hostitele.</span><span class="sxs-lookup"><span data-stu-id="ad727-250">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="ad727-251">Několik volání, která se mají `ConfigureServices` navzájem připojit.</span><span class="sxs-lookup"><span data-stu-id="ad727-251">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="ad727-252">Pokud `Configure` existuje více volání metody, `Configure` je použito poslední volání.</span><span class="sxs-lookup"><span data-stu-id="ad727-252">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="ad727-253">Rozšířené spuštění s filtry po spuštění</span><span class="sxs-lookup"><span data-stu-id="ad727-253">Extend Startup with startup filters</span></span>

<span data-ttu-id="ad727-254">Použijte <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> :</span><span class="sxs-lookup"><span data-stu-id="ad727-254">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span></span>

* <span data-ttu-id="ad727-255">Konfigurace middlewaru na začátku nebo na konci kanálu [Konfigurace](#the-configure-method) middlewaru aplikace bez explicitního volání `Use{Middleware}` .</span><span class="sxs-lookup"><span data-stu-id="ad727-255">To configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline without an explicit call to `Use{Middleware}`.</span></span> <span data-ttu-id="ad727-256">`IStartupFilter`se používá ASP.NET Core k přidání výchozích hodnot na začátek kanálu, aniž by bylo nutné, aby autor aplikace explicitně zaregistroval výchozí middleware.</span><span class="sxs-lookup"><span data-stu-id="ad727-256">`IStartupFilter` is used by ASP.NET Core to add defaults to the beginning of the pipeline without having to make the app author explicitly register the default middleware.</span></span> <span data-ttu-id="ad727-257">`IStartupFilter`umožňuje jinému volání komponenty `Use{Middleware}` jménem autora aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-257">`IStartupFilter` allows a different component call `Use{Middleware}` on behalf of the app author.</span></span>
* <span data-ttu-id="ad727-258">Pro vytvoření kanálu `Configure` metod.</span><span class="sxs-lookup"><span data-stu-id="ad727-258">To create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="ad727-259">[IStartupFilter.Configurovat](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) může nastavit middlewaru tak, aby běžel před nebo po middlewaru, který přidaly knihovny.</span><span class="sxs-lookup"><span data-stu-id="ad727-259">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="ad727-260">`IStartupFilter`implementuje <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> , které přijímá a vrací `Action<IApplicationBuilder>` .</span><span class="sxs-lookup"><span data-stu-id="ad727-260">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="ad727-261"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>Definuje třídu pro konfiguraci kanálu požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-261">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="ad727-262">Další informace najdete v tématu [vytvoření kanálu middlewaru pomocí IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span><span class="sxs-lookup"><span data-stu-id="ad727-262">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="ad727-263">Každý `IStartupFilter` z nich může do kanálu požadavků přidat jeden nebo více middlewarů.</span><span class="sxs-lookup"><span data-stu-id="ad727-263">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="ad727-264">Filtry jsou vyvolány v pořadí, v jakém byly přidány do kontejneru služby.</span><span class="sxs-lookup"><span data-stu-id="ad727-264">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="ad727-265">Filtry můžou přidat middleware před nebo po předání řízení k dalšímu filtru, takže se připojí k začátku nebo konci kanálu aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-265">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="ad727-266">Následující příklad ukazuje, jak registrovat middleware pomocí `IStartupFilter` .</span><span class="sxs-lookup"><span data-stu-id="ad727-266">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="ad727-267">`RequestSetOptionsMiddleware`Middleware nastaví hodnotu možností z parametru řetězce dotazu:</span><span class="sxs-lookup"><span data-stu-id="ad727-267">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

<span data-ttu-id="ad727-268">`RequestSetOptionsMiddleware`Je nakonfigurován ve `RequestSetOptionsStartupFilter` třídě:</span><span class="sxs-lookup"><span data-stu-id="ad727-268">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

<span data-ttu-id="ad727-269">`IStartupFilter`Je zaregistrován v kontejneru služby v <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="ad727-269">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

<span data-ttu-id="ad727-270">Pokud je k dispozici parametr řetězce dotazu pro `option` , middleware zpracovává přiřazení hodnoty před tím, než ASP.NET Core middleware vykreslí odpověď.</span><span class="sxs-lookup"><span data-stu-id="ad727-270">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="ad727-271">Pořadí spouštění middlewaru je nastaveno podle pořadí `IStartupFilter` registrací:</span><span class="sxs-lookup"><span data-stu-id="ad727-271">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="ad727-272">Více `IStartupFilter` implementací může komunikovat se stejnými objekty.</span><span class="sxs-lookup"><span data-stu-id="ad727-272">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="ad727-273">Pokud je řazení důležité, `IStartupFilter` zajistěte, aby registrace služeb odpovídaly pořadí, ve kterém by měly být spuštěny middleware.</span><span class="sxs-lookup"><span data-stu-id="ad727-273">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="ad727-274">Knihovny můžou přidat middlewaru s jednou nebo více `IStartupFilter` implementacemi, které se spouštějí před nebo po jiných middlewarech aplikace registrované `IStartupFilter` .</span><span class="sxs-lookup"><span data-stu-id="ad727-274">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="ad727-275">Vyvolání `IStartupFilter` middlewaru před middlewarem přidaným knihovnou `IStartupFilter` :</span><span class="sxs-lookup"><span data-stu-id="ad727-275">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="ad727-276">Před přidáním knihovny do kontejneru služby umístěte registraci služby.</span><span class="sxs-lookup"><span data-stu-id="ad727-276">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="ad727-277">Chcete-li provést vyvolání, umístěte registraci služby po přidání knihovny.</span><span class="sxs-lookup"><span data-stu-id="ad727-277">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="ad727-278">Přidat konfiguraci při spuštění z externího sestavení</span><span class="sxs-lookup"><span data-stu-id="ad727-278">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="ad727-279"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementace umožňuje přidání vylepšení aplikace při spuštění z externího sestavení mimo `Startup` třídu aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad727-279">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="ad727-280">Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="ad727-280">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ad727-281">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ad727-281">Additional resources</span></span>

* [<span data-ttu-id="ad727-282">Hostitel</span><span class="sxs-lookup"><span data-stu-id="ad727-282">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end