---
title: ASP.NET Core základy
author: rick-anderson
description: Seznamte se se základními pojmy pro vytváření ASP.NET Corech aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2019
uid: fundamentals/index
ms.openlocfilehash: 7e2901919c8b0165d0f169abf74fe5bc0edd8be4
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773745"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="f7358-103">ASP.NET Core základy</span><span class="sxs-lookup"><span data-stu-id="f7358-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="f7358-104">Tento článek je přehled klíčových témat pro pochopení způsobu vývoje ASP.NET Core aplikací.</span><span class="sxs-lookup"><span data-stu-id="f7358-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="f7358-105">Třída Startup</span><span class="sxs-lookup"><span data-stu-id="f7358-105">The Startup class</span></span>

<span data-ttu-id="f7358-106">Třída `Startup` je místo, kde:</span><span class="sxs-lookup"><span data-stu-id="f7358-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="f7358-107">Služby vyžadované aplikací jsou nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="f7358-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="f7358-108">Je definován kanál pro zpracování požadavků</span><span class="sxs-lookup"><span data-stu-id="f7358-108">The request handling pipeline is defined.</span></span>

<span data-ttu-id="f7358-109">*Služby* jsou komponenty, které se používají v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f7358-109">*Services* are components that are used by the app.</span></span> <span data-ttu-id="f7358-110">Komponenta protokolování je například služba.</span><span class="sxs-lookup"><span data-stu-id="f7358-110">For example, a logging component is a service.</span></span> <span data-ttu-id="f7358-111">Kód pro konfiguraci (nebo *registraci*) služeb je přidán do metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f7358-111">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="f7358-112">Kanál pro zpracování žádostí se skládá z řady *middlewarových* součástí.</span><span class="sxs-lookup"><span data-stu-id="f7358-112">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="f7358-113">Middleware může třeba obsluhovat požadavky na statické soubory nebo přesměrovávání požadavků HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f7358-113">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="f7358-114">Každý middleware provádí asynchronní operace na kontextu `HttpContext` a následně vyvolá další middleware v kanálu nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="f7358-114">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="f7358-115">Kód pro konfiguraci kanálu pro zpracování požadavků je přidán do metody `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f7358-115">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="f7358-116">Zde je ukázka třídy `Startup`:</span><span class="sxs-lookup"><span data-stu-id="f7358-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="f7358-117">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="f7358-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="f7358-118">Vkládání závislostí (služby)</span><span class="sxs-lookup"><span data-stu-id="f7358-118">Dependency injection (services)</span></span>

<span data-ttu-id="f7358-119">ASP.NET Core má integrovanou architekturu pro vkládání závislostí (Dependency Injection, DI), která zpřístupňuje nakonfigurované služby třídám vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7358-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="f7358-120">Jedním ze způsobů, jak získat instanci služby ve třídě, je vytvoření konstruktoru s parametrem požadovaného typu.</span><span class="sxs-lookup"><span data-stu-id="f7358-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="f7358-121">Tento parametr může mít typ požadované služby nebo jejího rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f7358-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="f7358-122">Systém vkládání závislostí poskytuje služby za běhu.</span><span class="sxs-lookup"><span data-stu-id="f7358-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="f7358-123">Zde je třída, která využívá DI pro získání kontextového objektu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f7358-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="f7358-124">Zvýrazněný řádek je příklad konstruktorového vkládání:</span><span class="sxs-lookup"><span data-stu-id="f7358-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="f7358-125">Přestože je vkládání závislostí integrováno v architektuře, je navrženo tak, aby bylo možné použít kontejner IOC (Inversion of Control) třetích stran, pokud tomu dáváte přednost.</span><span class="sxs-lookup"><span data-stu-id="f7358-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="f7358-126">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="f7358-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="f7358-127">Middleware</span><span class="sxs-lookup"><span data-stu-id="f7358-127">Middleware</span></span>

<span data-ttu-id="f7358-128">Kanál pro zpracování požadavků se skládá z řady middlewarových komponent.</span><span class="sxs-lookup"><span data-stu-id="f7358-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="f7358-129">Jednotlivé komponenty provádí asynchronní operace na kontextu `HttpContext` a následně vyvolají další middleware v kanálu nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="f7358-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="f7358-130">Konvenčně se komponenty middlewaru přidávají do kanálu zavoláním rozšiřující metody `Use...` v metodě `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f7358-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="f7358-131">Pro povolení vykreslování statických souborů se například volá metoda `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="f7358-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="f7358-132">Zvýrazněný kód v následujícím příkladu nastaví kanál pro zpracování požadavku:</span><span class="sxs-lookup"><span data-stu-id="f7358-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="f7358-133">ASP.NET Core obsahuje bohatou sadu integrovaných middleware a zároveň je možné napsat také vlastní middleware.</span><span class="sxs-lookup"><span data-stu-id="f7358-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="f7358-134">Další informace naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="f7358-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="f7358-135">Hostitel</span><span class="sxs-lookup"><span data-stu-id="f7358-135">Host</span></span>

<span data-ttu-id="f7358-136">ASP.NET Core aplikace vytváří *hostitele* při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f7358-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="f7358-137">Hostitel je objekt, který zapouzdřuje všechny prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="f7358-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="f7358-138">Implementaci HTTP serveru</span><span class="sxs-lookup"><span data-stu-id="f7358-138">An HTTP server implementation</span></span>
* <span data-ttu-id="f7358-139">Middlewarové komponenty</span><span class="sxs-lookup"><span data-stu-id="f7358-139">Middleware components</span></span>
* <span data-ttu-id="f7358-140">Protokolování</span><span class="sxs-lookup"><span data-stu-id="f7358-140">Logging</span></span>
* <span data-ttu-id="f7358-141">DI</span><span class="sxs-lookup"><span data-stu-id="f7358-141">DI</span></span>
* <span data-ttu-id="f7358-142">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="f7358-142">Configuration</span></span>

<span data-ttu-id="f7358-143">Hlavním důvodem pro začlenění všech vzájemně závislých prostředků v jednom objektu je správa životního cyklu: kontrola nad spuštěním aplikace a jejím řádném vypnutí.</span><span class="sxs-lookup"><span data-stu-id="f7358-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f7358-144">K dispozici jsou dva hostitelé: obecný hostitel a webový hostitel.</span><span class="sxs-lookup"><span data-stu-id="f7358-144">Two hosts are available: the Generic Host and the Web Host.</span></span> <span data-ttu-id="f7358-145">Doporučuje se použít obecného hostitele a webový hostitel je k dispozici pouze pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="f7358-145">The Generic Host is recommended, and the Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="f7358-146">Kód pro vytvoření hostitele je v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f7358-146">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

<span data-ttu-id="f7358-147">Metody `CreateDefaultBuilder` a`ConfigureWebHostDefaults` konfigurují hostitele pomocí běžně používaných možností, jako jsou například následující:</span><span class="sxs-lookup"><span data-stu-id="f7358-147">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="f7358-148">Použití [Kestrelu](#servers) jako webového serveru a povolení integrace do služby IIS.</span><span class="sxs-lookup"><span data-stu-id="f7358-148">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="f7358-149">Načtení konfigurace z *appsettings.json*, *appsettings.{ Název prostředí }.json*, proměnných prostředí, argumentů příkazového řádku a dalších zdrojů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f7358-149">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="f7358-150">Odeslání výstupu protokolování do konzoly a zprostředkovatelů ladění.</span><span class="sxs-lookup"><span data-stu-id="f7358-150">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="f7358-151">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="f7358-151">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f7358-152">K dispozici jsou dva hostitelé: webový hostitel a obecný hostitel.</span><span class="sxs-lookup"><span data-stu-id="f7358-152">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="f7358-153">V ASP.NET Core 2. x je obecný hostitel pouze pro scénáře, které nejsou webové.</span><span class="sxs-lookup"><span data-stu-id="f7358-153">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="f7358-154">Kód pro vytvoření hostitele je v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f7358-154">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

<span data-ttu-id="f7358-155">`CreateDefaultBuilder` Metoda Konfiguruje hostitele pomocí běžně používaných možností, jako například následujících:</span><span class="sxs-lookup"><span data-stu-id="f7358-155">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="f7358-156">Použití [Kestrelu](#servers) jako webového serveru a povolení integrace do služby IIS.</span><span class="sxs-lookup"><span data-stu-id="f7358-156">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="f7358-157">Načtení konfigurace z *appsettings.json*, *appsettings.{ Název prostředí }.json*, proměnných prostředí, argumentů příkazového řádku a dalších zdrojů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f7358-157">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="f7358-158">Odeslání výstupu protokolování do konzoly a zprostředkovatelů ladění.</span><span class="sxs-lookup"><span data-stu-id="f7358-158">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="f7358-159">Další informace naleznete v tématu <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="f7358-159">For more information, see <xref:fundamentals/host/web-host>.</span></span>

::: moniker-end

### <a name="non-web-scenarios"></a><span data-ttu-id="f7358-160">Jiné než webové scénáře</span><span class="sxs-lookup"><span data-stu-id="f7358-160">Non-web scenarios</span></span>

<span data-ttu-id="f7358-161">Obecný hostitel umožňuje jiným typům aplikací používat rozšíření rozhraní pro průřez, jako je protokolování, vkládání závislostí (DI), konfigurace a správa životního cyklu aplikací.</span><span class="sxs-lookup"><span data-stu-id="f7358-161">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="f7358-162">Další informace naleznete v tématu <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="f7358-162">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="f7358-163">Servery</span><span class="sxs-lookup"><span data-stu-id="f7358-163">Servers</span></span>

<span data-ttu-id="f7358-164">Aplikace ASP.NET Core využívá implementaci HTTP serveru pro naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f7358-164">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="f7358-165">Server projevuje požadavky do aplikace jako sadu [rysů požadavku](xref:fundamentals/request-features) složených do kontextu `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="f7358-165">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="f7358-166">Windows</span><span class="sxs-lookup"><span data-stu-id="f7358-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="f7358-167">ASP.NET Core poskytuje následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="f7358-167">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="f7358-168">*Kestrel* je webový server pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="f7358-168">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="f7358-169">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s použitím [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="f7358-169">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="f7358-170">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="f7358-170">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="f7358-171">*IIS HTTP server* je server pro systém Windows, který využívá službu IIS.</span><span class="sxs-lookup"><span data-stu-id="f7358-171">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="f7358-172">S tímto serverem jsou aplikace ASP.NET Core a služba IIS spuštěny v jednom společném procesu.</span><span class="sxs-lookup"><span data-stu-id="f7358-172">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="f7358-173">*HTTP.sys* je server pro Windows, který se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="f7358-173">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="f7358-174">macOS</span><span class="sxs-lookup"><span data-stu-id="f7358-174">macOS</span></span>](#tab/macos)

<span data-ttu-id="f7358-175">ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="f7358-175">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="f7358-176">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="f7358-176">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="f7358-177">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f7358-177">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="f7358-178">Linux</span><span class="sxs-lookup"><span data-stu-id="f7358-178">Linux</span></span>](#tab/linux)

<span data-ttu-id="f7358-179">ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="f7358-179">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="f7358-180">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="f7358-180">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="f7358-181">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f7358-181">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="f7358-182">Windows</span><span class="sxs-lookup"><span data-stu-id="f7358-182">Windows</span></span>](#tab/windows)

<span data-ttu-id="f7358-183">ASP.NET Core poskytuje následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="f7358-183">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="f7358-184">*Kestrel* je webový server pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="f7358-184">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="f7358-185">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s použitím [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="f7358-185">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="f7358-186">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="f7358-186">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="f7358-187">*HTTP.sys* je server pro Windows, který se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="f7358-187">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="f7358-188">macOS</span><span class="sxs-lookup"><span data-stu-id="f7358-188">macOS</span></span>](#tab/macos)

<span data-ttu-id="f7358-189">ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="f7358-189">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="f7358-190">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="f7358-190">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="f7358-191">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f7358-191">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="f7358-192">Linux</span><span class="sxs-lookup"><span data-stu-id="f7358-192">Linux</span></span>](#tab/linux)

<span data-ttu-id="f7358-193">ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="f7358-193">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="f7358-194">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="f7358-194">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="f7358-195">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f7358-195">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

<span data-ttu-id="f7358-196">Další informace naleznete v tématu <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="f7358-196">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="f7358-197">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="f7358-197">Configuration</span></span>

<span data-ttu-id="f7358-198">ASP.NET Core poskytuje konfigurační framework, který získává nastavení jako dvojice název-hodnota z uspořádané množiny poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f7358-198">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="f7358-199">Existuje široká škála integrovaných poskytovatelů konfigurace, např. pro soubory *.json*, *.xml*, proměnné prostředí a argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="f7358-199">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="f7358-200">Můžete si také napsat vlastního zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f7358-200">You can also write custom configuration providers.</span></span>

<span data-ttu-id="f7358-201">Jako příklad můžete ve své aplikaci specifikovat konfiguraci s využitím *appsettings.json* a proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="f7358-201">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="f7358-202">Když je následně požadována hodnota *ConnectionString*, framework ji prvně vyhledá v souboru *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="f7358-202">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="f7358-203">Pokud je hodnota nalezena v souboru, ale zároveň také v proměnné prostředí, hodnota proměnné prostředí bude mít přednost.</span><span class="sxs-lookup"><span data-stu-id="f7358-203">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="f7358-204">Pro správu důvěrných konfiguračních dat, jako jsou hesla, poskytuje ASP.NET Core [nástroj pro správu tajemství](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="f7358-204">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="f7358-205">Pro tajemství v produkčním prostředí doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="f7358-205">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="f7358-206">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="f7358-206">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="f7358-207">Možnosti</span><span class="sxs-lookup"><span data-stu-id="f7358-207">Options</span></span>

<span data-ttu-id="f7358-208">ASP.NET Core využívá *vzor možností nastavení* pro ukládání a načítání hodnot konfigurace, kde je to možné.</span><span class="sxs-lookup"><span data-stu-id="f7358-208">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="f7358-209">Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="f7358-209">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="f7358-210">Následující kód například nastavuje možnosti Websocketů:</span><span class="sxs-lookup"><span data-stu-id="f7358-210">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="f7358-211">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="f7358-211">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="f7358-212">Environment</span><span class="sxs-lookup"><span data-stu-id="f7358-212">Environments</span></span>

<span data-ttu-id="f7358-213">Prostředí spuštění aplikace, jako například *Developement*, *Staging* a *Production*, je výsadní pojem v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f7358-213">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="f7358-214">Prostředí, ve kterém je aplikace spuštěna, můžete určit pomocí proměnné prostředí `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="f7358-214">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="f7358-215">ASP.NET Core čte tuto proměnnou při spuštění aplikace a uloží její hodnotu v implementaci `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="f7358-215">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="f7358-216">Objekt prostředí je k dispozici kdekoli v aplikaci prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="f7358-216">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="f7358-217">Následující ukázkový kód z třídy `Startup` nakonfiguruje aplikaci tak, aby poskytovala podrobné informace o chybě pouze ve vývojovém (Development) prostředí:</span><span class="sxs-lookup"><span data-stu-id="f7358-217">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="f7358-218">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="f7358-218">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="f7358-219">Protokolování</span><span class="sxs-lookup"><span data-stu-id="f7358-219">Logging</span></span>

<span data-ttu-id="f7358-220">ASP.NET Core podporuje API pro protokolování, které funguje s množstvím zabudovaných poskytovatelů protokolování a poskytovatelů třetích stran.</span><span class="sxs-lookup"><span data-stu-id="f7358-220">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="f7358-221">Mezi dostupné poskytovatele patří:</span><span class="sxs-lookup"><span data-stu-id="f7358-221">Available providers include the following:</span></span>

* <span data-ttu-id="f7358-222">Konzola</span><span class="sxs-lookup"><span data-stu-id="f7358-222">Console</span></span>
* <span data-ttu-id="f7358-223">Ladění</span><span class="sxs-lookup"><span data-stu-id="f7358-223">Debug</span></span>
* <span data-ttu-id="f7358-224">Trasování událostí ve Windows</span><span class="sxs-lookup"><span data-stu-id="f7358-224">Event Tracing on Windows</span></span>
* <span data-ttu-id="f7358-225">Protokol událostí systému Windows</span><span class="sxs-lookup"><span data-stu-id="f7358-225">Windows Event Log</span></span>
* <span data-ttu-id="f7358-226">TraceSource</span><span class="sxs-lookup"><span data-stu-id="f7358-226">TraceSource</span></span>
* <span data-ttu-id="f7358-227">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="f7358-227">Azure App Service</span></span>
* <span data-ttu-id="f7358-228">Application Insights Azure</span><span class="sxs-lookup"><span data-stu-id="f7358-228">Azure Application Insights</span></span>

<span data-ttu-id="f7358-229">Zapisujte do protokolu kdekoli v kódu vaší aplikace voláním patřičných metod objektu `ILogger`, který je možné získat pomocí vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="f7358-229">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="f7358-230">Zde je ukázkový kód, který používá objekt `ILogger`, se zvýrazněním konstruktorového vkládání a protokolovacích metod.</span><span class="sxs-lookup"><span data-stu-id="f7358-230">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="f7358-231">Rozhraní `ILogger` umožňuje předat libovolný počet polí poskytovateli protokolování.</span><span class="sxs-lookup"><span data-stu-id="f7358-231">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="f7358-232">Pole se běžně používají pro konstrukci textu protokolovací zprávy, ale poskytovatel je může také odesílat odděleně do úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="f7358-232">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="f7358-233">Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="f7358-233">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="f7358-234">Další informace naleznete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="f7358-234">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="f7358-235">Směrování</span><span class="sxs-lookup"><span data-stu-id="f7358-235">Routing</span></span>

<span data-ttu-id="f7358-236">*Trasa* (route) je vzor adresy URL, který je namapovaný na obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="f7358-236">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="f7358-237">Obslužnou rutinou je obvykle stránka Razor, metoda akce v MVC kontroleru nebo middleware.</span><span class="sxs-lookup"><span data-stu-id="f7358-237">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="f7358-238">Směřování ASP.NET Core Vám umožňuje kontrolovat adresy URL používané Vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="f7358-238">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="f7358-239">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="f7358-239">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="f7358-240">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="f7358-240">Error handling</span></span>

<span data-ttu-id="f7358-241">ASP.NET Core obsahuje integrované funkce pro zpracování chyb, jako například:</span><span class="sxs-lookup"><span data-stu-id="f7358-241">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="f7358-242">Stránka výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="f7358-242">A developer exception page</span></span>
* <span data-ttu-id="f7358-243">Vlastní chybové stránky</span><span class="sxs-lookup"><span data-stu-id="f7358-243">Custom error pages</span></span>
* <span data-ttu-id="f7358-244">Statické stránky chybových kódů</span><span class="sxs-lookup"><span data-stu-id="f7358-244">Static status code pages</span></span>
* <span data-ttu-id="f7358-245">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="f7358-245">Startup exception handling</span></span>

<span data-ttu-id="f7358-246">Další informace naleznete v tématu <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="f7358-246">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="f7358-247">Vytváření požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="f7358-247">Make HTTP requests</span></span>

<span data-ttu-id="f7358-248">Implementace `IHttpClientFactory` je k dispozici pro vytváření instancí `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="f7358-248">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="f7358-249">Objekt pro vytváření (Factory):</span><span class="sxs-lookup"><span data-stu-id="f7358-249">The factory:</span></span>

* <span data-ttu-id="f7358-250">Poskytuje centrální místo pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="f7358-250">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="f7358-251">Například *github* klient může být zaregistrován a nakonfigurován tak, aby přistupoval ke GitHubu.</span><span class="sxs-lookup"><span data-stu-id="f7358-251">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="f7358-252">Výchozí klient může být zaregistrován k jiným účelům.</span><span class="sxs-lookup"><span data-stu-id="f7358-252">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="f7358-253">Podporuje registraci a řetězení více delegujících obslužných rutin k vytvoření middlewarového kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="f7358-253">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="f7358-254">Tento návrhový vzor je podobný příchozímu middlewarovému kanálu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f7358-254">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="f7358-255">Tento návrhový vzor poskytuje mechanismus pro implementaci průřezových zodpovědností kolem HTTP požadavků, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="f7358-255">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="f7358-256">Se integruje s *Polly*, oblíbenou knihovnou třetí strany pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="f7358-256">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="f7358-257">Spravuje sdružování a životní cyklus instancí `HttpClientMessageHandler`, aby zabránil častým problémům s DNS, ke kterým dochází při manuální správě životnosti `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="f7358-257">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="f7358-258">Přidává konfigurovatelné protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů, které jsou vytvořeny objektem pro vytváření (Factory).</span><span class="sxs-lookup"><span data-stu-id="f7358-258">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="f7358-259">Další informace naleznete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="f7358-259">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="f7358-260">Kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="f7358-260">Content root</span></span>

<span data-ttu-id="f7358-261">Kořenový adresář obsahu je základní cesta k privátnímu obsahu používanému aplikací, kterým jsou například soubory Razor.</span><span class="sxs-lookup"><span data-stu-id="f7358-261">The content root is the base path to any private content used by the app, such as its Razor files.</span></span> <span data-ttu-id="f7358-262">Ve výchozím nastavení je kořenový adresář obsahu shodný se základní cestou spustitelného souboru, který je hostitelem aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7358-262">By default, the content root is the base path for the executable hosting the app.</span></span> <span data-ttu-id="f7358-263">Alternativní umístění může být specifikováno při [vytváření hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="f7358-263">An alternative location can be specified when [building the host](#host).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f7358-264">Další informace najdete v tématu [kořenový adresář obsahu](xref:fundamentals/host/generic-host#content-root).</span><span class="sxs-lookup"><span data-stu-id="f7358-264">For more information, see [Content root](xref:fundamentals/host/generic-host#content-root).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f7358-265">Další informace najdete v tématu [kořenový adresář obsahu](xref:fundamentals/host/web-host#content-root).</span><span class="sxs-lookup"><span data-stu-id="f7358-265">For more information, see [Content root](xref:fundamentals/host/web-host#content-root).</span></span>

::: moniker-end

## <a name="web-root"></a><span data-ttu-id="f7358-266">Kořenový adresář webu</span><span class="sxs-lookup"><span data-stu-id="f7358-266">Web root</span></span>

<span data-ttu-id="f7358-267">Kořenový adresář webu (označovaný také jako *webroot*) je základní cesta k veřejným, statickým prostředkům, jako jsou CSS styly, JavaScript a soubory obrázků.</span><span class="sxs-lookup"><span data-stu-id="f7358-267">The web root (also known as *webroot*) is the base path to public, static resources, such as CSS, JavaScript, and image files.</span></span> <span data-ttu-id="f7358-268">Middleware pro statické soubory bude ve výchozím nastavení obsluhovat pouze soubory z tohoto kořenového adresáře webu (a jeho podadresářů).</span><span class="sxs-lookup"><span data-stu-id="f7358-268">The static files middleware will only serve files from the web root directory (and sub-directories) by default.</span></span> <span data-ttu-id="f7358-269">Výchozí hodnota kořenového adresáře webu je *{Kořenový adresář obsahu} / wwwroot*, jiné umístění však může být zadáno při [vytváření hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="f7358-269">The web root path defaults to *{Content Root}/wwwroot*, but a different location can be specified when [building the host](#host).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f7358-270">Další informace najdete v tématu [ContentRootPath](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#contentrootpath) .</span><span class="sxs-lookup"><span data-stu-id="f7358-270">For more information, see [ContentRootPath](/aspnet/core/fundamentals/host/generic-host?view=aspnetcore-3.0#contentrootpath)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f7358-271">Další informace najdete v tématu [Web root](/aspnet/core/fundamentals/host/web-host#webroot).</span><span class="sxs-lookup"><span data-stu-id="f7358-271">For more information, see [Web root](/aspnet/core/fundamentals/host/web-host#webroot).</span></span>

::: moniker-end

<span data-ttu-id="f7358-272">V Razor souborech ( *.cshtml*) odkazuje symbol vlnovky následované lomítkem `~/` na kořenový adresář webu.</span><span class="sxs-lookup"><span data-stu-id="f7358-272">In Razor (*.cshtml*) files, the tilde-slash `~/` points to the web root.</span></span> <span data-ttu-id="f7358-273">Cesty začínající `~/` jsou označovány jako virtuální cesty.</span><span class="sxs-lookup"><span data-stu-id="f7358-273">Paths beginning with `~/` are referred to as virtual paths.</span></span>

<span data-ttu-id="f7358-274">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="f7358-274">For more information, see <xref:fundamentals/static-files>.</span></span>
