---
title: Základy ASP.NET Core
author: rick-anderson
description: Seznamte se se základními koncepty pro vytváření aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: fundamentals/index
ms.openlocfilehash: 9c7bc25d813ad17825ef03f5176882993cc2dd63
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610325"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="011b2-103">Základy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="011b2-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="011b2-104">Tento článek je přehled klíčových témat pro pochopení způsobu vývoje ASP.NET Core aplikací.</span><span class="sxs-lookup"><span data-stu-id="011b2-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="011b2-105">Třída Startup</span><span class="sxs-lookup"><span data-stu-id="011b2-105">The Startup class</span></span>

<span data-ttu-id="011b2-106">Třída `Startup` je místo, kde:</span><span class="sxs-lookup"><span data-stu-id="011b2-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="011b2-107">Jsou nakonfigurovány všechny služby požadované aplikací.</span><span class="sxs-lookup"><span data-stu-id="011b2-107">Any services required by the app are configured.</span></span>
* <span data-ttu-id="011b2-108">Je definován kanál pro zpracování požadavků</span><span class="sxs-lookup"><span data-stu-id="011b2-108">The request handling pipeline is defined.</span></span>

* <span data-ttu-id="011b2-109">Kód pro konfiguraci (nebo *registraci*) služeb je přidán do metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="011b2-109">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="011b2-110">*Služby* jsou komponenty, které se používají v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="011b2-110">*Services* are components that are used by the app.</span></span> <span data-ttu-id="011b2-111">Například kontextový objekt Entity Framework Core je takovou službou.</span><span class="sxs-lookup"><span data-stu-id="011b2-111">For example, an Entity Framework Core context object is a service.</span></span>
* <span data-ttu-id="011b2-112">Kód pro konfiguraci kanálu pro zpracování požadavků je přidán do metody `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="011b2-112">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span> <span data-ttu-id="011b2-113">Kanál se skládá z řady *middlewarových* komponent.</span><span class="sxs-lookup"><span data-stu-id="011b2-113">The pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="011b2-114">Middleware může třeba obsluhovat požadavky na statické soubory nebo přesměrovávání požadavků HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="011b2-114">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="011b2-115">Každý middleware provádí asynchronní operace na kontextu `HttpContext` a následně vyvolá další middleware v kanálu nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="011b2-115">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="011b2-116">Zde je ukázka třídy `Startup`:</span><span class="sxs-lookup"><span data-stu-id="011b2-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="011b2-117">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="011b2-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="011b2-118">Vkládání závislostí (služby)</span><span class="sxs-lookup"><span data-stu-id="011b2-118">Dependency injection (services)</span></span>

<span data-ttu-id="011b2-119">ASP.NET Core má integrovanou architekturu pro vkládání závislostí (Dependency Injection, DI), která zpřístupňuje nakonfigurované služby třídám vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="011b2-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="011b2-120">Jedním ze způsobů, jak získat instanci služby ve třídě, je vytvoření konstruktoru s parametrem požadovaného typu.</span><span class="sxs-lookup"><span data-stu-id="011b2-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="011b2-121">Tento parametr může mít typ požadované služby nebo jejího rozhraní.</span><span class="sxs-lookup"><span data-stu-id="011b2-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="011b2-122">Systém vkládání závislostí poskytuje služby za běhu.</span><span class="sxs-lookup"><span data-stu-id="011b2-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="011b2-123">Zde je třída, která využívá DI pro získání kontextového objektu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="011b2-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="011b2-124">Zvýrazněný řádek je příklad konstruktorového vkládání:</span><span class="sxs-lookup"><span data-stu-id="011b2-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="011b2-125">Přestože je vkládání závislostí integrováno v architektuře, je navrženo tak, aby bylo možné použít kontejner IOC (Inversion of Control) třetích stran, pokud tomu dáváte přednost.</span><span class="sxs-lookup"><span data-stu-id="011b2-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="011b2-126">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="011b2-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="011b2-127">Middleware</span><span class="sxs-lookup"><span data-stu-id="011b2-127">Middleware</span></span>

<span data-ttu-id="011b2-128">Kanál pro zpracování požadavků se skládá z řady middlewarových komponent.</span><span class="sxs-lookup"><span data-stu-id="011b2-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="011b2-129">Jednotlivé komponenty provádí asynchronní operace na kontextu `HttpContext` a následně vyvolají další middleware v kanálu nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="011b2-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="011b2-130">Konvenčně se komponenty middlewaru přidávají do kanálu zavoláním rozšiřující metody `Use...` v metodě `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="011b2-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="011b2-131">Pro povolení vykreslování statických souborů se například volá metoda `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="011b2-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="011b2-132">Zvýrazněný kód v následujícím příkladu nastaví kanál pro zpracování požadavku:</span><span class="sxs-lookup"><span data-stu-id="011b2-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="011b2-133">ASP.NET Core obsahuje bohatou sadu integrovaných middleware a zároveň je možné napsat také vlastní middleware.</span><span class="sxs-lookup"><span data-stu-id="011b2-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="011b2-134">Další informace naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="011b2-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

<a id="host"/>

## <a name="the-host"></a><span data-ttu-id="011b2-135">Hostitel</span><span class="sxs-lookup"><span data-stu-id="011b2-135">The host</span></span>

<span data-ttu-id="011b2-136">ASP.NET Core aplikace vytváří *hostitele* při spuštění.</span><span class="sxs-lookup"><span data-stu-id="011b2-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="011b2-137">Hostitel je objekt, který zapouzdřuje všechny prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="011b2-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="011b2-138">Implementaci HTTP serveru</span><span class="sxs-lookup"><span data-stu-id="011b2-138">An HTTP server implementation</span></span>
* <span data-ttu-id="011b2-139">Middlewarové komponenty</span><span class="sxs-lookup"><span data-stu-id="011b2-139">Middleware components</span></span>
* <span data-ttu-id="011b2-140">Protokolování</span><span class="sxs-lookup"><span data-stu-id="011b2-140">Logging</span></span>
* <span data-ttu-id="011b2-141">DI</span><span class="sxs-lookup"><span data-stu-id="011b2-141">DI</span></span>
* <span data-ttu-id="011b2-142">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="011b2-142">Configuration</span></span>

<span data-ttu-id="011b2-143">Hlavním důvodem pro začlenění všech vzájemně závislých prostředků v jednom objektu je správa životního cyklu: kontrola nad spuštěním aplikace a jejím řádném vypnutí.</span><span class="sxs-lookup"><span data-stu-id="011b2-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="011b2-144">Kód pro vytvoření hostitele je umístěn v `Program.Main` a implementuje [návrhový vzor Builder](https://wikipedia.org/wiki/Builder_pattern).</span><span class="sxs-lookup"><span data-stu-id="011b2-144">The code to create a host is in `Program.Main` and follows the [builder pattern](https://wikipedia.org/wiki/Builder_pattern).</span></span> <span data-ttu-id="011b2-145">Metody jsou volány pro konfiguraci jednotlivých prostředků, které jsou součástí hostitele.</span><span class="sxs-lookup"><span data-stu-id="011b2-145">Methods are called to configure each resource that is part of the host.</span></span> <span data-ttu-id="011b2-146">Následně je volána metoda `Build`, která vše zkonsoliduje a vytvoří instanci objektu hostitele.</span><span class="sxs-lookup"><span data-stu-id="011b2-146">A builder method is called to pull it all together and instantiate the host object.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="011b2-147">`CreateHostBuilder` je speciální název, který identifikuje metodu Tvůrce na externí komponenty, jako například [Entity Framework](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="011b2-147">`CreateHostBuilder` is special name that identifies the builder method to external components, such as [Entity Framework](/ef/core/).</span></span>

<span data-ttu-id="011b2-148">V ASP.NET Core 3.0 a novějším je ve webové aplikaci možné využít obecného hostitele (třída `Host`) nebo webového hostitele (třída `WebHost`).</span><span class="sxs-lookup"><span data-stu-id="011b2-148">In ASP.NET Core 3.0 or later, Generic Host (`Host` class) or Web Host (`WebHost` class) can be used in a web app.</span></span> <span data-ttu-id="011b2-149">Doporučuje se použít obecného hostitele, webový hostitel je k dispozici pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="011b2-149">Generic Host is recommended, and Web Host is available for backwards compatibility.</span></span>

<span data-ttu-id="011b2-150">Architektura poskytuje metody `CreateDefaultBuilder` a `ConfigureWebHostDefaults` pro nastavení hostitele nejčastěji používanými možnostmi nastavení, jako jsou následující:</span><span class="sxs-lookup"><span data-stu-id="011b2-150">The framework provides the `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods to set up a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="011b2-151">Použití [Kestrelu](#servers) jako webového serveru a povolení integrace do služby IIS.</span><span class="sxs-lookup"><span data-stu-id="011b2-151">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="011b2-152">Načtení konfigurace z *appsettings.json*, *appsettings.{ Název prostředí }.json*, proměnných prostředí, argumentů příkazového řádku a dalších zdrojů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="011b2-152">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="011b2-153">Odeslání výstupu protokolování do konzoly a zprostředkovatelů ladění.</span><span class="sxs-lookup"><span data-stu-id="011b2-153">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="011b2-154">Zde je ukázkový kód, který vytváří hostitele.</span><span class="sxs-lookup"><span data-stu-id="011b2-154">Here's sample code that builds a host.</span></span> <span data-ttu-id="011b2-155">Metody, které nastavují hostitele běžně používanými možnosti, jsou zvýrazněné:</span><span class="sxs-lookup"><span data-stu-id="011b2-155">The methods that set up the host with commonly used options are highlighted:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs?highlight=9-10)]

<span data-ttu-id="011b2-156">Další informace naleznete v tématu <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="011b2-156">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/web-host>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="011b2-157">`CreateWebHostBuilder` je speciální název, který identifikuje metodu Tvůrce na externí komponenty, jako například [Entity Framework](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="011b2-157">`CreateWebHostBuilder` is special name that identifies the builder method to external components, such as [Entity Framework](/ef/core/).</span></span>

<span data-ttu-id="011b2-158">ASP.NET Core 2.x používá webového hostitele (třída `WebHost`) pro webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="011b2-158">ASP.NET Core 2.x uses Web Host (`WebHost` class) for web apps.</span></span> <span data-ttu-id="011b2-159">Architektura poskytuje metodu `CreateDefaultBuilder` pro nastavení hostitele nejčastěji používanými možnostmi nastavení, jako jsou následující:</span><span class="sxs-lookup"><span data-stu-id="011b2-159">The framework provides `CreateDefaultBuilder` to set up a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="011b2-160">Použití [Kestrelu](#servers) jako webového serveru a povolení integrace do služby IIS.</span><span class="sxs-lookup"><span data-stu-id="011b2-160">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="011b2-161">Načtení konfigurace z *appsettings.json*, *appsettings.{ Název prostředí }.json*, proměnných prostředí, argumentů příkazového řádku a dalších zdrojů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="011b2-161">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="011b2-162">Odeslání výstupu protokolování do konzoly a zprostředkovatelů ladění.</span><span class="sxs-lookup"><span data-stu-id="011b2-162">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="011b2-163">Zde je ukázkový kód, který vytváří hostitele:</span><span class="sxs-lookup"><span data-stu-id="011b2-163">Here's sample code that builds a host:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs?highlight=9)]

<span data-ttu-id="011b2-164">Další informace naleznete v tématu <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="011b2-164">For more information, see <xref:fundamentals/host/web-host>.</span></span>

::: moniker-end

### <a name="advanced-host-scenarios"></a><span data-ttu-id="011b2-165">Pokročilé scénáře s hostitelem</span><span class="sxs-lookup"><span data-stu-id="011b2-165">Advanced host scenarios</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="011b2-166">Obecný hostitel je k dispozici pro libovolnou aplikaci .NET Core &mdash; nejen pro aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="011b2-166">Generic Host is available for any .NET Core app to use&mdash;not just ASP.NET Core apps.</span></span> <span data-ttu-id="011b2-167">Obecný hostitel (třída `Host`) umožňuje jiným typům aplikací využívat společná rozšíření architektury, jako jsou například protokolování, DI, konfigurace a správa životního cyklu aplikace.</span><span class="sxs-lookup"><span data-stu-id="011b2-167">Generic Host (`Host` class) allows other types of apps to use cross-cutting framework extensions, such as logging, DI, configuration, and app lifetime management.</span></span> <span data-ttu-id="011b2-168">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="011b2-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="011b2-169">Webový hostitel je navržen pro použití s implementací HTTP serveru, který však není vyžadován u ostatních typů .NET aplikací.</span><span class="sxs-lookup"><span data-stu-id="011b2-169">Web Host is designed to include an HTTP server implementation, which isn't required for other kinds of .NET apps.</span></span> <span data-ttu-id="011b2-170">Od verze ASP.NET Core 2.1 je k dispozici obecný hostitel (třída `Host`) pro využití s libovolným typem .NET Core aplikace &mdash; nejen s aplikacemi ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="011b2-170">Starting in ASP.NET Core 2.1, the Generic Host (`Host` class) is available for any .NET Core app to use&mdash;not just ASP.NET Core apps.</span></span> <span data-ttu-id="011b2-171">Obecný hostitel umožňuje jiným typům aplikací využívat společná rozšíření frameworku, jako jsou například protokolování, DI, konfigurace a správa životního cyklu aplikace.</span><span class="sxs-lookup"><span data-stu-id="011b2-171">Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, DI, configuration, and app lifetime management.</span></span> <span data-ttu-id="011b2-172">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="011b2-172">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

<span data-ttu-id="011b2-173">Hostitele můžete také využít pro spouštění úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="011b2-173">You can also use the host to run background tasks.</span></span> <span data-ttu-id="011b2-174">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="011b2-174">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="011b2-175">Servery</span><span class="sxs-lookup"><span data-stu-id="011b2-175">Servers</span></span>

<span data-ttu-id="011b2-176">Aplikace ASP.NET Core využívá implementaci HTTP serveru pro naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="011b2-176">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="011b2-177">Server projevuje požadavky do aplikace jako sadu [rysů požadavku](xref:fundamentals/request-features) složených do kontextu `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="011b2-177">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="011b2-178">Windows</span><span class="sxs-lookup"><span data-stu-id="011b2-178">Windows</span></span>](#tab/windows)

<span data-ttu-id="011b2-179">ASP.NET Core nabízí následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="011b2-179">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="011b2-180">*Kestrel* je multiplatformní webový server.</span><span class="sxs-lookup"><span data-stu-id="011b2-180">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="011b2-181">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s použitím [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="011b2-181">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="011b2-182">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="011b2-182">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="011b2-183">*IIS HTTP server* je server pro systém Windows, který využívá službu IIS.</span><span class="sxs-lookup"><span data-stu-id="011b2-183">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="011b2-184">S tímto serverem jsou aplikace ASP.NET Core a služba IIS spuštěny v jednom společném procesu.</span><span class="sxs-lookup"><span data-stu-id="011b2-184">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="011b2-185">*HTTP.sys* je server pro Windows, který se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="011b2-185">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="011b2-186">macOS</span><span class="sxs-lookup"><span data-stu-id="011b2-186">macOS</span></span>](#tab/macos)

<span data-ttu-id="011b2-187">ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="011b2-187">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="011b2-188">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="011b2-188">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="011b2-189">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="011b2-189">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="011b2-190">Linux</span><span class="sxs-lookup"><span data-stu-id="011b2-190">Linux</span></span>](#tab/linux)

<span data-ttu-id="011b2-191">ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="011b2-191">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="011b2-192">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="011b2-192">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="011b2-193">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="011b2-193">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="011b2-194">Windows</span><span class="sxs-lookup"><span data-stu-id="011b2-194">Windows</span></span>](#tab/windows)

<span data-ttu-id="011b2-195">ASP.NET Core nabízí následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="011b2-195">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="011b2-196">*Kestrel* je multiplatformní webový server.</span><span class="sxs-lookup"><span data-stu-id="011b2-196">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="011b2-197">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s použitím [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="011b2-197">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="011b2-198">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="011b2-198">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="011b2-199">*HTTP.sys* je server pro Windows, který se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="011b2-199">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="011b2-200">macOS</span><span class="sxs-lookup"><span data-stu-id="011b2-200">macOS</span></span>](#tab/macos)

<span data-ttu-id="011b2-201">ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="011b2-201">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="011b2-202">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="011b2-202">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="011b2-203">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="011b2-203">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="011b2-204">Linux</span><span class="sxs-lookup"><span data-stu-id="011b2-204">Linux</span></span>](#tab/linux)

<span data-ttu-id="011b2-205">ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="011b2-205">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="011b2-206">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="011b2-206">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="011b2-207">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](http://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="011b2-207">Kestrel is often run in a reverse proxy configuration with [Nginx](http://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

<span data-ttu-id="011b2-208">Další informace naleznete v tématu <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="011b2-208">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="011b2-209">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="011b2-209">Configuration</span></span>

<span data-ttu-id="011b2-210">ASP.NET Core poskytuje konfigurační framework, který získává nastavení jako dvojice název-hodnota z uspořádané množiny poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="011b2-210">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="011b2-211">Existuje široká škála integrovaných poskytovatelů konfigurace, např. pro soubory *.json*, *.xml*, proměnné prostředí a argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="011b2-211">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="011b2-212">Můžete si také napsat vlastního zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="011b2-212">You can also write custom configuration providers.</span></span>

<span data-ttu-id="011b2-213">Jako příklad můžete ve své aplikaci specifikovat konfiguraci s využitím *appsettings.json* a proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="011b2-213">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="011b2-214">Když je následně požadována hodnota *ConnectionString*, framework ji prvně vyhledá v souboru *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="011b2-214">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="011b2-215">Pokud je hodnota nalezena v souboru, ale zároveň také v proměnné prostředí, hodnota proměnné prostředí bude mít přednost.</span><span class="sxs-lookup"><span data-stu-id="011b2-215">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="011b2-216">Pro správu důvěrné konfigurační data, jako jsou hesla, ASP.NET Core nabízí [nástroj tajný klíč správce](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="011b2-216">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="011b2-217">Pro tajné kódy v produkčním prostředí, doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="011b2-217">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="011b2-218">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="011b2-218">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="011b2-219">Možnosti</span><span class="sxs-lookup"><span data-stu-id="011b2-219">Options</span></span>

<span data-ttu-id="011b2-220">ASP.NET Core využívá *vzor možností nastavení* pro ukládání a načítání hodnot konfigurace, kde je to možné.</span><span class="sxs-lookup"><span data-stu-id="011b2-220">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="011b2-221">Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="011b2-221">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="011b2-222">Následující kód například nastavuje možnosti Websocketů:</span><span class="sxs-lookup"><span data-stu-id="011b2-222">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="011b2-223">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="011b2-223">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="011b2-224">Prostředí</span><span class="sxs-lookup"><span data-stu-id="011b2-224">Environments</span></span>

<span data-ttu-id="011b2-225">Prostředí spuštění aplikace, jako například *Developement*, *Staging* a *Production*, je výsadní pojem v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="011b2-225">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="011b2-226">Prostředí, ve kterém je aplikace spuštěna, můžete určit pomocí proměnné prostředí `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="011b2-226">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="011b2-227">ASP.NET Core čte tuto proměnnou při spuštění aplikace a uloží její hodnotu v implementaci `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="011b2-227">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="011b2-228">Objekt prostředí je k dispozici kdekoli v aplikaci prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="011b2-228">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="011b2-229">Následující ukázkový kód z třídy `Startup` nakonfiguruje aplikaci tak, aby poskytovala podrobné informace o chybě pouze ve vývojovém (Development) prostředí:</span><span class="sxs-lookup"><span data-stu-id="011b2-229">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="011b2-230">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="011b2-230">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="011b2-231">Protokolování</span><span class="sxs-lookup"><span data-stu-id="011b2-231">Logging</span></span>

<span data-ttu-id="011b2-232">ASP.NET Core podporuje API pro protokolování, které funguje s množstvím zabudovaných poskytovatelů protokolování a poskytovatelů třetích stran.</span><span class="sxs-lookup"><span data-stu-id="011b2-232">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="011b2-233">Mezi dostupné poskytovatele patří:</span><span class="sxs-lookup"><span data-stu-id="011b2-233">Available providers include the following:</span></span>

* <span data-ttu-id="011b2-234">Konzola</span><span class="sxs-lookup"><span data-stu-id="011b2-234">Console</span></span>
* <span data-ttu-id="011b2-235">Ladění</span><span class="sxs-lookup"><span data-stu-id="011b2-235">Debug</span></span>
* <span data-ttu-id="011b2-236">Trasování událostí ve Windows</span><span class="sxs-lookup"><span data-stu-id="011b2-236">Event Tracing on Windows</span></span>
* <span data-ttu-id="011b2-237">Protokol událostí Windows</span><span class="sxs-lookup"><span data-stu-id="011b2-237">Windows Event Log</span></span>
* <span data-ttu-id="011b2-238">TraceSource</span><span class="sxs-lookup"><span data-stu-id="011b2-238">TraceSource</span></span>
* <span data-ttu-id="011b2-239">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="011b2-239">Azure App Service</span></span>
* <span data-ttu-id="011b2-240">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="011b2-240">Azure Application Insights</span></span>

<span data-ttu-id="011b2-241">Zapisujte do protokolu kdekoli v kódu vaší aplikace voláním patřičných metod objektu `ILogger`, který je možné získat pomocí vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="011b2-241">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="011b2-242">Zde je ukázkový kód, který používá objekt `ILogger`, se zvýrazněním konstruktorového vkládání a protokolovacích metod.</span><span class="sxs-lookup"><span data-stu-id="011b2-242">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="011b2-243">Rozhraní `ILogger` umožňuje předat libovolný počet polí poskytovateli protokolování.</span><span class="sxs-lookup"><span data-stu-id="011b2-243">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="011b2-244">Pole se běžně používají pro konstrukci textu protokolovací zprávy, ale poskytovatel je může také odesílat odděleně do úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="011b2-244">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="011b2-245">Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="011b2-245">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="011b2-246">Další informace naleznete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="011b2-246">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="011b2-247">Směrování</span><span class="sxs-lookup"><span data-stu-id="011b2-247">Routing</span></span>

<span data-ttu-id="011b2-248">*Trasa* (route) je vzor adresy URL, který je namapovaný na obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="011b2-248">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="011b2-249">Obslužnou rutinou je obvykle stránka Razor, metoda akce v MVC kontroleru nebo middleware.</span><span class="sxs-lookup"><span data-stu-id="011b2-249">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="011b2-250">Směřování ASP.NET Core Vám umožňuje kontrolovat adresy URL používané Vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="011b2-250">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="011b2-251">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="011b2-251">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="011b2-252">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="011b2-252">Error handling</span></span>

<span data-ttu-id="011b2-253">ASP.NET Core má integrované funkce pro zpracování chyb, jako například:</span><span class="sxs-lookup"><span data-stu-id="011b2-253">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="011b2-254">Stránka výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="011b2-254">A developer exception page</span></span>
* <span data-ttu-id="011b2-255">Vlastní chybové stránky</span><span class="sxs-lookup"><span data-stu-id="011b2-255">Custom error pages</span></span>
* <span data-ttu-id="011b2-256">Statické stránky chybových kódů</span><span class="sxs-lookup"><span data-stu-id="011b2-256">Static status code pages</span></span>
* <span data-ttu-id="011b2-257">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="011b2-257">Startup exception handling</span></span>

<span data-ttu-id="011b2-258">Další informace naleznete v tématu <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="011b2-258">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="011b2-259">Vytváření požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="011b2-259">Make HTTP requests</span></span>

<span data-ttu-id="011b2-260">Implementace `IHttpClientFactory` je k dispozici pro vytváření instancí `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="011b2-260">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="011b2-261">Objekt pro vytváření (Factory):</span><span class="sxs-lookup"><span data-stu-id="011b2-261">The factory:</span></span>

* <span data-ttu-id="011b2-262">Poskytuje centrální místo pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="011b2-262">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="011b2-263">Například *github* klient může být zaregistrován a nakonfigurován tak, aby přistupoval ke GitHubu.</span><span class="sxs-lookup"><span data-stu-id="011b2-263">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="011b2-264">Výchozí klient může být zaregistrován k jiným účelům.</span><span class="sxs-lookup"><span data-stu-id="011b2-264">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="011b2-265">Podporuje registraci a řetězení více delegujících obslužných rutin k vytvoření middlewarového kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="011b2-265">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="011b2-266">Tento návrhový vzor je podobný příchozímu middlewarovému kanálu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="011b2-266">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="011b2-267">Tento návrhový vzor poskytuje mechanismus pro implementaci průřezových zodpovědností kolem HTTP požadavků, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="011b2-267">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="011b2-268">Se integruje s *Polly*, oblíbenou knihovnou třetí strany pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="011b2-268">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="011b2-269">Spravuje sdružování a životní cyklus instancí `HttpClientMessageHandler`, aby zabránil častým problémům s DNS, ke kterým dochází při manuální správě životnosti `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="011b2-269">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="011b2-270">Přidává konfigurovatelné protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů, které jsou vytvořeny objektem pro vytváření (Factory).</span><span class="sxs-lookup"><span data-stu-id="011b2-270">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="011b2-271">Další informace naleznete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="011b2-271">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="011b2-272">Kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="011b2-272">Content root</span></span>

<span data-ttu-id="011b2-273">Kořenový adresář obsahu je základní cesta k privátnímu obsahu používanému aplikací, kterým jsou například soubory Razor.</span><span class="sxs-lookup"><span data-stu-id="011b2-273">The content root is the base path to any private content used by the app, such as its Razor files.</span></span> <span data-ttu-id="011b2-274">Ve výchozím nastavení je kořenový adresář obsahu shodný se základní cestou spustitelného souboru, který je hostitelem aplikace. </span><span class="sxs-lookup"><span data-stu-id="011b2-274">By default, the content root is the base path for the executable hosting the app.</span></span> <span data-ttu-id="011b2-275">Alternativní umístění může být specifikováno při [vytváření hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="011b2-275">An alternative location can be specified when [building the host](#host).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="011b2-276">Další informace najdete v tématu [kořenový adresář obsahu](xref:fundamentals/host/generic-host#content-root).</span><span class="sxs-lookup"><span data-stu-id="011b2-276">For more information, see [Content root](xref:fundamentals/host/generic-host#content-root).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="011b2-277">Další informace najdete v tématu [kořenový adresář obsahu](xref:fundamentals/host/web-host#content-root).</span><span class="sxs-lookup"><span data-stu-id="011b2-277">For more information, see [Content root](xref:fundamentals/host/web-host#content-root).</span></span>

::: moniker-end

## <a name="web-root"></a><span data-ttu-id="011b2-278">Kořenový adresář webu</span><span class="sxs-lookup"><span data-stu-id="011b2-278">Web root</span></span>

<span data-ttu-id="011b2-279">Kořenový adresář webu (označovaný také jako *webroot*) je základní cesta k veřejným, statickým prostředkům, jako jsou CSS styly, JavaScript a soubory obrázků.</span><span class="sxs-lookup"><span data-stu-id="011b2-279">The web root (also known as *webroot*) is the base path to public, static resources, such as CSS, JavaScript, and image files.</span></span> <span data-ttu-id="011b2-280">Middleware pro statické soubory bude ve výchozím nastavení obsluhovat pouze soubory z tohoto kořenového adresáře webu (a jeho podadresářů).</span><span class="sxs-lookup"><span data-stu-id="011b2-280">The static files middleware will only serve files from the web root directory (and sub-directories) by default.</span></span> <span data-ttu-id="011b2-281">Výchozí hodnota kořenového adresáře webu je *{Kořenový adresář obsahu} / wwwroot*, jiné umístění však může být zadáno při [vytváření hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="011b2-281">The web root path defaults to *{Content Root}/wwwroot*, but a different location can be specified when [building the host](#host).</span></span>

<span data-ttu-id="011b2-282">V Razor souborech (*.cshtml*) odkazuje symbol vlnovky následované lomítkem `~/` na kořenový adresář webu.</span><span class="sxs-lookup"><span data-stu-id="011b2-282">In Razor (*.cshtml*) files, the tilde-slash `~/` points to the web root.</span></span> <span data-ttu-id="011b2-283">Cesty začínající `~/` jsou označovány jako virtuální cesty.</span><span class="sxs-lookup"><span data-stu-id="011b2-283">Paths beginning with `~/` are referred to as virtual paths.</span></span>

<span data-ttu-id="011b2-284">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="011b2-284">For more information, see <xref:fundamentals/static-files>.</span></span>
