---
title: ASP.NET Core základy
author: rick-anderson
description: Seznamte se se základními pojmy pro vytváření ASP.NET Corech aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/index
ms.openlocfilehash: a70d6aa05a2c92d19076b8d6e4ea24d7554368b6
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007116"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="5dd8e-103">ASP.NET Core základy</span><span class="sxs-lookup"><span data-stu-id="5dd8e-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="5dd8e-104">Tento článek je přehled klíčových témat pro pochopení způsobu vývoje ASP.NET Core aplikací.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="5dd8e-105">Třída Startup</span><span class="sxs-lookup"><span data-stu-id="5dd8e-105">The Startup class</span></span>

<span data-ttu-id="5dd8e-106">Třída `Startup` je místo, kde:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="5dd8e-107">Služby vyžadované aplikací jsou nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="5dd8e-108">Je definován kanál pro zpracování požadavků</span><span class="sxs-lookup"><span data-stu-id="5dd8e-108">The request handling pipeline is defined.</span></span>

<span data-ttu-id="5dd8e-109">*Služby* jsou komponenty, které se používají v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-109">*Services* are components that are used by the app.</span></span> <span data-ttu-id="5dd8e-110">Komponenta protokolování je například služba.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-110">For example, a logging component is a service.</span></span> <span data-ttu-id="5dd8e-111">Kód pro konfiguraci (nebo *registraci*) služeb je přidán do metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-111">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="5dd8e-112">Kanál pro zpracování žádostí se skládá z řady *middlewarových* součástí.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-112">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="5dd8e-113">Middleware může třeba obsluhovat požadavky na statické soubory nebo přesměrovávání požadavků HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-113">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="5dd8e-114">Každý middleware provádí asynchronní operace na kontextu `HttpContext` a následně vyvolá další middleware v kanálu nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-114">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="5dd8e-115">Kód pro konfiguraci kanálu pro zpracování požadavků je přidán do metody `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-115">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="5dd8e-116">Zde je ukázka třídy `Startup`:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="5dd8e-117">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="5dd8e-118">Vkládání závislostí (služby)</span><span class="sxs-lookup"><span data-stu-id="5dd8e-118">Dependency injection (services)</span></span>

<span data-ttu-id="5dd8e-119">ASP.NET Core má integrovanou architekturu pro vkládání závislostí (Dependency Injection, DI), která zpřístupňuje nakonfigurované služby třídám vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="5dd8e-120">Jedním ze způsobů, jak získat instanci služby ve třídě, je vytvoření konstruktoru s parametrem požadovaného typu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="5dd8e-121">Tento parametr může mít typ požadované služby nebo jejího rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="5dd8e-122">Systém vkládání závislostí poskytuje služby za běhu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="5dd8e-123">Zde je třída, která využívá DI pro získání kontextového objektu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="5dd8e-124">Zvýrazněný řádek je příklad konstruktorového vkládání:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="5dd8e-125">Přestože je vkládání závislostí integrováno v architektuře, je navrženo tak, aby bylo možné použít kontejner IOC (Inversion of Control) třetích stran, pokud tomu dáváte přednost.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="5dd8e-126">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="5dd8e-127">Middleware</span><span class="sxs-lookup"><span data-stu-id="5dd8e-127">Middleware</span></span>

<span data-ttu-id="5dd8e-128">Kanál pro zpracování požadavků se skládá z řady middlewarových komponent.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="5dd8e-129">Jednotlivé komponenty provádí asynchronní operace na kontextu `HttpContext` a následně vyvolají další middleware v kanálu nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="5dd8e-130">Konvenčně se komponenty middlewaru přidávají do kanálu zavoláním rozšiřující metody `Use...` v metodě `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="5dd8e-131">Pro povolení vykreslování statických souborů se například volá metoda `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="5dd8e-132">Zvýrazněný kód v následujícím příkladu nastaví kanál pro zpracování požadavku:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="5dd8e-133">ASP.NET Core obsahuje bohatou sadu integrovaných middleware a zároveň je možné napsat také vlastní middleware.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="5dd8e-134">Další informace naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="5dd8e-135">Host</span><span class="sxs-lookup"><span data-stu-id="5dd8e-135">Host</span></span>

<span data-ttu-id="5dd8e-136">ASP.NET Core aplikace vytváří *hostitele* při spuštění.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="5dd8e-137">Hostitel je objekt, který zapouzdřuje všechny prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="5dd8e-138">Implementaci HTTP serveru</span><span class="sxs-lookup"><span data-stu-id="5dd8e-138">An HTTP server implementation</span></span>
* <span data-ttu-id="5dd8e-139">Middlewarové komponenty</span><span class="sxs-lookup"><span data-stu-id="5dd8e-139">Middleware components</span></span>
* <span data-ttu-id="5dd8e-140">Protokolování</span><span class="sxs-lookup"><span data-stu-id="5dd8e-140">Logging</span></span>
* <span data-ttu-id="5dd8e-141">DI</span><span class="sxs-lookup"><span data-stu-id="5dd8e-141">DI</span></span>
* <span data-ttu-id="5dd8e-142">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="5dd8e-142">Configuration</span></span>

<span data-ttu-id="5dd8e-143">Hlavním důvodem pro začlenění všech vzájemně závislých prostředků v jednom objektu je správa životního cyklu: kontrola nad spuštěním aplikace a jejím řádném vypnutí.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5dd8e-144">K dispozici jsou dva hostitelé: obecný hostitel a webový hostitel.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-144">Two hosts are available: the Generic Host and the Web Host.</span></span> <span data-ttu-id="5dd8e-145">Doporučuje se použít obecného hostitele a webový hostitel je k dispozici pouze pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-145">The Generic Host is recommended, and the Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="5dd8e-146">Kód pro vytvoření hostitele je v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-146">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

<span data-ttu-id="5dd8e-147">Metody `CreateDefaultBuilder` a `ConfigureWebHostDefaults` konfigurují hostitele pomocí běžně používaných možností, jako jsou například následující:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-147">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="5dd8e-148">Použití [Kestrelu](#servers) jako webového serveru a povolení integrace do služby IIS.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-148">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="5dd8e-149">Načtení konfigurace z *appsettings.json*, *appsettings.{ Název prostředí }.json*, proměnných prostředí, argumentů příkazového řádku a dalších zdrojů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-149">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="5dd8e-150">Odeslání výstupu protokolování do konzoly a zprostředkovatelů ladění.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-150">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="5dd8e-151">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-151">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5dd8e-152">K dispozici jsou dva hostitelé: webový hostitel a obecný hostitel.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-152">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="5dd8e-153">V ASP.NET Core 2. x je obecný hostitel pouze pro scénáře, které nejsou webové.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-153">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="5dd8e-154">Kód pro vytvoření hostitele je v `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-154">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

<span data-ttu-id="5dd8e-155">Metoda `CreateDefaultBuilder` Konfiguruje hostitele s běžně používanými možnostmi, jako je například následující:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-155">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="5dd8e-156">Použití [Kestrelu](#servers) jako webového serveru a povolení integrace do služby IIS.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-156">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="5dd8e-157">Načtení konfigurace z *appsettings.json*, *appsettings.{ Název prostředí }.json*, proměnných prostředí, argumentů příkazového řádku a dalších zdrojů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-157">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="5dd8e-158">Odeslání výstupu protokolování do konzoly a zprostředkovatelů ladění.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-158">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="5dd8e-159">Další informace naleznete v tématu <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-159">For more information, see <xref:fundamentals/host/web-host>.</span></span>

::: moniker-end

### <a name="non-web-scenarios"></a><span data-ttu-id="5dd8e-160">Jiné než webové scénáře</span><span class="sxs-lookup"><span data-stu-id="5dd8e-160">Non-web scenarios</span></span>

<span data-ttu-id="5dd8e-161">Obecný hostitel umožňuje jiným typům aplikací používat rozšíření rozhraní pro průřez, jako je protokolování, vkládání závislostí (DI), konfigurace a správa životního cyklu aplikací.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-161">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="5dd8e-162">Další informace naleznete v tématu <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-162">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="5dd8e-163">Servery</span><span class="sxs-lookup"><span data-stu-id="5dd8e-163">Servers</span></span>

<span data-ttu-id="5dd8e-164">Aplikace ASP.NET Core využívá implementaci HTTP serveru pro naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-164">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="5dd8e-165">Server projevuje požadavky do aplikace jako sadu [rysů požadavku](xref:fundamentals/request-features) složených do kontextu `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-165">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="5dd8e-166">Windows</span><span class="sxs-lookup"><span data-stu-id="5dd8e-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="5dd8e-167">ASP.NET Core poskytuje následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-167">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="5dd8e-168">*Kestrel* je webový server pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-168">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="5dd8e-169">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s použitím [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-169">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="5dd8e-170">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-170">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="5dd8e-171">*IIS HTTP server* je server pro systém Windows, který využívá službu IIS.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-171">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="5dd8e-172">S tímto serverem jsou aplikace ASP.NET Core a služba IIS spuštěny v jednom společném procesu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-172">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="5dd8e-173">*HTTP.sys* je server pro Windows, který se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-173">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="5dd8e-174">macOS</span><span class="sxs-lookup"><span data-stu-id="5dd8e-174">macOS</span></span>](#tab/macos)

<span data-ttu-id="5dd8e-175">ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-175">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="5dd8e-176">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-176">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="5dd8e-177">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-177">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="5dd8e-178">Linux</span><span class="sxs-lookup"><span data-stu-id="5dd8e-178">Linux</span></span>](#tab/linux)

<span data-ttu-id="5dd8e-179">ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-179">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="5dd8e-180">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-180">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="5dd8e-181">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-181">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="5dd8e-182">Windows</span><span class="sxs-lookup"><span data-stu-id="5dd8e-182">Windows</span></span>](#tab/windows)

<span data-ttu-id="5dd8e-183">ASP.NET Core poskytuje následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-183">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="5dd8e-184">*Kestrel* je webový server pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-184">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="5dd8e-185">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s použitím [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-185">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="5dd8e-186">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-186">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="5dd8e-187">*HTTP.sys* je server pro Windows, který se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-187">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="5dd8e-188">macOS</span><span class="sxs-lookup"><span data-stu-id="5dd8e-188">macOS</span></span>](#tab/macos)

<span data-ttu-id="5dd8e-189">ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-189">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="5dd8e-190">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-190">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="5dd8e-191">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-191">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="5dd8e-192">Linux</span><span class="sxs-lookup"><span data-stu-id="5dd8e-192">Linux</span></span>](#tab/linux)

<span data-ttu-id="5dd8e-193">ASP.NET Core nabízí *Kestrel* jako implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-193">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="5dd8e-194">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-194">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="5dd8e-195">Kestrel je často spuštěn v konfiguraci s reverzním proxy serverem s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-195">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

<span data-ttu-id="5dd8e-196">Další informace naleznete v tématu <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-196">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="5dd8e-197">Konfiguraci</span><span class="sxs-lookup"><span data-stu-id="5dd8e-197">Configuration</span></span>

<span data-ttu-id="5dd8e-198">ASP.NET Core poskytuje konfigurační framework, který získává nastavení jako dvojice název-hodnota z uspořádané množiny poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-198">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="5dd8e-199">Existuje široká škála integrovaných poskytovatelů konfigurace, např. pro soubory *.json*, *.xml*, proměnné prostředí a argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-199">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="5dd8e-200">Můžete si také napsat vlastního zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-200">You can also write custom configuration providers.</span></span>

<span data-ttu-id="5dd8e-201">Jako příklad můžete ve své aplikaci specifikovat konfiguraci s využitím *appsettings.json* a proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-201">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="5dd8e-202">Když je následně požadována hodnota *ConnectionString*, framework ji prvně vyhledá v souboru *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-202">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="5dd8e-203">Pokud je hodnota nalezena v souboru, ale zároveň také v proměnné prostředí, hodnota proměnné prostředí bude mít přednost.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-203">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="5dd8e-204">Pro správu důvěrných konfiguračních dat, jako jsou hesla, ASP.NET Core poskytuje [Nástroj Správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-204">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="5dd8e-205">V případě produkčních tajných kódů doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-205">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="5dd8e-206">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-206">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="5dd8e-207">Možnosti</span><span class="sxs-lookup"><span data-stu-id="5dd8e-207">Options</span></span>

<span data-ttu-id="5dd8e-208">ASP.NET Core využívá *vzor možností nastavení* pro ukládání a načítání hodnot konfigurace, kde je to možné.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-208">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="5dd8e-209">Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-209">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="5dd8e-210">Následující kód například nastavuje možnosti Websocketů:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-210">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="5dd8e-211">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-211">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="5dd8e-212">Environment</span><span class="sxs-lookup"><span data-stu-id="5dd8e-212">Environments</span></span>

<span data-ttu-id="5dd8e-213">Prostředí spuštění aplikace, jako například *Developement*, *Staging* a *Production*, je výsadní pojem v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-213">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="5dd8e-214">Prostředí, ve kterém je aplikace spuštěna, můžete určit pomocí proměnné prostředí `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-214">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="5dd8e-215">ASP.NET Core čte tuto proměnnou při spuštění aplikace a uloží její hodnotu v implementaci `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-215">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="5dd8e-216">Objekt prostředí je k dispozici kdekoli v aplikaci prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-216">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="5dd8e-217">Následující ukázkový kód z třídy `Startup` nakonfiguruje aplikaci tak, aby poskytovala podrobné informace o chybě pouze ve vývojovém (Development) prostředí:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-217">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="5dd8e-218">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-218">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="5dd8e-219">Protokolování</span><span class="sxs-lookup"><span data-stu-id="5dd8e-219">Logging</span></span>

<span data-ttu-id="5dd8e-220">ASP.NET Core podporuje API pro protokolování, které funguje s množstvím zabudovaných poskytovatelů protokolování a poskytovatelů třetích stran.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-220">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="5dd8e-221">Mezi dostupné poskytovatele patří:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-221">Available providers include the following:</span></span>

* <span data-ttu-id="5dd8e-222">Konzola</span><span class="sxs-lookup"><span data-stu-id="5dd8e-222">Console</span></span>
* <span data-ttu-id="5dd8e-223">Ladění</span><span class="sxs-lookup"><span data-stu-id="5dd8e-223">Debug</span></span>
* <span data-ttu-id="5dd8e-224">Trasování událostí ve Windows</span><span class="sxs-lookup"><span data-stu-id="5dd8e-224">Event Tracing on Windows</span></span>
* <span data-ttu-id="5dd8e-225">Protokol událostí systému Windows</span><span class="sxs-lookup"><span data-stu-id="5dd8e-225">Windows Event Log</span></span>
* <span data-ttu-id="5dd8e-226">TraceSource</span><span class="sxs-lookup"><span data-stu-id="5dd8e-226">TraceSource</span></span>
* <span data-ttu-id="5dd8e-227">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5dd8e-227">Azure App Service</span></span>
* <span data-ttu-id="5dd8e-228">Application Insights Azure</span><span class="sxs-lookup"><span data-stu-id="5dd8e-228">Azure Application Insights</span></span>

<span data-ttu-id="5dd8e-229">Zapisujte do protokolu kdekoli v kódu vaší aplikace voláním patřičných metod objektu `ILogger`, který je možné získat pomocí vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-229">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="5dd8e-230">Zde je ukázkový kód, který používá objekt `ILogger`, se zvýrazněním konstruktorového vkládání a protokolovacích metod.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-230">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="5dd8e-231">Rozhraní `ILogger` umožňuje předat libovolný počet polí poskytovateli protokolování.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-231">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="5dd8e-232">Pole se běžně používají pro konstrukci textu protokolovací zprávy, ale poskytovatel je může také odesílat odděleně do úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-232">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="5dd8e-233">Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-233">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="5dd8e-234">Další informace naleznete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-234">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="5dd8e-235">Směrování</span><span class="sxs-lookup"><span data-stu-id="5dd8e-235">Routing</span></span>

<span data-ttu-id="5dd8e-236">*Trasa* (route) je vzor adresy URL, který je namapovaný na obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-236">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="5dd8e-237">Obslužnou rutinou je obvykle stránka Razor, metoda akce v MVC kontroleru nebo middleware.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-237">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="5dd8e-238">Směřování ASP.NET Core Vám umožňuje kontrolovat adresy URL používané Vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-238">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="5dd8e-239">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-239">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="5dd8e-240">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="5dd8e-240">Error handling</span></span>

<span data-ttu-id="5dd8e-241">ASP.NET Core obsahuje integrované funkce pro zpracování chyb, jako například:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-241">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="5dd8e-242">Stránka výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="5dd8e-242">A developer exception page</span></span>
* <span data-ttu-id="5dd8e-243">Vlastní chybové stránky</span><span class="sxs-lookup"><span data-stu-id="5dd8e-243">Custom error pages</span></span>
* <span data-ttu-id="5dd8e-244">Statické stránky chybových kódů</span><span class="sxs-lookup"><span data-stu-id="5dd8e-244">Static status code pages</span></span>
* <span data-ttu-id="5dd8e-245">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="5dd8e-245">Startup exception handling</span></span>

<span data-ttu-id="5dd8e-246">Další informace naleznete v tématu <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-246">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="5dd8e-247">Vytváření požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="5dd8e-247">Make HTTP requests</span></span>

<span data-ttu-id="5dd8e-248">Implementace `IHttpClientFactory` je k dispozici pro vytváření instancí `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-248">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="5dd8e-249">Objekt pro vytváření (Factory):</span><span class="sxs-lookup"><span data-stu-id="5dd8e-249">The factory:</span></span>

* <span data-ttu-id="5dd8e-250">Poskytuje centrální místo pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-250">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="5dd8e-251">Například *github* klient může být zaregistrován a nakonfigurován tak, aby přistupoval ke GitHubu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-251">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="5dd8e-252">Výchozí klient může být zaregistrován k jiným účelům.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-252">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="5dd8e-253">Podporuje registraci a řetězení více delegujících obslužných rutin k vytvoření middlewarového kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-253">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="5dd8e-254">Tento návrhový vzor je podobný příchozímu middlewarovému kanálu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-254">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="5dd8e-255">Tento návrhový vzor poskytuje mechanismus pro implementaci průřezových zodpovědností kolem HTTP požadavků, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-255">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="5dd8e-256">Se integruje s *Polly*, oblíbenou knihovnou třetí strany pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-256">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="5dd8e-257">Spravuje sdružování a životní cyklus instancí `HttpClientMessageHandler`, aby zabránil častým problémům s DNS, ke kterým dochází při manuální správě životnosti `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-257">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="5dd8e-258">Přidává konfigurovatelné protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů, které jsou vytvořeny objektem pro vytváření (Factory).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-258">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="5dd8e-259">Další informace naleznete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-259">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="5dd8e-260">Kořenový adresář obsahu</span><span class="sxs-lookup"><span data-stu-id="5dd8e-260">Content root</span></span>

<span data-ttu-id="5dd8e-261">Kořen obsahu je základní cesta k:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-261">The content root is the base path to the:</span></span>

* <span data-ttu-id="5dd8e-262">Spustitelný soubor, který hostuje aplikaci ( *. exe*).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-262">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="5dd8e-263">Kompilovaná sestavení, která tvoří aplikaci ( *. dll*).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-263">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="5dd8e-264">Soubory s obsahem neobsahující kód, které používá aplikace, například:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-264">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="5dd8e-265">Soubory Razor ( *. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="5dd8e-265">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="5dd8e-266">Konfigurační soubory ( *. JSON*, *. XML*)</span><span class="sxs-lookup"><span data-stu-id="5dd8e-266">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="5dd8e-267">Datové soubory ( *. DB*)</span><span class="sxs-lookup"><span data-stu-id="5dd8e-267">Data files (*.db*)</span></span>
* <span data-ttu-id="5dd8e-268">[Webový kořenový adresář](#web-root), obvykle publikovaná složka *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="5dd8e-268">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="5dd8e-269">Během vývoje:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-269">During development:</span></span>

* <span data-ttu-id="5dd8e-270">Kořen obsahu se nastaví jako kořenový adresář projektu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-270">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="5dd8e-271">Kořenový adresář projektu slouží k vytvoření:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-271">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="5dd8e-272">Cesta k souborům obsahu bez kódu aplikace v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-272">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="5dd8e-273">[Webový kořenový](#web-root)adresář, obvykle složka *wwwroot* v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-273">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5dd8e-274">Alternativní cestu k kořenovému obsahu lze zadat při [sestavování hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-274">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="5dd8e-275">Další informace naleznete v tématu <xref:fundamentals/host/generic-host#contentrootpath>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-275">For more information, see <xref:fundamentals/host/generic-host#contentrootpath>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5dd8e-276">Alternativní cestu k kořenovému obsahu lze zadat při [sestavování hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-276">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="5dd8e-277">Další informace naleznete v tématu <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-277">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

::: moniker-end

## <a name="web-root"></a><span data-ttu-id="5dd8e-278">Kořenový adresář webu</span><span class="sxs-lookup"><span data-stu-id="5dd8e-278">Web root</span></span>

<span data-ttu-id="5dd8e-279">Kořenový adresář webu je základní cesta k souborům statických prostředků bez kódu, jako je například:</span><span class="sxs-lookup"><span data-stu-id="5dd8e-279">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="5dd8e-280">Šablony stylů ( *. CSS*)</span><span class="sxs-lookup"><span data-stu-id="5dd8e-280">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="5dd8e-281">JavaScript ( *. js*)</span><span class="sxs-lookup"><span data-stu-id="5dd8e-281">JavaScript (*.js*)</span></span>
* <span data-ttu-id="5dd8e-282">Obrázky ( *. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="5dd8e-282">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="5dd8e-283">Statické soubory jsou obsluhovány ve výchozím nastavení pouze z kořenového adresáře webu (a podadresářů).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-283">Static files are only served by default from the web root directory (and sub-directories).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5dd8e-284">Kořenová cesta webu je ve výchozím nastavení *{root Content}/wwwroot*, ale při [sestavování hostitele](#host)je možné zadat jiný webový kořenový adresář.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-284">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="5dd8e-285">Další informace naleznete v tématu <xref:fundamentals/host/generic-host#webroot>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-285">For more information, see <xref:fundamentals/host/generic-host#webroot>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5dd8e-286">Kořenová cesta webu je ve výchozím nastavení *{root Content}/wwwroot*, ale při [sestavování hostitele](#host)je možné zadat jiný webový kořenový adresář.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-286">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="5dd8e-287">Další informace najdete v tématu [Web root](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="5dd8e-287">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

::: moniker-end

<span data-ttu-id="5dd8e-288">V souborech Razor ( *. cshtml*) se vlnové lomítko (`~/`) odkazuje na kořenový adresář webu.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-288">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="5dd8e-289">Cesta začínající řetězcem `~/` je označována jako *virtuální cesta*.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-289">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="5dd8e-290">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="5dd8e-290">For more information, see <xref:fundamentals/static-files>.</span></span>
