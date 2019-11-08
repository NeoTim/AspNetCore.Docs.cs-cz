---
title: ASP.NET Core základy
author: rick-anderson
description: Seznamte se se základními pojmy pro vytváření ASP.NET Corech aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/index
ms.openlocfilehash: 7173a732a04bf3e598adef298fa9120c15dd52fb
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799375"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="be24b-103">ASP.NET Core základy</span><span class="sxs-lookup"><span data-stu-id="be24b-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="be24b-104">Tento článek představuje přehled klíčových témat pro porozumění, jak vyvíjet aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="be24b-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="be24b-105">Spouštěcí třída</span><span class="sxs-lookup"><span data-stu-id="be24b-105">The Startup class</span></span>

<span data-ttu-id="be24b-106">`Startup` třídy je:</span><span class="sxs-lookup"><span data-stu-id="be24b-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="be24b-107">Služby vyžadované aplikací jsou nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="be24b-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="be24b-108">Je definovaný kanál pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="be24b-108">The request handling pipeline is defined.</span></span>

<span data-ttu-id="be24b-109">*Služby* jsou komponenty, které aplikace používá.</span><span class="sxs-lookup"><span data-stu-id="be24b-109">*Services* are components that are used by the app.</span></span> <span data-ttu-id="be24b-110">Komponenta protokolování je například služba.</span><span class="sxs-lookup"><span data-stu-id="be24b-110">For example, a logging component is a service.</span></span> <span data-ttu-id="be24b-111">Do metody `Startup.ConfigureServices` přidáte kód pro konfiguraci (nebo *registraci*) služeb.</span><span class="sxs-lookup"><span data-stu-id="be24b-111">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="be24b-112">Kanál pro zpracování žádostí se skládá z řady *middlewarových* součástí.</span><span class="sxs-lookup"><span data-stu-id="be24b-112">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="be24b-113">Middleware například může zpracovávat požadavky na statické soubory nebo přesměrovat požadavky HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="be24b-113">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="be24b-114">Každý middleware provádí asynchronní operace na `HttpContext` a poté buď vyvolá další middleware v kanálu, nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="be24b-114">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="be24b-115">Do metody `Startup.Configure` se přidá kód pro konfiguraci kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="be24b-115">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="be24b-116">Tady je ukázková `Startup` třída:</span><span class="sxs-lookup"><span data-stu-id="be24b-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="be24b-117">Další informace najdete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="be24b-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="be24b-118">Injektáž závislostí (služby)</span><span class="sxs-lookup"><span data-stu-id="be24b-118">Dependency injection (services)</span></span>

<span data-ttu-id="be24b-119">ASP.NET Core má integrovanou architekturu pro vkládání závislostí (DI), která zpřístupňuje nakonfigurované služby pro třídy aplikace.</span><span class="sxs-lookup"><span data-stu-id="be24b-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="be24b-120">Jedním ze způsobů, jak získat instanci služby ve třídě, je vytvořit konstruktor s parametrem požadovaného typu.</span><span class="sxs-lookup"><span data-stu-id="be24b-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="be24b-121">Parametr může být typ služby nebo rozhraní.</span><span class="sxs-lookup"><span data-stu-id="be24b-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="be24b-122">Systém DI poskytuje službu za běhu.</span><span class="sxs-lookup"><span data-stu-id="be24b-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="be24b-123">Zde je třída, která používá DI k získání objektu kontextu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="be24b-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="be24b-124">Zvýrazněný řádek je příkladem injektáže konstruktoru:</span><span class="sxs-lookup"><span data-stu-id="be24b-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="be24b-125">I když je systém v systému integrovaný, je navržený tak, aby umožňoval připojení kontejneru inverze správy (IoC) třetí strany, pokud budete chtít.</span><span class="sxs-lookup"><span data-stu-id="be24b-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="be24b-126">Další informace najdete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="be24b-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="be24b-127">Middleware</span><span class="sxs-lookup"><span data-stu-id="be24b-127">Middleware</span></span>

<span data-ttu-id="be24b-128">Kanál pro zpracování žádostí se skládá z řady middlewarových součástí.</span><span class="sxs-lookup"><span data-stu-id="be24b-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="be24b-129">Každá komponenta provádí asynchronní operace na `HttpContext` a poté buď vyvolá další middleware v kanálu, nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="be24b-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="be24b-130">Podle konvence je do kanálu přidána komponenta middleware, a to voláním metody rozšíření `Use...` v metodě `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="be24b-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="be24b-131">Chcete-li například povolit vykreslování statických souborů, zavolejte `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="be24b-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="be24b-132">Zvýrazněný kód v následujícím příkladu konfiguruje kanál zpracování žádostí:</span><span class="sxs-lookup"><span data-stu-id="be24b-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="be24b-133">ASP.NET Core obsahuje bohatou sadu předdefinovaných middlewarů a můžete napsat vlastní middleware.</span><span class="sxs-lookup"><span data-stu-id="be24b-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="be24b-134">Další informace najdete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="be24b-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="be24b-135">Hostitel</span><span class="sxs-lookup"><span data-stu-id="be24b-135">Host</span></span>

<span data-ttu-id="be24b-136">Aplikace ASP.NET Core při spuštění vytvoří *hostitele* .</span><span class="sxs-lookup"><span data-stu-id="be24b-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="be24b-137">Hostitel je objekt, který zapouzdřuje všechny prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="be24b-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="be24b-138">Implementace HTTP serveru</span><span class="sxs-lookup"><span data-stu-id="be24b-138">An HTTP server implementation</span></span>
* <span data-ttu-id="be24b-139">Komponenty middlewaru</span><span class="sxs-lookup"><span data-stu-id="be24b-139">Middleware components</span></span>
* <span data-ttu-id="be24b-140">protokolování</span><span class="sxs-lookup"><span data-stu-id="be24b-140">Logging</span></span>
* <span data-ttu-id="be24b-141">DŽI</span><span class="sxs-lookup"><span data-stu-id="be24b-141">DI</span></span>
* <span data-ttu-id="be24b-142">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="be24b-142">Configuration</span></span>

<span data-ttu-id="be24b-143">Hlavním důvodem pro zahrnutí všech vzájemně závislých prostředků aplikace do jednoho objektu je Správa životního cyklu: Kontrola spuštění aplikace a bezproblémové vypnutí.</span><span class="sxs-lookup"><span data-stu-id="be24b-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="be24b-144">K dispozici jsou dva hostitelé: obecný hostitel a webový hostitel.</span><span class="sxs-lookup"><span data-stu-id="be24b-144">Two hosts are available: the Generic Host and the Web Host.</span></span> <span data-ttu-id="be24b-145">Doporučuje se použít obecného hostitele a webový hostitel je k dispozici pouze pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="be24b-145">The Generic Host is recommended, and the Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="be24b-146">Kód pro vytvoření hostitele je `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="be24b-146">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

<span data-ttu-id="be24b-147">Metody `CreateDefaultBuilder` a `ConfigureWebHostDefaults` konfigurují hostitele pomocí běžně používaných možností, jako jsou například tyto:</span><span class="sxs-lookup"><span data-stu-id="be24b-147">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="be24b-148">Použijte [Kestrel](#servers) jako webový server a povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="be24b-148">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="be24b-149">Načíst konfiguraci z *appSettings. JSON*, *appSettings. { Název prostředí}. JSON*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="be24b-149">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="be24b-150">Odešlete výstup protokolování do konzoly a zprostředkovatele ladění.</span><span class="sxs-lookup"><span data-stu-id="be24b-150">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="be24b-151">Další informace najdete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="be24b-151">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="be24b-152">K dispozici jsou dva hostitelé: webový hostitel a obecný hostitel.</span><span class="sxs-lookup"><span data-stu-id="be24b-152">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="be24b-153">V ASP.NET Core 2. x je obecný hostitel pouze pro scénáře, které nejsou webové.</span><span class="sxs-lookup"><span data-stu-id="be24b-153">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="be24b-154">Kód pro vytvoření hostitele je `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="be24b-154">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

<span data-ttu-id="be24b-155">Metoda `CreateDefaultBuilder` Konfiguruje hostitele pomocí běžně používaných možností, jako jsou například následující:</span><span class="sxs-lookup"><span data-stu-id="be24b-155">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="be24b-156">Použijte [Kestrel](#servers) jako webový server a povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="be24b-156">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="be24b-157">Načíst konfiguraci z *appSettings. JSON*, *appSettings. { Název prostředí}. JSON*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="be24b-157">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="be24b-158">Odešlete výstup protokolování do konzoly a zprostředkovatele ladění.</span><span class="sxs-lookup"><span data-stu-id="be24b-158">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="be24b-159">Další informace najdete v tématu <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="be24b-159">For more information, see <xref:fundamentals/host/web-host>.</span></span>

::: moniker-end

### <a name="non-web-scenarios"></a><span data-ttu-id="be24b-160">Jiné než webové scénáře</span><span class="sxs-lookup"><span data-stu-id="be24b-160">Non-web scenarios</span></span>

<span data-ttu-id="be24b-161">Obecný hostitel umožňuje jiným typům aplikací používat rozšíření rozhraní pro průřez, jako je protokolování, vkládání závislostí (DI), konfigurace a správa životního cyklu aplikací.</span><span class="sxs-lookup"><span data-stu-id="be24b-161">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="be24b-162">Další informace naleznete v tématu <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="be24b-162">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="be24b-163">Servery</span><span class="sxs-lookup"><span data-stu-id="be24b-163">Servers</span></span>

<span data-ttu-id="be24b-164">ASP.NET Core aplikace používá implementaci HTTP serveru k naslouchání požadavkům HTTP.</span><span class="sxs-lookup"><span data-stu-id="be24b-164">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="be24b-165">Server objedná požadavky do aplikace jako sadu [funkcí požadavků](xref:fundamentals/request-features) složených do `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="be24b-165">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="be24b-166">Windows</span><span class="sxs-lookup"><span data-stu-id="be24b-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="be24b-167">ASP.NET Core poskytuje následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="be24b-167">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="be24b-168">*Kestrel* je webový server pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="be24b-168">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="be24b-169">Kestrel se často spouští v konfiguraci reverzního proxy serveru pomocí [služby IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="be24b-169">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="be24b-170">V ASP.NET Core 2,0 nebo novějším se Kestrel dá spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="be24b-170">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="be24b-171">*Http server IIS* je server pro Windows, který používá službu IIS.</span><span class="sxs-lookup"><span data-stu-id="be24b-171">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="be24b-172">S tímto serverem aplikace ASP.NET Core a služba IIS běží ve stejném procesu.</span><span class="sxs-lookup"><span data-stu-id="be24b-172">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="be24b-173">*Http. sys* je server pro Windows, který se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="be24b-173">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="be24b-174">macOS</span><span class="sxs-lookup"><span data-stu-id="be24b-174">macOS</span></span>](#tab/macos)

<span data-ttu-id="be24b-175">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="be24b-175">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="be24b-176">V ASP.NET Core 2,0 nebo novějším se Kestrel dá spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="be24b-176">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="be24b-177">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="be24b-177">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="be24b-178">Linux</span><span class="sxs-lookup"><span data-stu-id="be24b-178">Linux</span></span>](#tab/linux)

<span data-ttu-id="be24b-179">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="be24b-179">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="be24b-180">V ASP.NET Core 2,0 nebo novějším se Kestrel dá spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="be24b-180">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="be24b-181">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="be24b-181">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="be24b-182">Windows</span><span class="sxs-lookup"><span data-stu-id="be24b-182">Windows</span></span>](#tab/windows)

<span data-ttu-id="be24b-183">ASP.NET Core poskytuje následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="be24b-183">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="be24b-184">*Kestrel* je webový server pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="be24b-184">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="be24b-185">Kestrel se často spouští v konfiguraci reverzního proxy serveru pomocí [služby IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="be24b-185">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="be24b-186">V ASP.NET Core 2,0 nebo novějším se Kestrel dá spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="be24b-186">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="be24b-187">*Http. sys* je server pro Windows, který se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="be24b-187">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="be24b-188">macOS</span><span class="sxs-lookup"><span data-stu-id="be24b-188">macOS</span></span>](#tab/macos)

<span data-ttu-id="be24b-189">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="be24b-189">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="be24b-190">V ASP.NET Core 2,0 nebo novějším se Kestrel dá spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="be24b-190">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="be24b-191">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="be24b-191">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="be24b-192">Linux</span><span class="sxs-lookup"><span data-stu-id="be24b-192">Linux</span></span>](#tab/linux)

<span data-ttu-id="be24b-193">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="be24b-193">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="be24b-194">V ASP.NET Core 2,0 nebo novějším se Kestrel dá spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="be24b-194">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="be24b-195">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="be24b-195">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

<span data-ttu-id="be24b-196">Další informace najdete v tématu <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="be24b-196">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="be24b-197">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="be24b-197">Configuration</span></span>

<span data-ttu-id="be24b-198">ASP.NET Core poskytuje konfigurační rozhraní, které získá nastavení jako páry název-hodnota z seřazené sady poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="be24b-198">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="be24b-199">Existují předdefinované poskytovatelé konfigurace pro nejrůznější zdroje, například soubory *. JSON* , soubory *. XML* , proměnné prostředí a argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="be24b-199">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="be24b-200">Můžete také psát vlastní poskytovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="be24b-200">You can also write custom configuration providers.</span></span>

<span data-ttu-id="be24b-201">Například můžete určit, že konfigurace pochází z proměnných *appSettings. JSON* a proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="be24b-201">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="be24b-202">Poté, co je požadována hodnota *ConnectionString* , rozhraní vyhledá první v souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="be24b-202">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="be24b-203">Pokud je hodnota nalezena, ale také v proměnné prostředí, bude mít přednost hodnota z proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="be24b-203">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="be24b-204">Pro správu důvěrných konfiguračních dat, jako jsou hesla, ASP.NET Core poskytuje [Nástroj Správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="be24b-204">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="be24b-205">V případě produkčních tajných kódů doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="be24b-205">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="be24b-206">Další informace najdete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="be24b-206">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="be24b-207">Možnosti</span><span class="sxs-lookup"><span data-stu-id="be24b-207">Options</span></span>

<span data-ttu-id="be24b-208">Pokud je to možné, ASP.NET Core podle *vzoru možností* pro ukládání a načítání hodnot konfigurace.</span><span class="sxs-lookup"><span data-stu-id="be24b-208">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="be24b-209">Vzor možností používá třídy, které reprezentují skupiny souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="be24b-209">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="be24b-210">Například následující kód nastaví možnosti WebSockets:</span><span class="sxs-lookup"><span data-stu-id="be24b-210">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="be24b-211">Další informace najdete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="be24b-211">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="be24b-212">Environment</span><span class="sxs-lookup"><span data-stu-id="be24b-212">Environments</span></span>

<span data-ttu-id="be24b-213">Spouštěcí prostředí, jako je *vývoj*, *Příprava*a *produkce*, jsou první třídou pojmu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="be24b-213">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="be24b-214">Nastavením proměnné prostředí `ASPNETCORE_ENVIRONMENT` můžete určit prostředí, ve kterém je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="be24b-214">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="be24b-215">ASP.NET Core přečte tuto proměnnou prostředí při spuštění aplikace a uloží hodnotu do implementace `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="be24b-215">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="be24b-216">Objekt prostředí je k dispozici kdekoli v aplikaci přes DI.</span><span class="sxs-lookup"><span data-stu-id="be24b-216">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="be24b-217">Následující vzorový kód z `Startup` třídy nakonfiguruje aplikaci tak, aby poskytovala podrobné informace o chybách pouze v případě, že běží ve vývoji:</span><span class="sxs-lookup"><span data-stu-id="be24b-217">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="be24b-218">Další informace najdete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="be24b-218">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="be24b-219">protokolování</span><span class="sxs-lookup"><span data-stu-id="be24b-219">Logging</span></span>

<span data-ttu-id="be24b-220">ASP.NET Core podporuje protokolovací rozhraní API, které funguje s řadou integrovaných poskytovatelů protokolování a jiných výrobců.</span><span class="sxs-lookup"><span data-stu-id="be24b-220">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="be24b-221">K dispozici jsou následující poskytovatelé:</span><span class="sxs-lookup"><span data-stu-id="be24b-221">Available providers include the following:</span></span>

* <span data-ttu-id="be24b-222">Konzola</span><span class="sxs-lookup"><span data-stu-id="be24b-222">Console</span></span>
* <span data-ttu-id="be24b-223">Ladit</span><span class="sxs-lookup"><span data-stu-id="be24b-223">Debug</span></span>
* <span data-ttu-id="be24b-224">Trasování událostí ve Windows</span><span class="sxs-lookup"><span data-stu-id="be24b-224">Event Tracing on Windows</span></span>
* <span data-ttu-id="be24b-225">Protokol událostí systému Windows</span><span class="sxs-lookup"><span data-stu-id="be24b-225">Windows Event Log</span></span>
* <span data-ttu-id="be24b-226">TraceSource</span><span class="sxs-lookup"><span data-stu-id="be24b-226">TraceSource</span></span>
* <span data-ttu-id="be24b-227">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="be24b-227">Azure App Service</span></span>
* <span data-ttu-id="be24b-228">Application Insights Azure</span><span class="sxs-lookup"><span data-stu-id="be24b-228">Azure Application Insights</span></span>

<span data-ttu-id="be24b-229">Zápis protokolů z libovolného místa v kódu aplikace získáním `ILogger` objektu z DI a volání metod protokolu.</span><span class="sxs-lookup"><span data-stu-id="be24b-229">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="be24b-230">Zde je ukázkový kód, který používá objekt `ILogger`, s vkládáním konstruktoru a volání metody protokolování zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="be24b-230">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="be24b-231">Rozhraní `ILogger` umožňuje předat poskytovateli protokolování libovolný počet polí.</span><span class="sxs-lookup"><span data-stu-id="be24b-231">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="be24b-232">Pole jsou běžně používána k sestavení řetězce zprávy, ale zprostředkovatel je může také odeslat jako samostatné pole do úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="be24b-232">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="be24b-233">Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="be24b-233">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="be24b-234">Další informace najdete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="be24b-234">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="be24b-235">Směrování</span><span class="sxs-lookup"><span data-stu-id="be24b-235">Routing</span></span>

<span data-ttu-id="be24b-236">*Trasa* je vzor URL, který je namapován na obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="be24b-236">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="be24b-237">Obslužná rutina je typicky stránka Razor, metoda akce v řadiči MVC nebo middleware.</span><span class="sxs-lookup"><span data-stu-id="be24b-237">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="be24b-238">ASP.NET Core směrování vám umožní řídit adresy URL používané vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="be24b-238">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="be24b-239">Další informace najdete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="be24b-239">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="be24b-240">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="be24b-240">Error handling</span></span>

<span data-ttu-id="be24b-241">ASP.NET Core obsahuje integrované funkce pro zpracování chyb, jako například:</span><span class="sxs-lookup"><span data-stu-id="be24b-241">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="be24b-242">Stránka s výjimkou vývojáře</span><span class="sxs-lookup"><span data-stu-id="be24b-242">A developer exception page</span></span>
* <span data-ttu-id="be24b-243">Vlastní chybové stránky</span><span class="sxs-lookup"><span data-stu-id="be24b-243">Custom error pages</span></span>
* <span data-ttu-id="be24b-244">Statické stránky se stavovým kódem</span><span class="sxs-lookup"><span data-stu-id="be24b-244">Static status code pages</span></span>
* <span data-ttu-id="be24b-245">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="be24b-245">Startup exception handling</span></span>

<span data-ttu-id="be24b-246">Další informace najdete v tématu <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="be24b-246">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="be24b-247">Vytváření požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="be24b-247">Make HTTP requests</span></span>

<span data-ttu-id="be24b-248">Pro vytváření instancí `HttpClient` je k dispozici implementace `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="be24b-248">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="be24b-249">Objekt pro vytváření:</span><span class="sxs-lookup"><span data-stu-id="be24b-249">The factory:</span></span>

* <span data-ttu-id="be24b-250">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="be24b-250">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="be24b-251">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k GitHubu.</span><span class="sxs-lookup"><span data-stu-id="be24b-251">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="be24b-252">Výchozí klient může být zaregistrován pro jiné účely.</span><span class="sxs-lookup"><span data-stu-id="be24b-252">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="be24b-253">Podporuje registraci a řetězení více obslužných rutin delegování pro sestavení kanálu middlewaru odchozího požadavku.</span><span class="sxs-lookup"><span data-stu-id="be24b-253">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="be24b-254">Tento model je podobný vstupnímu kanálu middlewaru v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="be24b-254">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="be24b-255">Vzor poskytuje mechanismus pro správu otázek mezi jednotlivými požadavky HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="be24b-255">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="be24b-256">Integruje se s *Polly*oblíbenou knihovnou třetích stran pro zpracování přechodného selhání.</span><span class="sxs-lookup"><span data-stu-id="be24b-256">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="be24b-257">Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="be24b-257">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="be24b-258">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="be24b-258">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="be24b-259">Další informace najdete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="be24b-259">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="be24b-260">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="be24b-260">Content root</span></span>

<span data-ttu-id="be24b-261">Kořen obsahu je základní cesta k:</span><span class="sxs-lookup"><span data-stu-id="be24b-261">The content root is the base path to the:</span></span>

* <span data-ttu-id="be24b-262">Spustitelný soubor, který hostuje aplikaci ( *. exe*).</span><span class="sxs-lookup"><span data-stu-id="be24b-262">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="be24b-263">Kompilovaná sestavení, která tvoří aplikaci ( *. dll*).</span><span class="sxs-lookup"><span data-stu-id="be24b-263">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="be24b-264">Soubory s obsahem neobsahující kód, které používá aplikace, například:</span><span class="sxs-lookup"><span data-stu-id="be24b-264">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="be24b-265">Soubory Razor ( *. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="be24b-265">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="be24b-266">Konfigurační soubory ( *. JSON*, *. XML*)</span><span class="sxs-lookup"><span data-stu-id="be24b-266">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="be24b-267">Datové soubory ( *. DB*)</span><span class="sxs-lookup"><span data-stu-id="be24b-267">Data files (*.db*)</span></span>
* <span data-ttu-id="be24b-268">[Webový kořenový adresář](#web-root), obvykle publikovaná složka *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="be24b-268">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="be24b-269">Během vývoje:</span><span class="sxs-lookup"><span data-stu-id="be24b-269">During development:</span></span>

* <span data-ttu-id="be24b-270">Kořen obsahu se nastaví jako kořenový adresář projektu.</span><span class="sxs-lookup"><span data-stu-id="be24b-270">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="be24b-271">Kořenový adresář projektu slouží k vytvoření:</span><span class="sxs-lookup"><span data-stu-id="be24b-271">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="be24b-272">Cesta k souborům obsahu bez kódu aplikace v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="be24b-272">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="be24b-273">[Webový kořenový](#web-root)adresář, obvykle složka *wwwroot* v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="be24b-273">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="be24b-274">Alternativní cestu k kořenovému obsahu lze zadat při [sestavování hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="be24b-274">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="be24b-275">Další informace najdete v tématu <xref:fundamentals/host/generic-host#contentrootpath>.</span><span class="sxs-lookup"><span data-stu-id="be24b-275">For more information, see <xref:fundamentals/host/generic-host#contentrootpath>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="be24b-276">Alternativní cestu k kořenovému obsahu lze zadat při [sestavování hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="be24b-276">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="be24b-277">Další informace najdete v tématu <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="be24b-277">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

::: moniker-end

## <a name="web-root"></a><span data-ttu-id="be24b-278">Webový kořenový adresář</span><span class="sxs-lookup"><span data-stu-id="be24b-278">Web root</span></span>

<span data-ttu-id="be24b-279">Kořenový adresář webu je základní cesta k souborům statických prostředků bez kódu, jako je například:</span><span class="sxs-lookup"><span data-stu-id="be24b-279">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="be24b-280">Šablony stylů ( *. CSS*)</span><span class="sxs-lookup"><span data-stu-id="be24b-280">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="be24b-281">JavaScript ( *. js*)</span><span class="sxs-lookup"><span data-stu-id="be24b-281">JavaScript (*.js*)</span></span>
* <span data-ttu-id="be24b-282">Obrázky ( *. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="be24b-282">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="be24b-283">Statické soubory jsou obsluhovány ve výchozím nastavení pouze z kořenového adresáře webu (a podadresářů).</span><span class="sxs-lookup"><span data-stu-id="be24b-283">Static files are only served by default from the web root directory (and sub-directories).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="be24b-284">Kořenová cesta webu je ve výchozím nastavení *{root Content}/wwwroot*, ale při [sestavování hostitele](#host)je možné zadat jiný webový kořenový adresář.</span><span class="sxs-lookup"><span data-stu-id="be24b-284">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="be24b-285">Další informace najdete v tématu <xref:fundamentals/host/generic-host#webroot>.</span><span class="sxs-lookup"><span data-stu-id="be24b-285">For more information, see <xref:fundamentals/host/generic-host#webroot>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="be24b-286">Kořenová cesta webu je ve výchozím nastavení *{root Content}/wwwroot*, ale při [sestavování hostitele](#host)je možné zadat jiný webový kořenový adresář.</span><span class="sxs-lookup"><span data-stu-id="be24b-286">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="be24b-287">Další informace najdete v tématu [Web root](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="be24b-287">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

::: moniker-end

<span data-ttu-id="be24b-288">Zabraňte publikování souborů v *wwwroot* pomocí [\<obsahu > položky projektu](/visualstudio/msbuild/common-msbuild-project-items#content) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="be24b-288">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="be24b-289">Následující příklad zabraňuje publikování obsahu v adresáři *wwwroot/Local* a v podadresářích:</span><span class="sxs-lookup"><span data-stu-id="be24b-289">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="be24b-290">V souborech Razor ( *. cshtml*) směřuje vlnové lomítko (`~/`) do kořenového adresáře webu.</span><span class="sxs-lookup"><span data-stu-id="be24b-290">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="be24b-291">Cesta začínající na `~/` je označována jako *virtuální cesta*.</span><span class="sxs-lookup"><span data-stu-id="be24b-291">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="be24b-292">Další informace najdete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="be24b-292">For more information, see <xref:fundamentals/static-files>.</span></span>
