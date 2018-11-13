---
title: Implementace serveru webové kestrel v ASP.NET Core
author: guardrex
description: Další informace o Kestrel, napříč platformami webový server pro ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 11/05/2018
uid: fundamentals/servers/kestrel
ms.openlocfilehash: a26726a824db31e07b881dbfa8dc2ef37d4d3492
ms.sourcegitcommit: edb9d2d78c9a4d68b397e74ae2aff088b325a143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51505762"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="69c33-103">Implementace serveru webové kestrel v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="69c33-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="69c33-104">Podle [Petr Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), a [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="69c33-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

<span data-ttu-id="69c33-105">1.1 verzi tohoto tématu, stáhněte si [Kestrel webového serveru provedení v ASP.NET Core (verze 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Kestrel_1.1.pdf).</span><span class="sxs-lookup"><span data-stu-id="69c33-105">For the 1.1 version of this topic, download [Kestrel web server implementation in ASP.NET Core (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Kestrel_1.1.pdf).</span></span>

<span data-ttu-id="69c33-106">Kestrel je platformově univerzální [webového serveru pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="69c33-106">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="69c33-107">Kestrel je webový server, který je obsažen ve výchozím nastavení v šablonách projektů ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="69c33-107">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="69c33-108">Kestrel podporuje následující funkce:</span><span class="sxs-lookup"><span data-stu-id="69c33-108">Kestrel supports the following features:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="69c33-109">HTTPS</span><span class="sxs-lookup"><span data-stu-id="69c33-109">HTTPS</span></span>
* <span data-ttu-id="69c33-110">Neprůhledný upgrade nepoužívá k zapnutí [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="69c33-110">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="69c33-111">Soketů systému UNIX pro vysoký výkon za serveru Nginx</span><span class="sxs-lookup"><span data-stu-id="69c33-111">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="69c33-112">HTTP/2 (s výjimkou v systému macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="69c33-112">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="69c33-113">&dagger;HTTP/2 budou podporované v systému macOS v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="69c33-113">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="69c33-114">HTTPS</span><span class="sxs-lookup"><span data-stu-id="69c33-114">HTTPS</span></span>
* <span data-ttu-id="69c33-115">Neprůhledný upgrade nepoužívá k zapnutí [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="69c33-115">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="69c33-116">Soketů systému UNIX pro vysoký výkon za serveru Nginx</span><span class="sxs-lookup"><span data-stu-id="69c33-116">Unix sockets for high performance behind Nginx</span></span>

::: moniker-end

<span data-ttu-id="69c33-117">Kestrel se podporuje na všech platformách a verze, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="69c33-117">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="69c33-118">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="69c33-118">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="http2-support"></a><span data-ttu-id="69c33-119">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="69c33-119">HTTP/2 support</span></span>

<span data-ttu-id="69c33-120">[HTTP/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro aplikace ASP.NET Core, pokud tyto požadavky byly splněny:</span><span class="sxs-lookup"><span data-stu-id="69c33-120">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="69c33-121">Operační systém&dagger;</span><span class="sxs-lookup"><span data-stu-id="69c33-121">Operating system&dagger;</span></span>
  * <span data-ttu-id="69c33-122">Windows Server 2016 nebo Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="69c33-122">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="69c33-123">Linux s OpenSSL 1.0.2 nebo novější (například Ubuntu 16.04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="69c33-123">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="69c33-124">Cílová architektura: .NET Core 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="69c33-124">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="69c33-125">[Vyjednávání protokolu v aplikační vrstvě (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) připojení</span><span class="sxs-lookup"><span data-stu-id="69c33-125">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="69c33-126">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="69c33-126">TLS 1.2 or later connection</span></span>

<span data-ttu-id="69c33-127">&dagger;HTTP/2 budou podporované v systému macOS v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="69c33-127">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="69c33-128">&Dagger;Kestrel má omezenou podporu pro HTTP/2 pro systém Windows Server 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="69c33-128">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="69c33-129">Podpora je omezená, protože seznam podporovaných šifer TLS sady k dispozici v těchto operačních systémech je omezen.</span><span class="sxs-lookup"><span data-stu-id="69c33-129">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="69c33-130">Certifikát vytvořený pomocí křivky digitální podpis algoritmů ECDSA (Elliptic) může být nutné zabezpečení připojení protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="69c33-130">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="69c33-131">Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="69c33-131">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="69c33-132">HTTP/2 je ve výchozím nastavení zakázána.</span><span class="sxs-lookup"><span data-stu-id="69c33-132">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="69c33-133">Další informace o konfiguraci, najdete v článku [Kestrel možnosti](#kestrel-options) a [ListenOptions.Protocols](#listenoptionsprotocols) oddíly.</span><span class="sxs-lookup"><span data-stu-id="69c33-133">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

::: moniker-end

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="69c33-134">Kdy použít Kestrel pomocí reverzního proxy serveru</span><span class="sxs-lookup"><span data-stu-id="69c33-134">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="69c33-135">Kestrel můžete použít samostatně nebo se *reverzní proxy server*, jako je například Apache, IIS nebo Nginx.</span><span class="sxs-lookup"><span data-stu-id="69c33-135">You can use Kestrel by itself or with a *reverse proxy server*, such as IIS, Nginx, or Apache.</span></span> <span data-ttu-id="69c33-136">Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel po některé předběžného zpracování.</span><span class="sxs-lookup"><span data-stu-id="69c33-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling.</span></span>

![Kestrel komunikuje přímo s Internetu bez reverzní proxy server](kestrel/_static/kestrel-to-internet2.png)

![Kestrel nepřímo komunikuje přes Internet prostřednictvím reverzního proxy serveru, jako je například Apache, IIS nebo Nginx](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="69c33-139">Buď konfiguraci&mdash;s nebo bez něj reverzní proxy server&mdash;je platný a podporované konfigurace pro hostování pro ASP.NET Core 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="69c33-139">Either configuration&mdash;with or without a reverse proxy server&mdash;is a valid and supported hosting configuration for ASP.NET Core 2.0 or later apps.</span></span>

<span data-ttu-id="69c33-140">Scénář reverzního proxy serveru existuje, pokud existuje víc aplikací, které sdílejí stejné IP adresy a portu, které běží na jednom serveru.</span><span class="sxs-lookup"><span data-stu-id="69c33-140">A reverse proxy scenario exists when there are multiple apps that share the same IP and port running on a single server.</span></span> <span data-ttu-id="69c33-141">Kestrel tento scénář nepodporuje, protože Kestrel nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="69c33-141">Kestrel doesn't support this scenario because Kestrel doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="69c33-142">Když Kestrel je nakonfigurovaná k naslouchání na portu, zpracovává Kestrel veškerý síťový provoz na tento port bez ohledu na to, požadavky se hlavička hostitele.</span><span class="sxs-lookup"><span data-stu-id="69c33-142">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' host header.</span></span> <span data-ttu-id="69c33-143">Reverzní proxy server, který můžete sdílet porty má schopnost Kestrel na jedinečné IP adresy a portu směrování žádostí.</span><span class="sxs-lookup"><span data-stu-id="69c33-143">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="69c33-144">I v případě reverzního proxy serveru není povinné, pomocí reverzního proxy serveru může být dobrou volbou:</span><span class="sxs-lookup"><span data-stu-id="69c33-144">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice:</span></span>

* <span data-ttu-id="69c33-145">Může být omezena vystavené veřejné útoku na aplikace, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="69c33-145">It can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="69c33-146">Poskytuje další úroveň ochrany a konfigurace.</span><span class="sxs-lookup"><span data-stu-id="69c33-146">It provides an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="69c33-147">Integrace může lépe se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="69c33-147">It might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="69c33-148">Zjednodušuje Vyrovnávání zatížení a konfigurace protokolu SSL.</span><span class="sxs-lookup"><span data-stu-id="69c33-148">It simplifies load balancing and SSL configuration.</span></span> <span data-ttu-id="69c33-149">Reverzní proxy server vyžaduje certifikát SSL, a tento server může komunikovat s aplikačních serverů v interní síti přes standardní HTTP.</span><span class="sxs-lookup"><span data-stu-id="69c33-149">Only the reverse proxy server requires an SSL certificate, and that server can communicate with your app servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="69c33-150">Pokud nepoužíváte reverzního proxy serveru s hostitelem filtrování povolena, [hostitele filtrování](#host-filtering) musí být povolené.</span><span class="sxs-lookup"><span data-stu-id="69c33-150">If not using a reverse proxy with host filtering enabled, [host filtering](#host-filtering) must be enabled.</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="69c33-151">Jak používat Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="69c33-151">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="69c33-152">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí balíčku [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="69c33-152">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="69c33-153">Šablony projektů ASP.NET Core pomocí Kestrel ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="69c33-153">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="69c33-154">V *Program.cs*, kód volání šablony [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), který volá [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) na pozadí.</span><span class="sxs-lookup"><span data-stu-id="69c33-154">In *Program.cs*, the template code calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), which calls [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="69c33-155">Poskytnout další konfigurace po volání `CreateDefaultBuilder`, použijte `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="69c33-155">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Set properties and call methods on options
        });
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="69c33-156">Poskytnout další konfigurace po volání `CreateDefaultBuilder`, volání [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel):</span><span class="sxs-lookup"><span data-stu-id="69c33-156">To provide additional configuration after calling `CreateDefaultBuilder`, call [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel):</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            // Set properties and call methods on options
        });
```

::: moniker-end

## <a name="kestrel-options"></a><span data-ttu-id="69c33-157">Možnosti kestrel</span><span class="sxs-lookup"><span data-stu-id="69c33-157">Kestrel options</span></span>

<span data-ttu-id="69c33-158">Webový server Kestrel má omezení možnosti konfigurace, které jsou obzvláště užitečné v nasazeních s přístupem k Internetu.</span><span class="sxs-lookup"><span data-stu-id="69c33-158">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="69c33-159">Nastavit na omezení [omezení](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.limits) vlastnost [KestrelServerOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions) třídy.</span><span class="sxs-lookup"><span data-stu-id="69c33-159">Set constraints on the [Limits](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.limits) property of the [KestrelServerOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions) class.</span></span> <span data-ttu-id="69c33-160">`Limits` Vlastnost obsahuje instanci [KestrelServerLimits](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits) třídy.</span><span class="sxs-lookup"><span data-stu-id="69c33-160">The `Limits` property holds an instance of the [KestrelServerLimits](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits) class.</span></span>

### <a name="maximum-client-connections"></a><span data-ttu-id="69c33-161">Maximální počet klientských připojení</span><span class="sxs-lookup"><span data-stu-id="69c33-161">Maximum client connections</span></span>

[<span data-ttu-id="69c33-162">MaxConcurrentConnections</span><span class="sxs-lookup"><span data-stu-id="69c33-162">MaxConcurrentConnections</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxconcurrentconnections)  
[<span data-ttu-id="69c33-163">MaxConcurrentUpgradedConnections</span><span class="sxs-lookup"><span data-stu-id="69c33-163">MaxConcurrentUpgradedConnections</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxconcurrentupgradedconnections)

<span data-ttu-id="69c33-164">Lze nastavit maximální počet souběžných otevřená připojení TCP pro celou aplikaci s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="69c33-164">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxConcurrentConnections = 100;
        });
```

::: moniker-end

<span data-ttu-id="69c33-165">Neexistuje samostatné limit pro připojení, která se upgradovaly z protokolu HTTP nebo HTTPS na jiné protokol (například v požadavku Websocket).</span><span class="sxs-lookup"><span data-stu-id="69c33-165">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="69c33-166">Po dokončení upgradu spojení se započítává `MaxConcurrentConnections` limit.</span><span class="sxs-lookup"><span data-stu-id="69c33-166">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

::: moniker-end

<span data-ttu-id="69c33-167">Maximální počet připojení je neomezený počet (null) ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="69c33-167">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="69c33-168">Velikost textu maximální požadavku</span><span class="sxs-lookup"><span data-stu-id="69c33-168">Maximum request body size</span></span>

[<span data-ttu-id="69c33-169">MaxRequestBodySize</span><span class="sxs-lookup"><span data-stu-id="69c33-169">MaxRequestBodySize</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize)

<span data-ttu-id="69c33-170">Výchozí velikost těla maximální požadavku je 30,000,000 bajtů, což je přibližně 28.6 MB.</span><span class="sxs-lookup"><span data-stu-id="69c33-170">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="69c33-171">Doporučený postup, chcete-li přepsat omezení v aplikaci ASP.NET Core MVC je použít [RequestSizeLimit](/dotnet/api/microsoft.aspnetcore.mvc.requestsizelimitattribute) atributu na metodu akce:</span><span class="sxs-lookup"><span data-stu-id="69c33-171">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the [RequestSizeLimit](/dotnet/api/microsoft.aspnetcore.mvc.requestsizelimitattribute) attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="69c33-172">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikace u každého požadavku:</span><span class="sxs-lookup"><span data-stu-id="69c33-172">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="69c33-173">Můžete přepsat nastavení pro konkrétní žádost a v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="69c33-173">You can override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

::: moniker-end

<span data-ttu-id="69c33-174">Pokud se pokusíte nakonfigurovat limit na vyžádání po spuštění aplikace k přečtení požadavku, je vyvolána výjimka.</span><span class="sxs-lookup"><span data-stu-id="69c33-174">An exception is thrown if you attempt to configure the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="69c33-175">Je `IsReadOnly` vlastnost, která označuje, zda `MaxRequestBodySize` vlastnost je ve stavu jen pro čtení, což znamená, je příliš pozdě Konfigurace limitu.</span><span class="sxs-lookup"><span data-stu-id="69c33-175">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="69c33-176">Minimální požadavek tělo přenosová rychlost</span><span class="sxs-lookup"><span data-stu-id="69c33-176">Minimum request body data rate</span></span>

[<span data-ttu-id="69c33-177">MinRequestBodyDataRate</span><span class="sxs-lookup"><span data-stu-id="69c33-177">MinRequestBodyDataRate</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.minrequestbodydatarate)  
[<span data-ttu-id="69c33-178">MinResponseDataRate</span><span class="sxs-lookup"><span data-stu-id="69c33-178">MinResponseDataRate</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.minresponsedatarate)

<span data-ttu-id="69c33-179">Kestrel kontroluje každou sekundu, pokud je dat přicházejících u určenou míru v bajtech/sekundu.</span><span class="sxs-lookup"><span data-stu-id="69c33-179">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="69c33-180">Pokud míra klesne pod minimální, vypršení časového limitu připojení. Období odkladu je množství času, aby Kestrel poskytuje klienta ke zvýšení jeho míra odesílání z až Toto minimum, frekvence není kontrolován během této doby.</span><span class="sxs-lookup"><span data-stu-id="69c33-180">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="69c33-181">Období odkladu pomáhá předejít, vyřadit připojení, která původně odesílají data s nízkou rychlostí kvůli zpomalit TCP-start.</span><span class="sxs-lookup"><span data-stu-id="69c33-181">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="69c33-182">Výchozí minimální rychlost je 240 bajtů za sekundu s období odkladu 5 sekund.</span><span class="sxs-lookup"><span data-stu-id="69c33-182">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="69c33-183">Minimální sazba platí také pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="69c33-183">A minimum rate also applies to the response.</span></span> <span data-ttu-id="69c33-184">Kód pro nastavení limitu požadavků a omezení odpovědi je stejná s výjimkou s `RequestBody` nebo `Response` v názvech vlastností a interface.</span><span class="sxs-lookup"><span data-stu-id="69c33-184">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="69c33-185">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové sazby v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="69c33-185">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            options.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

::: moniker-end

<span data-ttu-id="69c33-186">Omezení pro minimální rychlost žádosti v middlewaru můžete přepsat:</span><span class="sxs-lookup"><span data-stu-id="69c33-186">You can override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="69c33-187">Ani jedna kurzu funkce odkazované v předchozí ukázce se nacházejí v `HttpContext.Features` pro požadavky HTTP/2, protože úprava šířku pásma na základě žádosti se nepodporuje pro HTTP/2 z důvodu multiplexing žádost o podporu protokolu.</span><span class="sxs-lookup"><span data-stu-id="69c33-187">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="69c33-188">Omezení přenosové rychlosti úrovni serveru, které jsou nakonfigurované přes `KestrelServerOptions.Limits` stále použít k připojení HTTP/1.x a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="69c33-188">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="69c33-189">Maximální datových proudů za připojení</span><span class="sxs-lookup"><span data-stu-id="69c33-189">Maximum streams per connection</span></span>

<span data-ttu-id="69c33-190">`Http2.MaxStreamsPerConnection` omezuje počet datových proudů souběžný požadavek na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="69c33-190">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="69c33-191">Nadbytečné datové proudy jsou odmítnuta.</span><span class="sxs-lookup"><span data-stu-id="69c33-191">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="69c33-192">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="69c33-192">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="69c33-193">Velikost záhlaví tabulky</span><span class="sxs-lookup"><span data-stu-id="69c33-193">Header table size</span></span>

<span data-ttu-id="69c33-194">Dekodér HPACK dekomprimuje hlavičky protokolu HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="69c33-194">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="69c33-195">`Http2.HeaderTableSize` omezuje velikost tabulky záhlaví komprese, který používá HPACK dekodéru.</span><span class="sxs-lookup"><span data-stu-id="69c33-195">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="69c33-196">Hodnota je k dispozici v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="69c33-196">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="69c33-197">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="69c33-197">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="69c33-198">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="69c33-198">Maximum frame size</span></span>

<span data-ttu-id="69c33-199">`Http2.MaxFrameSize` Určuje maximální velikost datové rámce připojení HTTP/2 pro příjem.</span><span class="sxs-lookup"><span data-stu-id="69c33-199">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="69c33-200">Hodnota je k dispozici v oktetech a musí být mezi 2 ^ 14 (16384) a 2 ^ 16 777 24-1 (215).</span><span class="sxs-lookup"><span data-stu-id="69c33-200">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="69c33-201">Výchozí hodnota je 2 ^ 14 (16384).</span><span class="sxs-lookup"><span data-stu-id="69c33-201">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="69c33-202">Požadavek na maximální velikost záhlaví</span><span class="sxs-lookup"><span data-stu-id="69c33-202">Maximum request header size</span></span>

<span data-ttu-id="69c33-203">`Http2.MaxRequestHeaderFieldSize` Určuje maximální povolenou velikost v oktetech hodnot hlavičky žádosti.</span><span class="sxs-lookup"><span data-stu-id="69c33-203">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="69c33-204">Toto omezení platí pro název a hodnotu společně v jejich reprezentace nekomprimovaný a komprimovaný.</span><span class="sxs-lookup"><span data-stu-id="69c33-204">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="69c33-205">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="69c33-205">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="69c33-206">Výchozí hodnota je 8192.</span><span class="sxs-lookup"><span data-stu-id="69c33-206">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="69c33-207">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="69c33-207">Initial connection window size</span></span>

<span data-ttu-id="69c33-208">`Http2.InitialConnectionWindowSize` Určuje maximální žádosti o data těla bajtovou vyrovnávací paměti serveru v jednom okamžiku agregované napříč všemi požadavky (datové proudy) za připojení.</span><span class="sxs-lookup"><span data-stu-id="69c33-208">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="69c33-209">Požadavky jsou také omezit pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="69c33-209">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="69c33-210">Hodnota musí být větší než nebo rovna hodnotě 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="69c33-210">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="69c33-211">Výchozí hodnota je 128 KB (131,072).</span><span class="sxs-lookup"><span data-stu-id="69c33-211">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="69c33-212">Datový proud počáteční velikost okna</span><span class="sxs-lookup"><span data-stu-id="69c33-212">Initial stream window size</span></span>

<span data-ttu-id="69c33-213">`Http2.InitialStreamWindowSize` Určuje maximální žádosti o data těla v bajtech vyrovnávacích pamětí serveru současně za žádost (datový proud).</span><span class="sxs-lookup"><span data-stu-id="69c33-213">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="69c33-214">Požadavky jsou také omezit pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="69c33-214">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="69c33-215">Hodnota musí být větší než nebo rovna hodnotě 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="69c33-215">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="69c33-216">Výchozí hodnota je velikosti 96 KB (98,304).</span><span class="sxs-lookup"><span data-stu-id="69c33-216">The default value is 96 KB (98,304).</span></span>

::: moniker-end

<span data-ttu-id="69c33-217">Informace o dalších možnostech Kestrel a omezení najdete tady:</span><span class="sxs-lookup"><span data-stu-id="69c33-217">For information about other Kestrel options and limits, see:</span></span>

* [<span data-ttu-id="69c33-218">KestrelServerOptions</span><span class="sxs-lookup"><span data-stu-id="69c33-218">KestrelServerOptions</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions)
* [<span data-ttu-id="69c33-219">KestrelServerLimits</span><span class="sxs-lookup"><span data-stu-id="69c33-219">KestrelServerLimits</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits)
* [<span data-ttu-id="69c33-220">ListenOptions</span><span class="sxs-lookup"><span data-stu-id="69c33-220">ListenOptions</span></span>](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.listenoptions)

## <a name="endpoint-configuration"></a><span data-ttu-id="69c33-221">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="69c33-221">Endpoint configuration</span></span>

<span data-ttu-id="69c33-222">Ve výchozím nastavení ASP.NET Core váže na:</span><span class="sxs-lookup"><span data-stu-id="69c33-222">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="69c33-223">`https://localhost:5001` (Pokud je místní vývojový certifikát k dispozici)</span><span class="sxs-lookup"><span data-stu-id="69c33-223">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="69c33-224">Certifikát pro vývoj se vytvoří:</span><span class="sxs-lookup"><span data-stu-id="69c33-224">A development certificate is created:</span></span>

* <span data-ttu-id="69c33-225">Když [.NET Core SDK](/dotnet/core/sdk) je nainstalována.</span><span class="sxs-lookup"><span data-stu-id="69c33-225">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="69c33-226">[Nástroji dev-certs](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="69c33-226">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="69c33-227">Některé prohlížeče vyžadují, abyste udělili explicitní oprávnění pro prohlížeč důvěřovat certifikátům místní vývoj.</span><span class="sxs-lookup"><span data-stu-id="69c33-227">Some browsers require that you grant explicit permission to the browser to trust the local development certificate.</span></span>

<span data-ttu-id="69c33-228">ASP.NET Core 2.1 a vyšší šablony projektů konfigurace aplikací ve výchozím nastavení spouští na protokol HTTPS a zahrnout [přesměrování protokolu HTTPS a HSTS podporují](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="69c33-228">ASP.NET Core 2.1 and later project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="69c33-229">Volání [naslouchání](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listen) nebo [ListenUnixSocket](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listenunixsocket) metody [KestrelServerOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions) konfigurace předpony adres URL a portů pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="69c33-229">Call [Listen](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listen) or [ListenUnixSocket](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listenunixsocket) methods on [KestrelServerOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions) to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="69c33-230">`UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele a `ASPNETCORE_URLS` proměnnou prostředí také pracovní ale mají omezení, později uvedené v této části (výchozí certifikát musí být k dispozici pro koncový bod HTTPS Konfigurace).</span><span class="sxs-lookup"><span data-stu-id="69c33-230">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="69c33-231">ASP.NET Core 2.1 `KestrelServerOptions` konfigurace:</span><span class="sxs-lookup"><span data-stu-id="69c33-231">ASP.NET Core 2.1 `KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionltlistenoptionsgt"></a><span data-ttu-id="69c33-232">ConfigureEndpointDefaults (akce&lt;ListenOptions&gt;)</span><span class="sxs-lookup"><span data-stu-id="69c33-232">ConfigureEndpointDefaults(Action&lt;ListenOptions&gt;)</span></span>

<span data-ttu-id="69c33-233">Určuje konfiguraci `Action` pro spuštění v každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="69c33-233">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="69c33-234">Volání `ConfigureEndpointDefaults` více než jednou nahradí předchozí `Action`s poslední `Action` zadané.</span><span class="sxs-lookup"><span data-stu-id="69c33-234">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

### <a name="configurehttpsdefaultsactionlthttpsconnectionadapteroptionsgt"></a><span data-ttu-id="69c33-235">ConfigureHttpsDefaults (akce&lt;HttpsConnectionAdapterOptions&gt;)</span><span class="sxs-lookup"><span data-stu-id="69c33-235">ConfigureHttpsDefaults(Action&lt;HttpsConnectionAdapterOptions&gt;)</span></span>

<span data-ttu-id="69c33-236">Určuje konfiguraci `Action` ke spuštění pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="69c33-236">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="69c33-237">Volání `ConfigureHttpsDefaults` více než jednou nahradí předchozí `Action`s poslední `Action` zadané.</span><span class="sxs-lookup"><span data-stu-id="69c33-237">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="69c33-238">Configure(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="69c33-238">Configure(IConfiguration)</span></span>

<span data-ttu-id="69c33-239">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá [parametry IConfiguration](/dotnet/api/microsoft.extensions.configuration.iconfiguration) jako vstup.</span><span class="sxs-lookup"><span data-stu-id="69c33-239">Creates a configuration loader for setting up Kestrel that takes an [IConfiguration](/dotnet/api/microsoft.extensions.configuration.iconfiguration) as input.</span></span> <span data-ttu-id="69c33-240">Konfigurace musí být určená ke konfiguračnímu oddílu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="69c33-240">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="69c33-241">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="69c33-241">ListenOptions.UseHttps</span></span>

<span data-ttu-id="69c33-242">Nakonfigurujte Kestrel k používání HTTPS.</span><span class="sxs-lookup"><span data-stu-id="69c33-242">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="69c33-243">`ListenOptions.UseHttps` rozšíření:</span><span class="sxs-lookup"><span data-stu-id="69c33-243">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="69c33-244">`UseHttps` &ndash; Nakonfigurujte Kestrel k používání HTTPS pomocí certifikátu výchozí.</span><span class="sxs-lookup"><span data-stu-id="69c33-244">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="69c33-245">Vyvolá výjimku, pokud je nakonfigurovaný žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="69c33-245">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="69c33-246">`ListenOptions.UseHttps` Parametry:</span><span class="sxs-lookup"><span data-stu-id="69c33-246">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="69c33-247">`filename` je název a cesta k souboru soubor certifikátu, relativní k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="69c33-247">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="69c33-248">`password` je heslo pro přístup k datům certifikátu X.509.</span><span class="sxs-lookup"><span data-stu-id="69c33-248">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="69c33-249">`configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="69c33-249">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="69c33-250">Vrátí `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="69c33-250">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="69c33-251">`storeName` je do úložiště certifikátů, ze kterého se má načíst certifikát.</span><span class="sxs-lookup"><span data-stu-id="69c33-251">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="69c33-252">`subject` je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="69c33-252">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="69c33-253">`allowInvalid` Označuje, pokud neplatné certifikáty by měl být, jako jsou certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="69c33-253">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="69c33-254">`location` je umístění úložiště pro načtení certifikátu.</span><span class="sxs-lookup"><span data-stu-id="69c33-254">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="69c33-255">`serverCertificate` je certifikát X.509.</span><span class="sxs-lookup"><span data-stu-id="69c33-255">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="69c33-256">V produkčním prostředí musí být explicitně nakonfigurován protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="69c33-256">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="69c33-257">Minimálně je třeba zadat výchozího certifikátu.</span><span class="sxs-lookup"><span data-stu-id="69c33-257">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="69c33-258">Podporované konfigurace je popsáno dále:</span><span class="sxs-lookup"><span data-stu-id="69c33-258">Supported configurations described next:</span></span>

* <span data-ttu-id="69c33-259">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="69c33-259">No configuration</span></span>
* <span data-ttu-id="69c33-260">Nahraďte výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="69c33-260">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="69c33-261">Změnit výchozí nastavení v kódu</span><span class="sxs-lookup"><span data-stu-id="69c33-261">Change the defaults in code</span></span>

<span data-ttu-id="69c33-262">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="69c33-262">*No configuration*</span></span>

<span data-ttu-id="69c33-263">Naslouchá kestrel `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí cert).</span><span class="sxs-lookup"><span data-stu-id="69c33-263">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<span data-ttu-id="69c33-264">Určení adres URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="69c33-264">Specify URLs using the:</span></span>

* <span data-ttu-id="69c33-265">`ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="69c33-265">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="69c33-266">`--urls` argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="69c33-266">`--urls` command-line argument.</span></span>
* <span data-ttu-id="69c33-267">`urls` Konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="69c33-267">`urls` host configuration key.</span></span>
* <span data-ttu-id="69c33-268">`UseUrls` metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="69c33-268">`UseUrls` extension method.</span></span>

<span data-ttu-id="69c33-269">Další informace najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [konfigurace přepisování](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="69c33-269">For more information, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="69c33-270">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více HTTP a HTTPS koncové body (HTTPS Pokud je k dispozici výchozí cert).</span><span class="sxs-lookup"><span data-stu-id="69c33-270">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="69c33-271">Nakonfigurujte tuto hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="69c33-271">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="69c33-272">*Nahraďte výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="69c33-272">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="69c33-273">[WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) volání `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` ve výchozím nastavení se načíst konfiguraci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="69c33-273">[WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="69c33-274">Schéma konfigurace k nastavení výchozí HTTPS aplikace je k dispozici pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="69c33-274">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="69c33-275">Konfigurovat několik koncových bodů, včetně adresy URL a certifikáty, které chcete použít, ze souboru na disku nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="69c33-275">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="69c33-276">V následujícím *appsettings.json* příkladu:</span><span class="sxs-lookup"><span data-stu-id="69c33-276">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="69c33-277">Nastavte **AllowInvalid** k `true` tak, aby povolovala použití neplatné certifikáty (například certifikáty podepsané svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="69c33-277">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="69c33-278">Libovolný koncový bod HTTPS, který nemá určenou certifikát (**HttpsDefaultCert** v následujícím příkladu) spadne zpět na cert definované v části **certifikáty** > **výchozí**  nebo certifikát pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="69c33-278">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
"Kestrel": {
  "EndPoints": {
    "Http": {
      "Url": "http://localhost:5000"
    },

    "HttpsInlineCertFile": {
      "Url": "https://localhost:5001",
      "Certificate": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    },

    "HttpsInlineCertStore": {
      "Url": "https://localhost:5002",
      "Certificate": {
        "Subject": "<subject; required>",
        "Store": "<certificate store; defaults to My>",
        "Location": "<location; defaults to CurrentUser>",
        "AllowInvalid": "<true or false; defaults to false>"
      }
    },

    "HttpsDefaultCert": {
      "Url": "https://localhost:5003"
    },

    "Https": {
      "Url": "https://*:5004",
      "Certificate": {
      "Path": "<path to .pfx file>",
      "Password": "<certificate password>"
      }
    }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="69c33-279">O alternativu k použití **cesta** a **heslo** pro jakýkoliv certifikát je uzel a určete certifikát, pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="69c33-279">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="69c33-280">Například **certifikáty** > **výchozí** certifikát lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="69c33-280">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; defaults to My>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="69c33-281">Schéma poznámek:</span><span class="sxs-lookup"><span data-stu-id="69c33-281">Schema notes:</span></span>

* <span data-ttu-id="69c33-282">Koncové body názvy jsou malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="69c33-282">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="69c33-283">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="69c33-283">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="69c33-284">`Url` Parametr je povinný pro každý koncový bod.</span><span class="sxs-lookup"><span data-stu-id="69c33-284">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="69c33-285">Formát pro tento parametr je stejné jako na nejvyšší úrovni `Urls` parametr konfigurace s výjimkou, že je omezena na jednu hodnotu.</span><span class="sxs-lookup"><span data-stu-id="69c33-285">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="69c33-286">Nahraďte tyto koncové body jsou definované na nejvyšší úrovni `Urls` místo přidávání k nim.</span><span class="sxs-lookup"><span data-stu-id="69c33-286">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="69c33-287">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní se koncové body definované v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="69c33-287">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="69c33-288">`Certificate` Část je nepovinná.</span><span class="sxs-lookup"><span data-stu-id="69c33-288">The `Certificate` section is optional.</span></span> <span data-ttu-id="69c33-289">Pokud `Certificate` oddílu není zadán, budou použity výchozí hodnoty definované v předchozích případech.</span><span class="sxs-lookup"><span data-stu-id="69c33-289">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="69c33-290">Pokud nejsou k dispozici žádné výchozí hodnoty, vyvolá výjimku, server a nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="69c33-290">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="69c33-291">`Certificate` Části podporuje obě **cesta**&ndash;**heslo** a **subjektu**&ndash;**Store** certifikáty.</span><span class="sxs-lookup"><span data-stu-id="69c33-291">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="69c33-292">Tímto způsobem lze definovat libovolný počet koncových bodů tak dlouho, dokud není způsobují konflikty portu.</span><span class="sxs-lookup"><span data-stu-id="69c33-292">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="69c33-293">`options.Configure(context.Configuration.GetSection("Kestrel"))` Vrátí `KestrelConfigurationLoader` s `.Endpoint(string name, options => { })` metodu, která slouží k doplnění nastavení konfigurovaný koncový bod:</span><span class="sxs-lookup"><span data-stu-id="69c33-293">`options.Configure(context.Configuration.GetSection("Kestrel"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, options => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

  ```csharp
  options.Configure(context.Configuration.GetSection("Kestrel"))
      .Endpoint("HTTPS", opt =>
      {
          opt.HttpsOptions.SslProtocols = SslProtocols.Tls12;
      });
  ```

  <span data-ttu-id="69c33-294">Můžete také přímý přístup k `KestrelServerOptions.ConfigurationLoader` chcete zachovat iterace na existující zavaděč, jako je ta poskytuje [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder).</span><span class="sxs-lookup"><span data-stu-id="69c33-294">You can also directly access `KestrelServerOptions.ConfigurationLoader` to keep iterating on the existing loader, such as the one provided by [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder).</span></span>

* <span data-ttu-id="69c33-295">Oddíl konfigurace pro každý koncový bod je k dispozici na možnosti v `Endpoint` metodu tak, aby mohou číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="69c33-295">The configuration section for each endpoint is a available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="69c33-296">Více konfigurací může být načten voláním `options.Configure(context.Configuration.GetSection("Kestrel"))` znovu s jinou část.</span><span class="sxs-lookup"><span data-stu-id="69c33-296">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("Kestrel"))` again with another section.</span></span> <span data-ttu-id="69c33-297">Pouze poslední konfigurace se použije, pokud `Load` je explicitně zavolán v předchozích instancí.</span><span class="sxs-lookup"><span data-stu-id="69c33-297">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="69c33-298">Microsoft.aspnetcore.all nevolá `Load` tak, aby jeho výchozí konfigurační oddíl může být nahrazen.</span><span class="sxs-lookup"><span data-stu-id="69c33-298">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="69c33-299">`KestrelConfigurationLoader` zrcadlení `Listen` rozhraní API z řady `KestrelServerOptions` jako `Endpoint` přetížení, takže koncové body kódu a konfiguračním může být nakonfigurována na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="69c33-299">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="69c33-300">Tato přetížení nepoužívejte názvy a využívat pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="69c33-300">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="69c33-301">*Změnit výchozí nastavení v kódu*</span><span class="sxs-lookup"><span data-stu-id="69c33-301">*Change the defaults in code*</span></span>

<span data-ttu-id="69c33-302">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` můžete použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepisuje výchozí certifikát uvedený v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="69c33-302">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="69c33-303">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měla být volána před všechny koncové body se konfigurují.</span><span class="sxs-lookup"><span data-stu-id="69c33-303">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
options.ConfigureEndpointDefaults(opt =>
{
    opt.NoDelay = true;
});

options.ConfigureHttpsDefaults(httpsOptions =>
{
    httpsOptions.SslProtocols = SslProtocols.Tls12;
});
```

<span data-ttu-id="69c33-304">*Podpora kestrel SNI*</span><span class="sxs-lookup"><span data-stu-id="69c33-304">*Kestrel support for SNI*</span></span>

<span data-ttu-id="69c33-305">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="69c33-305">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="69c33-306">Pro SNI na funkci klient odešle název hostitele pro zabezpečenou relaci na server během provádění metody handshake TLS server může poskytnout správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="69c33-306">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="69c33-307">Klient použije certifikát zařízená šifrovanou komunikaci se serverem během zabezpečené relace, který následuje TLS handshake.</span><span class="sxs-lookup"><span data-stu-id="69c33-307">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="69c33-308">Podporuje SNI přes kestrel `ServerCertificateSelector` zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="69c33-308">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="69c33-309">Zpětné volání je vyvolat jednou za připojení, aby mohla aplikace ke kontrole názvu hostitele a vyberte příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="69c33-309">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="69c33-310">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="69c33-310">SNI support requires:</span></span>

* <span data-ttu-id="69c33-311">Běží na rozhraní .NET framework `netcoreapp2.1`.</span><span class="sxs-lookup"><span data-stu-id="69c33-311">Running on target framework `netcoreapp2.1`.</span></span> <span data-ttu-id="69c33-312">Na `netcoreapp2.0` a `net461`, zpětné volání vyvolat ale `name` je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="69c33-312">On `netcoreapp2.0` and `net461`, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="69c33-313">`name` Je také `null` Pokud klienta neobsahuje hostitelský název parametru v TLS handshake.</span><span class="sxs-lookup"><span data-stu-id="69c33-313">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="69c33-314">Spustit všechny weby na stejnou instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="69c33-314">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="69c33-315">Kestrel nepodporuje sdílení IP adresu a port ve více instancích bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="69c33-315">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, options) =>
        {
            options.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser, 
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

::: moniker-end

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="69c33-316">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="69c33-316">Bind to a TCP socket</span></span>

<span data-ttu-id="69c33-317">[Naslouchání](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listen) metoda vytvoří vazbu na soket TCP a umožňuje lambda s možností konfigurace certifikátu SSL:</span><span class="sxs-lookup"><span data-stu-id="69c33-317">The [Listen](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listen) method binds to a TCP socket, and an options lambda permits SSL certificate configuration:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Listen(IPAddress.Loopback, 5000);
            options.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Listen(IPAddress.Loopback, 5000);
            options.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

::: moniker-end

<span data-ttu-id="69c33-318">Tento příklad konfiguruje SSL pro koncový bod s [ListenOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.listenoptions).</span><span class="sxs-lookup"><span data-stu-id="69c33-318">The example configures SSL for an endpoint with [ListenOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.listenoptions).</span></span> <span data-ttu-id="69c33-319">Nakonfigurujte další nastavení Kestrel pro konkrétní koncové body pomocí stejného rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="69c33-319">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="69c33-320">Vytvoření vazby k Unixovému soketu</span><span class="sxs-lookup"><span data-stu-id="69c33-320">Bind to a Unix socket</span></span>

<span data-ttu-id="69c33-321">Naslouchání soketu Unix s [ListenUnixSocket](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listenunixsocket) dosáhnout tak vyššího výkonu se serverem Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="69c33-321">Listen on a Unix socket with [ListenUnixSocket](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listenunixsocket) for improved performance with Nginx, as shown in this example:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.ListenUnixSocket("/tmp/kestrel-test.sock");
            options.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

::: moniker-end

### <a name="port-0"></a><span data-ttu-id="69c33-322">Port 0</span><span class="sxs-lookup"><span data-stu-id="69c33-322">Port 0</span></span>

<span data-ttu-id="69c33-323">Když číslo portu `0` určena Kestrel dynamicky váže dostupný port.</span><span class="sxs-lookup"><span data-stu-id="69c33-323">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="69c33-324">Následující příklad ukazuje, jak určit port, který Kestrel vázán skutečně za běhu:</span><span class="sxs-lookup"><span data-stu-id="69c33-324">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="69c33-325">Při spuštění aplikace Určuje výstup okna konzoly dynamický port, kde se dá kontaktovat aplikace:</span><span class="sxs-lookup"><span data-stu-id="69c33-325">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="69c33-326">Omezení</span><span class="sxs-lookup"><span data-stu-id="69c33-326">Limitations</span></span>

<span data-ttu-id="69c33-327">Konfigurace koncových bodů pomocí následujících postupů:</span><span class="sxs-lookup"><span data-stu-id="69c33-327">Configure endpoints with the following approaches:</span></span>

* [<span data-ttu-id="69c33-328">UseUrls</span><span class="sxs-lookup"><span data-stu-id="69c33-328">UseUrls</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useurls)
* <span data-ttu-id="69c33-329">`--urls` argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="69c33-329">`--urls` command-line argument</span></span>
* <span data-ttu-id="69c33-330">`urls` Konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="69c33-330">`urls` host configuration key</span></span>
* <span data-ttu-id="69c33-331">`ASPNETCORE_URLS` Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="69c33-331">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="69c33-332">Tyto metody jsou užitečné pro provádění kódu práce se servery než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="69c33-332">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="69c33-333">Nicméně mějte na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="69c33-333">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="69c33-334">Protokol SSL nelze použít s těchto přístupů, pokud výchozí certifikát je součástí konfigurace koncového bodu HTTPS (například pomocí `KestrelServerOptions` konfigurace nebo konfiguračního souboru, jak je znázorněno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="69c33-334">SSL can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="69c33-335">Při i `Listen` a `UseUrls` přístupy se využívat současně, `Listen` přepsat koncových bodů `UseUrls` koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="69c33-335">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="69c33-336">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="69c33-336">IIS endpoint configuration</span></span>

<span data-ttu-id="69c33-337">Při použití služby IIS, adresa URL vazeb pro služby IIS změnit vazby jsou nastaveny buď `Listen` nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="69c33-337">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="69c33-338">Další informace najdete v tématu [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) tématu.</span><span class="sxs-lookup"><span data-stu-id="69c33-338">For more information, see the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) topic.</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="listenoptionsprotocols"></a><span data-ttu-id="69c33-339">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="69c33-339">ListenOptions.Protocols</span></span>

<span data-ttu-id="69c33-340">`Protocols` Vlastnost vytvoří protokol HTTP (`HttpProtocols`) povolený koncový bod připojení nebo na serveru.</span><span class="sxs-lookup"><span data-stu-id="69c33-340">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="69c33-341">Přiřaďte hodnotu `Protocols` vlastnost z `HttpProtocols` výčtu.</span><span class="sxs-lookup"><span data-stu-id="69c33-341">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="69c33-342">`HttpProtocols` Hodnota výčtu</span><span class="sxs-lookup"><span data-stu-id="69c33-342">`HttpProtocols` enum value</span></span> | <span data-ttu-id="69c33-343">Protokol připojení povoleno</span><span class="sxs-lookup"><span data-stu-id="69c33-343">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="69c33-344">HTTP/1.1 pouze.</span><span class="sxs-lookup"><span data-stu-id="69c33-344">HTTP/1.1 only.</span></span> <span data-ttu-id="69c33-345">Je možné s nebo bez protokolu TLS.</span><span class="sxs-lookup"><span data-stu-id="69c33-345">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="69c33-346">HTTP/2 pouze.</span><span class="sxs-lookup"><span data-stu-id="69c33-346">HTTP/2 only.</span></span> <span data-ttu-id="69c33-347">Používá se především s TLS.</span><span class="sxs-lookup"><span data-stu-id="69c33-347">Primarily used with TLS.</span></span> <span data-ttu-id="69c33-348">Může použít bez TLS pouze v případě, že klient podporuje [předchozí znalosti režimu](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="69c33-348">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="69c33-349">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="69c33-349">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="69c33-350">Vyžaduje TLS a [vyjednávání protokolu v aplikační vrstvě (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) připojení pro vyjednávání protokolu HTTP/2; v opačném případě výchozí nastavení připojení k protokolu HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="69c33-350">Requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection to negotiate HTTP/2; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="69c33-351">Výchozí protokol je HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="69c33-351">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="69c33-352">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="69c33-352">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="69c33-353">TLS verze 1.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="69c33-353">TLS version 1.2 or later</span></span>
* <span data-ttu-id="69c33-354">Opakované vyjednávání zakázáno</span><span class="sxs-lookup"><span data-stu-id="69c33-354">Renegotiation disabled</span></span>
* <span data-ttu-id="69c33-355">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="69c33-355">Compression disabled</span></span>
* <span data-ttu-id="69c33-356">Velikost minimální dočasné výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="69c33-356">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="69c33-357">Eliptické křivky Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bits minimální</span><span class="sxs-lookup"><span data-stu-id="69c33-357">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="69c33-358">Konečná pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; minimální hodnotě 2 048 bitů</span><span class="sxs-lookup"><span data-stu-id="69c33-358">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="69c33-359">Šifrovací sada není na seznamu zakázaných adres</span><span class="sxs-lookup"><span data-stu-id="69c33-359">Cipher suite not blacklisted</span></span>

<span data-ttu-id="69c33-360">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; s eliptické křivky p-256 &lbrack; `FIPS186` &rbrack; se podporuje ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="69c33-360">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="69c33-361">Následující příklad umožňuje HTTP/1.1 a připojení HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="69c33-361">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="69c33-362">Připojení jsou zabezpečená pomocí protokolu TLS s poskytnutý certifikát:</span><span class="sxs-lookup"><span data-stu-id="69c33-362">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Listen(IPAddress.Any, 8000, listenOptions =>
            {
                listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="69c33-363">Volitelně můžete vytvořit `IConnectionAdapter` implementace pro filtrování počet metod Handshake TLS na jednotlivá připojení pro konkrétní šifrování:</span><span class="sxs-lookup"><span data-stu-id="69c33-363">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Listen(IPAddress.Any, 8000, listenOptions =>
            {
                listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
                listenOptions.UseHttps("testCert.pfx", "testPassword");
                listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
            });
        });
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that you don't 
        // wish to support. Alternatively, define and compare 
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher 
        // suites.
        //
        // A ITlsHandshakeFeature.CipherAlgorithm of CipherAlgorithmType.Null 
        // indicates that no cipher algorithm supported by Kestrel matches the 
        // requested algorithm(s).
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="69c33-364">*Nastavení protokolu z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="69c33-364">*Set the protocol from configuration*</span></span>

<span data-ttu-id="69c33-365">[WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) volání `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` ve výchozím nastavení se načíst konfiguraci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="69c33-365">[WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="69c33-366">V následujícím *appsettings.json* pokládáme stav pro všechny koncové body pro Kestrel například výchozí připojení protokol (HTTP/1.1 a HTTP/2):</span><span class="sxs-lookup"><span data-stu-id="69c33-366">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndPointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="69c33-367">Následující příklad souboru konfigurace naváže připojení protokolu pro určitý koncový bod:</span><span class="sxs-lookup"><span data-stu-id="69c33-367">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "EndPoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="69c33-368">Protokoly se zadat v kódu přepsat hodnoty nastavené v konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="69c33-368">Protocols specified in code override values set by configuration.</span></span>

::: moniker-end

## <a name="transport-configuration"></a><span data-ttu-id="69c33-369">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="69c33-369">Transport configuration</span></span>

<span data-ttu-id="69c33-370">Verze technologie ASP.NET Core 2.1 Kestrel pro výchozí přenos je již podle Libuv ale místo toho podle spravované sokety.</span><span class="sxs-lookup"><span data-stu-id="69c33-370">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="69c33-371">Toto je zásadní změny pro aplikace ASP.NET Core 2.0 upgrade na verzi 2.1, které volají [WebHostBuilderLibuvExtensions.UseLibuv](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderlibuvextensions.uselibuv) a závisí na jednu z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="69c33-371">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call [WebHostBuilderLibuvExtensions.UseLibuv](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderlibuvextensions.uselibuv) and depend on either of the following packages:</span></span>

* <span data-ttu-id="69c33-372">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="69c33-372">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="69c33-373">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="69c33-373">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="69c33-374">Pro ASP.NET Core 2.1 nebo novější projekty, které používají [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) a vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="69c33-374">For ASP.NET Core 2.1 or later projects that use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and require the use of Libuv:</span></span>

* <span data-ttu-id="69c33-375">Přidat závislost [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) balíček do souboru projektu vaší aplikace:</span><span class="sxs-lookup"><span data-stu-id="69c33-375">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv" 
                      Version="<LATEST_VERSION>" />
    ```

* <span data-ttu-id="69c33-376">Volání [WebHostBuilderLibuvExtensions.UseLibuv](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderlibuvextensions.uselibuv):</span><span class="sxs-lookup"><span data-stu-id="69c33-376">Call [WebHostBuilderLibuvExtensions.UseLibuv](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderlibuvextensions.uselibuv):</span></span>

    ```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            CreateWebHostBuilder(args).Build().Run();
        }

        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseLibuv()
                .UseStartup<Startup>();
    }
    ```

### <a name="url-prefixes"></a><span data-ttu-id="69c33-377">Předpony adres URL</span><span class="sxs-lookup"><span data-stu-id="69c33-377">URL prefixes</span></span>

<span data-ttu-id="69c33-378">Při použití `UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele, nebo `ASPNETCORE_URLS` proměnné prostředí, předpony adres URL může být v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="69c33-378">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="69c33-379">Platné jsou pouze předpony adres URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="69c33-379">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="69c33-380">Kestrel nepodporuje SSL při konfiguraci adresy URL vazby, které používají `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="69c33-380">Kestrel doesn't support SSL when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="69c33-381">Adresu IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="69c33-381">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="69c33-382">`0.0.0.0` je zvláštní případ s vazbou na všechny adresy IPv4.</span><span class="sxs-lookup"><span data-stu-id="69c33-382">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="69c33-383">Adresa protokolu IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="69c33-383">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="69c33-384">`[::]` je ekvivalentem IPv6 IPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="69c33-384">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="69c33-385">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="69c33-385">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="69c33-386">Názvy hostitelů `*`, a `+`, nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="69c33-386">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="69c33-387">Nic není rozpoznán jako platná IP adresa nebo `localhost` vytvoří vazbu pro všechny IP adresy IPv6 a IPv4.</span><span class="sxs-lookup"><span data-stu-id="69c33-387">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="69c33-388">Pro vázání názvů jiného hostitele a různé aplikace ASP.NET Core na stejném portu, použijte [HTTP.sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, jako je například Apache, IIS nebo Nginx.</span><span class="sxs-lookup"><span data-stu-id="69c33-388">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="69c33-389">Pokud nepoužíváte reverzního proxy serveru s hostitelem filtrování povolené, povolte [hostitele filtrování](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="69c33-389">If not using a reverse proxy with host filtering enabled, enable [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="69c33-390">Hostitel `localhost` název portu s číslem portu číslo nebo adresu zpětné smyčky IP</span><span class="sxs-lookup"><span data-stu-id="69c33-390">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="69c33-391">Když `localhost` určena Kestrel pokusí o připojení k rozhraní zpětné smyčky protokolu IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="69c33-391">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="69c33-392">Pokud požadovaný port je používán jinou službou buď rozhraní zpětné smyčky, Kestrel nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="69c33-392">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="69c33-393">Pokud je buď rozhraní zpětné smyčky není k dispozici z jiného důvodu (většinu běžně, protože protokol IPv6 není podporován), Kestrel protokoluje upozornění.</span><span class="sxs-lookup"><span data-stu-id="69c33-393">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="69c33-394">Hostitel filtrování</span><span class="sxs-lookup"><span data-stu-id="69c33-394">Host filtering</span></span>

<span data-ttu-id="69c33-395">I když Kestrel podporuje konfigurace, například podle předpon `http://example.com:5000`, Kestrel do značné míry ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="69c33-395">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="69c33-396">Hostitel `localhost` je zvláštní případ použité pro vazbu na adresu zpětné smyčky adresy.</span><span class="sxs-lookup"><span data-stu-id="69c33-396">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="69c33-397">Všechny hostitele, jiné než explicitních IP adresu vytvoří vazbu na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="69c33-397">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="69c33-398">Žádná z těchto informací slouží k ověření požadavku `Host` záhlaví.</span><span class="sxs-lookup"><span data-stu-id="69c33-398">None of this information is used to validate request `Host` headers.</span></span>

<span data-ttu-id="69c33-399">Jako alternativní řešení použijte hostitele filtrování middlewaru.</span><span class="sxs-lookup"><span data-stu-id="69c33-399">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="69c33-400">Poskytuje Middleware filtrování hostitele [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) balíček, který je součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="69c33-400">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span> <span data-ttu-id="69c33-401">Přidá middleware [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), který volá [AddHostFiltering](/dotnet/api/microsoft.aspnetcore.builder.hostfilteringservicesextensions.addhostfiltering):</span><span class="sxs-lookup"><span data-stu-id="69c33-401">The middleware is added by [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), which calls [AddHostFiltering](/dotnet/api/microsoft.aspnetcore.builder.hostfilteringservicesextensions.addhostfiltering):</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="69c33-402">Middleware filtrování hostitele je ve výchozím nastavení zakázána.</span><span class="sxs-lookup"><span data-stu-id="69c33-402">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="69c33-403">Chcete-li povolit middleware, definujte `AllowedHosts` klíče v *appsettings.json*/*appsettings.\< EnvironmentName > .json*.</span><span class="sxs-lookup"><span data-stu-id="69c33-403">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="69c33-404">Hodnota je středníkem oddělený seznam názvů hostitele bez čísla portů:</span><span class="sxs-lookup"><span data-stu-id="69c33-404">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="69c33-405">*appSettings.JSON*:</span><span class="sxs-lookup"><span data-stu-id="69c33-405">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="69c33-406">[Předané záhlaví Middleware](xref:host-and-deploy/proxy-load-balancer) má také [ForwardedHeadersOptions.AllowedHosts](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.allowedhosts) možnost.</span><span class="sxs-lookup"><span data-stu-id="69c33-406">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an [ForwardedHeadersOptions.AllowedHosts](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.allowedhosts) option.</span></span> <span data-ttu-id="69c33-407">Přesměrovaná záhlaví Middleware a filtrování Middleware hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="69c33-407">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="69c33-408">Nastavení `AllowedHosts` předané Middleware hlavičky je vhodné při hlavičku hostitele nezachová při předávání žádostí s reverzní proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="69c33-408">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the Host header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="69c33-409">Nastavení `AllowedHosts` s Middlewarem filtrování hostitele je vhodné při použití Kestrel jako veřejnou hraniční server nebo pokud je hlavička hostitele přímo předán.</span><span class="sxs-lookup"><span data-stu-id="69c33-409">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the Host header is directly forwarded.</span></span>
>
> <span data-ttu-id="69c33-410">Další informace o předávaných Middleware záhlaví, naleznete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="69c33-410">For more information on Forwarded Headers Middleware, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="69c33-411">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="69c33-411">Additional resources</span></span>

* [<span data-ttu-id="69c33-412">Vynucení protokolu HTTPS</span><span class="sxs-lookup"><span data-stu-id="69c33-412">Enforce HTTPS</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="69c33-413">Kestrel zdrojového kódu</span><span class="sxs-lookup"><span data-stu-id="69c33-413">Kestrel source code</span></span>](https://github.com/aspnet/KestrelHttpServer)
* [<span data-ttu-id="69c33-414">RFC 7230: Syntaxe a směrování zpráv (oddíl 5.4: hostitele)</span><span class="sxs-lookup"><span data-stu-id="69c33-414">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
* [<span data-ttu-id="69c33-415">Konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="69c33-415">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](xref:host-and-deploy/proxy-load-balancer)
