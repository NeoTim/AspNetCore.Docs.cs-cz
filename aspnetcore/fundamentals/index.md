---
title: Základy ASP.NET Core
author: rick-anderson
description: Seznamte se se základními koncepty pro vytváření ASP.NET základních aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
uid: fundamentals/index
ms.openlocfilehash: c675644d8480ef7a5290045067e6cec2ea6f4764
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384064"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="34f48-103">Základy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="34f48-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="34f48-104">Tento článek obsahuje přehled klíčových témat, která popisují, jak vyvíjet ASP.NET základní aplikace.</span><span class="sxs-lookup"><span data-stu-id="34f48-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="34f48-105">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="34f48-105">The Startup class</span></span>

<span data-ttu-id="34f48-106">Třída `Startup` je, kde:</span><span class="sxs-lookup"><span data-stu-id="34f48-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="34f48-107">Služby vyžadované aplikací jsou konfigurovány.</span><span class="sxs-lookup"><span data-stu-id="34f48-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="34f48-108">Kanál zpracování požadavků aplikace je definován jako řada middleware komponent.</span><span class="sxs-lookup"><span data-stu-id="34f48-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="34f48-109">Zde je ukázka `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="34f48-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="34f48-110">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="34f48-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="34f48-111">Injektáž závislostí (služby)</span><span class="sxs-lookup"><span data-stu-id="34f48-111">Dependency injection (services)</span></span>

<span data-ttu-id="34f48-112">ASP.NET Core obsahuje integrované rozhraní pro vkládání závislostí (DI), které zpřístupňuje nakonfigurované služby v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="34f48-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="34f48-113">Například součást protokolování je služba.</span><span class="sxs-lookup"><span data-stu-id="34f48-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="34f48-114">Kód pro konfiguraci (nebo *registr* `Startup.ConfigureServices` ) služby je přidán do metody.</span><span class="sxs-lookup"><span data-stu-id="34f48-114">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="34f48-115">Příklad:</span><span class="sxs-lookup"><span data-stu-id="34f48-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="34f48-116">Služby jsou obvykle vyřešeny z DI pomocí vkládání konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="34f48-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="34f48-117">S vstřikování konstruktoru třída deklaruje parametr konstruktoru požadovaného typu nebo rozhraní.</span><span class="sxs-lookup"><span data-stu-id="34f48-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="34f48-118">Rozhraní DI poskytuje instanci této služby za běhu.</span><span class="sxs-lookup"><span data-stu-id="34f48-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="34f48-119">Následující příklad používá vkládání konstruktoru k vyřešení a `RazorPagesMovieContext` z DI:</span><span class="sxs-lookup"><span data-stu-id="34f48-119">The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="34f48-120">Pokud integrovaný kontejner Inversion of Control (IoC) nesplňuje všechny potřeby aplikace, lze místo toho použít kontejner IoC jiného výrobce.</span><span class="sxs-lookup"><span data-stu-id="34f48-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="34f48-121">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="34f48-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="34f48-122">Middleware</span><span class="sxs-lookup"><span data-stu-id="34f48-122">Middleware</span></span>

<span data-ttu-id="34f48-123">Kanál zpracování požadavků se skládá jako řada middleware komponent.</span><span class="sxs-lookup"><span data-stu-id="34f48-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="34f48-124">Každá komponenta provádí `HttpContext` operace na a buď vyvolá další middleware v kanálu nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="34f48-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="34f48-125">Podle konvence middleware komponenty je přidán do kanálu `Use...` vyvoláním metody `Startup.Configure` rozšíření v metodě.</span><span class="sxs-lookup"><span data-stu-id="34f48-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="34f48-126">Chcete-li například povolit vykreslování statických souborů, volejte `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="34f48-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="34f48-127">Následující příklad konfiguruje kanál zpracování požadavků:</span><span class="sxs-lookup"><span data-stu-id="34f48-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="34f48-128">ASP.NET Core obsahuje bohatou sadu vestavěných middleware.</span><span class="sxs-lookup"><span data-stu-id="34f48-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="34f48-129">Vlastní middleware komponenty mohou být také napsány.</span><span class="sxs-lookup"><span data-stu-id="34f48-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="34f48-130">Další informace naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="34f48-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="34f48-131">Hostitel</span><span class="sxs-lookup"><span data-stu-id="34f48-131">Host</span></span>

<span data-ttu-id="34f48-132">Při spuštění vytvoří aplikace ASP.NET Core *hostitele*.</span><span class="sxs-lookup"><span data-stu-id="34f48-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="34f48-133">Hostitel zapouzdřuje všechny prostředky aplikace, například:</span><span class="sxs-lookup"><span data-stu-id="34f48-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="34f48-134">Implementace http serveru</span><span class="sxs-lookup"><span data-stu-id="34f48-134">An HTTP server implementation</span></span>
* <span data-ttu-id="34f48-135">Komponenty middlewaru</span><span class="sxs-lookup"><span data-stu-id="34f48-135">Middleware components</span></span>
* <span data-ttu-id="34f48-136">Protokolování</span><span class="sxs-lookup"><span data-stu-id="34f48-136">Logging</span></span>
* <span data-ttu-id="34f48-137">Služby vkládání závislostí (DI)</span><span class="sxs-lookup"><span data-stu-id="34f48-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="34f48-138">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="34f48-138">Configuration</span></span>

<span data-ttu-id="34f48-139">Existují dva různí hostitelé:</span><span class="sxs-lookup"><span data-stu-id="34f48-139">There are two different hosts:</span></span> 

* <span data-ttu-id="34f48-140">Obecný hostitel rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="34f48-140">.NET Generic Host</span></span>
* <span data-ttu-id="34f48-141">ASP.NET základní webový hostitel</span><span class="sxs-lookup"><span data-stu-id="34f48-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="34f48-142">Obecný hostitel .NET se doporučuje.</span><span class="sxs-lookup"><span data-stu-id="34f48-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="34f48-143">Základní webový hostitel ASP.NET je k dispozici pouze pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="34f48-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="34f48-144">Následující příklad vytvoří obecný hostitel .NET:</span><span class="sxs-lookup"><span data-stu-id="34f48-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="34f48-145">Metody `CreateDefaultBuilder` `ConfigureWebHostDefaults` a nakonfigurují hostitele se sadou výchozích možností, například:</span><span class="sxs-lookup"><span data-stu-id="34f48-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="34f48-146">Jako webový server použijte [kestrel](#servers) a povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="34f48-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="34f48-147">Načtěte konfiguraci z *souboru appsettings.json*, *appsettings.{ Název prostředí}.json*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="34f48-147">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="34f48-148">Odešlete výstup protokolování do konzoly a zprostředkovatele ladění.</span><span class="sxs-lookup"><span data-stu-id="34f48-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="34f48-149">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="34f48-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="34f48-150">Scénáře mimo web</span><span class="sxs-lookup"><span data-stu-id="34f48-150">Non-web scenarios</span></span>

<span data-ttu-id="34f48-151">Obecný hostitel umožňuje jiným typům aplikací používat rozšíření architektury průřezu, jako je protokolování, vkládání závislostí (DI), konfigurace a správa životnosti aplikací.</span><span class="sxs-lookup"><span data-stu-id="34f48-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="34f48-152">Další informace naleznete v tématech <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="34f48-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="34f48-153">Servery</span><span class="sxs-lookup"><span data-stu-id="34f48-153">Servers</span></span>

<span data-ttu-id="34f48-154">Aplikace ASP.NET Core používá implementaci http serveru k naslouchání požadavkům HTTP.</span><span class="sxs-lookup"><span data-stu-id="34f48-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="34f48-155">Server zobrazí požadavky na aplikaci jako sadu [funkcí](xref:fundamentals/request-features) `HttpContext`požadavků složených do aplikace .</span><span class="sxs-lookup"><span data-stu-id="34f48-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="34f48-156">Windows</span><span class="sxs-lookup"><span data-stu-id="34f48-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="34f48-157">ASP.NET Core poskytuje následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="34f48-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="34f48-158">*Kestrel* je webový server pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="34f48-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="34f48-159">Kestrel je často spuštěn v konfiguraci reverzníproxy pomocí [služby IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="34f48-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="34f48-160">V ASP.NET Core 2.0 nebo novější, Kestrel lze spustit jako veřejný hraniční server vystavený přímo internetu.</span><span class="sxs-lookup"><span data-stu-id="34f48-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="34f48-161">*Server HTTP služby IIS* je server systému Windows, který používá službu IIS.</span><span class="sxs-lookup"><span data-stu-id="34f48-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="34f48-162">S tímto serverem běží aplikace ASP.NET Core a služba IIS ve stejném procesu.</span><span class="sxs-lookup"><span data-stu-id="34f48-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="34f48-163">*HTTP.sys* je server pro systém Windows, který se nepoužívá ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="34f48-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="34f48-164">macOS</span><span class="sxs-lookup"><span data-stu-id="34f48-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="34f48-165">ASP.NET Core poskytuje implementaci serveru *Kestrel* cross-platform.</span><span class="sxs-lookup"><span data-stu-id="34f48-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="34f48-166">V ASP.NET Jádrem 2.0 nebo novějším může Kestrel běžet jako veřejný hraniční server vystavený přímo internetu.</span><span class="sxs-lookup"><span data-stu-id="34f48-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="34f48-167">Poštolka je často spuštěna v reverzní konfiguraci proxy s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="34f48-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="34f48-168">Linux</span><span class="sxs-lookup"><span data-stu-id="34f48-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="34f48-169">ASP.NET Core poskytuje implementaci serveru *Kestrel* cross-platform.</span><span class="sxs-lookup"><span data-stu-id="34f48-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="34f48-170">V ASP.NET Jádrem 2.0 nebo novějším může Kestrel běžet jako veřejný hraniční server vystavený přímo internetu.</span><span class="sxs-lookup"><span data-stu-id="34f48-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="34f48-171">Poštolka je často spuštěna v reverzní konfiguraci proxy s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="34f48-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="34f48-172">Další informace naleznete v tématu <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="34f48-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="34f48-173">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="34f48-173">Configuration</span></span>

<span data-ttu-id="34f48-174">ASP.NET Core poskytuje konfigurační rámec, který získá nastavení jako dvojice název-hodnota z uspořádané sady poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="34f48-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="34f48-175">Předdefinované zprostředkovatele konfigurace jsou k dispozici pro různé zdroje, jako jsou soubory *JSON,* soubory *XML,* proměnné prostředí a argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="34f48-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="34f48-176">Napište vlastní zprostředkovatele konfigurace pro podporu jiných zdrojů.</span><span class="sxs-lookup"><span data-stu-id="34f48-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="34f48-177">[Ve výchozím nastavení](xref:fundamentals/configuration/index#default)jsou aplikace ASP.NET Core nakonfigurovány tak, aby četly z aplikace *appsettings.json*, proměnných prostředí, příkazového řádku a dalších.</span><span class="sxs-lookup"><span data-stu-id="34f48-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *appsettings.json*, environment variables, the command line, and more.</span></span> <span data-ttu-id="34f48-178">Při načtení konfigurace aplikace přepíší hodnoty z proměnných prostředí hodnoty z *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="34f48-178">When the app's configuration is loaded, values from environment variables override values from *appsettings.json*.</span></span>

<span data-ttu-id="34f48-179">Upřednostňovaným způsobem čtení souvisejících hodnot konfigurace je použití [vzoru voleb](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="34f48-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="34f48-180">Další informace naleznete v tématu [Vazba hierarchických konfiguračních dat pomocí vzoru voleb](xref:fundamentals/configuration/index#optpat).</span><span class="sxs-lookup"><span data-stu-id="34f48-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="34f48-181">Pro správu důvěrných konfiguračních dat, jako jsou hesla, ASP.NET Core poskytuje [Správce tajných barev](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="34f48-181">For managing confidential configuration data such as passwords, ASP.NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="34f48-182">Pro produkční tajné kódy doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="34f48-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="34f48-183">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="34f48-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="34f48-184">Prostředí</span><span class="sxs-lookup"><span data-stu-id="34f48-184">Environments</span></span>

<span data-ttu-id="34f48-185">Spuštění prostředí, jako `Development` `Staging`je `Production`například , a , jsou prvotřídní pojem v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="34f48-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="34f48-186">Nastavením proměnné `ASPNETCORE_ENVIRONMENT` prostředí určete prostředí, ve které aplikace běží.</span><span class="sxs-lookup"><span data-stu-id="34f48-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="34f48-187">ASP.NET Core přečte tuto proměnnou prostředí při `IWebHostEnvironment` spuštění aplikace a uloží hodnotu v implementaci.</span><span class="sxs-lookup"><span data-stu-id="34f48-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="34f48-188">Tato implementace je k dispozici kdekoli v aplikaci prostřednictvím vkládání závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="34f48-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="34f48-189">Následující příklad konfiguruje aplikaci tak, `Development` aby poskytovala podrobné informace o chybě při spuštění v prostředí:</span><span class="sxs-lookup"><span data-stu-id="34f48-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="34f48-190">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="34f48-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="34f48-191">Protokolování</span><span class="sxs-lookup"><span data-stu-id="34f48-191">Logging</span></span>

<span data-ttu-id="34f48-192">ASP.NET Core podporuje rozhraní API pro protokolování, které funguje s různými vestavěnými poskytovateli protokolování a poskytovateli protokolování třetích stran.</span><span class="sxs-lookup"><span data-stu-id="34f48-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="34f48-193">Mezi dostupné poskytovatele patří:</span><span class="sxs-lookup"><span data-stu-id="34f48-193">Available providers include:</span></span>

* <span data-ttu-id="34f48-194">Konzola</span><span class="sxs-lookup"><span data-stu-id="34f48-194">Console</span></span>
* <span data-ttu-id="34f48-195">Ladit</span><span class="sxs-lookup"><span data-stu-id="34f48-195">Debug</span></span>
* <span data-ttu-id="34f48-196">Trasování událostí v systému Windows</span><span class="sxs-lookup"><span data-stu-id="34f48-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="34f48-197">Protokol událostí systému Windows</span><span class="sxs-lookup"><span data-stu-id="34f48-197">Windows Event Log</span></span>
* <span data-ttu-id="34f48-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="34f48-198">TraceSource</span></span>
* <span data-ttu-id="34f48-199">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="34f48-199">Azure App Service</span></span>
* <span data-ttu-id="34f48-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="34f48-200">Azure Application Insights</span></span>

<span data-ttu-id="34f48-201">Chcete-li vytvořit protokoly, vyřešit službu <xref:Microsoft.Extensions.Logging.ILogger%601> z vkládání závislostí <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>(DI) a volání metody protokolování, jako je například .</span><span class="sxs-lookup"><span data-stu-id="34f48-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="34f48-202">Příklad:</span><span class="sxs-lookup"><span data-stu-id="34f48-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="34f48-203">Metody protokolování, jako `LogInformation` je například podpora libovolného počtu polí.</span><span class="sxs-lookup"><span data-stu-id="34f48-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="34f48-204">Tato pole se běžně používají `string`k vytvoření zprávy , ale někteří poskytovatelé protokolování je odesílají do úložiště dat jako samostatná pole.</span><span class="sxs-lookup"><span data-stu-id="34f48-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="34f48-205">Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="34f48-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="34f48-206">Další informace naleznete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="34f48-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="34f48-207">Směrování</span><span class="sxs-lookup"><span data-stu-id="34f48-207">Routing</span></span>

<span data-ttu-id="34f48-208">*Trasa* je vzor adresy URL, který je mapován na obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="34f48-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="34f48-209">Obslužná rutina je obvykle Razor stránky, metoda akce v řadiči MVC nebo middleware.</span><span class="sxs-lookup"><span data-stu-id="34f48-209">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="34f48-210">ASP.NET základní směrování vám dává kontrolu nad adresami URL používanými vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="34f48-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="34f48-211">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="34f48-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="34f48-212">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="34f48-212">Error handling</span></span>

<span data-ttu-id="34f48-213">ASP.NET Core má vestavěné funkce pro zpracování chyb, jako jsou:</span><span class="sxs-lookup"><span data-stu-id="34f48-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="34f48-214">Stránka výjimky pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="34f48-214">A developer exception page</span></span>
* <span data-ttu-id="34f48-215">Vlastní chybové stránky</span><span class="sxs-lookup"><span data-stu-id="34f48-215">Custom error pages</span></span>
* <span data-ttu-id="34f48-216">Statické stavové kódové stránky</span><span class="sxs-lookup"><span data-stu-id="34f48-216">Static status code pages</span></span>
* <span data-ttu-id="34f48-217">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="34f48-217">Startup exception handling</span></span>

<span data-ttu-id="34f48-218">Další informace naleznete v tématu <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="34f48-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="34f48-219">Vytváření požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="34f48-219">Make HTTP requests</span></span>

<span data-ttu-id="34f48-220">Implementace `IHttpClientFactory` je k dispozici `HttpClient` pro vytváření instancí.</span><span class="sxs-lookup"><span data-stu-id="34f48-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="34f48-221">Továrna:</span><span class="sxs-lookup"><span data-stu-id="34f48-221">The factory:</span></span>

* <span data-ttu-id="34f48-222">Poskytuje centrální umístění pro pojmenování `HttpClient` a konfiguraci logických instancí.</span><span class="sxs-lookup"><span data-stu-id="34f48-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="34f48-223">Například zaregistrujte a nakonfigurujte klienta *githubpro* přístup k GitHubu.</span><span class="sxs-lookup"><span data-stu-id="34f48-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="34f48-224">Zaregistrujte a nakonfigurujte výchozího klienta pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="34f48-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="34f48-225">Podporuje registraci a řetězení více delegování obslužné rutiny k vytvoření kanálu middleware odchozí požadavek.</span><span class="sxs-lookup"><span data-stu-id="34f48-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="34f48-226">Tento vzor je podobný ASP.NET příchozím middlewarovým kanálem core.</span><span class="sxs-lookup"><span data-stu-id="34f48-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="34f48-227">Vzor poskytuje mechanismus pro správu průřezové obavy pro požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="34f48-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="34f48-228">Integruje se s *Polly*, oblíbenou knihovnou třetích stran pro přechodnou manipulaci s chybami.</span><span class="sxs-lookup"><span data-stu-id="34f48-228">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="34f48-229">Spravuje sdružování a `HttpClientHandler` životnost základních instancí, aby `HttpClient` se zabránilo běžným problémům DNS, ke kterým dochází při ruční správě životnosti.</span><span class="sxs-lookup"><span data-stu-id="34f48-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="34f48-230">Přidá konfigurovatelné prostředí <xref:Microsoft.Extensions.Logging.ILogger> protokolování prostřednictvím všech požadavků odeslaných prostřednictvím klientů vytvořených výrobcem.</span><span class="sxs-lookup"><span data-stu-id="34f48-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="34f48-231">Další informace naleznete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="34f48-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="34f48-232">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="34f48-232">Content root</span></span>

<span data-ttu-id="34f48-233">Kořen obsahu je základní cesta pro:</span><span class="sxs-lookup"><span data-stu-id="34f48-233">The content root is the base path for:</span></span>

* <span data-ttu-id="34f48-234">Spustitelný soubor hostující aplikaci (*.exe*).</span><span class="sxs-lookup"><span data-stu-id="34f48-234">The executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="34f48-235">Kompilovaná sestavení, která tvoří aplikaci (*DLL*).</span><span class="sxs-lookup"><span data-stu-id="34f48-235">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="34f48-236">Soubory obsahu používané aplikací, například:</span><span class="sxs-lookup"><span data-stu-id="34f48-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="34f48-237">Razor soubory (*.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="34f48-237">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="34f48-238">Konfigurační soubory (*.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="34f48-238">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="34f48-239">Datové soubory (*.db*)</span><span class="sxs-lookup"><span data-stu-id="34f48-239">Data files (*.db*)</span></span>
* <span data-ttu-id="34f48-240">[Kořenový adresář webu](#web-root), obvykle složka *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="34f48-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="34f48-241">Během vývoje je kořenový obsah výchozí kořenový adresář projektu.</span><span class="sxs-lookup"><span data-stu-id="34f48-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="34f48-242">Tento adresář je také základní cestou pro soubory obsahu aplikace i [pro kořenový adresář webu](#web-root).</span><span class="sxs-lookup"><span data-stu-id="34f48-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="34f48-243">Zadejte jiný kořen obsahu nastavením jeho cesty při [vytváření hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="34f48-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="34f48-244">Další informace naleznete v [tématu Kořenový obsah](xref:fundamentals/host/generic-host#contentroot).</span><span class="sxs-lookup"><span data-stu-id="34f48-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentroot).</span></span>

## <a name="web-root"></a><span data-ttu-id="34f48-245">Kořenový adresář webu</span><span class="sxs-lookup"><span data-stu-id="34f48-245">Web root</span></span>

<span data-ttu-id="34f48-246">Kořenový adresář webu je základní cesta pro veřejné statické soubory prostředků, například:</span><span class="sxs-lookup"><span data-stu-id="34f48-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="34f48-247">Šablony stylů (*.css*)</span><span class="sxs-lookup"><span data-stu-id="34f48-247">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="34f48-248">JavaScript (*.js*)</span><span class="sxs-lookup"><span data-stu-id="34f48-248">JavaScript (*.js*)</span></span>
* <span data-ttu-id="34f48-249">Obrázky (*.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="34f48-249">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="34f48-250">Ve výchozím nastavení jsou statické soubory obsluhovány pouze z kořenového adresáře webu a jeho podadresářů.</span><span class="sxs-lookup"><span data-stu-id="34f48-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="34f48-251">Webová kořenová cesta je výchozí na *{content root}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="34f48-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="34f48-252">Zadejte jiný kořen webu nastavením jeho cesty při [vytváření hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="34f48-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="34f48-253">Další informace naleznete v [tématu Kořenový adresář webu](xref:fundamentals/host/generic-host#webroot).</span><span class="sxs-lookup"><span data-stu-id="34f48-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot).</span></span>

<span data-ttu-id="34f48-254">Zabránit publikování souborů v *wwwroot* s [ \<položkou projektu Content>](/visualstudio/msbuild/common-msbuild-project-items#content) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="34f48-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="34f48-255">Následující příklad brání publikování obsahu v *wwwroot/local* a jeho podadresářích:</span><span class="sxs-lookup"><span data-stu-id="34f48-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="34f48-256">V souborech Razor *.cshtml* ukazuje`~/`vlnovka ( ) na kořenový adresář webu.</span><span class="sxs-lookup"><span data-stu-id="34f48-256">In Razor *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="34f48-257">Cesta začínající `~/` programem se označuje jako *virtuální cesta*.</span><span class="sxs-lookup"><span data-stu-id="34f48-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="34f48-258">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="34f48-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="34f48-259">Tento článek je přehled klíčových témat pro pochopení, jak vyvíjet ASP.NET základní aplikace.</span><span class="sxs-lookup"><span data-stu-id="34f48-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="34f48-260">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="34f48-260">The Startup class</span></span>

<span data-ttu-id="34f48-261">Třída `Startup` je, kde:</span><span class="sxs-lookup"><span data-stu-id="34f48-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="34f48-262">Služby vyžadované aplikací jsou konfigurovány.</span><span class="sxs-lookup"><span data-stu-id="34f48-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="34f48-263">Je definován kanál zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="34f48-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="34f48-264">*Služby* jsou součásti, které aplikace používá.</span><span class="sxs-lookup"><span data-stu-id="34f48-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="34f48-265">Například součást protokolování je služba.</span><span class="sxs-lookup"><span data-stu-id="34f48-265">For example, a logging component is a service.</span></span> <span data-ttu-id="34f48-266">Kód pro konfiguraci (nebo *registr* `Startup.ConfigureServices` ) služby je přidán do metody.</span><span class="sxs-lookup"><span data-stu-id="34f48-266">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="34f48-267">Kanál zpracování požadavků se skládá jako řada *middleware* komponent.</span><span class="sxs-lookup"><span data-stu-id="34f48-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="34f48-268">Middleware může například zpracovávat požadavky na statické soubory nebo přesměrovat požadavky HTTP na protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="34f48-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="34f48-269">Každý middleware provádí asynchronní `HttpContext` operace na a potom buď vyvolá další middleware v kanálu nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="34f48-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="34f48-270">Kód pro konfiguraci kanálu zpracování `Startup.Configure` požadavků je přidán do metody.</span><span class="sxs-lookup"><span data-stu-id="34f48-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="34f48-271">Zde je ukázka `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="34f48-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="34f48-272">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="34f48-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="34f48-273">Injektáž závislostí (služby)</span><span class="sxs-lookup"><span data-stu-id="34f48-273">Dependency injection (services)</span></span>

<span data-ttu-id="34f48-274">ASP.NET Core má integrované rozhraní vkládání závislostí (DI), které zpřístupňuje nakonfigurované služby pro třídy aplikace.</span><span class="sxs-lookup"><span data-stu-id="34f48-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="34f48-275">Jedním ze způsobů, jak získat instanci služby ve třídě, je vytvoření konstruktoru s parametrem požadovaného typu.</span><span class="sxs-lookup"><span data-stu-id="34f48-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="34f48-276">Parametr emituje typ služby nebo rozhraní.</span><span class="sxs-lookup"><span data-stu-id="34f48-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="34f48-277">Systém DI poskytuje službu za běhu.</span><span class="sxs-lookup"><span data-stu-id="34f48-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="34f48-278">Tady je třída, která používá DI k získání objektu kontextu Core entity frameworku.</span><span class="sxs-lookup"><span data-stu-id="34f48-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="34f48-279">Zvýrazněná čára je příkladem vstřikování konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="34f48-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="34f48-280">Zatímco DI je vestavěný, je navržen tak, aby vám umožní připojit kontejner Inversion of Control (IoC) třetí strany, pokud dáváte přednost.</span><span class="sxs-lookup"><span data-stu-id="34f48-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="34f48-281">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="34f48-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="34f48-282">Middleware</span><span class="sxs-lookup"><span data-stu-id="34f48-282">Middleware</span></span>

<span data-ttu-id="34f48-283">Kanál zpracování požadavků se skládá jako řada middleware komponent.</span><span class="sxs-lookup"><span data-stu-id="34f48-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="34f48-284">Každá komponenta provádí asynchronní `HttpContext` operace na a potom buď vyvolá další middleware v kanálu nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="34f48-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="34f48-285">Podle konvence middleware komponenty je přidán do kanálu `Use...` vyvoláním jeho `Startup.Configure` rozšíření metody v metodě.</span><span class="sxs-lookup"><span data-stu-id="34f48-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="34f48-286">Chcete-li například povolit vykreslování statických souborů, volejte `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="34f48-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="34f48-287">Zvýrazněný kód v následujícím příkladu konfiguruje kanál zpracování požadavků:</span><span class="sxs-lookup"><span data-stu-id="34f48-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="34f48-288">ASP.NET Core obsahuje bohatou sadu vestavěných middleware, a můžete psát vlastní middleware.</span><span class="sxs-lookup"><span data-stu-id="34f48-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="34f48-289">Další informace naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="34f48-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="34f48-290">Hostitel</span><span class="sxs-lookup"><span data-stu-id="34f48-290">Host</span></span>

<span data-ttu-id="34f48-291">Aplikace ASP.NET Core vytvoří *hostitele* při spuštění.</span><span class="sxs-lookup"><span data-stu-id="34f48-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="34f48-292">Hostitel je objekt, který zapouzdřuje všechny prostředky aplikace, například:</span><span class="sxs-lookup"><span data-stu-id="34f48-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="34f48-293">Implementace http serveru</span><span class="sxs-lookup"><span data-stu-id="34f48-293">An HTTP server implementation</span></span>
* <span data-ttu-id="34f48-294">Komponenty middlewaru</span><span class="sxs-lookup"><span data-stu-id="34f48-294">Middleware components</span></span>
* <span data-ttu-id="34f48-295">Protokolování</span><span class="sxs-lookup"><span data-stu-id="34f48-295">Logging</span></span>
* <span data-ttu-id="34f48-296">Di</span><span class="sxs-lookup"><span data-stu-id="34f48-296">DI</span></span>
* <span data-ttu-id="34f48-297">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="34f48-297">Configuration</span></span>

<span data-ttu-id="34f48-298">Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je správa životnosti: kontrola nad spuštěním aplikace a bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="34f48-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="34f48-299">K dispozici jsou dva hostitelé: webový hostitel a obecný hostitel.</span><span class="sxs-lookup"><span data-stu-id="34f48-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="34f48-300">V ASP.NET Core 2.x, obecný hostitel je pouze pro scénáře mimo web.</span><span class="sxs-lookup"><span data-stu-id="34f48-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="34f48-301">Kód pro vytvoření hostitele `Program.Main`je v :</span><span class="sxs-lookup"><span data-stu-id="34f48-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="34f48-302">Metoda `CreateDefaultBuilder` konfiguruje hostitele s běžně používanými možnostmi, například následujícími:</span><span class="sxs-lookup"><span data-stu-id="34f48-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="34f48-303">Jako webový server použijte [kestrel](#servers) a povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="34f48-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="34f48-304">Načtěte konfiguraci z *souboru appsettings.json*, *appsettings.{ Název prostředí}.json*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="34f48-304">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="34f48-305">Odešlete výstup protokolování do konzoly a zprostředkovatele ladění.</span><span class="sxs-lookup"><span data-stu-id="34f48-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="34f48-306">Další informace naleznete v tématu <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="34f48-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="34f48-307">Scénáře mimo web</span><span class="sxs-lookup"><span data-stu-id="34f48-307">Non-web scenarios</span></span>

<span data-ttu-id="34f48-308">Obecný hostitel umožňuje jiným typům aplikací používat rozšíření architektury průřezu, jako je protokolování, vkládání závislostí (DI), konfigurace a správa životnosti aplikací.</span><span class="sxs-lookup"><span data-stu-id="34f48-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="34f48-309">Další informace naleznete v tématech <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="34f48-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="34f48-310">Servery</span><span class="sxs-lookup"><span data-stu-id="34f48-310">Servers</span></span>

<span data-ttu-id="34f48-311">Aplikace ASP.NET Core používá implementaci http serveru k naslouchání požadavkům HTTP.</span><span class="sxs-lookup"><span data-stu-id="34f48-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="34f48-312">Server zobrazí požadavky na aplikaci jako sadu [funkcí](xref:fundamentals/request-features) `HttpContext`požadavků složených do aplikace .</span><span class="sxs-lookup"><span data-stu-id="34f48-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="34f48-313">Windows</span><span class="sxs-lookup"><span data-stu-id="34f48-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="34f48-314">ASP.NET Core poskytuje následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="34f48-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="34f48-315">*Kestrel* je webový server pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="34f48-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="34f48-316">Kestrel je často spuštěn v konfiguraci reverzníproxy pomocí [služby IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="34f48-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="34f48-317">Poštolka lze spustit jako veřejný hraniční server vystavený přímo internetu.</span><span class="sxs-lookup"><span data-stu-id="34f48-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="34f48-318">*Server HTTP služby IIS* je server pro systém Windows, který používá službu IIS.</span><span class="sxs-lookup"><span data-stu-id="34f48-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="34f48-319">S tímto serverem běží aplikace ASP.NET Core a služba IIS ve stejném procesu.</span><span class="sxs-lookup"><span data-stu-id="34f48-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="34f48-320">*HTTP.sys* je server pro systém Windows, který se nepoužívá ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="34f48-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="34f48-321">macOS</span><span class="sxs-lookup"><span data-stu-id="34f48-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="34f48-322">ASP.NET Core poskytuje implementaci serveru *Kestrel* cross-platform.</span><span class="sxs-lookup"><span data-stu-id="34f48-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="34f48-323">Poštolka lze spustit jako veřejný hraniční server vystavený přímo internetu.</span><span class="sxs-lookup"><span data-stu-id="34f48-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="34f48-324">Poštolka je často spuštěna v reverzní konfiguraci proxy s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="34f48-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="34f48-325">Linux</span><span class="sxs-lookup"><span data-stu-id="34f48-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="34f48-326">ASP.NET Core poskytuje implementaci serveru *Kestrel* cross-platform.</span><span class="sxs-lookup"><span data-stu-id="34f48-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="34f48-327">Poštolka lze spustit jako veřejný hraniční server vystavený přímo internetu.</span><span class="sxs-lookup"><span data-stu-id="34f48-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="34f48-328">Poštolka je často spuštěna v reverzní konfiguraci proxy s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="34f48-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="34f48-329">Windows</span><span class="sxs-lookup"><span data-stu-id="34f48-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="34f48-330">ASP.NET Core poskytuje následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="34f48-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="34f48-331">*Kestrel* je webový server pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="34f48-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="34f48-332">Kestrel je často spuštěn v konfiguraci reverzníproxy pomocí [služby IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="34f48-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="34f48-333">Poštolka lze spustit jako veřejný hraniční server vystavený přímo internetu.</span><span class="sxs-lookup"><span data-stu-id="34f48-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="34f48-334">*HTTP.sys* je server pro systém Windows, který se nepoužívá ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="34f48-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="34f48-335">macOS</span><span class="sxs-lookup"><span data-stu-id="34f48-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="34f48-336">ASP.NET Core poskytuje implementaci serveru *Kestrel* cross-platform.</span><span class="sxs-lookup"><span data-stu-id="34f48-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="34f48-337">Poštolka lze spustit jako veřejný hraniční server vystavený přímo internetu.</span><span class="sxs-lookup"><span data-stu-id="34f48-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="34f48-338">Poštolka je často spuštěna v reverzní konfiguraci proxy s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="34f48-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="34f48-339">Linux</span><span class="sxs-lookup"><span data-stu-id="34f48-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="34f48-340">ASP.NET Core poskytuje implementaci serveru *Kestrel* cross-platform.</span><span class="sxs-lookup"><span data-stu-id="34f48-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="34f48-341">Poštolka lze spustit jako veřejný hraniční server vystavený přímo internetu.</span><span class="sxs-lookup"><span data-stu-id="34f48-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="34f48-342">Poštolka je často spuštěna v reverzní konfiguraci proxy s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="34f48-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="34f48-343">Další informace naleznete v tématu <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="34f48-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="34f48-344">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="34f48-344">Configuration</span></span>

<span data-ttu-id="34f48-345">ASP.NET Core poskytuje konfigurační rámec, který získá nastavení jako dvojice název-hodnota z uspořádané sady poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="34f48-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="34f48-346">Existují předdefinovaní zprostředkovatelé konfigurace pro různé zdroje, jako jsou soubory *JSON,* soubory *XML,* proměnné prostředí a argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="34f48-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="34f48-347">Můžete také psát vlastní zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="34f48-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="34f48-348">Můžete například určit, že konfigurace pochází z *proměnných appsettings.json* a prostředí.</span><span class="sxs-lookup"><span data-stu-id="34f48-348">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="34f48-349">Potom při požadavku na hodnotu *ConnectionString,* rozhraní nejprve vypadá v souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="34f48-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="34f48-350">Pokud je hodnota nalezena tam, ale také v proměnné prostředí, hodnota z proměnné prostředí by mít přednost.</span><span class="sxs-lookup"><span data-stu-id="34f48-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="34f48-351">Pro správu důvěrných konfiguračních dat, jako jsou hesla, ASP.NET Core poskytuje [nástroj Správce tajných barev](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="34f48-351">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="34f48-352">Pro produkční tajné kódy doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="34f48-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="34f48-353">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="34f48-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="34f48-354">Možnosti</span><span class="sxs-lookup"><span data-stu-id="34f48-354">Options</span></span>

<span data-ttu-id="34f48-355">Pokud je to možné, ASP.NET Jádro se řídí *vzorem možností* pro ukládání a načítání hodnot konfigurace.</span><span class="sxs-lookup"><span data-stu-id="34f48-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="34f48-356">Vzor voleb používá třídy k reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="34f48-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="34f48-357">Například následující kód nastavuje možnosti WebSockets:</span><span class="sxs-lookup"><span data-stu-id="34f48-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="34f48-358">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="34f48-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="34f48-359">Prostředí</span><span class="sxs-lookup"><span data-stu-id="34f48-359">Environments</span></span>

<span data-ttu-id="34f48-360">Spuštění prostředí, jako je *například vývoj*, *staging*a *výroba*, jsou prvotřídní pojem v ASP.NET core.</span><span class="sxs-lookup"><span data-stu-id="34f48-360">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="34f48-361">Prostředí, ve které aplikace běží, můžete `ASPNETCORE_ENVIRONMENT` určit nastavením proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="34f48-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="34f48-362">ASP.NET Core přečte tuto proměnnou prostředí při `IHostingEnvironment` spuštění aplikace a uloží hodnotu v implementaci.</span><span class="sxs-lookup"><span data-stu-id="34f48-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="34f48-363">Objekt prostředí je k dispozici kdekoli v aplikaci přes DI.</span><span class="sxs-lookup"><span data-stu-id="34f48-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="34f48-364">Následující ukázkový kód `Startup` z třídy konfiguruje aplikaci tak, aby poskytovala podrobné informace o chybě pouze při spuštění ve vývoji:</span><span class="sxs-lookup"><span data-stu-id="34f48-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="34f48-365">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="34f48-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="34f48-366">Protokolování</span><span class="sxs-lookup"><span data-stu-id="34f48-366">Logging</span></span>

<span data-ttu-id="34f48-367">ASP.NET Core podporuje rozhraní API pro protokolování, které funguje s různými vestavěnými poskytovateli protokolování a poskytovateli protokolování třetích stran.</span><span class="sxs-lookup"><span data-stu-id="34f48-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="34f48-368">Mezi dostupné poskytovatele patří následující:</span><span class="sxs-lookup"><span data-stu-id="34f48-368">Available providers include the following:</span></span>

* <span data-ttu-id="34f48-369">Konzola</span><span class="sxs-lookup"><span data-stu-id="34f48-369">Console</span></span>
* <span data-ttu-id="34f48-370">Ladit</span><span class="sxs-lookup"><span data-stu-id="34f48-370">Debug</span></span>
* <span data-ttu-id="34f48-371">Trasování událostí v systému Windows</span><span class="sxs-lookup"><span data-stu-id="34f48-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="34f48-372">Protokol událostí systému Windows</span><span class="sxs-lookup"><span data-stu-id="34f48-372">Windows Event Log</span></span>
* <span data-ttu-id="34f48-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="34f48-373">TraceSource</span></span>
* <span data-ttu-id="34f48-374">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="34f48-374">Azure App Service</span></span>
* <span data-ttu-id="34f48-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="34f48-375">Azure Application Insights</span></span>

<span data-ttu-id="34f48-376">Zápis protokolů z libovolného místa v `ILogger` kódu aplikace získáním objektu z DI a volání mj.</span><span class="sxs-lookup"><span data-stu-id="34f48-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="34f48-377">Zde je ukázkový kód, který používá `ILogger` objekt, s vkládání konstruktoru a volání metody protokolování zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="34f48-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="34f48-378">Rozhraní `ILogger` umožňuje předat libovolný počet polí zprostředkovateli protokolování.</span><span class="sxs-lookup"><span data-stu-id="34f48-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="34f48-379">Pole se běžně používají k vytvoření řetězce zprávy, ale zprostředkovatel je může také odeslat jako samostatná pole do úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="34f48-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="34f48-380">Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="34f48-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="34f48-381">Další informace naleznete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="34f48-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="34f48-382">Směrování</span><span class="sxs-lookup"><span data-stu-id="34f48-382">Routing</span></span>

<span data-ttu-id="34f48-383">*Trasa* je vzor adresy URL, který je mapován na obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="34f48-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="34f48-384">Obslužná rutina je obvykle Razor stránky, metoda akce v řadiči MVC nebo middleware.</span><span class="sxs-lookup"><span data-stu-id="34f48-384">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="34f48-385">ASP.NET základní směrování vám dává kontrolu nad adresami URL používanými vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="34f48-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="34f48-386">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="34f48-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="34f48-387">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="34f48-387">Error handling</span></span>

<span data-ttu-id="34f48-388">ASP.NET Core má vestavěné funkce pro zpracování chyb, jako jsou:</span><span class="sxs-lookup"><span data-stu-id="34f48-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="34f48-389">Stránka výjimky pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="34f48-389">A developer exception page</span></span>
* <span data-ttu-id="34f48-390">Vlastní chybové stránky</span><span class="sxs-lookup"><span data-stu-id="34f48-390">Custom error pages</span></span>
* <span data-ttu-id="34f48-391">Statické stavové kódové stránky</span><span class="sxs-lookup"><span data-stu-id="34f48-391">Static status code pages</span></span>
* <span data-ttu-id="34f48-392">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="34f48-392">Startup exception handling</span></span>

<span data-ttu-id="34f48-393">Další informace naleznete v tématu <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="34f48-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="34f48-394">Vytváření požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="34f48-394">Make HTTP requests</span></span>

<span data-ttu-id="34f48-395">Implementace `IHttpClientFactory` je k dispozici `HttpClient` pro vytváření instancí.</span><span class="sxs-lookup"><span data-stu-id="34f48-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="34f48-396">Továrna:</span><span class="sxs-lookup"><span data-stu-id="34f48-396">The factory:</span></span>

* <span data-ttu-id="34f48-397">Poskytuje centrální umístění pro pojmenování `HttpClient` a konfiguraci logických instancí.</span><span class="sxs-lookup"><span data-stu-id="34f48-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="34f48-398">Například klient *githubu* může být registrován a nakonfigurován pro přístup k GitHubu.</span><span class="sxs-lookup"><span data-stu-id="34f48-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="34f48-399">Výchozího klienta lze zaregistrovat pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="34f48-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="34f48-400">Podporuje registraci a řetězení více delegování obslužné rutiny k vytvoření kanálu middleware odchozí požadavek.</span><span class="sxs-lookup"><span data-stu-id="34f48-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="34f48-401">Tento vzor je podobný příchozí middleware kanálu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="34f48-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="34f48-402">Vzor poskytuje mechanismus pro správu průřezové obavy kolem požadavků HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="34f48-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="34f48-403">Integruje se s *Polly*, oblíbenou knihovnou třetích stran pro přechodnou manipulaci s chybami.</span><span class="sxs-lookup"><span data-stu-id="34f48-403">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="34f48-404">Spravuje sdružování a `HttpClientHandler` životnost základních instancí, aby se `HttpClient` zabránilo běžným problémům DNS, ke kterým dochází při ruční správě životnosti.</span><span class="sxs-lookup"><span data-stu-id="34f48-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="34f48-405">Přidá konfigurovatelné prostředí protokolování (přes) `ILogger`pro všechny požadavky odeslané prostřednictvím klientů vytvořených výrobcem.</span><span class="sxs-lookup"><span data-stu-id="34f48-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="34f48-406">Další informace naleznete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="34f48-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="34f48-407">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="34f48-407">Content root</span></span>

<span data-ttu-id="34f48-408">Kořen obsahu je základní cesta k:</span><span class="sxs-lookup"><span data-stu-id="34f48-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="34f48-409">Spustitelný soubor hostující aplikaci (*.exe*).</span><span class="sxs-lookup"><span data-stu-id="34f48-409">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="34f48-410">Kompilovaná sestavení, která tvoří aplikaci (*DLL*).</span><span class="sxs-lookup"><span data-stu-id="34f48-410">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="34f48-411">Soubory obsahu, které aplikace nepoužívá kód, například:</span><span class="sxs-lookup"><span data-stu-id="34f48-411">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="34f48-412">Razor soubory (*.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="34f48-412">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="34f48-413">Konfigurační soubory (*.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="34f48-413">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="34f48-414">Datové soubory (*.db*)</span><span class="sxs-lookup"><span data-stu-id="34f48-414">Data files (*.db*)</span></span>
* <span data-ttu-id="34f48-415">[Kořenový adresář webu](#web-root), obvykle publikovaná složka *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="34f48-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="34f48-416">Během vývoje:</span><span class="sxs-lookup"><span data-stu-id="34f48-416">During development:</span></span>

* <span data-ttu-id="34f48-417">Kořenový obsah je výchozí pro kořenový adresář projektu.</span><span class="sxs-lookup"><span data-stu-id="34f48-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="34f48-418">Kořenový adresář projektu se používá k vytvoření:</span><span class="sxs-lookup"><span data-stu-id="34f48-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="34f48-419">Cesta k souborům obsahu aplikace bez kódu v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="34f48-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="34f48-420">[Kořenový adresář webu](#web-root), obvykle složka *wwwroot* v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="34f48-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="34f48-421">Alternativní kořenová cesta obsahu může být [zadána při vytváření hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="34f48-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="34f48-422">Další informace naleznete v tématu <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="34f48-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="34f48-423">Kořenový adresář webu</span><span class="sxs-lookup"><span data-stu-id="34f48-423">Web root</span></span>

<span data-ttu-id="34f48-424">Kořenový adresář webu je základní cesta k veřejným, nekódovým, statickým souborům prostředků, například:</span><span class="sxs-lookup"><span data-stu-id="34f48-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="34f48-425">Šablony stylů (*.css*)</span><span class="sxs-lookup"><span data-stu-id="34f48-425">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="34f48-426">JavaScript (*.js*)</span><span class="sxs-lookup"><span data-stu-id="34f48-426">JavaScript (*.js*)</span></span>
* <span data-ttu-id="34f48-427">Obrázky (*.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="34f48-427">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="34f48-428">Statické soubory jsou ve výchozím nastavení obsluhovány pouze z kořenového adresáře (a podadresářů).</span><span class="sxs-lookup"><span data-stu-id="34f48-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="34f48-429">Web root cesta výchozí *{content root}/wwwroot*, ale jiný kořen ový web lze zadat při [vytváření hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="34f48-429">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="34f48-430">Další informace naleznete v [tématu Kořenový adresář webu](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="34f48-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="34f48-431">Zabránit publikování souborů v *wwwroot* s [ \<položkou projektu Content>](/visualstudio/msbuild/common-msbuild-project-items#content) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="34f48-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="34f48-432">Následující příklad brání publikování obsahu v adresáři *wwwroot/local* a podadresářích:</span><span class="sxs-lookup"><span data-stu-id="34f48-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="34f48-433">V souborech Razor (*.cshtml*)`~/`ukazuje vlnoboze ( ) na kořen webu.</span><span class="sxs-lookup"><span data-stu-id="34f48-433">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="34f48-434">Cesta začínající `~/` programem se označuje jako *virtuální cesta*.</span><span class="sxs-lookup"><span data-stu-id="34f48-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="34f48-435">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="34f48-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
