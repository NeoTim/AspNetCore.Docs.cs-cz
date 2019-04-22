---
title: Základy ASP.NET Core
author: rick-anderson
description: Seznamte se se základními koncepty pro vytváření aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2019
uid: fundamentals/index
ms.openlocfilehash: a1fed574db0baab391ebb9cfc44664ceddbfa69b
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "58809286"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="cfeff-103">Základy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cfeff-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="cfeff-104">Tento článek je přehled o klíčových témata pro pochopení způsobu, jak vyvíjet aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cfeff-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="cfeff-105">Třída Startup</span><span class="sxs-lookup"><span data-stu-id="cfeff-105">The Startup class</span></span>

<span data-ttu-id="cfeff-106">`Startup` Třídy je tam, kde:</span><span class="sxs-lookup"><span data-stu-id="cfeff-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="cfeff-107">Všechny služby nezbytné aplikací jsou nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="cfeff-107">Any services required by the app are configured.</span></span>
* <span data-ttu-id="cfeff-108">Žádost o zpracování kanálu je definována.</span><span class="sxs-lookup"><span data-stu-id="cfeff-108">The request handling pipeline is defined.</span></span>

* <span data-ttu-id="cfeff-109">Kód ke konfiguraci (nebo *zaregistrovat*) služby se přidá do `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="cfeff-109">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cfeff-110">*Služby* jsou komponenty, které používají aplikace.</span><span class="sxs-lookup"><span data-stu-id="cfeff-110">*Services* are components that are used by the app.</span></span> <span data-ttu-id="cfeff-111">Objekt kontextu Entity Framework Core je například služba.</span><span class="sxs-lookup"><span data-stu-id="cfeff-111">For example, an Entity Framework Core context object is a service.</span></span>
* <span data-ttu-id="cfeff-112">Kód ke konfiguraci požadavku zpracování kanálu je přidán do `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="cfeff-112">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span> <span data-ttu-id="cfeff-113">Kanál se skládá jako řadu objektů *middleware* komponenty.</span><span class="sxs-lookup"><span data-stu-id="cfeff-113">The pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="cfeff-114">Middleware může třeba obsluhovat požadavky na statické soubory nebo přesměrovávání požadavků HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="cfeff-114">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="cfeff-115">Každý middleware provádí asynchronní operace `HttpContext` a potom vyvolá další middleware v kanálu nebo ukončí požadavku.</span><span class="sxs-lookup"><span data-stu-id="cfeff-115">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="cfeff-116">Tady je ukázka `Startup` třídy:</span><span class="sxs-lookup"><span data-stu-id="cfeff-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="cfeff-117">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="cfeff-118">Vkládání závislostí (služby)</span><span class="sxs-lookup"><span data-stu-id="cfeff-118">Dependency injection (services)</span></span>

<span data-ttu-id="cfeff-119">ASP.NET Core má integrované závislost vkládání (DI) rozhraní tohoto umožňuje nakonfigurovat služby k dispozici na třídy vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="cfeff-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="cfeff-120">Jeden způsob, jak získat instanci služby ve třídě je vytvořit konstruktor s parametrem požadovaného typu.</span><span class="sxs-lookup"><span data-stu-id="cfeff-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="cfeff-121">Tento parametr může být typ služby nebo rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cfeff-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="cfeff-122">Poskytuje tento systém DI služby za běhu.</span><span class="sxs-lookup"><span data-stu-id="cfeff-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="cfeff-123">Tady je třída, která používá DI jak získat objekt kontextu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="cfeff-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="cfeff-124">Zvýrazněný řádek je příklad vkládání konstruktor:</span><span class="sxs-lookup"><span data-stu-id="cfeff-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="cfeff-125">Přestože je součástí DI, je navržena tak, aby modulu plug-in v kontejneru řízení IOC (Inversion) třetích stran, pokud dáváte přednost.</span><span class="sxs-lookup"><span data-stu-id="cfeff-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="cfeff-126">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="cfeff-127">Middleware</span><span class="sxs-lookup"><span data-stu-id="cfeff-127">Middleware</span></span>

<span data-ttu-id="cfeff-128">Žádost o zpracování kanálu se skládá jako řadu objektů middlewarových komponent.</span><span class="sxs-lookup"><span data-stu-id="cfeff-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="cfeff-129">Jednotlivé komponenty provádí asynchronní operace `HttpContext` a potom vyvolá další middleware v kanálu nebo ukončí požadavku.</span><span class="sxs-lookup"><span data-stu-id="cfeff-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="cfeff-130">Podle konvence komponenty middlewaru se přidá do kanálu vyvoláním jeho `Use...` metody rozšíření v `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="cfeff-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="cfeff-131">Například pokud chcete povolit vykreslování statických souborů, volání `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="cfeff-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="cfeff-132">Zvýrazněný kód v následujícím příkladu nastaví požadavek zpracování kanálu:</span><span class="sxs-lookup"><span data-stu-id="cfeff-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="cfeff-133">ASP.NET Core obsahuje bohatou sadu integrovaných middleware a můžete napsat vlastního middlewaru.</span><span class="sxs-lookup"><span data-stu-id="cfeff-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="cfeff-134">Další informace naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

<a id="host"/>

## <a name="the-host"></a><span data-ttu-id="cfeff-135">Hostitel</span><span class="sxs-lookup"><span data-stu-id="cfeff-135">The host</span></span>

<span data-ttu-id="cfeff-136">Sestavení aplikace ASP.NET Core *hostitele* při spuštění.</span><span class="sxs-lookup"><span data-stu-id="cfeff-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="cfeff-137">Hostitel je objekt, který zapouzdřuje všechny prostředky aplikaci, jako například:</span><span class="sxs-lookup"><span data-stu-id="cfeff-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="cfeff-138">Implementaci serveru HTTP</span><span class="sxs-lookup"><span data-stu-id="cfeff-138">An HTTP server implementation</span></span>
* <span data-ttu-id="cfeff-139">Middlewarových komponent</span><span class="sxs-lookup"><span data-stu-id="cfeff-139">Middleware components</span></span>
* <span data-ttu-id="cfeff-140">Protokolování</span><span class="sxs-lookup"><span data-stu-id="cfeff-140">Logging</span></span>
* <span data-ttu-id="cfeff-141">DI</span><span class="sxs-lookup"><span data-stu-id="cfeff-141">DI</span></span>
* <span data-ttu-id="cfeff-142">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="cfeff-142">Configuration</span></span>

<span data-ttu-id="cfeff-143">Hlavním důvodem pro všechny aplikace vzájemně závislých prostředků včetně do jednoho objektu je správa životního cyklu: kontrolu nad spuštění aplikace a řádné vypnutí.</span><span class="sxs-lookup"><span data-stu-id="cfeff-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="cfeff-144">Kód pro vytvoření hostitele je v `Program.Main` a řídí [Tvůrce modelu](https://wikipedia.org/wiki/Builder_pattern).</span><span class="sxs-lookup"><span data-stu-id="cfeff-144">The code to create a host is in `Program.Main` and follows the [builder pattern](https://wikipedia.org/wiki/Builder_pattern).</span></span> <span data-ttu-id="cfeff-145">Metody jsou volány ke konfiguraci jednotlivých prostředků, které je součástí hostitele.</span><span class="sxs-lookup"><span data-stu-id="cfeff-145">Methods are called to configure each resource that is part of the host.</span></span> <span data-ttu-id="cfeff-146">Tvůrce metoda je volána k stáhnout všechno dohromady a vytvoření instance objektu hostitele.</span><span class="sxs-lookup"><span data-stu-id="cfeff-146">A builder method is called to pull it all together and instantiate the host object.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cfeff-147">V ASP.NET Core 3.0 nebo novější, obecný hostitele (`Host` třídy) nebo webového hostitele (`WebHost` třídy) je možné ve webové aplikaci.</span><span class="sxs-lookup"><span data-stu-id="cfeff-147">In ASP.NET Core 3.0 or later, Generic Host (`Host` class) or Web Host (`WebHost` class) can be used in a web app.</span></span> <span data-ttu-id="cfeff-148">Obecný hostitele se doporučuje a webového hostitele je k dispozici pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="cfeff-148">Generic Host is recommended, and Web Host is available for backwards compatibility.</span></span>

<span data-ttu-id="cfeff-149">Poskytuje rozhraní `CreateDefaultBuilder` a `ConfigureWebHostDefaults` metody k nastavení hostitele, který má běžně používají možnosti, jako je následující:</span><span class="sxs-lookup"><span data-stu-id="cfeff-149">The framework provides the `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods to set up a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="cfeff-150">Použití [Kestrel](#servers) jako webového serveru a povolení integrace služby IIS.</span><span class="sxs-lookup"><span data-stu-id="cfeff-150">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="cfeff-151">Načtení konfigurace z *appsettings.json*, *appsettings. { Název prostředí} .json*, proměnné, argumenty příkazového řádku a další zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="cfeff-151">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="cfeff-152">Odeslání výstupu protokolování do konzoly a ladění zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="cfeff-152">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="cfeff-153">Tady je ukázkový kód, který vytváří hostitele.</span><span class="sxs-lookup"><span data-stu-id="cfeff-153">Here's sample code that builds a host.</span></span> <span data-ttu-id="cfeff-154">Metody, které hostitele s běžně používané možnosti zvýrazněné:</span><span class="sxs-lookup"><span data-stu-id="cfeff-154">The methods that set up the host with commonly used options are highlighted:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs?highlight=9-10)]

<span data-ttu-id="cfeff-155">Další informace naleznete v tématu <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-155">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/web-host>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cfeff-156">ASP.NET Core 2.x používá webového hostitele (`WebHost` třída) pro webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="cfeff-156">ASP.NET Core 2.x uses Web Host (`WebHost` class) for web apps.</span></span> <span data-ttu-id="cfeff-157">Poskytuje rozhraní `CreateDefaultBuilder` do hostitele se běžně používá možnosti, jako je následující:</span><span class="sxs-lookup"><span data-stu-id="cfeff-157">The framework provides `CreateDefaultBuilder` to set up a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="cfeff-158">Použití [Kestrel](#servers) jako webového serveru a povolení integrace služby IIS.</span><span class="sxs-lookup"><span data-stu-id="cfeff-158">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="cfeff-159">Načtení konfigurace z *appsettings.json*, *appsettings. { Název prostředí} .json*, proměnné, argumenty příkazového řádku a další zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="cfeff-159">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="cfeff-160">Odeslání výstupu protokolování do konzoly a ladění zprostředkovatelů.</span><span class="sxs-lookup"><span data-stu-id="cfeff-160">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="cfeff-161">Tady je ukázkový kód, který vytváří hostitele:</span><span class="sxs-lookup"><span data-stu-id="cfeff-161">Here's sample code that builds a host:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs?highlight=9)]

<span data-ttu-id="cfeff-162">Další informace naleznete v tématu <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-162">For more information, see <xref:fundamentals/host/web-host>.</span></span>

::: moniker-end

### <a name="advanced-host-scenarios"></a><span data-ttu-id="cfeff-163">Pokročilé hostitele scénáře</span><span class="sxs-lookup"><span data-stu-id="cfeff-163">Advanced host scenarios</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cfeff-164">Není k dispozici pro libovolnou aplikaci .NET Core používat obecný hostitel&mdash;nejen aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cfeff-164">Generic Host is available for any .NET Core app to use&mdash;not just ASP.NET Core apps.</span></span> <span data-ttu-id="cfeff-165">Obecný hostitele (`Host` třídy) umožňuje jiné typy aplikace, které používají společné rozhraní framework rozšíření, jako je například Správa životního cyklu protokolování, DI, konfigurace a aplikace.</span><span class="sxs-lookup"><span data-stu-id="cfeff-165">Generic Host (`Host` class) allows other types of apps to use cross-cutting framework extensions, such as logging, DI, configuration, and app lifetime management.</span></span> <span data-ttu-id="cfeff-166">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-166">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cfeff-167">Webový hostitel slouží k zahrnují implementaci serveru HTTP, který není vyžadováno pro ostatní typy aplikací .NET.</span><span class="sxs-lookup"><span data-stu-id="cfeff-167">Web Host is designed to include an HTTP server implementation, which isn't required for other kinds of .NET apps.</span></span> <span data-ttu-id="cfeff-168">Od verze technologie ASP.NET Core 2.1 obecný hostitele (`Host` třídy) je k dispozici pro libovolnou aplikaci .NET Core používat&mdash;nejen aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cfeff-168">Starting in ASP.NET Core 2.1, the Generic Host (`Host` class) is available for any .NET Core app to use&mdash;not just ASP.NET Core apps.</span></span> <span data-ttu-id="cfeff-169">Obecný hostitele umožňuje jiné typy aplikace, které používají společné rozhraní framework rozšíření, jako je například Správa životního cyklu protokolování, DI, konfigurace a aplikace.</span><span class="sxs-lookup"><span data-stu-id="cfeff-169">Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, DI, configuration, and app lifetime management.</span></span> <span data-ttu-id="cfeff-170">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-170">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

<span data-ttu-id="cfeff-171">Hostitele taky můžete spouštět úlohy na pozadí.</span><span class="sxs-lookup"><span data-stu-id="cfeff-171">You can also use the host to run background tasks.</span></span> <span data-ttu-id="cfeff-172">Další informace naleznete v tématu <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-172">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="cfeff-173">Servery</span><span class="sxs-lookup"><span data-stu-id="cfeff-173">Servers</span></span>

<span data-ttu-id="cfeff-174">Aplikace ASP.NET Core využívá implementaci serveru HTTP pro naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cfeff-174">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="cfeff-175">Požadavky serveru plochy na aplikaci jako sada [funkce požadavků](xref:fundamentals/request-features) složený do `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="cfeff-175">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="cfeff-176">Windows</span><span class="sxs-lookup"><span data-stu-id="cfeff-176">Windows</span></span>](#tab/windows)

<span data-ttu-id="cfeff-177">ASP.NET Core nabízí následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="cfeff-177">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="cfeff-178">*Kestrel* je multiplatformní webový server.</span><span class="sxs-lookup"><span data-stu-id="cfeff-178">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="cfeff-179">Pomocí konfigurace reverzního proxy serveru je často spustit kestrel [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="cfeff-179">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="cfeff-180">V technologii ASP.NET Core 2.0 nebo novější Kestrel může běžet jako veřejnou hraniční server, který přímo přístupný z Internetu.</span><span class="sxs-lookup"><span data-stu-id="cfeff-180">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="cfeff-181">*Server služby IIS HTTP* je server pro systém windows, který používá službu IIS.</span><span class="sxs-lookup"><span data-stu-id="cfeff-181">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="cfeff-182">S tímto serverem aplikace ASP.NET Core a IIS spuštění v rámci stejného procesu.</span><span class="sxs-lookup"><span data-stu-id="cfeff-182">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="cfeff-183">*Ovladač HTTP.sys* je server pro Windows, která se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="cfeff-183">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="cfeff-184">macOS</span><span class="sxs-lookup"><span data-stu-id="cfeff-184">macOS</span></span>](#tab/macos)

<span data-ttu-id="cfeff-185">ASP.NET Core nabízí *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="cfeff-185">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="cfeff-186">V technologii ASP.NET Core 2.0 nebo novější Kestrel může běžet jako veřejnou hraniční server, který přímo přístupný z Internetu.</span><span class="sxs-lookup"><span data-stu-id="cfeff-186">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="cfeff-187">Kestrel často běží v konfiguraci reverzní proxy server s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="cfeff-187">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="cfeff-188">Linux</span><span class="sxs-lookup"><span data-stu-id="cfeff-188">Linux</span></span>](#tab/linux)

<span data-ttu-id="cfeff-189">ASP.NET Core nabízí *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="cfeff-189">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="cfeff-190">V technologii ASP.NET Core 2.0 nebo novější Kestrel může běžet jako veřejnou hraniční server, který přímo přístupný z Internetu.</span><span class="sxs-lookup"><span data-stu-id="cfeff-190">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="cfeff-191">Kestrel často běží v konfiguraci reverzní proxy server s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="cfeff-191">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="cfeff-192">Windows</span><span class="sxs-lookup"><span data-stu-id="cfeff-192">Windows</span></span>](#tab/windows)

<span data-ttu-id="cfeff-193">ASP.NET Core nabízí následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="cfeff-193">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="cfeff-194">*Kestrel* je multiplatformní webový server.</span><span class="sxs-lookup"><span data-stu-id="cfeff-194">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="cfeff-195">Pomocí konfigurace reverzního proxy serveru je často spustit kestrel [IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="cfeff-195">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="cfeff-196">V technologii ASP.NET Core 2.0 nebo novější Kestrel může běžet jako veřejnou hraniční server, který přímo přístupný z Internetu.</span><span class="sxs-lookup"><span data-stu-id="cfeff-196">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="cfeff-197">*Ovladač HTTP.sys* je server pro Windows, která se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="cfeff-197">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="cfeff-198">macOS</span><span class="sxs-lookup"><span data-stu-id="cfeff-198">macOS</span></span>](#tab/macos)

<span data-ttu-id="cfeff-199">ASP.NET Core nabízí *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="cfeff-199">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="cfeff-200">V technologii ASP.NET Core 2.0 nebo novější Kestrel může běžet jako veřejnou hraniční server, který přímo přístupný z Internetu.</span><span class="sxs-lookup"><span data-stu-id="cfeff-200">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="cfeff-201">Kestrel často běží v konfiguraci reverzní proxy server s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="cfeff-201">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="cfeff-202">Linux</span><span class="sxs-lookup"><span data-stu-id="cfeff-202">Linux</span></span>](#tab/linux)

<span data-ttu-id="cfeff-203">ASP.NET Core nabízí *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="cfeff-203">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="cfeff-204">V technologii ASP.NET Core 2.0 nebo novější Kestrel může běžet jako veřejnou hraniční server, který přímo přístupný z Internetu.</span><span class="sxs-lookup"><span data-stu-id="cfeff-204">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="cfeff-205">Kestrel často běží v konfiguraci reverzní proxy server s [Nginx](http://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="cfeff-205">Kestrel is often run in a reverse proxy configuration with [Nginx](http://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

<span data-ttu-id="cfeff-206">Další informace naleznete v tématu <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-206">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="cfeff-207">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="cfeff-207">Configuration</span></span>

<span data-ttu-id="cfeff-208">ASP.NET Core poskytuje rozhraní konfigurace, která získá nastavení jako dvojice název hodnota ze seřazené sady poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="cfeff-208">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="cfeff-209">Integrovaná konfigurace poskytovatelů pro širokou škálu zdrojů, jako jsou *.json* soubory, *.xml* souborů, proměnných prostředí a argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="cfeff-209">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="cfeff-210">Můžete taky psát vlastní zprostředkovatelé konfigurace.</span><span class="sxs-lookup"><span data-stu-id="cfeff-210">You can also write custom configuration providers.</span></span>

<span data-ttu-id="cfeff-211">Například můžete zadat, že konfigurace pochází z *appsettings.json* a proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="cfeff-211">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="cfeff-212">Následně když hodnota *ConnectionString* je požadováno rozhraní vypadá v první *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="cfeff-212">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="cfeff-213">Pokud je nalezena hodnota existuje, ale také v proměnné prostředí, hodnotu proměnné prostředí by měl přednost.</span><span class="sxs-lookup"><span data-stu-id="cfeff-213">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="cfeff-214">Pro správu důvěrné konfigurační data, jako jsou hesla, ASP.NET Core nabízí [nástroj tajný klíč správce](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="cfeff-214">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="cfeff-215">Pro tajné kódy v produkčním prostředí, doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="cfeff-215">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="cfeff-216">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-216">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="cfeff-217">Možnosti</span><span class="sxs-lookup"><span data-stu-id="cfeff-217">Options</span></span>

<span data-ttu-id="cfeff-218">Kde je to možné, následuje ASP.NET Core *možnosti vzor* pro ukládání a načítání hodnot konfigurace.</span><span class="sxs-lookup"><span data-stu-id="cfeff-218">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="cfeff-219">Možnosti vzor používá k reprezentování skupiny související nastavení třídy.</span><span class="sxs-lookup"><span data-stu-id="cfeff-219">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="cfeff-220">Například následující kód nastaví objekty Websocket možnosti:</span><span class="sxs-lookup"><span data-stu-id="cfeff-220">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="cfeff-221">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-221">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="cfeff-222">Prostředí</span><span class="sxs-lookup"><span data-stu-id="cfeff-222">Environments</span></span>

<span data-ttu-id="cfeff-223">Spouštěcí prostředí, jako například *vývoj*, *pracovní*, a *produkční*, jsou hodnoty první třídy v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cfeff-223">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="cfeff-224">Můžete určit nastavením je spuštěno prostředí aplikace `ASPNETCORE_ENVIRONMENT` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="cfeff-224">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="cfeff-225">Přečte tuto proměnnou prostředí při spuštění aplikace ASP.NET Core a uloží hodnotu v `IHostingEnvironment` implementace.</span><span class="sxs-lookup"><span data-stu-id="cfeff-225">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="cfeff-226">Objekt prostředí je k dispozici kdekoli v aplikaci prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="cfeff-226">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="cfeff-227">Následující ukázkový kód z `Startup` třída nakonfiguruje aplikaci pouze při spuštění ve vývoji poskytnout podrobné informace o chybě:</span><span class="sxs-lookup"><span data-stu-id="cfeff-227">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="cfeff-228">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-228">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="cfeff-229">Protokolování</span><span class="sxs-lookup"><span data-stu-id="cfeff-229">Logging</span></span>

<span data-ttu-id="cfeff-230">ASP.NET Core podporuje protokolování rozhraní API, která funguje s různých poskytovatelů třetích stran a vestavěné protokolování.</span><span class="sxs-lookup"><span data-stu-id="cfeff-230">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="cfeff-231">Dostupní zprostředkovatelé patří:</span><span class="sxs-lookup"><span data-stu-id="cfeff-231">Available providers include the following:</span></span>

* <span data-ttu-id="cfeff-232">Konzola</span><span class="sxs-lookup"><span data-stu-id="cfeff-232">Console</span></span>
* <span data-ttu-id="cfeff-233">Ladit</span><span class="sxs-lookup"><span data-stu-id="cfeff-233">Debug</span></span>
* <span data-ttu-id="cfeff-234">Trasování událostí ve Windows</span><span class="sxs-lookup"><span data-stu-id="cfeff-234">Event Tracing on Windows</span></span>
* <span data-ttu-id="cfeff-235">Protokol událostí Windows</span><span class="sxs-lookup"><span data-stu-id="cfeff-235">Windows Event Log</span></span>
* <span data-ttu-id="cfeff-236">TraceSource</span><span class="sxs-lookup"><span data-stu-id="cfeff-236">TraceSource</span></span>
* <span data-ttu-id="cfeff-237">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="cfeff-237">Azure App Service</span></span>
* <span data-ttu-id="cfeff-238">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="cfeff-238">Azure Application Insights</span></span>

<span data-ttu-id="cfeff-239">Zápis protokoly z kdekoli v kódu vaší aplikace s informacemi `ILogger` objekt z DI a volání metod protokolu.</span><span class="sxs-lookup"><span data-stu-id="cfeff-239">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="cfeff-240">Tady je ukázkový kód, který se používá `ILogger` objekt konstruktoru vkládání a volání metody protokolování zvýrazní.</span><span class="sxs-lookup"><span data-stu-id="cfeff-240">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="cfeff-241">`ILogger` Rozhraní umožňuje předat libovolný počet polí do zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="cfeff-241">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="cfeff-242">Pole se běžně používají k vytvoření řetězec zprávy, ale poskytovateli jim také poslat jako oddělovače do úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="cfeff-242">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="cfeff-243">Tato funkce umožňuje protokolování poskytovatelé k implementaci [sémantického protokolování, označovaného také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="cfeff-243">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="cfeff-244">Další informace naleznete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-244">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="cfeff-245">Směrování</span><span class="sxs-lookup"><span data-stu-id="cfeff-245">Routing</span></span>

<span data-ttu-id="cfeff-246">A *trasy* je vzor adresy URL, který je namapovaný na obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="cfeff-246">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="cfeff-247">Obslužná rutina se obvykle stránky Razor, metodu akce v kontroleru MVC nebo middleware.</span><span class="sxs-lookup"><span data-stu-id="cfeff-247">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="cfeff-248">ASP.NET Core směrování získáte tak kontrolu nad adresy URL, která vaše aplikace používá.</span><span class="sxs-lookup"><span data-stu-id="cfeff-248">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="cfeff-249">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-249">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="cfeff-250">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="cfeff-250">Error handling</span></span>

<span data-ttu-id="cfeff-251">ASP.NET Core má integrované funkce pro zpracování chyb, jako například:</span><span class="sxs-lookup"><span data-stu-id="cfeff-251">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="cfeff-252">Na stránce výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="cfeff-252">A developer exception page</span></span>
* <span data-ttu-id="cfeff-253">Vlastní chybové stránky</span><span class="sxs-lookup"><span data-stu-id="cfeff-253">Custom error pages</span></span>
* <span data-ttu-id="cfeff-254">Statický stav znakové stránky</span><span class="sxs-lookup"><span data-stu-id="cfeff-254">Static status code pages</span></span>
* <span data-ttu-id="cfeff-255">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="cfeff-255">Startup exception handling</span></span>

<span data-ttu-id="cfeff-256">Další informace naleznete v tématu <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-256">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="cfeff-257">Vytváření požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="cfeff-257">Make HTTP requests</span></span>

<span data-ttu-id="cfeff-258">Implementace `IHttpClientFactory` je k dispozici pro vytváření `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="cfeff-258">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="cfeff-259">Objekt pro vytváření:</span><span class="sxs-lookup"><span data-stu-id="cfeff-259">The factory:</span></span>

* <span data-ttu-id="cfeff-260">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logické `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="cfeff-260">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="cfeff-261">Například *githubu* klient může zaregistrované a nakonfigurovat tak, aby ke Githubu přistupovat.</span><span class="sxs-lookup"><span data-stu-id="cfeff-261">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="cfeff-262">Výchozí klienta lze zaregistrovat k jiným účelům.</span><span class="sxs-lookup"><span data-stu-id="cfeff-262">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="cfeff-263">Podporuje registraci a řetězení více Delegující obslužných rutin k sestavení kanál middleware odchozí požadavek.</span><span class="sxs-lookup"><span data-stu-id="cfeff-263">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="cfeff-264">Tento model je podobný kanál příchozí middlewaru v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cfeff-264">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="cfeff-265">Vzor poskytuje mechanismus ke správě vyskytující aspekty kolem požadavků protokolu HTTP, včetně ukládání do mezipaměti, zpracování chyb, serializaci a protokolování.</span><span class="sxs-lookup"><span data-stu-id="cfeff-265">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="cfeff-266">Se integruje s *Polly*, Oblíbené knihovny třetí strany pro zpracování přechodných chyb.</span><span class="sxs-lookup"><span data-stu-id="cfeff-266">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="cfeff-267">Spravuje sdružování a dobu života základní `HttpClientMessageHandler` instancí se vyhnout běžným potížím DNS, ke kterým dochází při správě ručně `HttpClient` životnosti.</span><span class="sxs-lookup"><span data-stu-id="cfeff-267">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="cfeff-268">Přidá prostředí konfigurovat protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů, které jsou vytvořeny procesem.</span><span class="sxs-lookup"><span data-stu-id="cfeff-268">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="cfeff-269">Další informace naleznete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-269">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="cfeff-270">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="cfeff-270">Content root</span></span>

<span data-ttu-id="cfeff-271">Obsahu kořenový adresář je základní cesta k privátní obsahu používat aplikace, jako jsou soubory Razor.</span><span class="sxs-lookup"><span data-stu-id="cfeff-271">The content root is the base path to any private content used by the app, such as its Razor files.</span></span> <span data-ttu-id="cfeff-272">Ve výchozím nastavení je obsah kořenové základní cestu pro spustitelný soubor, který je hostitelem aplikace.</span><span class="sxs-lookup"><span data-stu-id="cfeff-272">By default, the content root is the base path for the executable hosting the app.</span></span> <span data-ttu-id="cfeff-273">Alternativní umístění může být zadán při [vytváření hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="cfeff-273">An alternative location can be specified when [building the host](#host).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cfeff-274">Další informace najdete v tématu [obsahu kořenové](xref:fundamentals/host/generic-host#content-root).</span><span class="sxs-lookup"><span data-stu-id="cfeff-274">For more information, see [Content root](xref:fundamentals/host/generic-host#content-root).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cfeff-275">Další informace najdete v tématu [obsahu kořenové](xref:fundamentals/host/web-host#content-root).</span><span class="sxs-lookup"><span data-stu-id="cfeff-275">For more information, see [Content root](xref:fundamentals/host/web-host#content-root).</span></span>

::: moniker-end

## <a name="web-root"></a><span data-ttu-id="cfeff-276">Kořen webu</span><span class="sxs-lookup"><span data-stu-id="cfeff-276">Web root</span></span>

<span data-ttu-id="cfeff-277">Kořenový adresář webové (označované také jako *webroot*) je základní cesta pro veřejné, statické prostředky, jako jsou šablony stylů CSS, JavaScript a soubory obrázků.</span><span class="sxs-lookup"><span data-stu-id="cfeff-277">The web root (also known as *webroot*) is the base path to public, static resources, such as CSS, JavaScript, and image files.</span></span> <span data-ttu-id="cfeff-278">Statické soubory middleware bude sloužit pouze soubory z kořenové složky webu (a v podadresářích) ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="cfeff-278">The static files middleware will only serve files from the web root directory (and sub-directories) by default.</span></span> <span data-ttu-id="cfeff-279">Kořenová cesta webové výchozí hodnota je *{obsahu kořenové} / wwwroot*, ale jiné umístění může být zadán při [vytváření hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="cfeff-279">The web root path defaults to *{Content Root}/wwwroot*, but a different location can be specified when [building the host](#host).</span></span>

<span data-ttu-id="cfeff-280">V prostředí Razor (*.cshtml*) soubory tilda lomítky `~/` odkazuje na kořenový web.</span><span class="sxs-lookup"><span data-stu-id="cfeff-280">In Razor (*.cshtml*) files, the tilde-slash `~/` points to the web root.</span></span> <span data-ttu-id="cfeff-281">Cesty začínající `~/` se označují jako virtuální cesty.</span><span class="sxs-lookup"><span data-stu-id="cfeff-281">Paths beginning with `~/` are referred to as virtual paths.</span></span>

<span data-ttu-id="cfeff-282">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="cfeff-282">For more information, see <xref:fundamentals/static-files>.</span></span>
