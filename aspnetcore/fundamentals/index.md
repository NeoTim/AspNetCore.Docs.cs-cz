---
title: ASP.NET Core základy
author: rick-anderson
description: Seznamte se se základními pojmy pro vytváření ASP.NET Corech aplikací.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: fundamentals/index
ms.openlocfilehash: a16a2fbb4ad2a79f96b6646ffdc359619d361a25
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434314"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="07786-103">ASP.NET Core základy</span><span class="sxs-lookup"><span data-stu-id="07786-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07786-104">Tento článek je přehled klíčových témat pro pochopení způsobu vývoje ASP.NET Core aplikací.</span><span class="sxs-lookup"><span data-stu-id="07786-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="07786-105">Třída Startup</span><span class="sxs-lookup"><span data-stu-id="07786-105">The Startup class</span></span>

<span data-ttu-id="07786-106">`Startup` třídy je:</span><span class="sxs-lookup"><span data-stu-id="07786-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="07786-107">Služby vyžadované aplikací jsou nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="07786-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="07786-108">Je definován kanál pro zpracování požadavků</span><span class="sxs-lookup"><span data-stu-id="07786-108">The request handling pipeline is defined.</span></span>

<span data-ttu-id="07786-109">*Služby* jsou komponenty, které aplikace používá.</span><span class="sxs-lookup"><span data-stu-id="07786-109">*Services* are components that are used by the app.</span></span> <span data-ttu-id="07786-110">Komponenta protokolování je například služba.</span><span class="sxs-lookup"><span data-stu-id="07786-110">For example, a logging component is a service.</span></span> <span data-ttu-id="07786-111">Do metody `Startup.ConfigureServices` přidáte kód pro konfiguraci (nebo *registraci*) služeb.</span><span class="sxs-lookup"><span data-stu-id="07786-111">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="07786-112">Kanál pro zpracování žádostí se skládá z řady *middlewarových* součástí.</span><span class="sxs-lookup"><span data-stu-id="07786-112">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="07786-113">Middleware může třeba obsluhovat požadavky na statické soubory nebo přesměrovávání požadavků HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="07786-113">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="07786-114">Každý middleware provádí asynchronní operace na `HttpContext` a poté buď vyvolá další middleware v kanálu, nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="07786-114">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="07786-115">Do metody `Startup.Configure` se přidá kód pro konfiguraci kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="07786-115">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="07786-116">Tady je ukázková `Startup` třída:</span><span class="sxs-lookup"><span data-stu-id="07786-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="07786-117">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="07786-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="07786-118">Vkládání závislostí (služby)</span><span class="sxs-lookup"><span data-stu-id="07786-118">Dependency injection (services)</span></span>

<span data-ttu-id="07786-119">ASP.NET Core má integrovanou architekturu pro vkládání závislostí (Dependency Injection, DI), která zpřístupňuje nakonfigurované služby třídám vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="07786-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="07786-120">Jedním ze způsobů, jak získat instanci služby ve třídě, je vytvoření konstruktoru s parametrem požadovaného typu.</span><span class="sxs-lookup"><span data-stu-id="07786-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="07786-121">Tento parametr může mít typ požadované služby nebo jejího rozhraní.</span><span class="sxs-lookup"><span data-stu-id="07786-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="07786-122">Systém vkládání závislostí poskytuje služby za běhu.</span><span class="sxs-lookup"><span data-stu-id="07786-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="07786-123">Zde je třída, která využívá DI pro získání kontextového objektu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="07786-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="07786-124">Zvýrazněný řádek je příklad konstruktorového vkládání:</span><span class="sxs-lookup"><span data-stu-id="07786-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="07786-125">Přestože je vkládání závislostí integrováno v architektuře, je navrženo tak, aby bylo možné použít kontejner IOC (Inversion of Control) třetích stran, pokud tomu dáváte přednost.</span><span class="sxs-lookup"><span data-stu-id="07786-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="07786-126">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="07786-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="07786-127">Middleware</span><span class="sxs-lookup"><span data-stu-id="07786-127">Middleware</span></span>

<span data-ttu-id="07786-128">Kanál pro zpracování požadavků se skládá z řady middlewarových komponent.</span><span class="sxs-lookup"><span data-stu-id="07786-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="07786-129">Každá komponenta provádí asynchronní operace na `HttpContext` a poté buď vyvolá další middleware v kanálu, nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="07786-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="07786-130">Podle konvence je do kanálu přidána komponenta middleware, a to voláním metody rozšíření `Use...` v metodě `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="07786-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="07786-131">Chcete-li například povolit vykreslování statických souborů, zavolejte `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="07786-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="07786-132">Zvýrazněný kód v následujícím příkladu nastaví kanál pro zpracování požadavku:</span><span class="sxs-lookup"><span data-stu-id="07786-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="07786-133">ASP.NET Core obsahuje bohatou sadu integrovaných middleware a zároveň je možné napsat také vlastní middleware.</span><span class="sxs-lookup"><span data-stu-id="07786-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="07786-134">Další informace naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="07786-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="07786-135">Hostitel</span><span class="sxs-lookup"><span data-stu-id="07786-135">Host</span></span>

<span data-ttu-id="07786-136">Aplikace ASP.NET Core při spuštění vytvoří *hostitele* .</span><span class="sxs-lookup"><span data-stu-id="07786-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="07786-137">Hostitel je objekt, který zapouzdřuje všechny prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="07786-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="07786-138">Implementaci HTTP serveru</span><span class="sxs-lookup"><span data-stu-id="07786-138">An HTTP server implementation</span></span>
* <span data-ttu-id="07786-139">Middlewarové komponenty</span><span class="sxs-lookup"><span data-stu-id="07786-139">Middleware components</span></span>
* <span data-ttu-id="07786-140">Protokolování</span><span class="sxs-lookup"><span data-stu-id="07786-140">Logging</span></span>
* <span data-ttu-id="07786-141">DI</span><span class="sxs-lookup"><span data-stu-id="07786-141">DI</span></span>
* <span data-ttu-id="07786-142">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="07786-142">Configuration</span></span>

<span data-ttu-id="07786-143">Hlavním důvodem pro začlenění všech vzájemně závislých prostředků v jednom objektu je správa životního cyklu: kontrola nad spuštěním aplikace a jejím řádném vypnutí.</span><span class="sxs-lookup"><span data-stu-id="07786-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="07786-144">K dispozici jsou dva hostitelé: obecný hostitel a webový hostitel.</span><span class="sxs-lookup"><span data-stu-id="07786-144">Two hosts are available: the Generic Host and the Web Host.</span></span> <span data-ttu-id="07786-145">Doporučuje se použít obecného hostitele a webový hostitel je k dispozici pouze pro zpětnou kompatibilitu.</span><span class="sxs-lookup"><span data-stu-id="07786-145">The Generic Host is recommended, and the Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="07786-146">Kód pro vytvoření hostitele je `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="07786-146">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

<span data-ttu-id="07786-147">Metody `CreateDefaultBuilder` a `ConfigureWebHostDefaults` konfigurují hostitele pomocí běžně používaných možností, jako jsou například tyto:</span><span class="sxs-lookup"><span data-stu-id="07786-147">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="07786-148">Použijte [Kestrel](#servers) jako webový server a povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="07786-148">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="07786-149">Načíst konfiguraci z *appSettings. JSON*, *appSettings. { Název prostředí}. JSON*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="07786-149">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="07786-150">Odeslání výstupu protokolování do konzoly a zprostředkovatelů ladění.</span><span class="sxs-lookup"><span data-stu-id="07786-150">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="07786-151">Další informace naleznete v tématu <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="07786-151">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="07786-152">Jiné než webové scénáře</span><span class="sxs-lookup"><span data-stu-id="07786-152">Non-web scenarios</span></span>

<span data-ttu-id="07786-153">Obecný hostitel umožňuje jiným typům aplikací používat rozšíření rozhraní pro průřez, jako je protokolování, vkládání závislostí (DI), konfigurace a správa životního cyklu aplikací.</span><span class="sxs-lookup"><span data-stu-id="07786-153">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="07786-154">Další informace naleznete v tématech <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="07786-154">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="07786-155">Servery</span><span class="sxs-lookup"><span data-stu-id="07786-155">Servers</span></span>

<span data-ttu-id="07786-156">Aplikace ASP.NET Core využívá implementaci HTTP serveru pro naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="07786-156">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="07786-157">Server objedná požadavky do aplikace jako sadu [funkcí požadavků](xref:fundamentals/request-features) složených do `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="07786-157">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="07786-158">Windows</span><span class="sxs-lookup"><span data-stu-id="07786-158">Windows</span></span>](#tab/windows)

<span data-ttu-id="07786-159">ASP.NET Core poskytuje následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="07786-159">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="07786-160">*Kestrel* je webový server pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="07786-160">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="07786-161">Kestrel se často spouští v konfiguraci reverzního proxy serveru pomocí [služby IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="07786-161">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="07786-162">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="07786-162">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="07786-163">*Http server IIS* je server pro Windows, který používá službu IIS.</span><span class="sxs-lookup"><span data-stu-id="07786-163">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="07786-164">S tímto serverem jsou aplikace ASP.NET Core a služba IIS spuštěny v jednom společném procesu.</span><span class="sxs-lookup"><span data-stu-id="07786-164">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="07786-165">*Http. sys* je server pro Windows, který se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="07786-165">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="07786-166">macOS</span><span class="sxs-lookup"><span data-stu-id="07786-166">macOS</span></span>](#tab/macos)

<span data-ttu-id="07786-167">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="07786-167">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="07786-168">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="07786-168">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="07786-169">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="07786-169">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="07786-170">Linux</span><span class="sxs-lookup"><span data-stu-id="07786-170">Linux</span></span>](#tab/linux)

<span data-ttu-id="07786-171">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="07786-171">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="07786-172">V ASP.NET Core 2.0 nebo novějším může být Kestrel spuštěn jako veřejný, hraniční server, přístupný přímo z Internetu.</span><span class="sxs-lookup"><span data-stu-id="07786-172">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="07786-173">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="07786-173">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="07786-174">Další informace naleznete v tématu <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="07786-174">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="07786-175">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="07786-175">Configuration</span></span>

<span data-ttu-id="07786-176">ASP.NET Core poskytuje konfigurační framework, který získává nastavení jako dvojice název-hodnota z uspořádané množiny poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="07786-176">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="07786-177">Existují předdefinované poskytovatelé konfigurace pro nejrůznější zdroje, například soubory *. JSON* , soubory *. XML* , proměnné prostředí a argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="07786-177">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="07786-178">Můžete si také napsat vlastního zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="07786-178">You can also write custom configuration providers.</span></span>

<span data-ttu-id="07786-179">Například můžete určit, že konfigurace pochází z proměnných *appSettings. JSON* a proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="07786-179">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="07786-180">Poté, co je požadována hodnota *ConnectionString* , rozhraní vyhledá první v souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="07786-180">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="07786-181">Pokud je hodnota nalezena v souboru, ale zároveň také v proměnné prostředí, hodnota proměnné prostředí bude mít přednost.</span><span class="sxs-lookup"><span data-stu-id="07786-181">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="07786-182">Pro správu důvěrných konfiguračních dat, jako jsou hesla, ASP.NET Core poskytuje [Nástroj Správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="07786-182">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="07786-183">V případě produkčních tajných kódů doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="07786-183">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="07786-184">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="07786-184">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="07786-185">Možnosti</span><span class="sxs-lookup"><span data-stu-id="07786-185">Options</span></span>

<span data-ttu-id="07786-186">Pokud je to možné, ASP.NET Core podle *vzoru možností* pro ukládání a načítání hodnot konfigurace.</span><span class="sxs-lookup"><span data-stu-id="07786-186">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="07786-187">Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="07786-187">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="07786-188">Následující kód například nastavuje možnosti Websocketů:</span><span class="sxs-lookup"><span data-stu-id="07786-188">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="07786-189">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="07786-189">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="07786-190">Prostředí</span><span class="sxs-lookup"><span data-stu-id="07786-190">Environments</span></span>

<span data-ttu-id="07786-191">Spouštěcí prostředí, jako je *vývoj*, *Příprava*a *produkce*, jsou první třídou pojmu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07786-191">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="07786-192">Nastavením proměnné prostředí `ASPNETCORE_ENVIRONMENT` můžete určit prostředí, ve kterém je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="07786-192">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="07786-193">ASP.NET Core přečte tuto proměnnou prostředí při spuštění aplikace a uloží hodnotu do implementace `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="07786-193">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="07786-194">Objekt prostředí je k dispozici kdekoli v aplikaci prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="07786-194">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="07786-195">Následující vzorový kód z `Startup` třídy nakonfiguruje aplikaci tak, aby poskytovala podrobné informace o chybách pouze v případě, že běží ve vývoji:</span><span class="sxs-lookup"><span data-stu-id="07786-195">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="07786-196">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="07786-196">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="07786-197">Protokolování</span><span class="sxs-lookup"><span data-stu-id="07786-197">Logging</span></span>

<span data-ttu-id="07786-198">ASP.NET Core podporuje API pro protokolování, které funguje s množstvím zabudovaných poskytovatelů protokolování a poskytovatelů třetích stran.</span><span class="sxs-lookup"><span data-stu-id="07786-198">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="07786-199">Mezi dostupné poskytovatele patří:</span><span class="sxs-lookup"><span data-stu-id="07786-199">Available providers include the following:</span></span>

* <span data-ttu-id="07786-200">Konzola</span><span class="sxs-lookup"><span data-stu-id="07786-200">Console</span></span>
* <span data-ttu-id="07786-201">Ladění</span><span class="sxs-lookup"><span data-stu-id="07786-201">Debug</span></span>
* <span data-ttu-id="07786-202">Trasování událostí ve Windows</span><span class="sxs-lookup"><span data-stu-id="07786-202">Event Tracing on Windows</span></span>
* <span data-ttu-id="07786-203">Protokol událostí systému Windows</span><span class="sxs-lookup"><span data-stu-id="07786-203">Windows Event Log</span></span>
* <span data-ttu-id="07786-204">TraceSource</span><span class="sxs-lookup"><span data-stu-id="07786-204">TraceSource</span></span>
* <span data-ttu-id="07786-205">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="07786-205">Azure App Service</span></span>
* <span data-ttu-id="07786-206">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="07786-206">Azure Application Insights</span></span>

<span data-ttu-id="07786-207">Zápis protokolů z libovolného místa v kódu aplikace získáním `ILogger` objektu z DI a volání metod protokolu.</span><span class="sxs-lookup"><span data-stu-id="07786-207">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="07786-208">Zde je ukázkový kód, který používá objekt `ILogger`, s vkládáním konstruktoru a volání metody protokolování zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="07786-208">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="07786-209">Rozhraní `ILogger` umožňuje předat poskytovateli protokolování libovolný počet polí.</span><span class="sxs-lookup"><span data-stu-id="07786-209">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="07786-210">Pole se běžně používají pro konstrukci textu protokolovací zprávy, ale poskytovatel je může také odesílat odděleně do úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="07786-210">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="07786-211">Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="07786-211">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="07786-212">Další informace naleznete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="07786-212">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="07786-213">Směrování</span><span class="sxs-lookup"><span data-stu-id="07786-213">Routing</span></span>

<span data-ttu-id="07786-214">*Trasa* je vzor URL, který je namapován na obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="07786-214">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="07786-215">Obslužnou rutinou je obvykle stránka Razor, metoda akce v MVC kontroleru nebo middleware.</span><span class="sxs-lookup"><span data-stu-id="07786-215">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="07786-216">Směřování ASP.NET Core Vám umožňuje kontrolovat adresy URL používané Vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="07786-216">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="07786-217">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="07786-217">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="07786-218">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="07786-218">Error handling</span></span>

<span data-ttu-id="07786-219">ASP.NET Core obsahuje integrované funkce pro zpracování chyb, jako například:</span><span class="sxs-lookup"><span data-stu-id="07786-219">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="07786-220">Stránka výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="07786-220">A developer exception page</span></span>
* <span data-ttu-id="07786-221">Vlastní chybové stránky</span><span class="sxs-lookup"><span data-stu-id="07786-221">Custom error pages</span></span>
* <span data-ttu-id="07786-222">Statické stránky chybových kódů</span><span class="sxs-lookup"><span data-stu-id="07786-222">Static status code pages</span></span>
* <span data-ttu-id="07786-223">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="07786-223">Startup exception handling</span></span>

<span data-ttu-id="07786-224">Další informace naleznete v tématu <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="07786-224">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="07786-225">Vytváření požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="07786-225">Make HTTP requests</span></span>

<span data-ttu-id="07786-226">Pro vytváření instancí `HttpClient` je k dispozici implementace `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="07786-226">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="07786-227">Objekt pro vytváření (Factory):</span><span class="sxs-lookup"><span data-stu-id="07786-227">The factory:</span></span>

* <span data-ttu-id="07786-228">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="07786-228">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="07786-229">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k GitHubu.</span><span class="sxs-lookup"><span data-stu-id="07786-229">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="07786-230">Výchozí klient může být zaregistrován k jiným účelům.</span><span class="sxs-lookup"><span data-stu-id="07786-230">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="07786-231">Podporuje registraci a řetězení více delegujících obslužných rutin k vytvoření middlewarového kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="07786-231">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="07786-232">Tento návrhový vzor je podobný příchozímu middlewarovému kanálu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07786-232">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="07786-233">Tento návrhový vzor poskytuje mechanismus pro implementaci průřezových zodpovědností kolem HTTP požadavků, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="07786-233">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="07786-234">Integruje se s *Polly*oblíbenou knihovnou třetích stran pro zpracování přechodného selhání.</span><span class="sxs-lookup"><span data-stu-id="07786-234">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="07786-235">Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="07786-235">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="07786-236">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="07786-236">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="07786-237">Další informace naleznete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="07786-237">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="07786-238">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="07786-238">Content root</span></span>

<span data-ttu-id="07786-239">Kořen obsahu je základní cesta k:</span><span class="sxs-lookup"><span data-stu-id="07786-239">The content root is the base path to the:</span></span>

* <span data-ttu-id="07786-240">Spustitelný soubor, který hostuje aplikaci ( *. exe*).</span><span class="sxs-lookup"><span data-stu-id="07786-240">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="07786-241">Kompilovaná sestavení, která tvoří aplikaci ( *. dll*).</span><span class="sxs-lookup"><span data-stu-id="07786-241">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="07786-242">Soubory s obsahem neobsahující kód, které používá aplikace, například:</span><span class="sxs-lookup"><span data-stu-id="07786-242">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="07786-243">Soubory Razor ( *. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="07786-243">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="07786-244">Konfigurační soubory ( *. JSON*, *. XML*)</span><span class="sxs-lookup"><span data-stu-id="07786-244">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="07786-245">Datové soubory ( *. DB*)</span><span class="sxs-lookup"><span data-stu-id="07786-245">Data files (*.db*)</span></span>
* <span data-ttu-id="07786-246">[Webový kořenový adresář](#web-root), obvykle publikovaná složka *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="07786-246">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="07786-247">Během vývoje:</span><span class="sxs-lookup"><span data-stu-id="07786-247">During development:</span></span>

* <span data-ttu-id="07786-248">Kořen obsahu se nastaví jako kořenový adresář projektu.</span><span class="sxs-lookup"><span data-stu-id="07786-248">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="07786-249">Kořenový adresář projektu slouží k vytvoření:</span><span class="sxs-lookup"><span data-stu-id="07786-249">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="07786-250">Cesta k souborům obsahu bez kódu aplikace v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="07786-250">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="07786-251">[Webový kořenový](#web-root)adresář, obvykle složka *wwwroot* v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="07786-251">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="07786-252">Alternativní cestu k kořenovému obsahu lze zadat při [sestavování hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="07786-252">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="07786-253">Další informace naleznete v tématu <xref:fundamentals/host/generic-host#contentrootpath>.</span><span class="sxs-lookup"><span data-stu-id="07786-253">For more information, see <xref:fundamentals/host/generic-host#contentrootpath>.</span></span>

## <a name="web-root"></a><span data-ttu-id="07786-254">Kořenový adresář webu</span><span class="sxs-lookup"><span data-stu-id="07786-254">Web root</span></span>

<span data-ttu-id="07786-255">Kořenový adresář webu je základní cesta k souborům statických prostředků bez kódu, jako je například:</span><span class="sxs-lookup"><span data-stu-id="07786-255">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="07786-256">Šablony stylů ( *. CSS*)</span><span class="sxs-lookup"><span data-stu-id="07786-256">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="07786-257">JavaScript ( *. js*)</span><span class="sxs-lookup"><span data-stu-id="07786-257">JavaScript (*.js*)</span></span>
* <span data-ttu-id="07786-258">Obrázky ( *. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="07786-258">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="07786-259">Statické soubory jsou obsluhovány ve výchozím nastavení pouze z kořenového adresáře webu (a podadresářů).</span><span class="sxs-lookup"><span data-stu-id="07786-259">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="07786-260">Kořenová cesta webu je ve výchozím nastavení *{root Content}/wwwroot*, ale při [sestavování hostitele](#host)je možné zadat jiný webový kořenový adresář.</span><span class="sxs-lookup"><span data-stu-id="07786-260">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="07786-261">Další informace naleznete v tématu <xref:fundamentals/host/generic-host#webroot>.</span><span class="sxs-lookup"><span data-stu-id="07786-261">For more information, see <xref:fundamentals/host/generic-host#webroot>.</span></span>

<span data-ttu-id="07786-262">Zabraňte publikování souborů v *wwwroot* pomocí [\<obsahu > položky projektu](/visualstudio/msbuild/common-msbuild-project-items#content) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="07786-262">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="07786-263">Následující příklad zabraňuje publikování obsahu v adresáři *wwwroot/Local* a v podadresářích:</span><span class="sxs-lookup"><span data-stu-id="07786-263">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="07786-264">Pokud chcete zabránit publikování prostředků statických identit do kořenového adresáře webu, přečtěte si téma <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span><span class="sxs-lookup"><span data-stu-id="07786-264">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

<span data-ttu-id="07786-265">V souborech Razor ( *. cshtml*) směřuje vlnové lomítko (`~/`) do kořenového adresáře webu.</span><span class="sxs-lookup"><span data-stu-id="07786-265">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="07786-266">Cesta začínající na `~/` je označována jako *virtuální cesta*.</span><span class="sxs-lookup"><span data-stu-id="07786-266">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="07786-267">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="07786-267">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="07786-268">Tento článek je přehled klíčových témat pro pochopení způsobu vývoje ASP.NET Core aplikací.</span><span class="sxs-lookup"><span data-stu-id="07786-268">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="07786-269">Třída Startup</span><span class="sxs-lookup"><span data-stu-id="07786-269">The Startup class</span></span>

<span data-ttu-id="07786-270">`Startup` třídy je:</span><span class="sxs-lookup"><span data-stu-id="07786-270">The `Startup` class is where:</span></span>

* <span data-ttu-id="07786-271">Služby vyžadované aplikací jsou nakonfigurované.</span><span class="sxs-lookup"><span data-stu-id="07786-271">Services required by the app are configured.</span></span>
* <span data-ttu-id="07786-272">Je definován kanál pro zpracování požadavků</span><span class="sxs-lookup"><span data-stu-id="07786-272">The request handling pipeline is defined.</span></span>

<span data-ttu-id="07786-273">*Služby* jsou komponenty, které aplikace používá.</span><span class="sxs-lookup"><span data-stu-id="07786-273">*Services* are components that are used by the app.</span></span> <span data-ttu-id="07786-274">Komponenta protokolování je například služba.</span><span class="sxs-lookup"><span data-stu-id="07786-274">For example, a logging component is a service.</span></span> <span data-ttu-id="07786-275">Do metody `Startup.ConfigureServices` přidáte kód pro konfiguraci (nebo *registraci*) služeb.</span><span class="sxs-lookup"><span data-stu-id="07786-275">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="07786-276">Kanál pro zpracování žádostí se skládá z řady *middlewarových* součástí.</span><span class="sxs-lookup"><span data-stu-id="07786-276">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="07786-277">Middleware může třeba obsluhovat požadavky na statické soubory nebo přesměrovávání požadavků HTTP na HTTPS.</span><span class="sxs-lookup"><span data-stu-id="07786-277">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="07786-278">Každý middleware provádí asynchronní operace na `HttpContext` a poté buď vyvolá další middleware v kanálu, nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="07786-278">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="07786-279">Do metody `Startup.Configure` se přidá kód pro konfiguraci kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="07786-279">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="07786-280">Tady je ukázková `Startup` třída:</span><span class="sxs-lookup"><span data-stu-id="07786-280">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="07786-281">Další informace naleznete v tématu <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="07786-281">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="07786-282">Vkládání závislostí (služby)</span><span class="sxs-lookup"><span data-stu-id="07786-282">Dependency injection (services)</span></span>

<span data-ttu-id="07786-283">ASP.NET Core má integrovanou architekturu pro vkládání závislostí (Dependency Injection, DI), která zpřístupňuje nakonfigurované služby třídám vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="07786-283">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="07786-284">Jedním ze způsobů, jak získat instanci služby ve třídě, je vytvoření konstruktoru s parametrem požadovaného typu.</span><span class="sxs-lookup"><span data-stu-id="07786-284">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="07786-285">Tento parametr může mít typ požadované služby nebo jejího rozhraní.</span><span class="sxs-lookup"><span data-stu-id="07786-285">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="07786-286">Systém vkládání závislostí poskytuje služby za běhu.</span><span class="sxs-lookup"><span data-stu-id="07786-286">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="07786-287">Zde je třída, která využívá DI pro získání kontextového objektu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="07786-287">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="07786-288">Zvýrazněný řádek je příklad konstruktorového vkládání:</span><span class="sxs-lookup"><span data-stu-id="07786-288">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="07786-289">Přestože je vkládání závislostí integrováno v architektuře, je navrženo tak, aby bylo možné použít kontejner IOC (Inversion of Control) třetích stran, pokud tomu dáváte přednost.</span><span class="sxs-lookup"><span data-stu-id="07786-289">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="07786-290">Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="07786-290">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="07786-291">Middleware</span><span class="sxs-lookup"><span data-stu-id="07786-291">Middleware</span></span>

<span data-ttu-id="07786-292">Kanál pro zpracování požadavků se skládá z řady middlewarových komponent.</span><span class="sxs-lookup"><span data-stu-id="07786-292">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="07786-293">Každá komponenta provádí asynchronní operace na `HttpContext` a poté buď vyvolá další middleware v kanálu, nebo ukončí požadavek.</span><span class="sxs-lookup"><span data-stu-id="07786-293">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="07786-294">Podle konvence je do kanálu přidána komponenta middleware, a to voláním metody rozšíření `Use...` v metodě `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="07786-294">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="07786-295">Chcete-li například povolit vykreslování statických souborů, zavolejte `UseStaticFiles`.</span><span class="sxs-lookup"><span data-stu-id="07786-295">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="07786-296">Zvýrazněný kód v následujícím příkladu nastaví kanál pro zpracování požadavku:</span><span class="sxs-lookup"><span data-stu-id="07786-296">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="07786-297">ASP.NET Core obsahuje bohatou sadu integrovaných middleware a zároveň je možné napsat také vlastní middleware.</span><span class="sxs-lookup"><span data-stu-id="07786-297">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="07786-298">Další informace naleznete v tématu <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="07786-298">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="07786-299">Hostitel</span><span class="sxs-lookup"><span data-stu-id="07786-299">Host</span></span>

<span data-ttu-id="07786-300">Aplikace ASP.NET Core při spuštění vytvoří *hostitele* .</span><span class="sxs-lookup"><span data-stu-id="07786-300">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="07786-301">Hostitel je objekt, který zapouzdřuje všechny prostředky aplikace, jako například:</span><span class="sxs-lookup"><span data-stu-id="07786-301">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="07786-302">Implementaci HTTP serveru</span><span class="sxs-lookup"><span data-stu-id="07786-302">An HTTP server implementation</span></span>
* <span data-ttu-id="07786-303">Middlewarové komponenty</span><span class="sxs-lookup"><span data-stu-id="07786-303">Middleware components</span></span>
* <span data-ttu-id="07786-304">Protokolování</span><span class="sxs-lookup"><span data-stu-id="07786-304">Logging</span></span>
* <span data-ttu-id="07786-305">DI</span><span class="sxs-lookup"><span data-stu-id="07786-305">DI</span></span>
* <span data-ttu-id="07786-306">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="07786-306">Configuration</span></span>

<span data-ttu-id="07786-307">Hlavním důvodem pro začlenění všech vzájemně závislých prostředků v jednom objektu je správa životního cyklu: kontrola nad spuštěním aplikace a jejím řádném vypnutí.</span><span class="sxs-lookup"><span data-stu-id="07786-307">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="07786-308">K dispozici jsou dva hostitelé: webový hostitel a obecný hostitel.</span><span class="sxs-lookup"><span data-stu-id="07786-308">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="07786-309">V ASP.NET Core 2. x je obecný hostitel pouze pro scénáře, které nejsou webové.</span><span class="sxs-lookup"><span data-stu-id="07786-309">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="07786-310">Kód pro vytvoření hostitele je `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="07786-310">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

<span data-ttu-id="07786-311">Metoda `CreateDefaultBuilder` Konfiguruje hostitele pomocí běžně používaných možností, jako jsou například následující:</span><span class="sxs-lookup"><span data-stu-id="07786-311">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="07786-312">Použijte [Kestrel](#servers) jako webový server a povolte integraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="07786-312">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="07786-313">Načíst konfiguraci z *appSettings. JSON*, *appSettings. { Název prostředí}. JSON*, proměnné prostředí, argumenty příkazového řádku a další zdroje konfigurace.</span><span class="sxs-lookup"><span data-stu-id="07786-313">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="07786-314">Odeslání výstupu protokolování do konzoly a zprostředkovatelů ladění.</span><span class="sxs-lookup"><span data-stu-id="07786-314">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="07786-315">Další informace naleznete v tématu <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="07786-315">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="07786-316">Jiné než webové scénáře</span><span class="sxs-lookup"><span data-stu-id="07786-316">Non-web scenarios</span></span>

<span data-ttu-id="07786-317">Obecný hostitel umožňuje jiným typům aplikací používat rozšíření rozhraní pro průřez, jako je protokolování, vkládání závislostí (DI), konfigurace a správa životního cyklu aplikací.</span><span class="sxs-lookup"><span data-stu-id="07786-317">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="07786-318">Další informace naleznete v tématech <xref:fundamentals/host/generic-host> a <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="07786-318">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="07786-319">Servery</span><span class="sxs-lookup"><span data-stu-id="07786-319">Servers</span></span>

<span data-ttu-id="07786-320">Aplikace ASP.NET Core využívá implementaci HTTP serveru pro naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="07786-320">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="07786-321">Server objedná požadavky do aplikace jako sadu [funkcí požadavků](xref:fundamentals/request-features) složených do `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="07786-321">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="07786-322">Windows</span><span class="sxs-lookup"><span data-stu-id="07786-322">Windows</span></span>](#tab/windows)

<span data-ttu-id="07786-323">ASP.NET Core poskytuje následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="07786-323">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="07786-324">*Kestrel* je webový server pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="07786-324">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="07786-325">Kestrel se často spouští v konfiguraci reverzního proxy serveru pomocí [služby IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="07786-325">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="07786-326">Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="07786-326">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="07786-327">*Http server IIS* je server pro Windows, který používá službu IIS.</span><span class="sxs-lookup"><span data-stu-id="07786-327">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="07786-328">S tímto serverem jsou aplikace ASP.NET Core a služba IIS spuštěny v jednom společném procesu.</span><span class="sxs-lookup"><span data-stu-id="07786-328">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="07786-329">*Http. sys* je server pro Windows, který se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="07786-329">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="07786-330">macOS</span><span class="sxs-lookup"><span data-stu-id="07786-330">macOS</span></span>](#tab/macos)

<span data-ttu-id="07786-331">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="07786-331">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="07786-332">Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="07786-332">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="07786-333">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="07786-333">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="07786-334">Linux</span><span class="sxs-lookup"><span data-stu-id="07786-334">Linux</span></span>](#tab/linux)

<span data-ttu-id="07786-335">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="07786-335">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="07786-336">Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="07786-336">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="07786-337">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="07786-337">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="07786-338">Windows</span><span class="sxs-lookup"><span data-stu-id="07786-338">Windows</span></span>](#tab/windows)

<span data-ttu-id="07786-339">ASP.NET Core poskytuje následující implementace serveru:</span><span class="sxs-lookup"><span data-stu-id="07786-339">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="07786-340">*Kestrel* je webový server pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="07786-340">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="07786-341">Kestrel se často spouští v konfiguraci reverzního proxy serveru pomocí [služby IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="07786-341">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="07786-342">Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="07786-342">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="07786-343">*Http. sys* je server pro Windows, který se nepoužívá se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="07786-343">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="07786-344">macOS</span><span class="sxs-lookup"><span data-stu-id="07786-344">macOS</span></span>](#tab/macos)

<span data-ttu-id="07786-345">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="07786-345">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="07786-346">Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="07786-346">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="07786-347">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="07786-347">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="07786-348">Linux</span><span class="sxs-lookup"><span data-stu-id="07786-348">Linux</span></span>](#tab/linux)

<span data-ttu-id="07786-349">ASP.NET Core poskytuje *Kestrel* implementaci serveru pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="07786-349">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="07786-350">Kestrel je možné spustit jako veřejně přístupný hraniční Server, který je přístupný přímo na internetu.</span><span class="sxs-lookup"><span data-stu-id="07786-350">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="07786-351">Kestrel se často spouští v konfiguraci reverzního proxy serveru s [Nginx](https://nginx.org) nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="07786-351">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="07786-352">Další informace naleznete v tématu <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="07786-352">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="07786-353">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="07786-353">Configuration</span></span>

<span data-ttu-id="07786-354">ASP.NET Core poskytuje konfigurační framework, který získává nastavení jako dvojice název-hodnota z uspořádané množiny poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="07786-354">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="07786-355">Existují předdefinované poskytovatelé konfigurace pro nejrůznější zdroje, například soubory *. JSON* , soubory *. XML* , proměnné prostředí a argumenty příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="07786-355">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="07786-356">Můžete si také napsat vlastního zprostředkovatele konfigurace.</span><span class="sxs-lookup"><span data-stu-id="07786-356">You can also write custom configuration providers.</span></span>

<span data-ttu-id="07786-357">Například můžete určit, že konfigurace pochází z proměnných *appSettings. JSON* a proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="07786-357">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="07786-358">Poté, co je požadována hodnota *ConnectionString* , rozhraní vyhledá první v souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="07786-358">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="07786-359">Pokud je hodnota nalezena v souboru, ale zároveň také v proměnné prostředí, hodnota proměnné prostředí bude mít přednost.</span><span class="sxs-lookup"><span data-stu-id="07786-359">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="07786-360">Pro správu důvěrných konfiguračních dat, jako jsou hesla, ASP.NET Core poskytuje [Nástroj Správce tajných klíčů](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="07786-360">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="07786-361">V případě produkčních tajných kódů doporučujeme [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="07786-361">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="07786-362">Další informace naleznete v tématu <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="07786-362">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="07786-363">Možnosti</span><span class="sxs-lookup"><span data-stu-id="07786-363">Options</span></span>

<span data-ttu-id="07786-364">Pokud je to možné, ASP.NET Core podle *vzoru možností* pro ukládání a načítání hodnot konfigurace.</span><span class="sxs-lookup"><span data-stu-id="07786-364">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="07786-365">Vzor možností nastavení využívá třídy pro reprezentaci skupin souvisejících nastavení.</span><span class="sxs-lookup"><span data-stu-id="07786-365">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="07786-366">Následující kód například nastavuje možnosti Websocketů:</span><span class="sxs-lookup"><span data-stu-id="07786-366">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="07786-367">Další informace naleznete v tématu <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="07786-367">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="07786-368">Prostředí</span><span class="sxs-lookup"><span data-stu-id="07786-368">Environments</span></span>

<span data-ttu-id="07786-369">Spouštěcí prostředí, jako je *vývoj*, *Příprava*a *produkce*, jsou první třídou pojmu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07786-369">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="07786-370">Nastavením proměnné prostředí `ASPNETCORE_ENVIRONMENT` můžete určit prostředí, ve kterém je aplikace spuštěná.</span><span class="sxs-lookup"><span data-stu-id="07786-370">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="07786-371">ASP.NET Core přečte tuto proměnnou prostředí při spuštění aplikace a uloží hodnotu do implementace `IHostingEnvironment`.</span><span class="sxs-lookup"><span data-stu-id="07786-371">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="07786-372">Objekt prostředí je k dispozici kdekoli v aplikaci prostřednictvím DI.</span><span class="sxs-lookup"><span data-stu-id="07786-372">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="07786-373">Následující vzorový kód z `Startup` třídy nakonfiguruje aplikaci tak, aby poskytovala podrobné informace o chybách pouze v případě, že běží ve vývoji:</span><span class="sxs-lookup"><span data-stu-id="07786-373">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="07786-374">Další informace naleznete v tématu <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="07786-374">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="07786-375">Protokolování</span><span class="sxs-lookup"><span data-stu-id="07786-375">Logging</span></span>

<span data-ttu-id="07786-376">ASP.NET Core podporuje API pro protokolování, které funguje s množstvím zabudovaných poskytovatelů protokolování a poskytovatelů třetích stran.</span><span class="sxs-lookup"><span data-stu-id="07786-376">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="07786-377">Mezi dostupné poskytovatele patří:</span><span class="sxs-lookup"><span data-stu-id="07786-377">Available providers include the following:</span></span>

* <span data-ttu-id="07786-378">Konzola</span><span class="sxs-lookup"><span data-stu-id="07786-378">Console</span></span>
* <span data-ttu-id="07786-379">Ladění</span><span class="sxs-lookup"><span data-stu-id="07786-379">Debug</span></span>
* <span data-ttu-id="07786-380">Trasování událostí ve Windows</span><span class="sxs-lookup"><span data-stu-id="07786-380">Event Tracing on Windows</span></span>
* <span data-ttu-id="07786-381">Protokol událostí systému Windows</span><span class="sxs-lookup"><span data-stu-id="07786-381">Windows Event Log</span></span>
* <span data-ttu-id="07786-382">TraceSource</span><span class="sxs-lookup"><span data-stu-id="07786-382">TraceSource</span></span>
* <span data-ttu-id="07786-383">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="07786-383">Azure App Service</span></span>
* <span data-ttu-id="07786-384">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="07786-384">Azure Application Insights</span></span>

<span data-ttu-id="07786-385">Zápis protokolů z libovolného místa v kódu aplikace získáním `ILogger` objektu z DI a volání metod protokolu.</span><span class="sxs-lookup"><span data-stu-id="07786-385">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="07786-386">Zde je ukázkový kód, který používá objekt `ILogger`, s vkládáním konstruktoru a volání metody protokolování zvýrazněné.</span><span class="sxs-lookup"><span data-stu-id="07786-386">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="07786-387">Rozhraní `ILogger` umožňuje předat poskytovateli protokolování libovolný počet polí.</span><span class="sxs-lookup"><span data-stu-id="07786-387">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="07786-388">Pole se běžně používají pro konstrukci textu protokolovací zprávy, ale poskytovatel je může také odesílat odděleně do úložiště dat.</span><span class="sxs-lookup"><span data-stu-id="07786-388">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="07786-389">Tato funkce umožňuje poskytovatelům protokolování implementovat [sémantické protokolování, označované také jako strukturované protokolování](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="07786-389">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="07786-390">Další informace naleznete v tématu <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="07786-390">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="07786-391">Směrování</span><span class="sxs-lookup"><span data-stu-id="07786-391">Routing</span></span>

<span data-ttu-id="07786-392">*Trasa* je vzor URL, který je namapován na obslužnou rutinu.</span><span class="sxs-lookup"><span data-stu-id="07786-392">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="07786-393">Obslužnou rutinou je obvykle stránka Razor, metoda akce v MVC kontroleru nebo middleware.</span><span class="sxs-lookup"><span data-stu-id="07786-393">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="07786-394">Směřování ASP.NET Core Vám umožňuje kontrolovat adresy URL používané Vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="07786-394">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="07786-395">Další informace naleznete v tématu <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="07786-395">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="07786-396">Zpracování chyb</span><span class="sxs-lookup"><span data-stu-id="07786-396">Error handling</span></span>

<span data-ttu-id="07786-397">ASP.NET Core obsahuje integrované funkce pro zpracování chyb, jako například:</span><span class="sxs-lookup"><span data-stu-id="07786-397">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="07786-398">Stránka výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="07786-398">A developer exception page</span></span>
* <span data-ttu-id="07786-399">Vlastní chybové stránky</span><span class="sxs-lookup"><span data-stu-id="07786-399">Custom error pages</span></span>
* <span data-ttu-id="07786-400">Statické stránky chybových kódů</span><span class="sxs-lookup"><span data-stu-id="07786-400">Static status code pages</span></span>
* <span data-ttu-id="07786-401">Zpracování výjimek při spuštění</span><span class="sxs-lookup"><span data-stu-id="07786-401">Startup exception handling</span></span>

<span data-ttu-id="07786-402">Další informace naleznete v tématu <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="07786-402">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="07786-403">Vytváření požadavků HTTP</span><span class="sxs-lookup"><span data-stu-id="07786-403">Make HTTP requests</span></span>

<span data-ttu-id="07786-404">Pro vytváření instancí `HttpClient` je k dispozici implementace `IHttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="07786-404">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="07786-405">Objekt pro vytváření (Factory):</span><span class="sxs-lookup"><span data-stu-id="07786-405">The factory:</span></span>

* <span data-ttu-id="07786-406">Poskytuje centrální umístění pro pojmenovávání a konfiguraci logických `HttpClient` instancí.</span><span class="sxs-lookup"><span data-stu-id="07786-406">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="07786-407">Můžete například zaregistrovat klienta *GitHubu* a nakonfigurovat ho pro přístup k GitHubu.</span><span class="sxs-lookup"><span data-stu-id="07786-407">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="07786-408">Výchozí klient může být zaregistrován k jiným účelům.</span><span class="sxs-lookup"><span data-stu-id="07786-408">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="07786-409">Podporuje registraci a řetězení více delegujících obslužných rutin k vytvoření middlewarového kanálu požadavků.</span><span class="sxs-lookup"><span data-stu-id="07786-409">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="07786-410">Tento návrhový vzor je podobný příchozímu middlewarovému kanálu v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="07786-410">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="07786-411">Tento návrhový vzor poskytuje mechanismus pro implementaci průřezových zodpovědností kolem HTTP požadavků, včetně ukládání do mezipaměti, zpracování chyb, serializace a protokolování.</span><span class="sxs-lookup"><span data-stu-id="07786-411">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="07786-412">Integruje se s *Polly*oblíbenou knihovnou třetích stran pro zpracování přechodného selhání.</span><span class="sxs-lookup"><span data-stu-id="07786-412">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="07786-413">Spravuje sdružování a životnost základních instancí `HttpClientMessageHandler`, aby nedocházelo k běžným problémům služby DNS, ke kterým dochází při ruční správě `HttpClient` životního cyklu.</span><span class="sxs-lookup"><span data-stu-id="07786-413">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="07786-414">Přidá konfigurovatelné prostředí protokolování (prostřednictvím `ILogger`) pro všechny požadavky odeslané prostřednictvím klientů vytvořených pomocí továrny.</span><span class="sxs-lookup"><span data-stu-id="07786-414">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="07786-415">Další informace naleznete v tématu <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="07786-415">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="07786-416">Kořen obsahu</span><span class="sxs-lookup"><span data-stu-id="07786-416">Content root</span></span>

<span data-ttu-id="07786-417">Kořen obsahu je základní cesta k:</span><span class="sxs-lookup"><span data-stu-id="07786-417">The content root is the base path to the:</span></span>

* <span data-ttu-id="07786-418">Spustitelný soubor, který hostuje aplikaci ( *. exe*).</span><span class="sxs-lookup"><span data-stu-id="07786-418">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="07786-419">Kompilovaná sestavení, která tvoří aplikaci ( *. dll*).</span><span class="sxs-lookup"><span data-stu-id="07786-419">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="07786-420">Soubory s obsahem neobsahující kód, které používá aplikace, například:</span><span class="sxs-lookup"><span data-stu-id="07786-420">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="07786-421">Soubory Razor ( *. cshtml*, *. Razor*)</span><span class="sxs-lookup"><span data-stu-id="07786-421">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="07786-422">Konfigurační soubory ( *. JSON*, *. XML*)</span><span class="sxs-lookup"><span data-stu-id="07786-422">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="07786-423">Datové soubory ( *. DB*)</span><span class="sxs-lookup"><span data-stu-id="07786-423">Data files (*.db*)</span></span>
* <span data-ttu-id="07786-424">[Webový kořenový adresář](#web-root), obvykle publikovaná složka *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="07786-424">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="07786-425">Během vývoje:</span><span class="sxs-lookup"><span data-stu-id="07786-425">During development:</span></span>

* <span data-ttu-id="07786-426">Kořen obsahu se nastaví jako kořenový adresář projektu.</span><span class="sxs-lookup"><span data-stu-id="07786-426">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="07786-427">Kořenový adresář projektu slouží k vytvoření:</span><span class="sxs-lookup"><span data-stu-id="07786-427">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="07786-428">Cesta k souborům obsahu bez kódu aplikace v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="07786-428">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="07786-429">[Webový kořenový](#web-root)adresář, obvykle složka *wwwroot* v kořenovém adresáři projektu.</span><span class="sxs-lookup"><span data-stu-id="07786-429">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="07786-430">Alternativní cestu k kořenovému obsahu lze zadat při [sestavování hostitele](#host).</span><span class="sxs-lookup"><span data-stu-id="07786-430">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="07786-431">Další informace naleznete v tématu <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="07786-431">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="07786-432">Kořenový adresář webu</span><span class="sxs-lookup"><span data-stu-id="07786-432">Web root</span></span>

<span data-ttu-id="07786-433">Kořenový adresář webu je základní cesta k souborům statických prostředků bez kódu, jako je například:</span><span class="sxs-lookup"><span data-stu-id="07786-433">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="07786-434">Šablony stylů ( *. CSS*)</span><span class="sxs-lookup"><span data-stu-id="07786-434">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="07786-435">JavaScript ( *. js*)</span><span class="sxs-lookup"><span data-stu-id="07786-435">JavaScript (*.js*)</span></span>
* <span data-ttu-id="07786-436">Obrázky ( *. png*, *. jpg*)</span><span class="sxs-lookup"><span data-stu-id="07786-436">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="07786-437">Statické soubory jsou obsluhovány ve výchozím nastavení pouze z kořenového adresáře webu (a podadresářů).</span><span class="sxs-lookup"><span data-stu-id="07786-437">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="07786-438">Kořenová cesta webu je ve výchozím nastavení *{root Content}/wwwroot*, ale při [sestavování hostitele](#host)je možné zadat jiný webový kořenový adresář.</span><span class="sxs-lookup"><span data-stu-id="07786-438">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="07786-439">Další informace najdete v tématu [Web root](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="07786-439">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="07786-440">Zabraňte publikování souborů v *wwwroot* pomocí [\<obsahu > položky projektu](/visualstudio/msbuild/common-msbuild-project-items#content) v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="07786-440">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="07786-441">Následující příklad zabraňuje publikování obsahu v adresáři *wwwroot/Local* a v podadresářích:</span><span class="sxs-lookup"><span data-stu-id="07786-441">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="07786-442">V souborech Razor ( *. cshtml*) směřuje vlnové lomítko (`~/`) do kořenového adresáře webu.</span><span class="sxs-lookup"><span data-stu-id="07786-442">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="07786-443">Cesta začínající na `~/` je označována jako *virtuální cesta*.</span><span class="sxs-lookup"><span data-stu-id="07786-443">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="07786-444">Další informace naleznete v tématu <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="07786-444">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end