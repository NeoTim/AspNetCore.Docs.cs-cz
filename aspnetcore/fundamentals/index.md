---
title: Základy ASP.NET Core
author: rick-anderson
description: Seznamte se základními koncepty pro vytváření aplikací ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 01/06/2019
uid: fundamentals/index
ms.openlocfilehash: a56beebd796448705c7b84f47699e9739f451419
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099231"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="abfaa-103">Základy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="abfaa-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="abfaa-104">Aplikace ASP.NET Core je konzolová aplikace, která vytvoří webovým serverem v jeho `Program.Main` metoda.</span><span class="sxs-lookup"><span data-stu-id="abfaa-104">An ASP.NET Core app is a console app that creates a web server in its `Program.Main` method.</span></span> <span data-ttu-id="abfaa-105">`Main` Aplikace je metoda *spravované vstupní bod*:</span><span class="sxs-lookup"><span data-stu-id="abfaa-105">The `Main` method is the app's *managed entry point*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/snapshots/2.x/Program.cs)]

<span data-ttu-id="abfaa-106">.NET Core hostitele:</span><span class="sxs-lookup"><span data-stu-id="abfaa-106">The .NET Core Host:</span></span>

* <span data-ttu-id="abfaa-107">Načtení [.NET Core runtime](https://github.com/dotnet/coreclr).</span><span class="sxs-lookup"><span data-stu-id="abfaa-107">Loads the [.NET Core runtime](https://github.com/dotnet/coreclr).</span></span>
* <span data-ttu-id="abfaa-108">První argument příkazového řádku používá jako cestu pro spravované binární soubor, který obsahuje vstupní bod (`Main`) a zahájí provádění kódu.</span><span class="sxs-lookup"><span data-stu-id="abfaa-108">Uses the first command-line argument as the path to the managed binary that contains the entry point (`Main`) and begins code execution.</span></span>

<span data-ttu-id="abfaa-109">V metodě `Main` se volá metoda [WebHost.CreateDefaultBuilder](xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*), která poskytuje implementaci návrhového vzoru [Builder](https://wikipedia.org/wiki/Builder_pattern) a umožňuje tak sestavení hostitele webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="abfaa-109">The `Main` method invokes [WebHost.CreateDefaultBuilder](xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*), which follows the [builder pattern](https://wikipedia.org/wiki/Builder_pattern) to create a web host.</span></span> <span data-ttu-id="abfaa-110">Tvůrce obsahuje metody, které definují webového serveru (například <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>) a třída při spuštění (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>).</span><span class="sxs-lookup"><span data-stu-id="abfaa-110">The builder has methods that define a web server (for example, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>) and the startup class (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>).</span></span> <span data-ttu-id="abfaa-111">Ve výše uvedeném příkladu je automaticky použit webový server [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="abfaa-111">In the preceding example, the [Kestrel](xref:fundamentals/servers/kestrel) web server is automatically allocated.</span></span> <span data-ttu-id="abfaa-112">Webového hostitele ASP.NET Core se pokusí o spuštění [Internetové informační služby (IIS)](https://www.iis.net/), pokud je k dispozici.</span><span class="sxs-lookup"><span data-stu-id="abfaa-112">ASP.NET Core's web host attempts to run on [Internet Information Services (IIS)](https://www.iis.net/), if available.</span></span> <span data-ttu-id="abfaa-113">Voláním patřičné rozšiřující metody je možné aplikaci hostovat i na jiných webových serverech, jako například [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="abfaa-113">Other web servers, such as [HTTP.sys](xref:fundamentals/servers/httpsys), can be used by invoking the appropriate extension method.</span></span> <span data-ttu-id="abfaa-114">`UseStartup` je vysvětleno dále v [spuštění](#startup) oddílu.</span><span class="sxs-lookup"><span data-stu-id="abfaa-114">`UseStartup` is explained further in the [Startup](#startup) section.</span></span>

<span data-ttu-id="abfaa-115">Typ <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, který je vrácen jako výsledek volání `WebHost.CreateDefaultBuilder`, obsahuje mnoho volitelných metod.</span><span class="sxs-lookup"><span data-stu-id="abfaa-115"><xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, the return type of the `WebHost.CreateDefaultBuilder` invocation, provides many optional methods.</span></span> <span data-ttu-id="abfaa-116">Jednou z těchto metod je `UseHttpSys` pro hostování aplikace pomocí HTTP.sys a <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> pro určení kořenového adresáře s obsahem.</span><span class="sxs-lookup"><span data-stu-id="abfaa-116">Some of these methods include `UseHttpSys` for hosting the app in HTTP.sys and <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> for specifying the root content directory.</span></span> <span data-ttu-id="abfaa-117">Metody <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> a <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> slouží k sestavení objektu <xref:Microsoft.AspNetCore.Hosting.IWebHost>, který je hostitelem aplikace, resp. zahájí naslouchání HTTP požadavků.</span><span class="sxs-lookup"><span data-stu-id="abfaa-117">The <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> and <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> methods build the <xref:Microsoft.AspNetCore.Hosting.IWebHost> object that hosts the app and begins listening for HTTP requests.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/snapshots/1.x/Program.cs)]

<span data-ttu-id="abfaa-118">.NET Core hostitele:</span><span class="sxs-lookup"><span data-stu-id="abfaa-118">The .NET Core Host:</span></span>

* <span data-ttu-id="abfaa-119">Načtení [.NET Core runtime](https://github.com/dotnet/coreclr).</span><span class="sxs-lookup"><span data-stu-id="abfaa-119">Loads the [.NET Core runtime](https://github.com/dotnet/coreclr).</span></span>
* <span data-ttu-id="abfaa-120">První argument příkazového řádku používá jako cestu pro spravované binární soubor, který obsahuje vstupní bod (`Main`) a zahájí provádění kódu.</span><span class="sxs-lookup"><span data-stu-id="abfaa-120">Uses the first command-line argument as the path to the managed binary that contains the entry point (`Main`) and begins code execution.</span></span>

<span data-ttu-id="abfaa-121">V metodě `Main` se používá třída <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>, která implementuje návrhový vzor [Builder](https://wikipedia.org/wiki/Builder_pattern) a umožňuje tak sestavení hostitele webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="abfaa-121">The `Main` method uses <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>, which follows the [builder pattern](https://wikipedia.org/wiki/Builder_pattern) to create a web app host.</span></span> <span data-ttu-id="abfaa-122">Samotný Builder obsahuje metody, které specifikují webový server (například <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>), který se má použít, a třídu pro spuštění (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>).</span><span class="sxs-lookup"><span data-stu-id="abfaa-122">The builder has methods that define the web server (for example, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>) and the startup class (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>).</span></span> <span data-ttu-id="abfaa-123">Ve výše uvedeném příkladu je použit webový server [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="abfaa-123">In the preceding example, the [Kestrel](xref:fundamentals/servers/kestrel) web server is used.</span></span> <span data-ttu-id="abfaa-124">Voláním patřičné rozšiřující metody je možné aplikaci hostovat i na jiných webových serverech, jako například [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="abfaa-124">Other web servers, such as [HTTP.sys](xref:fundamentals/servers/httpsys), can be used by invoking the appropriate extension method.</span></span> <span data-ttu-id="abfaa-125">`UseStartup` je vysvětleno dále v [spuštění](#startup) oddílu.</span><span class="sxs-lookup"><span data-stu-id="abfaa-125">`UseStartup` is explained further in the [Startup](#startup) section.</span></span>

<span data-ttu-id="abfaa-126">Třída `WebHostBuilder` obsahuje mnoho volitelných metod. Jednou z těchto metod je <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> pro hostování aplikace pomocí služby IIS nebo IIS Express a <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> pro určení kořenového adresáře s obsahem.</span><span class="sxs-lookup"><span data-stu-id="abfaa-126">`WebHostBuilder` provides many optional methods, including <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> for hosting in IIS and IIS Express and <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> for specifying the root content directory.</span></span> <span data-ttu-id="abfaa-127">Metody <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> a <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> slouží k sestavení objektu <xref:Microsoft.AspNetCore.Hosting.IWebHost>, který je hostitelem aplikace, resp. zahájí naslouchání HTTP požadavků.</span><span class="sxs-lookup"><span data-stu-id="abfaa-127">The <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> and <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> methods build the <xref:Microsoft.AspNetCore.Hosting.IWebHost> object that hosts the app and begins listening for HTTP requests.</span></span>

::: moniker-end

## <a name="startup"></a><span data-ttu-id="abfaa-128">Třída pro spuštění</span><span class="sxs-lookup"><span data-stu-id="abfaa-128">Startup</span></span>

<span data-ttu-id="abfaa-129">Metoda `UseStartup` třídy `WebHostBuilder` určuje spouštěcí třídu `Startup` Vaší aplikace:</span><span class="sxs-lookup"><span data-stu-id="abfaa-129">The `UseStartup` method on `WebHostBuilder` specifies the `Startup` class for your app:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/snapshots/2.x/Program.cs?highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/snapshots/1.x/Program.cs?highlight=7)]

::: moniker-end

<span data-ttu-id="abfaa-130">`Startup` Je třída, ve kterém jsou nakonfigurované všechny služby nezbytné aplikace a je definován kanál zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="abfaa-130">The `Startup` class is where any services required by the app are configured and the request handling pipeline is defined.</span></span> <span data-ttu-id="abfaa-131">`Startup` Třída musí být veřejné a obvykle obsahuje následující metody.</span><span class="sxs-lookup"><span data-stu-id="abfaa-131">The `Startup` class must be public and usually contains the following methods.</span></span> <span data-ttu-id="abfaa-132">`Startup.ConfigureServices` je volitelný.</span><span class="sxs-lookup"><span data-stu-id="abfaa-132">`Startup.ConfigureServices` is optional.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/snapshots/2.x/Startup.cs)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/snapshots/1.x/Startup.cs)]

::: moniker-end

<span data-ttu-id="abfaa-133"><xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> definuje [služby](#dependency-injection-services) používané v aplikaci (například ASP.NET, Core MVC, Entity Framework Core, Identity).</span><span class="sxs-lookup"><span data-stu-id="abfaa-133"><xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> defines the [Services](#dependency-injection-services) used by your app (for example, ASP.NET Core MVC, Entity Framework Core, Identity).</span></span> <span data-ttu-id="abfaa-134"><xref:Microsoft.AspNetCore.Hosting.IStartup.Configure*> definuje [middlewary](xref:fundamentals/middleware/index) pro kanál zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="abfaa-134"><xref:Microsoft.AspNetCore.Hosting.IStartup.Configure*> defines the [middleware](xref:fundamentals/middleware/index) called in the request pipeline.</span></span>

<span data-ttu-id="abfaa-135">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="abfaa-135">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="content-root"></a><span data-ttu-id="abfaa-136">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="abfaa-136">Content root</span></span>

<span data-ttu-id="abfaa-137">Kořen obsahu (Content Root) je bázová cesta k libovolnému obsahu využívaného aplikací, jako jsou například pohledy, [Stránky Razor](xref:razor-pages/index) a statický obsah (obrázky apod.).</span><span class="sxs-lookup"><span data-stu-id="abfaa-137">The content root is the base path to any content used by the app, such as [Razor Pages](xref:razor-pages/index), MVC views, and static assets.</span></span> <span data-ttu-id="abfaa-138">Ve výchozím nastavení je kořenu obsahu stejný jako bázová cesta aplikace ke spustitelnému souboru, který je hostitelem aplikace.</span><span class="sxs-lookup"><span data-stu-id="abfaa-138">By default, the content root is the same location as the app base path for the executable hosting the app.</span></span>

## <a name="web-root-webroot"></a><span data-ttu-id="abfaa-139">Kořenový adresář webové (webroot)</span><span class="sxs-lookup"><span data-stu-id="abfaa-139">Web root (webroot)</span></span>

<span data-ttu-id="abfaa-140">Webroot aplikace je adresář, do projektu obsahující veřejné, statické prostředky, jako jsou šablony stylů CSS, JavaScript a soubory obrázků.</span><span class="sxs-lookup"><span data-stu-id="abfaa-140">The webroot of an app is the directory in the project containing public, static resources, such as CSS, JavaScript, and image files.</span></span> <span data-ttu-id="abfaa-141">Ve výchozím nastavení *wwwroot* je webroot.</span><span class="sxs-lookup"><span data-stu-id="abfaa-141">By default, *wwwroot* is the webroot.</span></span>

<span data-ttu-id="abfaa-142">Pro syntaxi Razor (*.cshtml*) soubory tilda lomítky `~/` odkazuje webroot.</span><span class="sxs-lookup"><span data-stu-id="abfaa-142">For Razor (*.cshtml*) files, the tilde-slash  `~/` points to the webroot.</span></span> <span data-ttu-id="abfaa-143">Cesty začínající `~/` se označují jako virtuální cesty.</span><span class="sxs-lookup"><span data-stu-id="abfaa-143">Paths beginning with `~/` are referred to as virtual paths.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="abfaa-144">Vkládání závislostí (služby)</span><span class="sxs-lookup"><span data-stu-id="abfaa-144">Dependency injection (services)</span></span>

<span data-ttu-id="abfaa-145">*Služba* je komponenta, která je určená pro společné používání v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="abfaa-145">A *service* is a component that's intended for common consumption in an app.</span></span> <span data-ttu-id="abfaa-146">Služby jsou zpřístupněny prostřednictvím [vkládání závislosti](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="abfaa-146">Services are made available through [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="abfaa-147">ASP.NET Core zahrnuje nativní kontejner pro inverzi závislostí, který podporuje [vkládání závislostí pomocí konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection).</span><span class="sxs-lookup"><span data-stu-id="abfaa-147">ASP.NET Core includes a native Inversion of Control (IoC) container that supports [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) by default.</span></span> <span data-ttu-id="abfaa-148">Nativní kontejner je možné nahradit jiným.</span><span class="sxs-lookup"><span data-stu-id="abfaa-148">You can replace the default container if you wish.</span></span> <span data-ttu-id="abfaa-149">Výhoda použití vkládání závislostí spočívá ve [volnějším propojení jednotlivých komponent](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) a zpřístupnění služeb (jako je třeba [protokolování](xref:fundamentals/logging/index)) všude v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="abfaa-149">In addition to its [loose coupling benefit](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation), DI makes services, such as [logging](xref:fundamentals/logging/index), available throughout your app.</span></span>

<span data-ttu-id="abfaa-150">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="abfaa-150">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="abfaa-151">Middleware</span><span class="sxs-lookup"><span data-stu-id="abfaa-151">Middleware</span></span>

<span data-ttu-id="abfaa-152">V ASP.NET Core budujete kanál zpracování požadavků pomocí [middlewarů](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="abfaa-152">In ASP.NET Core, you compose your request pipeline using [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="abfaa-153">ASP.NET Core middleware nejprve provádí asynchronní operace nad kontextem `HttpContext`, a následně může předat řízení dalšímu middlewaru v pořadí nebo okamžitě ukončit zpracování HTTP požadavku.</span><span class="sxs-lookup"><span data-stu-id="abfaa-153">ASP.NET Core middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="abfaa-154">Middlewarová komponenta s názvem "XYZ" se přidá pomocí volání rozšiřující metody `UseXYZ` uvnitř metody `Configure`.</span><span class="sxs-lookup"><span data-stu-id="abfaa-154">By convention, a middleware component called "XYZ" is added to the pipeline by invoking a `UseXYZ` extension method in the `Configure` method.</span></span>

<span data-ttu-id="abfaa-155">ASP.NET Core obsahuje pestrý výběr vestavěných middlewarů, nebo můžete použít vlastní middleware.</span><span class="sxs-lookup"><span data-stu-id="abfaa-155">ASP.NET Core includes a rich set of built-in middleware, and you can write your own custom middleware.</span></span> <span data-ttu-id="abfaa-156">[Otevřete Web Interface pro .NET (OWIN)](xref:fundamentals/owin), který umožňuje webové aplikace k oddělení od webové servery, je podporováno v aplikacích pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="abfaa-156">[Open Web Interface for .NET (OWIN)](xref:fundamentals/owin), which allows web apps to be decoupled from web servers, is supported in ASP.NET Core apps.</span></span>

<span data-ttu-id="abfaa-157">Další informace naleznete v tématu <xref:fundamentals/middleware/index> a <xref:fundamentals/owin>.</span><span class="sxs-lookup"><span data-stu-id="abfaa-157">For more information, see <xref:fundamentals/middleware/index> and <xref:fundamentals/owin>.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="initiate-http-requests"></a><span data-ttu-id="abfaa-158">Iniciování HTTP požadavků</span><span class="sxs-lookup"><span data-stu-id="abfaa-158">Initiate HTTP requests</span></span>

<span data-ttu-id="abfaa-159"><xref:System.Net.Http.IHttpClientFactory> je k dispozici pro přístup k <xref:System.Net.Http.HttpClient> instance požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="abfaa-159"><xref:System.Net.Http.IHttpClientFactory> is available to access <xref:System.Net.Http.HttpClient> instances to make HTTP requests.</span></span>

<span data-ttu-id="abfaa-160">Další informace naleznete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="abfaa-160">For more information, see <xref:fundamentals/http-requests>.</span></span>

::: moniker-end

## <a name="environments"></a><span data-ttu-id="abfaa-161">Prostředí</span><span class="sxs-lookup"><span data-stu-id="abfaa-161">Environments</span></span>

<span data-ttu-id="abfaa-162">ASP.NET Core umožňuje rozlišení vývojového *Development* a produkčního *Production* prostředí, konkrétní prostředí lze nastavit pomocí proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="abfaa-162">Environments, such as *Development* and *Production*, are a first-class notion in ASP.NET Core and can be set using an environment variable, settings file, and command-line argument.</span></span>

<span data-ttu-id="abfaa-163">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="abfaa-163">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="hosting"></a><span data-ttu-id="abfaa-164">Hostování</span><span class="sxs-lookup"><span data-stu-id="abfaa-164">Hosting</span></span>

<span data-ttu-id="abfaa-165">Aplikace ASP.NET Core konfiguruje a spouští *hostitele*, který je zodpovědný za správu životního cyklu aplikace.</span><span class="sxs-lookup"><span data-stu-id="abfaa-165">ASP.NET Core apps configure and launch a *host*, which is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="abfaa-166">Další informace naleznete v tématu <xref:fundamentals/host/index>.</span><span class="sxs-lookup"><span data-stu-id="abfaa-166">For more information, see <xref:fundamentals/host/index>.</span></span>

## <a name="servers"></a><span data-ttu-id="abfaa-167">Servery</span><span class="sxs-lookup"><span data-stu-id="abfaa-167">Servers</span></span>

<span data-ttu-id="abfaa-168">ASP.NET Core nenaslouchá přímo HTTP požadavkům.</span><span class="sxs-lookup"><span data-stu-id="abfaa-168">The ASP.NET Core hosting model doesn't directly listen for requests.</span></span> <span data-ttu-id="abfaa-169">Spoléhá na HTTP server, který aplikaci předává jednotlivé požadavky.</span><span class="sxs-lookup"><span data-stu-id="abfaa-169">The hosting model relies on an HTTP server implementation to forward the request to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="abfaa-170">Windows</span><span class="sxs-lookup"><span data-stu-id="abfaa-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="abfaa-171">ASP.NET Core nabízí následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="abfaa-171">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="abfaa-172">[Kestrel](xref:fundamentals/servers/kestrel) serveru je spravovaná, napříč platformami webový server.</span><span class="sxs-lookup"><span data-stu-id="abfaa-172">[Kestrel](xref:fundamentals/servers/kestrel) server is a managed, cross-platform web server.</span></span> <span data-ttu-id="abfaa-173">Pomocí konfigurace reverzního proxy serveru je často spustit kestrel [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="abfaa-173">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="abfaa-174">Kestrel můžete také spustit jako veřejnou hraniční server přístup přímo k Internetu v ASP.NET Core 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="abfaa-174">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="abfaa-175">Server služby IIS protokolu HTTP (`IISHttpServer`) je [v procesový server](xref:fundamentals/servers/index#in-process-hosting-model) pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="abfaa-175">IIS HTTP Server (`IISHttpServer`) is an [in-process server](xref:fundamentals/servers/index#in-process-hosting-model) for IIS.</span></span>
* <span data-ttu-id="abfaa-176">[Ovladač HTTP.sys](xref:fundamentals/servers/httpsys) server je webový server pro ASP.NET Core ve Windows.</span><span class="sxs-lookup"><span data-stu-id="abfaa-176">[HTTP.sys](xref:fundamentals/servers/httpsys) server is a web server for ASP.NET Core on Windows.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="abfaa-177">macOS</span><span class="sxs-lookup"><span data-stu-id="abfaa-177">macOS</span></span>](#tab/macos)

<span data-ttu-id="abfaa-178">ASP.NET Core využívá [Kestrel](xref:fundamentals/servers/kestrel) implementaci serveru.</span><span class="sxs-lookup"><span data-stu-id="abfaa-178">ASP.NET Core uses the [Kestrel](xref:fundamentals/servers/kestrel) server implementation.</span></span> <span data-ttu-id="abfaa-179">Kestrel je spravovaná, napříč platformami webový server.</span><span class="sxs-lookup"><span data-stu-id="abfaa-179">Kestrel is a managed, cross-platform web server.</span></span> <span data-ttu-id="abfaa-180">Kestrel můžete také spustit jako veřejnou hraniční server přístup přímo k Internetu v ASP.NET Core 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="abfaa-180">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="abfaa-181">Linux</span><span class="sxs-lookup"><span data-stu-id="abfaa-181">Linux</span></span>](#tab/linux)

<span data-ttu-id="abfaa-182">ASP.NET Core využívá [Kestrel](xref:fundamentals/servers/kestrel) implementaci serveru.</span><span class="sxs-lookup"><span data-stu-id="abfaa-182">ASP.NET Core uses the [Kestrel](xref:fundamentals/servers/kestrel) server implementation.</span></span> <span data-ttu-id="abfaa-183">Kestrel je spravovaná, napříč platformami webový server.</span><span class="sxs-lookup"><span data-stu-id="abfaa-183">Kestrel is a managed, cross-platform web server.</span></span> <span data-ttu-id="abfaa-184">Kestrel často běží v konfiguraci reverzní proxy server s [Nginx](http://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="abfaa-184">Kestrel is often run in a reverse proxy configuration with [Nginx](http://nginx.org) or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="abfaa-185">Kestrel můžete také spustit jako veřejnou hraniční server přístup přímo k Internetu v ASP.NET Core 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="abfaa-185">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="abfaa-186">Windows</span><span class="sxs-lookup"><span data-stu-id="abfaa-186">Windows</span></span>](#tab/windows)

<span data-ttu-id="abfaa-187">ASP.NET Core nabízí následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="abfaa-187">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="abfaa-188">[Kestrel](xref:fundamentals/servers/kestrel) serveru je spravovaná, napříč platformami webový server.</span><span class="sxs-lookup"><span data-stu-id="abfaa-188">[Kestrel](xref:fundamentals/servers/kestrel) server is a managed, cross-platform web server.</span></span> <span data-ttu-id="abfaa-189">Pomocí konfigurace reverzního proxy serveru je často spustit kestrel [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="abfaa-189">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="abfaa-190">Kestrel můžete také spustit jako veřejnou hraniční server přístup přímo k Internetu v ASP.NET Core 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="abfaa-190">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="abfaa-191">[Ovladač HTTP.sys](xref:fundamentals/servers/httpsys) server je webový server pro ASP.NET Core ve Windows.</span><span class="sxs-lookup"><span data-stu-id="abfaa-191">[HTTP.sys](xref:fundamentals/servers/httpsys) server is a web server for ASP.NET Core on Windows.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="abfaa-192">macOS</span><span class="sxs-lookup"><span data-stu-id="abfaa-192">macOS</span></span>](#tab/macos)

<span data-ttu-id="abfaa-193">ASP.NET Core využívá [Kestrel](xref:fundamentals/servers/kestrel) implementaci serveru.</span><span class="sxs-lookup"><span data-stu-id="abfaa-193">ASP.NET Core uses the [Kestrel](xref:fundamentals/servers/kestrel) server implementation.</span></span> <span data-ttu-id="abfaa-194">Kestrel je spravovaná, napříč platformami webový server.</span><span class="sxs-lookup"><span data-stu-id="abfaa-194">Kestrel is a managed, cross-platform web server.</span></span> <span data-ttu-id="abfaa-195">Kestrel můžete také spustit jako veřejnou hraniční server přístup přímo k Internetu v ASP.NET Core 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="abfaa-195">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="abfaa-196">Linux</span><span class="sxs-lookup"><span data-stu-id="abfaa-196">Linux</span></span>](#tab/linux)

<span data-ttu-id="abfaa-197">ASP.NET Core využívá [Kestrel](xref:fundamentals/servers/kestrel) implementaci serveru.</span><span class="sxs-lookup"><span data-stu-id="abfaa-197">ASP.NET Core uses the [Kestrel](xref:fundamentals/servers/kestrel) server implementation.</span></span> <span data-ttu-id="abfaa-198">Kestrel je spravovaná, napříč platformami webový server.</span><span class="sxs-lookup"><span data-stu-id="abfaa-198">Kestrel is a managed, cross-platform web server.</span></span> <span data-ttu-id="abfaa-199">Kestrel často běží v konfiguraci reverzní proxy server s [Nginx](http://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="abfaa-199">Kestrel is often run in a reverse proxy configuration with [Nginx](http://nginx.org) or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="abfaa-200">Kestrel můžete také spustit jako veřejnou hraniční server přístup přímo k Internetu v ASP.NET Core 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="abfaa-200">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>

---

::: moniker-end

<span data-ttu-id="abfaa-201">Další informace naleznete v tématu <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="abfaa-201">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="abfaa-202">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="abfaa-202">Configuration</span></span>

<span data-ttu-id="abfaa-203">ASP.NET Core využívá konfigurační model založený na dvojicích název-hodnota.</span><span class="sxs-lookup"><span data-stu-id="abfaa-203">ASP.NET Core uses a configuration model based on name-value pairs.</span></span> <span data-ttu-id="abfaa-204">Konfigurační model již není založen na <xref:System.Configuration> nebo *web.config*. Hodnoty konfigurace jsou získávány z uspořádané množiny zprostředkovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="abfaa-204">The configuration model isn't based on <xref:System.Configuration> or *web.config*. Configuration obtains settings from an ordered set of configuration providers.</span></span> <span data-ttu-id="abfaa-205">Vestavění zprostředkovatelé konfigurace podporují množství standardních formátů (XML, JSON, INI) nebo konfiguraci pomocí proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="abfaa-205">The built-in configuration providers support a variety of file formats (XML, JSON, INI), environment variables, and command-line arguments.</span></span> <span data-ttu-id="abfaa-206">Můžete vytvořit i vlastního zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="abfaa-206">You can also write your own custom configuration providers.</span></span>

<span data-ttu-id="abfaa-207">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="abfaa-207">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="logging"></a><span data-ttu-id="abfaa-208">Protokolování</span><span class="sxs-lookup"><span data-stu-id="abfaa-208">Logging</span></span>

<span data-ttu-id="abfaa-209">ASP.NET Core poskytuje obecné API pro protokolování, které spolupracuje s různými zprostředkovateli protokolování.</span><span class="sxs-lookup"><span data-stu-id="abfaa-209">ASP.NET Core supports a Logging API that works with a variety of logging providers.</span></span> <span data-ttu-id="abfaa-210">Předdefinovaní zprostředkovatelé protokolování podporují odesílání záznamů do jednoho i více míst zároveň.</span><span class="sxs-lookup"><span data-stu-id="abfaa-210">Built-in providers support sending logs to one or more destinations.</span></span> <span data-ttu-id="abfaa-211">Použít je možné i zprostředkovatele třetích stran.</span><span class="sxs-lookup"><span data-stu-id="abfaa-211">Third-party logging frameworks can be used.</span></span>

<span data-ttu-id="abfaa-212">Další informace naleznete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="abfaa-212">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="abfaa-213">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="abfaa-213">Error handling</span></span>

<span data-ttu-id="abfaa-214">ASP.NET Core má integrované funkce pro zpracování chyb v aplikacích, vč. stránky pro diagnostiku výjimek, vlastní chybové stránky a zpracování výjimek při spuštění.</span><span class="sxs-lookup"><span data-stu-id="abfaa-214">ASP.NET Core has built-in scenarios for handling errors in apps, including a developer exception page, custom error pages, static status code pages, and startup exception handling.</span></span>

<span data-ttu-id="abfaa-215">Další informace naleznete v tématu <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="abfaa-215">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="routing"></a><span data-ttu-id="abfaa-216">Směrování</span><span class="sxs-lookup"><span data-stu-id="abfaa-216">Routing</span></span>

<span data-ttu-id="abfaa-217">ASP.NET Core nabízí funkce pro směrování požadavků do obslužné rutiny směrovače.</span><span class="sxs-lookup"><span data-stu-id="abfaa-217">ASP.NET Core offers scenarios for routing of app requests to route handlers.</span></span>

<span data-ttu-id="abfaa-218">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="abfaa-218">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="background-tasks"></a><span data-ttu-id="abfaa-219">Úlohy na pozadí</span><span class="sxs-lookup"><span data-stu-id="abfaa-219">Background tasks</span></span>

<span data-ttu-id="abfaa-220">Úlohy na pozadí jsou implementovány jako *hostované služby*.</span><span class="sxs-lookup"><span data-stu-id="abfaa-220">Background tasks are implemented as *hosted services*.</span></span> <span data-ttu-id="abfaa-221">Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>.</span><span class="sxs-lookup"><span data-stu-id="abfaa-221">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span>

<span data-ttu-id="abfaa-222">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="abfaa-222">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="access-httpcontext"></a><span data-ttu-id="abfaa-223">Přístup k objektu HttpContext</span><span class="sxs-lookup"><span data-stu-id="abfaa-223">Access HttpContext</span></span>

<span data-ttu-id="abfaa-224">`HttpContext` Při zpracování žádosti se stránkami Razor a technologií MVC je automaticky k dispozici.</span><span class="sxs-lookup"><span data-stu-id="abfaa-224">`HttpContext` is automatically available when processing requests with Razor Pages and MVC.</span></span> <span data-ttu-id="abfaa-225">V případech kdy `HttpContext` není snadno k dispozici, můžete přistupovat `HttpContext` prostřednictvím <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> rozhraní a jeho výchozí implementace <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span><span class="sxs-lookup"><span data-stu-id="abfaa-225">In circumstances where `HttpContext` isn't readily available, you can access the `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation, <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span>

<span data-ttu-id="abfaa-226">Další informace naleznete v tématu <xref:fundamentals/httpcontext>.</span><span class="sxs-lookup"><span data-stu-id="abfaa-226">For more information, see <xref:fundamentals/httpcontext>.</span></span>
