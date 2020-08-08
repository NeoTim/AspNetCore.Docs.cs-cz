---
title: Základy ASP.NET Core
author: rick-anderson
description: Seznamte se se základními pojmy pro vytváření ASP.NET Corech aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
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
uid: fundamentals/index
ms.openlocfilehash: f141e9248a702ad9a1d9737f82543a0ccc8fb573
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017204"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="1a00a-103">Základy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1a00a-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1a00a-104">Tento článek poskytuje přehled klíčových témat pro porozumění, jak vyvíjet aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1a00a-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="1a00a-105">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="1a00a-105">The Startup class</span></span>

<span data-ttu-id="1a00a-106">`Startup`Třída je:</span><span class="sxs-lookup"><span data-stu-id="1a00a-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="1a00a-107">Služby vyžadované aplikací jsou nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="1a00a-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="1a00a-108">Kanál pro zpracování požadavků aplikace je definován jako série součástí middlewaru.</span><span class="sxs-lookup"><span data-stu-id="1a00a-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="1a00a-109">Tady je ukázková `Startup` Třída:</span><span class="sxs-lookup"><span data-stu-id="1a00a-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="1a00a-110">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="1a00a-111">Injektáž závislostí (služby)</span><span class="sxs-lookup"><span data-stu-id="1a00a-111">Dependency injection (services)</span></span>

<span data-ttu-id="1a00a-112">ASP.NET Core obsahuje integrovanou platformu pro vkládání závislostí (DI), která zpřístupňuje nakonfigurované služby v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1a00a-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="1a00a-113">Komponenta protokolování je například služba.</span><span class="sxs-lookup"><span data-stu-id="1a00a-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="1a00a-114">Do metody se přidá kód pro konfiguraci (nebo *registrování*) služeb `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1a00a-114">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="1a00a-115">Například:</span><span class="sxs-lookup"><span data-stu-id="1a00a-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="1a00a-116">Služby jsou obvykle vyřešeny z DI pomocí injektáže konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="1a00a-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="1a00a-117">Pomocí injektáže konstruktoru třída deklaruje parametr konstruktoru buď požadovaného typu, nebo rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1a00a-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="1a00a-118">Rozhraní DI Framework poskytuje instanci této služby za běhu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="1a00a-119">Následující příklad používá vkládání konstruktoru k vyřešení `RazorPagesMovieContext` od di:</span><span class="sxs-lookup"><span data-stu-id="1a00a-119">The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="1a00a-120">Pokud integrovaný IoC kontejner inverze nesplňuje všechny požadavky aplikace, můžete místo toho použít kontejner IoC třetí strany.</span><span class="sxs-lookup"><span data-stu-id="1a00a-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="1a00a-121">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="1a00a-122">Middleware</span><span class="sxs-lookup"><span data-stu-id="1a00a-122">Middleware</span></span>

<span data-ttu-id="1a00a-123">Kanál pro zpracování žádostí se skládá z řady middlewarových součástí.</span><span class="sxs-lookup"><span data-stu-id="1a00a-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="1a00a-124">Každá komponenta provádí operace s `HttpContext` a buď vyvolá další middleware v kanálu, nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="1a00a-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="1a00a-125">Podle konvence je do kanálu přidána komponenta middleware vyvoláním `Use...` metody rozšíření v `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="1a00a-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="1a00a-126">Chcete-li například povolit vykreslování statických souborů, zavolejte `UseStaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="1a00a-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="1a00a-127">Následující příklad konfiguruje kanál zpracování žádostí:</span><span class="sxs-lookup"><span data-stu-id="1a00a-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="1a00a-128">ASP.NET Core obsahuje bohatou sadu předdefinovaných middlewarů.</span><span class="sxs-lookup"><span data-stu-id="1a00a-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="1a00a-129">Je také možné zapsat vlastní součásti middlewaru.</span><span class="sxs-lookup"><span data-stu-id="1a00a-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="1a00a-130">Další informace naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="1a00a-131">Hostitel</span><span class="sxs-lookup"><span data-stu-id="1a00a-131">Host</span></span>

<span data-ttu-id="1a00a-132">Při spuštění aplikace ASP.NET Core vytvoří *hostitele*.</span><span class="sxs-lookup"><span data-stu-id="1a00a-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="1a00a-133">Hostitel zapouzdřuje všechny prostředky aplikace, například:</span><span class="sxs-lookup"><span data-stu-id="1a00a-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="1a00a-134">Implementace HTTP serveru</span><span class="sxs-lookup"><span data-stu-id="1a00a-134">An HTTP server implementation</span></span>
* <span data-ttu-id="1a00a-135">Komponenty middlewaru</span><span class="sxs-lookup"><span data-stu-id="1a00a-135">Middleware components</span></span>
* <span data-ttu-id="1a00a-136">protokolování</span><span class="sxs-lookup"><span data-stu-id="1a00a-136">Logging</span></span>
* <span data-ttu-id="1a00a-137">Služby pro vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="1a00a-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="1a00a-138">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="1a00a-138">Configuration</span></span>

<span data-ttu-id="1a00a-139">Existují dva různé hostitele:</span><span class="sxs-lookup"><span data-stu-id="1a00a-139">There are two different hosts:</span></span> 

* <span data-ttu-id="1a00a-140">Obecný hostitel .NET</span><span class="sxs-lookup"><span data-stu-id="1a00a-140">.NET Generic Host</span></span>
* <span data-ttu-id="1a00a-141">ASP.NET Core webového hostitele</span><span class="sxs-lookup"><span data-stu-id="1a00a-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="1a00a-142">Doporučuje se obecný hostitel .NET.</span><span class="sxs-lookup"><span data-stu-id="1a00a-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="1a00a-143">Webový hostitel ASP.NET Core je k dispozici pouze pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="1a00a-144">Následující příklad vytvoří obecný hostitel .NET:</span><span class="sxs-lookup"><span data-stu-id="1a00a-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="1a00a-145">`CreateDefaultBuilder`Metody a `ConfigureWebHostDefaults` konfigurují hostitele pomocí sady výchozích možností, jako například:</span><span class="sxs-lookup"><span data-stu-id="1a00a-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="1a00a-146">Použijte [Kestrel](#servers) jako webový server a povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="1a00a-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="1a00a-147">Načíst konfiguraci z *appsettings.jsna*, *appSettings. { Název prostředí}. JSON*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1a00a-147">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="1a00a-148">Odešlete výstup protokolování do konzoly a zprostředkovatele ladění.</span><span class="sxs-lookup"><span data-stu-id="1a00a-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="1a00a-149">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="1a00a-150">Jiné než webové scénáře</span><span class="sxs-lookup"><span data-stu-id="1a00a-150">Non-web scenarios</span></span>

<span data-ttu-id="1a00a-151">Obecný hostitel umožňuje jiným typům aplikací používat rozšíření rozhraní pro průřez, jako je protokolování, vkládání závislostí (DI), konfigurace a správa životního cyklu aplikací.</span><span class="sxs-lookup"><span data-stu-id="1a00a-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="1a00a-152">Další informace naleznete v tématech <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="1a00a-153">Servery</span><span class="sxs-lookup"><span data-stu-id="1a00a-153">Servers</span></span>

<span data-ttu-id="1a00a-154">ASP.NET Core aplikace používá implementaci HTTP serveru k naslouchání požadavkům HTTP.</span><span class="sxs-lookup"><span data-stu-id="1a00a-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="1a00a-155">Serverový povrch vyžádá aplikaci jako sadu [funkcí požadavků](xref:fundamentals/request-features) složených do `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="1a00a-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="1a00a-156">Windows</span><span class="sxs-lookup"><span data-stu-id="1a00a-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="1a00a-157">ASP.NET Core poskytuje následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="1a00a-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="1a00a-158">*Kestrel* je webový server pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="1a00a-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="1a00a-159">Kestrel se často spouští v konfiguraci reverzního proxy serveru pomocí [služby IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="1a00a-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="1a00a-160">V ASP.NET Core 2,0 nebo novějším se Kestrel dá spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="1a00a-161">*Http server IIS* je server pro Windows, který používá službu IIS.</span><span class="sxs-lookup"><span data-stu-id="1a00a-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="1a00a-162">S tímto serverem aplikace ASP.NET Core a služba IIS běží ve stejném procesu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="1a00a-163">*HTTP.sys* je server pro Windows, který se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="1a00a-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="1a00a-164">macOS</span><span class="sxs-lookup"><span data-stu-id="1a00a-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="1a00a-165">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="1a00a-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="1a00a-166">V ASP.NET Core 2,0 nebo novějším může Kestrel běžet jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="1a00a-167">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1a00a-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="1a00a-168">Linux</span><span class="sxs-lookup"><span data-stu-id="1a00a-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="1a00a-169">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="1a00a-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="1a00a-170">V ASP.NET Core 2,0 nebo novějším může Kestrel běžet jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="1a00a-171">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1a00a-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="1a00a-172">Další informace naleznete v tématu <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="1a00a-173">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="1a00a-173">Configuration</span></span>

<span data-ttu-id="1a00a-174">ASP.NET Core poskytuje konfigurační rozhraní, které získá nastavení jako páry název-hodnota z seřazené sady poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1a00a-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="1a00a-175">Předdefinovaná poskytovatelé konfigurace jsou k dispozici pro nejrůznější zdroje, například soubory *. JSON* , soubory *. XML* , proměnné prostředí a argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="1a00a-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="1a00a-176">Napište vlastní poskytovatele konfigurace pro podporu jiných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="1a00a-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="1a00a-177">Ve [výchozím nastavení](xref:fundamentals/configuration/index#default)jsou aplikace ASP.NET Core nakonfigurované pro čtení z *appsettings.jsna*, proměnných prostředí, příkazového řádku a dalších.</span><span class="sxs-lookup"><span data-stu-id="1a00a-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *appsettings.json*, environment variables, the command line, and more.</span></span> <span data-ttu-id="1a00a-178">Když je načtena konfigurace aplikace, hodnoty z proměnných prostředí přepíší hodnoty z *appsettings.js*.</span><span class="sxs-lookup"><span data-stu-id="1a00a-178">When the app's configuration is loaded, values from environment variables override values from *appsettings.json*.</span></span>

<span data-ttu-id="1a00a-179">Upřednostňovaným způsobem, jak číst související konfigurační hodnoty, je použít [vzor možností](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="1a00a-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="1a00a-180">Další informace najdete v tématu [vázání hierarchických konfiguračních dat pomocí vzoru možností](xref:fundamentals/configuration/index#optpat).</span><span class="sxs-lookup"><span data-stu-id="1a00a-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="1a00a-181">Pro správu důvěrných konfiguračních dat, jako jsou hesla, ASP.NET Core poskytuje [správce tajných klíčů](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="1a00a-181">For managing confidential configuration data such as passwords, ASP.NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="1a00a-182">V případě produkčních tajných kódů doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="1a00a-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="1a00a-183">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="1a00a-184">Prostředí</span><span class="sxs-lookup"><span data-stu-id="1a00a-184">Environments</span></span>

<span data-ttu-id="1a00a-185">Spouštěcí prostředí, jako jsou `Development` , `Staging` a `Production` , jsou první třídou pojmu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1a00a-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="1a00a-186">Určete prostředí, ve kterém je aplikace spuštěná, nastavením `ASPNETCORE_ENVIRONMENT` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="1a00a-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="1a00a-187">ASP.NET Core přečte tuto proměnnou prostředí při spuštění aplikace a uloží hodnotu v `IWebHostEnvironment` implementaci.</span><span class="sxs-lookup"><span data-stu-id="1a00a-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="1a00a-188">Tato implementace je k dispozici kdekoli v aplikaci prostřednictvím injektáže závislosti (DI).</span><span class="sxs-lookup"><span data-stu-id="1a00a-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="1a00a-189">Následující příklad nakonfiguruje aplikaci tak, aby při spuštění v prostředí poskytovala podrobné informace o chybě `Development` :</span><span class="sxs-lookup"><span data-stu-id="1a00a-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="1a00a-190">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="1a00a-191">protokolování</span><span class="sxs-lookup"><span data-stu-id="1a00a-191">Logging</span></span>

<span data-ttu-id="1a00a-192">ASP.NET Core podporuje protokolovací rozhraní API, které funguje s řadou integrovaných poskytovatelů protokolování a jiných výrobců.</span><span class="sxs-lookup"><span data-stu-id="1a00a-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="1a00a-193">Mezi dostupné zprostředkovatele patří:</span><span class="sxs-lookup"><span data-stu-id="1a00a-193">Available providers include:</span></span>

* <span data-ttu-id="1a00a-194">Konzola</span><span class="sxs-lookup"><span data-stu-id="1a00a-194">Console</span></span>
* <span data-ttu-id="1a00a-195">Ladění</span><span class="sxs-lookup"><span data-stu-id="1a00a-195">Debug</span></span>
* <span data-ttu-id="1a00a-196">Trasování událostí ve Windows</span><span class="sxs-lookup"><span data-stu-id="1a00a-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="1a00a-197">Protokol událostí systému Windows</span><span class="sxs-lookup"><span data-stu-id="1a00a-197">Windows Event Log</span></span>
* <span data-ttu-id="1a00a-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="1a00a-198">TraceSource</span></span>
* <span data-ttu-id="1a00a-199">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="1a00a-199">Azure App Service</span></span>
* <span data-ttu-id="1a00a-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="1a00a-200">Azure Application Insights</span></span>

<span data-ttu-id="1a00a-201">Chcete-li vytvořit protokoly, vyřešte <xref:Microsoft.Extensions.Logging.ILogger%601> službu z vkládání závislostí (di) a metody protokolování volání jako <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> .</span><span class="sxs-lookup"><span data-stu-id="1a00a-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="1a00a-202">Například:</span><span class="sxs-lookup"><span data-stu-id="1a00a-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="1a00a-203">Metody protokolování, jako je například `LogInformation` Podpora libovolného počtu polí.</span><span class="sxs-lookup"><span data-stu-id="1a00a-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="1a00a-204">Tato pole se běžně používají k vytvoření zprávy `string` , ale někteří zprostředkovatelé protokolování odesílají tato data do úložiště dat jako samostatná pole.</span><span class="sxs-lookup"><span data-stu-id="1a00a-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="1a00a-205">Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="1a00a-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="1a00a-206">Další informace naleznete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="1a00a-207">Směrování</span><span class="sxs-lookup"><span data-stu-id="1a00a-207">Routing</span></span>

<span data-ttu-id="1a00a-208">*Trasa* je vzor URL, který je namapován na obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="1a00a-209">Obslužná rutina je typicky Razor Stránka, metoda akce v řadiči MVC nebo middleware.</span><span class="sxs-lookup"><span data-stu-id="1a00a-209">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="1a00a-210">ASP.NET Core směrování vám umožní řídit adresy URL používané vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="1a00a-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="1a00a-211">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="1a00a-212">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="1a00a-212">Error handling</span></span>

<span data-ttu-id="1a00a-213">ASP.NET Core obsahuje integrované funkce pro zpracování chyb, jako například:</span><span class="sxs-lookup"><span data-stu-id="1a00a-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="1a00a-214">Stránka s výjimkou vývojáře</span><span class="sxs-lookup"><span data-stu-id="1a00a-214">A developer exception page</span></span>
* <span data-ttu-id="1a00a-215">Vlastní chybové stránky</span><span class="sxs-lookup"><span data-stu-id="1a00a-215">Custom error pages</span></span>
* <span data-ttu-id="1a00a-216">Statické stránky se stavovým kódem</span><span class="sxs-lookup"><span data-stu-id="1a00a-216">Static status code pages</span></span>
* <span data-ttu-id="1a00a-217">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="1a00a-217">Startup exception handling</span></span>

<span data-ttu-id="1a00a-218">Další informace naleznete v tématu <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="1a00a-219">Vytváření požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="1a00a-219">Make HTTP requests</span></span>

<span data-ttu-id="1a00a-220">Implementace `IHttpClientFactory` je k dispozici pro vytváření `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="1a00a-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="1a00a-221">Objekt pro vytváření:</span><span class="sxs-lookup"><span data-stu-id="1a00a-221">The factory:</span></span>

* <span data-ttu-id="1a00a-222">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="1a00a-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="1a00a-223">Můžete například zaregistrovat a nakonfigurovat klienta *GitHub* pro přístup k GitHubu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="1a00a-224">Zaregistrujte a nakonfigurujte výchozího klienta pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="1a00a-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="1a00a-225">Podporuje registraci a řetězení více obslužných rutin delegování pro sestavení kanálu middlewaru odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="1a00a-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="1a00a-226">Tento model je podobný vstupnímu kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1a00a-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="1a00a-227">Vzor poskytuje mechanismus pro správu otázek pro různé průřezy pro požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="1a00a-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="1a00a-228">Integruje se s *Polly*oblíbenou knihovnou třetích stran pro zpracování přechodného selhání.</span><span class="sxs-lookup"><span data-stu-id="1a00a-228">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="1a00a-229">Spravuje sdružování a životnost základních `HttpClientHandler` instancí, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při `HttpClient` Ruční správě životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="1a00a-230">Přidá konfigurovatelné prostředí protokolování prostřednictvím <xref:Microsoft.Extensions.Logging.ILogger> všech požadavků odesílaných klienty vytvořenými pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="1a00a-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="1a00a-231">Další informace naleznete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="1a00a-232">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="1a00a-232">Content root</span></span>

<span data-ttu-id="1a00a-233">Kořen obsahu je základní cesta pro:</span><span class="sxs-lookup"><span data-stu-id="1a00a-233">The content root is the base path for:</span></span>

* <span data-ttu-id="1a00a-234">Spustitelný soubor, který hostuje aplikaci (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="1a00a-234">The executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="1a00a-235">Kompilovaná sestavení, která tvoří aplikaci (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="1a00a-235">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="1a00a-236">Soubory obsahu používané aplikací, jako například:</span><span class="sxs-lookup"><span data-stu-id="1a00a-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="1a00a-237">Razorsoubory (*. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="1a00a-237">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="1a00a-238">Konfigurační soubory (*. JSON*, *. XML*)</span><span class="sxs-lookup"><span data-stu-id="1a00a-238">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="1a00a-239">Datové soubory (*. DB*)</span><span class="sxs-lookup"><span data-stu-id="1a00a-239">Data files (*.db*)</span></span>
* <span data-ttu-id="1a00a-240">[Kořenový adresář webu](#web-root), obvykle složka *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="1a00a-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="1a00a-241">Během vývoje se výchozí hodnota kořenu obsahu nastaví na kořenový adresář projektu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="1a00a-242">Tento adresář je také základní cestou pro soubory obsahu aplikace i pro [kořenový adresář webu](#web-root).</span><span class="sxs-lookup"><span data-stu-id="1a00a-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="1a00a-243">Určete jiný kořen obsahu nastavením jeho cesty při [sestavování hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="1a00a-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="1a00a-244">Další informace najdete v tématu [kořenový adresář obsahu](xref:fundamentals/host/generic-host#contentroot).</span><span class="sxs-lookup"><span data-stu-id="1a00a-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentroot).</span></span>

## <a name="web-root"></a><span data-ttu-id="1a00a-245">Webový kořenový adresář</span><span class="sxs-lookup"><span data-stu-id="1a00a-245">Web root</span></span>

<span data-ttu-id="1a00a-246">Kořenový adresář webu je základní cesta pro veřejné a statické soubory prostředků, jako například:</span><span class="sxs-lookup"><span data-stu-id="1a00a-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="1a00a-247">Šablony stylů (*. CSS*)</span><span class="sxs-lookup"><span data-stu-id="1a00a-247">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="1a00a-248">JavaScript (*. js*)</span><span class="sxs-lookup"><span data-stu-id="1a00a-248">JavaScript (*.js*)</span></span>
* <span data-ttu-id="1a00a-249">Obrázky (*. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="1a00a-249">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="1a00a-250">Ve výchozím nastavení jsou statické soubory obsluhovány pouze z kořenového adresáře webu a jeho podadresářů.</span><span class="sxs-lookup"><span data-stu-id="1a00a-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="1a00a-251">Kořenová cesta webu je ve výchozím nastavení *{root obsahu}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="1a00a-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="1a00a-252">Určete jiný webový kořenový adresář nastavením jeho cesty při [sestavování hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="1a00a-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="1a00a-253">Další informace najdete v tématu [Web root](xref:fundamentals/host/generic-host#webroot).</span><span class="sxs-lookup"><span data-stu-id="1a00a-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot).</span></span>

<span data-ttu-id="1a00a-254">Zabraňte publikování souborů v *wwwroot* pomocí [ \<Content> položky projektu](/visualstudio/msbuild/common-msbuild-project-items#content) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="1a00a-255">Následující příklad brání publikování obsahu v *wwwroot/Local* a jeho podadresářích:</span><span class="sxs-lookup"><span data-stu-id="1a00a-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="1a00a-256">V Razor souborech *. cshtml* odkazuje vlnové lomítko ( `~/` ) na kořenový adresář webu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-256">In Razor *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="1a00a-257">Cesta začínající `~/` na je označována jako *virtuální cesta*.</span><span class="sxs-lookup"><span data-stu-id="1a00a-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="1a00a-258">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1a00a-259">Tento článek představuje přehled klíčových témat pro porozumění, jak vyvíjet aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1a00a-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="1a00a-260">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="1a00a-260">The Startup class</span></span>

<span data-ttu-id="1a00a-261">`Startup`Třída je:</span><span class="sxs-lookup"><span data-stu-id="1a00a-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="1a00a-262">Služby vyžadované aplikací jsou nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="1a00a-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="1a00a-263">Je definovaný kanál pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="1a00a-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="1a00a-264">*Služby* jsou komponenty, které aplikace používá.</span><span class="sxs-lookup"><span data-stu-id="1a00a-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="1a00a-265">Komponenta protokolování je například služba.</span><span class="sxs-lookup"><span data-stu-id="1a00a-265">For example, a logging component is a service.</span></span> <span data-ttu-id="1a00a-266">Do metody se přidá kód pro konfiguraci (nebo *registrování*) služeb `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1a00a-266">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="1a00a-267">Kanál pro zpracování žádostí se skládá z řady *middlewarových* součástí.</span><span class="sxs-lookup"><span data-stu-id="1a00a-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="1a00a-268">Middleware například může zpracovávat požadavky na statické soubory nebo přesměrovat požadavky HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="1a00a-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="1a00a-269">Každý middleware provádí asynchronní operace s `HttpContext` a pak buď vyvolá další middleware v kanálu, nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="1a00a-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="1a00a-270">Do metody se přidá kód pro konfiguraci kanálu zpracování požadavků `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="1a00a-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="1a00a-271">Tady je ukázková `Startup` Třída:</span><span class="sxs-lookup"><span data-stu-id="1a00a-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="1a00a-272">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="1a00a-273">Injektáž závislostí (služby)</span><span class="sxs-lookup"><span data-stu-id="1a00a-273">Dependency injection (services)</span></span>

<span data-ttu-id="1a00a-274">ASP.NET Core má integrovanou architekturu pro vkládání závislostí (DI), která zpřístupňuje nakonfigurované služby pro třídy aplikace.</span><span class="sxs-lookup"><span data-stu-id="1a00a-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="1a00a-275">Jedním ze způsobů, jak získat instanci služby ve třídě, je vytvořit konstruktor s parametrem požadovaného typu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="1a00a-276">Parametr může být typ služby nebo rozhraní.</span><span class="sxs-lookup"><span data-stu-id="1a00a-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="1a00a-277">Systém DI poskytuje službu za běhu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="1a00a-278">Zde je třída, která používá DI k získání objektu kontextu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="1a00a-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="1a00a-279">Zvýrazněný řádek je příkladem injektáže konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="1a00a-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="1a00a-280">I když je systém v systému integrovaný, je navržený tak, aby umožňoval připojení kontejneru inverze správy (IoC) třetí strany, pokud budete chtít.</span><span class="sxs-lookup"><span data-stu-id="1a00a-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="1a00a-281">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="1a00a-282">Middleware</span><span class="sxs-lookup"><span data-stu-id="1a00a-282">Middleware</span></span>

<span data-ttu-id="1a00a-283">Kanál pro zpracování žádostí se skládá z řady middlewarových součástí.</span><span class="sxs-lookup"><span data-stu-id="1a00a-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="1a00a-284">Každá komponenta provádí asynchronní operace s `HttpContext` a pak buď vyvolá další middleware v kanálu, nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="1a00a-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="1a00a-285">Podle konvence je do kanálu přidána komponenta middleware vyvoláním `Use...` metody rozšíření v `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="1a00a-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="1a00a-286">Chcete-li například povolit vykreslování statických souborů, zavolejte `UseStaticFiles` .</span><span class="sxs-lookup"><span data-stu-id="1a00a-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="1a00a-287">Zvýrazněný kód v následujícím příkladu konfiguruje kanál zpracování žádostí:</span><span class="sxs-lookup"><span data-stu-id="1a00a-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="1a00a-288">ASP.NET Core obsahuje bohatou sadu předdefinovaných middlewarů a můžete napsat vlastní middleware.</span><span class="sxs-lookup"><span data-stu-id="1a00a-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="1a00a-289">Další informace naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="1a00a-290">Hostitel</span><span class="sxs-lookup"><span data-stu-id="1a00a-290">Host</span></span>

<span data-ttu-id="1a00a-291">Aplikace ASP.NET Core při spuštění vytvoří *hostitele* .</span><span class="sxs-lookup"><span data-stu-id="1a00a-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="1a00a-292">Hostitel je objekt, který zapouzdřuje všechny prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="1a00a-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="1a00a-293">Implementace HTTP serveru</span><span class="sxs-lookup"><span data-stu-id="1a00a-293">An HTTP server implementation</span></span>
* <span data-ttu-id="1a00a-294">Komponenty middlewaru</span><span class="sxs-lookup"><span data-stu-id="1a00a-294">Middleware components</span></span>
* <span data-ttu-id="1a00a-295">protokolování</span><span class="sxs-lookup"><span data-stu-id="1a00a-295">Logging</span></span>
* <span data-ttu-id="1a00a-296">DŽI</span><span class="sxs-lookup"><span data-stu-id="1a00a-296">DI</span></span>
* <span data-ttu-id="1a00a-297">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="1a00a-297">Configuration</span></span>

<span data-ttu-id="1a00a-298">Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je Správa životního cyklu: Kontrola spuštění aplikace a bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="1a00a-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="1a00a-299">K dispozici jsou dva hostitelé: webový hostitel a obecný hostitel.</span><span class="sxs-lookup"><span data-stu-id="1a00a-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="1a00a-300">V ASP.NET Core 2. x je obecný hostitel pouze pro scénáře, které nejsou webové.</span><span class="sxs-lookup"><span data-stu-id="1a00a-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="1a00a-301">Kód pro vytvoření hostitele je v `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="1a00a-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="1a00a-302">`CreateDefaultBuilder`Metoda Konfiguruje hostitele pomocí běžně používaných možností, jako například následujících:</span><span class="sxs-lookup"><span data-stu-id="1a00a-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="1a00a-303">Použijte [Kestrel](#servers) jako webový server a povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="1a00a-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="1a00a-304">Načíst konfiguraci z *appsettings.jsna*, *appSettings. { Název prostředí}. JSON*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1a00a-304">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="1a00a-305">Odešlete výstup protokolování do konzoly a zprostředkovatele ladění.</span><span class="sxs-lookup"><span data-stu-id="1a00a-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="1a00a-306">Další informace naleznete v tématu <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="1a00a-307">Jiné než webové scénáře</span><span class="sxs-lookup"><span data-stu-id="1a00a-307">Non-web scenarios</span></span>

<span data-ttu-id="1a00a-308">Obecný hostitel umožňuje jiným typům aplikací používat rozšíření rozhraní pro průřez, jako je protokolování, vkládání závislostí (DI), konfigurace a správa životního cyklu aplikací.</span><span class="sxs-lookup"><span data-stu-id="1a00a-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="1a00a-309">Další informace naleznete v tématech <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="1a00a-310">Servery</span><span class="sxs-lookup"><span data-stu-id="1a00a-310">Servers</span></span>

<span data-ttu-id="1a00a-311">ASP.NET Core aplikace používá implementaci HTTP serveru k naslouchání požadavkům HTTP.</span><span class="sxs-lookup"><span data-stu-id="1a00a-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="1a00a-312">Serverový povrch vyžádá aplikaci jako sadu [funkcí požadavků](xref:fundamentals/request-features) složených do `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="1a00a-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="1a00a-313">Windows</span><span class="sxs-lookup"><span data-stu-id="1a00a-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="1a00a-314">ASP.NET Core poskytuje následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="1a00a-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="1a00a-315">*Kestrel* je webový server pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="1a00a-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="1a00a-316">Kestrel se často spouští v konfiguraci reverzního proxy serveru pomocí [služby IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="1a00a-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="1a00a-317">Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="1a00a-318">*Http server IIS* je server pro Windows, který používá službu IIS.</span><span class="sxs-lookup"><span data-stu-id="1a00a-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="1a00a-319">S tímto serverem aplikace ASP.NET Core a služba IIS běží ve stejném procesu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="1a00a-320">*HTTP.sys* je server pro Windows, který se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="1a00a-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="1a00a-321">macOS</span><span class="sxs-lookup"><span data-stu-id="1a00a-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="1a00a-322">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="1a00a-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="1a00a-323">Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="1a00a-324">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1a00a-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="1a00a-325">Linux</span><span class="sxs-lookup"><span data-stu-id="1a00a-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="1a00a-326">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="1a00a-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="1a00a-327">Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="1a00a-328">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1a00a-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="1a00a-329">Windows</span><span class="sxs-lookup"><span data-stu-id="1a00a-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="1a00a-330">ASP.NET Core poskytuje následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="1a00a-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="1a00a-331">*Kestrel* je webový server pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="1a00a-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="1a00a-332">Kestrel se často spouští v konfiguraci reverzního proxy serveru pomocí [služby IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="1a00a-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="1a00a-333">Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="1a00a-334">*HTTP.sys* je server pro Windows, který se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="1a00a-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="1a00a-335">macOS</span><span class="sxs-lookup"><span data-stu-id="1a00a-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="1a00a-336">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="1a00a-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="1a00a-337">Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="1a00a-338">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1a00a-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="1a00a-339">Linux</span><span class="sxs-lookup"><span data-stu-id="1a00a-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="1a00a-340">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="1a00a-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="1a00a-341">Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="1a00a-342">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1a00a-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1a00a-343">Další informace naleznete v tématu <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="1a00a-344">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="1a00a-344">Configuration</span></span>

<span data-ttu-id="1a00a-345">ASP.NET Core poskytuje konfigurační rozhraní, které získá nastavení jako páry název-hodnota z seřazené sady poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1a00a-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="1a00a-346">Existují předdefinované poskytovatelé konfigurace pro nejrůznější zdroje, například soubory *. JSON* , soubory *. XML* , proměnné prostředí a argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="1a00a-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="1a00a-347">Můžete také psát vlastní poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1a00a-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="1a00a-348">Například můžete určit, že konfigurace pochází z *appsettings.js* a proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="1a00a-348">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="1a00a-349">Poté, co je požadována hodnota *ConnectionString* , rozhraní nejprve vyhledá v souboru *appsettings.js* .</span><span class="sxs-lookup"><span data-stu-id="1a00a-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="1a00a-350">Pokud je hodnota nalezena, ale také v proměnné prostředí, bude mít přednost hodnota z proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="1a00a-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="1a00a-351">Pro správu důvěrných konfiguračních dat, jako jsou hesla, ASP.NET Core poskytuje [Nástroj Správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="1a00a-351">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="1a00a-352">V případě produkčních tajných kódů doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="1a00a-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="1a00a-353">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="1a00a-354">Možnosti</span><span class="sxs-lookup"><span data-stu-id="1a00a-354">Options</span></span>

<span data-ttu-id="1a00a-355">Pokud je to možné, ASP.NET Core podle *vzoru možností* pro ukládání a načítání hodnot konfigurace.</span><span class="sxs-lookup"><span data-stu-id="1a00a-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="1a00a-356">Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="1a00a-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="1a00a-357">Například následující kód nastaví možnosti WebSockets:</span><span class="sxs-lookup"><span data-stu-id="1a00a-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="1a00a-358">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="1a00a-359">Prostředí</span><span class="sxs-lookup"><span data-stu-id="1a00a-359">Environments</span></span>

<span data-ttu-id="1a00a-360">Spouštěcí prostředí, jako je *vývoj*, *Příprava*a *produkce*, jsou první třídou pojmu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1a00a-360">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="1a00a-361">Nastavením proměnné prostředí můžete určit prostředí, ve kterém je aplikace spuštěná `ASPNETCORE_ENVIRONMENT` .</span><span class="sxs-lookup"><span data-stu-id="1a00a-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="1a00a-362">ASP.NET Core přečte tuto proměnnou prostředí při spuštění aplikace a uloží hodnotu v `IHostingEnvironment` implementaci.</span><span class="sxs-lookup"><span data-stu-id="1a00a-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="1a00a-363">Objekt prostředí je k dispozici kdekoli v aplikaci přes DI.</span><span class="sxs-lookup"><span data-stu-id="1a00a-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="1a00a-364">Následující vzorový kód z `Startup` třídy nakonfiguruje aplikaci tak, aby poskytovala podrobné informace o chybách pouze v případě, že běží ve vývoji:</span><span class="sxs-lookup"><span data-stu-id="1a00a-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="1a00a-365">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="1a00a-366">protokolování</span><span class="sxs-lookup"><span data-stu-id="1a00a-366">Logging</span></span>

<span data-ttu-id="1a00a-367">ASP.NET Core podporuje protokolovací rozhraní API, které funguje s řadou integrovaných poskytovatelů protokolování a jiných výrobců.</span><span class="sxs-lookup"><span data-stu-id="1a00a-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="1a00a-368">K dispozici jsou následující poskytovatelé:</span><span class="sxs-lookup"><span data-stu-id="1a00a-368">Available providers include the following:</span></span>

* <span data-ttu-id="1a00a-369">Konzola</span><span class="sxs-lookup"><span data-stu-id="1a00a-369">Console</span></span>
* <span data-ttu-id="1a00a-370">Ladění</span><span class="sxs-lookup"><span data-stu-id="1a00a-370">Debug</span></span>
* <span data-ttu-id="1a00a-371">Trasování událostí ve Windows</span><span class="sxs-lookup"><span data-stu-id="1a00a-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="1a00a-372">Protokol událostí systému Windows</span><span class="sxs-lookup"><span data-stu-id="1a00a-372">Windows Event Log</span></span>
* <span data-ttu-id="1a00a-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="1a00a-373">TraceSource</span></span>
* <span data-ttu-id="1a00a-374">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="1a00a-374">Azure App Service</span></span>
* <span data-ttu-id="1a00a-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="1a00a-375">Azure Application Insights</span></span>

<span data-ttu-id="1a00a-376">Zápis protokolů z libovolného místa v kódu aplikace získáním `ILogger` objektu z metody di a volání metod protokolu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="1a00a-377">Zde je ukázkový kód, který používá `ILogger` objekt, s vkládáním konstruktoru a volání metody protokolování zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="1a00a-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="1a00a-378">`ILogger`Rozhraní vám umožní předat do zprostředkovatele protokolování libovolný počet polí.</span><span class="sxs-lookup"><span data-stu-id="1a00a-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="1a00a-379">Pole jsou běžně používána k sestavení řetězce zprávy, ale zprostředkovatel je může také odeslat jako samostatné pole do úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="1a00a-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="1a00a-380">Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="1a00a-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="1a00a-381">Další informace naleznete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="1a00a-382">Směrování</span><span class="sxs-lookup"><span data-stu-id="1a00a-382">Routing</span></span>

<span data-ttu-id="1a00a-383">*Trasa* je vzor URL, který je namapován na obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="1a00a-384">Obslužná rutina je typicky Razor Stránka, metoda akce v řadiči MVC nebo middleware.</span><span class="sxs-lookup"><span data-stu-id="1a00a-384">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="1a00a-385">ASP.NET Core směrování vám umožní řídit adresy URL používané vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="1a00a-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="1a00a-386">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="1a00a-387">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="1a00a-387">Error handling</span></span>

<span data-ttu-id="1a00a-388">ASP.NET Core obsahuje integrované funkce pro zpracování chyb, jako například:</span><span class="sxs-lookup"><span data-stu-id="1a00a-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="1a00a-389">Stránka s výjimkou vývojáře</span><span class="sxs-lookup"><span data-stu-id="1a00a-389">A developer exception page</span></span>
* <span data-ttu-id="1a00a-390">Vlastní chybové stránky</span><span class="sxs-lookup"><span data-stu-id="1a00a-390">Custom error pages</span></span>
* <span data-ttu-id="1a00a-391">Statické stránky se stavovým kódem</span><span class="sxs-lookup"><span data-stu-id="1a00a-391">Static status code pages</span></span>
* <span data-ttu-id="1a00a-392">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="1a00a-392">Startup exception handling</span></span>

<span data-ttu-id="1a00a-393">Další informace naleznete v tématu <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="1a00a-394">Vytváření požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="1a00a-394">Make HTTP requests</span></span>

<span data-ttu-id="1a00a-395">Implementace `IHttpClientFactory` je k dispozici pro vytváření `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="1a00a-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="1a00a-396">Objekt pro vytváření:</span><span class="sxs-lookup"><span data-stu-id="1a00a-396">The factory:</span></span>

* <span data-ttu-id="1a00a-397">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="1a00a-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="1a00a-398">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k GitHubu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="1a00a-399">Výchozí klient může být zaregistrován pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="1a00a-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="1a00a-400">Podporuje registraci a řetězení více obslužných rutin delegování pro sestavení kanálu middlewaru odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="1a00a-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="1a00a-401">Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="1a00a-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="1a00a-402">Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="1a00a-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="1a00a-403">Integruje se s *Polly*oblíbenou knihovnou třetích stran pro zpracování přechodného selhání.</span><span class="sxs-lookup"><span data-stu-id="1a00a-403">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="1a00a-404">Spravuje sdružování a životnost základních `HttpClientHandler` instancí, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="1a00a-405">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger` ) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="1a00a-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="1a00a-406">Další informace naleznete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="1a00a-407">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="1a00a-407">Content root</span></span>

<span data-ttu-id="1a00a-408">Kořen obsahu je základní cesta k:</span><span class="sxs-lookup"><span data-stu-id="1a00a-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="1a00a-409">Spustitelný soubor, který hostuje aplikaci (*. exe*).</span><span class="sxs-lookup"><span data-stu-id="1a00a-409">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="1a00a-410">Kompilovaná sestavení, která tvoří aplikaci (*. dll*).</span><span class="sxs-lookup"><span data-stu-id="1a00a-410">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="1a00a-411">Soubory s obsahem neobsahující kód, které používá aplikace, například:</span><span class="sxs-lookup"><span data-stu-id="1a00a-411">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="1a00a-412">Razorsoubory (*. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="1a00a-412">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="1a00a-413">Konfigurační soubory (*. JSON*, *. XML*)</span><span class="sxs-lookup"><span data-stu-id="1a00a-413">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="1a00a-414">Datové soubory (*. DB*)</span><span class="sxs-lookup"><span data-stu-id="1a00a-414">Data files (*.db*)</span></span>
* <span data-ttu-id="1a00a-415">[Webový kořenový adresář](#web-root), obvykle publikovaná složka *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="1a00a-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="1a00a-416">Během vývoje:</span><span class="sxs-lookup"><span data-stu-id="1a00a-416">During development:</span></span>

* <span data-ttu-id="1a00a-417">Kořen obsahu se nastaví jako kořenový adresář projektu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="1a00a-418">Kořenový adresář projektu slouží k vytvoření:</span><span class="sxs-lookup"><span data-stu-id="1a00a-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="1a00a-419">Cesta k souborům obsahu bez kódu aplikace v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="1a00a-420">[Webový kořenový](#web-root)adresář, obvykle složka *wwwroot* v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="1a00a-421">Alternativní cestu k kořenovému obsahu lze zadat při [sestavování hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="1a00a-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="1a00a-422">Další informace naleznete v tématu <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="1a00a-423">Webový kořenový adresář</span><span class="sxs-lookup"><span data-stu-id="1a00a-423">Web root</span></span>

<span data-ttu-id="1a00a-424">Kořenový adresář webu je základní cesta k souborům statických prostředků bez kódu, jako je například:</span><span class="sxs-lookup"><span data-stu-id="1a00a-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="1a00a-425">Šablony stylů (*. CSS*)</span><span class="sxs-lookup"><span data-stu-id="1a00a-425">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="1a00a-426">JavaScript (*. js*)</span><span class="sxs-lookup"><span data-stu-id="1a00a-426">JavaScript (*.js*)</span></span>
* <span data-ttu-id="1a00a-427">Obrázky (*. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="1a00a-427">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="1a00a-428">Statické soubory jsou obsluhovány ve výchozím nastavení pouze z kořenového adresáře webu (a podadresářů).</span><span class="sxs-lookup"><span data-stu-id="1a00a-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="1a00a-429">Kořenová cesta webu je ve výchozím nastavení *{root Content}/wwwroot*, ale při [sestavování hostitele](#host)je možné zadat jiný webový kořenový adresář.</span><span class="sxs-lookup"><span data-stu-id="1a00a-429">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="1a00a-430">Další informace najdete v tématu [Web root](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="1a00a-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="1a00a-431">Zabraňte publikování souborů v *wwwroot* pomocí [ \<Content> položky projektu](/visualstudio/msbuild/common-msbuild-project-items#content) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="1a00a-432">Následující příklad zabraňuje publikování obsahu v adresáři *wwwroot/Local* a v podadresářích:</span><span class="sxs-lookup"><span data-stu-id="1a00a-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="1a00a-433">V Razor souborech (*. cshtml*) odkazuje vlnové lomítko ( `~/` ) na kořenový adresář webu.</span><span class="sxs-lookup"><span data-stu-id="1a00a-433">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="1a00a-434">Cesta začínající `~/` na je označována jako *virtuální cesta*.</span><span class="sxs-lookup"><span data-stu-id="1a00a-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="1a00a-435">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="1a00a-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
