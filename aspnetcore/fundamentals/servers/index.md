---
title: Implementací webového serveru v ASP.NET Core
author: guardrex
description: Zjišťování webové servery přes Kestrel a HTTP.sys pro ASP.NET Core. Zjistěte, jak vybrat server a kdy použít reverzní proxy server.
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/01/2018
uid: fundamentals/servers/index
ms.openlocfilehash: 965d69dd071ec71d283284d58e6e1a6e78604f90
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861353"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="23d2d-104">Implementací webového serveru v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23d2d-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="23d2d-105">Podle [Petr Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), a [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="23d2d-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="23d2d-106">Aplikace ASP.NET Core spouští implementaci serveru HTTP v procesu.</span><span class="sxs-lookup"><span data-stu-id="23d2d-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="23d2d-107">Implementace server přijímá požadavky protokolu HTTP a poskytuje je na aplikaci jako sady [funkce požadavků](xref:fundamentals/request-features) složený do <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="23d2d-107">The server implementation listens for HTTP requests and surfaces them to the app as sets of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="23d2d-108">Windows</span><span class="sxs-lookup"><span data-stu-id="23d2d-108">Windows</span></span>](#tab/windows)

<span data-ttu-id="23d2d-109">ASP.NET Core se dodává s následujícími možnostmi:</span><span class="sxs-lookup"><span data-stu-id="23d2d-109">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="23d2d-110">[Kestrel server](xref:fundamentals/servers/kestrel) je výchozí, server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="23d2d-110">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="23d2d-111">HTTP serveru služby IIS (`IISHttpServer`) je [v procesu serveru služby IIS](xref:fundamentals/servers/aspnet-core-module#in-process-hosting-model) použitá s implementace [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="23d2d-111">IIS HTTP Server (`IISHttpServer`) is an [IIS in-process server](xref:fundamentals/servers/aspnet-core-module#in-process-hosting-model) implementation used with the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span>
* <span data-ttu-id="23d2d-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) je na základě protokolu HTTP jenom pro Windows server [ovladač HTTP.sys jádra a rozhraní API serveru HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span><span class="sxs-lookup"><span data-stu-id="23d2d-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span> <span data-ttu-id="23d2d-113">Je volána HTTP.sys [WebListener](xref:fundamentals/servers/weblistener) v ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="23d2d-113">HTTP.sys is called [WebListener](xref:fundamentals/servers/weblistener) in ASP.NET Core 1.x.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="23d2d-114">macOS</span><span class="sxs-lookup"><span data-stu-id="23d2d-114">macOS</span></span>](#tab/macos)

<span data-ttu-id="23d2d-115">ASP.NET Core se dodává s [Kestrel server](xref:fundamentals/servers/kestrel), což je výchozí, server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="23d2d-115">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="23d2d-116">Linux</span><span class="sxs-lookup"><span data-stu-id="23d2d-116">Linux</span></span>](#tab/linux)

<span data-ttu-id="23d2d-117">ASP.NET Core se dodává s [Kestrel server](xref:fundamentals/servers/kestrel), což je výchozí, server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="23d2d-117">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="23d2d-118">Windows</span><span class="sxs-lookup"><span data-stu-id="23d2d-118">Windows</span></span>](#tab/windows)

<span data-ttu-id="23d2d-119">ASP.NET Core se dodává s následujícími možnostmi:</span><span class="sxs-lookup"><span data-stu-id="23d2d-119">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="23d2d-120">[Kestrel server](xref:fundamentals/servers/kestrel) je výchozí, server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="23d2d-120">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="23d2d-121">[HTTP.sys server](xref:fundamentals/servers/httpsys) je na základě protokolu HTTP jenom pro Windows server [ovladač HTTP.sys jádra a rozhraní API serveru HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span><span class="sxs-lookup"><span data-stu-id="23d2d-121">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span> <span data-ttu-id="23d2d-122">Je volána HTTP.sys [WebListener](xref:fundamentals/servers/weblistener) v ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="23d2d-122">HTTP.sys is called [WebListener](xref:fundamentals/servers/weblistener) in ASP.NET Core 1.x.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="23d2d-123">macOS</span><span class="sxs-lookup"><span data-stu-id="23d2d-123">macOS</span></span>](#tab/macos)

<span data-ttu-id="23d2d-124">ASP.NET Core se dodává s [Kestrel server](xref:fundamentals/servers/kestrel), což je výchozí, server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="23d2d-124">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="23d2d-125">Linux</span><span class="sxs-lookup"><span data-stu-id="23d2d-125">Linux</span></span>](#tab/linux)

<span data-ttu-id="23d2d-126">ASP.NET Core se dodává s [Kestrel server](xref:fundamentals/servers/kestrel), což je výchozí, server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="23d2d-126">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

## <a name="kestrel"></a><span data-ttu-id="23d2d-127">Kestrel</span><span class="sxs-lookup"><span data-stu-id="23d2d-127">Kestrel</span></span>

<span data-ttu-id="23d2d-128">Kestrel je výchozí webový server, která je součástí šablony projektů ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="23d2d-128">Kestrel is the default web server included in ASP.NET Core project templates.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="23d2d-129">Dá se kestrel:</span><span class="sxs-lookup"><span data-stu-id="23d2d-129">Kestrel can be used:</span></span>

* <span data-ttu-id="23d2d-130">Samostatně jako hraniční server zpracování požadavků přímo ze sítě, včetně Internetu.</span><span class="sxs-lookup"><span data-stu-id="23d2d-130">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>
* <span data-ttu-id="23d2d-131">S *reverzní proxy server*, jako například [Internetové informační služby (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="23d2d-131">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="23d2d-132">Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="23d2d-132">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

![Kestrel komunikuje přímo s Internetu bez reverzní proxy server](kestrel/_static/kestrel-to-internet2.png)

![Kestrel nepřímo komunikuje přes Internet prostřednictvím reverzního proxy serveru, jako je například Apache, IIS nebo Nginx](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="23d2d-135">Buď konfiguraci&mdash;s nebo bez něj reverzní proxy server&mdash;je platný a podporované konfigurace pro hostování pro ASP.NET Core 2.0 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="23d2d-135">Either configuration&mdash;with or without a reverse proxy server&mdash;is a valid and supported hosting configuration for ASP.NET Core 2.0 or later apps.</span></span> <span data-ttu-id="23d2d-136">Další informace najdete v tématu [použití Kestrel s reverzní proxy server](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="23d2d-136">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="23d2d-137">Pokud aplikace přijímá jenom požadavky z interní sítě, je možné Kestrel samostatně.</span><span class="sxs-lookup"><span data-stu-id="23d2d-137">If the app only accepts requests from an internal network, Kestrel can be used by itself.</span></span>

![Kestrel komunikuje přímo s interní sítě](kestrel/_static/kestrel-to-internal.png)

<span data-ttu-id="23d2d-139">Pokud aplikace je přístupný z Internetu, musíte použít Kestrel *reverzní proxy server*, jako například [Internetové informační služby (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), nebo [Apache ](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="23d2d-139">If the app is exposed to the Internet, Kestrel must use a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="23d2d-140">Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="23d2d-140">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

![Kestrel nepřímo komunikuje přes Internet prostřednictvím reverzního proxy serveru, jako je například Apache, IIS nebo Nginx](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="23d2d-142">Nejdůležitější důvod pomocí reverzního proxy serveru pro veřejnou hraniční server nasazení, které jsou vystaveny přímo k Internetu je zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="23d2d-142">The most important reason for using a reverse proxy for public-facing edge server deployments that are exposed directly the Internet is security.</span></span> <span data-ttu-id="23d2d-143">Verze 1.x Kestrel nezahrnují funkcí důležité zabezpečení, ochranu před útoky z Internetu.</span><span class="sxs-lookup"><span data-stu-id="23d2d-143">The 1.x versions of Kestrel don't include important security features to defend against attacks from the Internet.</span></span> <span data-ttu-id="23d2d-144">To zahrnuje, ale není omezený na, odpovídající časové limity, žádost o velikosti omezení a omezení počtu souběžných připojení.</span><span class="sxs-lookup"><span data-stu-id="23d2d-144">This includes, but isn't limited to, appropriate timeouts, request size limits, and concurrent connection limits.</span></span>

<span data-ttu-id="23d2d-145">Další informace najdete v tématu [použití Kestrel s reverzní proxy server](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="23d2d-145">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

::: moniker-end

### <a name="iis-with-kestrel"></a><span data-ttu-id="23d2d-146">IIS s Kestrel</span><span class="sxs-lookup"><span data-stu-id="23d2d-146">IIS with Kestrel</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="23d2d-147">Při použití [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), aplikace ASP.NET Core je buď spuštěn ve stejném procesu jako pracovní proces služby IIS ( *vnitroprocesové* model hostingu) nebo v samostatném procesu z pracovní proces služby IIS ( *mimo proces* model hostingu).</span><span class="sxs-lookup"><span data-stu-id="23d2d-147">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the ASP.NET Core app either runs in the same process as the IIS worker process (the *in-process* hosting model) or in a process separate from the IIS worker process (the *out-of-process* hosting model).</span></span>

<span data-ttu-id="23d2d-148">[Modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) je nativní modul služby IIS, která zpracovává nativní požadavků služby IIS mezi Server HTTP v procesu služby IIS nebo server Kestrel mimo proces.</span><span class="sxs-lookup"><span data-stu-id="23d2d-148">The [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) is a native IIS module that handles native IIS requests between either the in-process IIS HTTP Server or the out-of-process Kestrel server.</span></span> <span data-ttu-id="23d2d-149">Další informace naleznete v tématu <xref:fundamentals/servers/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="23d2d-149">For more information, see <xref:fundamentals/servers/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="23d2d-150">Při použití [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) jako reverzní proxy server pro ASP.NET Core, ASP.NET Core aplikace běží v procesu nezávisle na pracovní proces služby IIS.</span><span class="sxs-lookup"><span data-stu-id="23d2d-150">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) as a reverse proxy for ASP.NET Core, the ASP.NET Core app runs in a process separate from the IIS worker process.</span></span> <span data-ttu-id="23d2d-151">V procesu služby IIS [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) koordinuje vztah reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="23d2d-151">In the IIS process, the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) coordinates the reverse proxy relationship.</span></span> <span data-ttu-id="23d2d-152">Primární funkce modul ASP.NET Core jsou ke spuštění aplikace, restartujte aplikaci, pokud ho dojde k chybě a přesměrování provozu HTTP na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="23d2d-152">The primary functions of the ASP.NET Core Module are to start the app, restart the app when it crashes, and forward HTTP traffic to the app.</span></span> <span data-ttu-id="23d2d-153">Další informace naleznete v tématu <xref:fundamentals/servers/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="23d2d-153">For more information, see <xref:fundamentals/servers/aspnet-core-module>.</span></span>

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="23d2d-154">Server Nginx s Kestrel</span><span class="sxs-lookup"><span data-stu-id="23d2d-154">Nginx with Kestrel</span></span>

<span data-ttu-id="23d2d-155">Informace o tom, jak použít Nginx jako reverzní proxy server pro Kestrel v Linuxu najdete v tématu <xref:host-and-deploy/linux-nginx>.</span><span class="sxs-lookup"><span data-stu-id="23d2d-155">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="23d2d-156">Apache s Kestrel</span><span class="sxs-lookup"><span data-stu-id="23d2d-156">Apache with Kestrel</span></span>

<span data-ttu-id="23d2d-157">Informace o tom, jak používat Apache na platformě Linux jako reverzní proxy server pro Kestrel najdete v tématu <xref:host-and-deploy/linux-apache>.</span><span class="sxs-lookup"><span data-stu-id="23d2d-157">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="23d2d-158">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="23d2d-158">HTTP.sys</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="23d2d-159">Pokud aplikace ASP.NET Core běží na Windows, ovladač HTTP.sys se o alternativu k Kestrel.</span><span class="sxs-lookup"><span data-stu-id="23d2d-159">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="23d2d-160">Kestrel obecně se doporučuje pro zajištění nejlepšího výkonu.</span><span class="sxs-lookup"><span data-stu-id="23d2d-160">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="23d2d-161">Ovladač HTTP.sys lze použít v situacích, kdy aplikace je přístupný z Internetu a požadované funkce jsou podporovány, ale ne Kestrel HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="23d2d-161">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="23d2d-162">Další informace naleznete v tématu <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="23d2d-162">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![Ovladač HTTP.sys komunikuje přímo s Internetem](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="23d2d-164">Ovladač HTTP.sys lze použít také pro aplikace, které jsou přístupné pouze k interní síti.</span><span class="sxs-lookup"><span data-stu-id="23d2d-164">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![Ovladač HTTP.sys komunikuje přímo s interní sítě](httpsys/_static/httpsys-to-internal.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="23d2d-166">Název souboru HTTP.sys [WebListener](xref:fundamentals/servers/weblistener) v ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="23d2d-166">HTTP.sys is named [WebListener](xref:fundamentals/servers/weblistener) in ASP.NET Core 1.x.</span></span> <span data-ttu-id="23d2d-167">Pokud aplikace ASP.NET Core běží na Windows, WebListener se o alternativu pro scénářů, kdy není k dispozici pro hostování aplikací služby IIS.</span><span class="sxs-lookup"><span data-stu-id="23d2d-167">If ASP.NET Core apps are run on Windows, WebListener is an alternative for scenarios where IIS isn't available to host apps.</span></span>

![Weblistener komunikuje přímo s Internetem](weblistener/_static/weblistener-to-internet.png)

<span data-ttu-id="23d2d-169">WebListener lze také místo Kestrel pro aplikace, které jsou přístupné pouze k interní síti, pokud je to nutné, že funkce jsou podporovány, ale ne Kestrel WebListener.</span><span class="sxs-lookup"><span data-stu-id="23d2d-169">WebListener can also be used in place of Kestrel for apps that are only exposed to an internal network, if required capabilities are supported by WebListener but not Kestrel.</span></span> <span data-ttu-id="23d2d-170">Informace o WebListener najdete v tématu [WebListener](xref:fundamentals/servers/weblistener).</span><span class="sxs-lookup"><span data-stu-id="23d2d-170">For information on WebListener, see [WebListener](xref:fundamentals/servers/weblistener).</span></span>

![Weblistener komunikuje přímo s interní sítě](weblistener/_static/weblistener-to-internal.png)

::: moniker-end

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="23d2d-172">ASP.NET Core server infrastruktury</span><span class="sxs-lookup"><span data-stu-id="23d2d-172">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="23d2d-173">[IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) k dispozici v `Startup.Configure` zpřístupňuje metodu [ServerFeatures](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.serverfeatures) vlastnost typu [IFeatureCollection](/dotnet/api/microsoft.aspnetcore.http.features.ifeaturecollection).</span><span class="sxs-lookup"><span data-stu-id="23d2d-173">The [IApplicationBuilder](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder) available in the `Startup.Configure` method exposes the [ServerFeatures](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.serverfeatures) property of type [IFeatureCollection](/dotnet/api/microsoft.aspnetcore.http.features.ifeaturecollection).</span></span> <span data-ttu-id="23d2d-174">Přes kestrel a HTTP.sys (WebListener v ASP.NET Core 1.x) vystavit pouze jednu funkci, [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature), ale implementace jiný server může vystavit další funkce.</span><span class="sxs-lookup"><span data-stu-id="23d2d-174">Kestrel and HTTP.sys (WebListener in ASP.NET Core 1.x) only expose a single feature each, [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature), but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="23d2d-175">`IServerAddressesFeature` umožňuje zjistit, port, který obsahuje implementaci serveru vázán v době běhu.</span><span class="sxs-lookup"><span data-stu-id="23d2d-175">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="23d2d-176">Vlastní servery</span><span class="sxs-lookup"><span data-stu-id="23d2d-176">Custom servers</span></span>

<span data-ttu-id="23d2d-177">Pokud integrované servery nesplňují požadavky aplikace, implementace vlastního serveru vytvořit.</span><span class="sxs-lookup"><span data-stu-id="23d2d-177">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="23d2d-178">[Open Web Interface pro .NET (OWIN) průvodce](xref:fundamentals/owin) ukazuje, jak psát [Nowin](https://github.com/Bobris/Nowin)– na základě [IServer](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) implementace.</span><span class="sxs-lookup"><span data-stu-id="23d2d-178">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based [IServer](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) implementation.</span></span> <span data-ttu-id="23d2d-179">Pouze funkce rozhraní, které aplikace používá potřeba provádění, když minimálně [IHttpRequestFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttprequestfeature) a [IHttpResponseFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpresponsefeature) musí podporovat.</span><span class="sxs-lookup"><span data-stu-id="23d2d-179">Only the feature interfaces that the app uses require implementation, though at a minimum [IHttpRequestFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttprequestfeature) and [IHttpResponseFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpresponsefeature) must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="23d2d-180">Při spuštění serveru</span><span class="sxs-lookup"><span data-stu-id="23d2d-180">Server startup</span></span>

<span data-ttu-id="23d2d-181">Při použití [sady Visual Studio](https://www.visualstudio.com/vs/), [Visual Studio for Mac](https://www.visualstudio.com/vs/mac/), nebo [Visual Studio Code](https://code.visualstudio.com/), server se spustí, když se aplikace spustí na integrované vývojové prostředí ( INTEGROVANÉ VÝVOJOVÉ PROSTŘEDÍ).</span><span class="sxs-lookup"><span data-stu-id="23d2d-181">When using [Visual Studio](https://www.visualstudio.com/vs/), [Visual Studio for Mac](https://www.visualstudio.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/), the server is launched when the app is started by the Integrated Development Environment (IDE).</span></span> <span data-ttu-id="23d2d-182">V sadě Visual Studio na Windows, můžete profily spouštění použije ke spuštění aplikace a serveru s oběma [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) nebo konzoly.</span><span class="sxs-lookup"><span data-stu-id="23d2d-182">In Visual Studio on Windows, launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) or the console.</span></span> <span data-ttu-id="23d2d-183">Ve Visual Studio Code, aplikace a serveru jsou spouštěny [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), která aktivuje CoreCLR ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="23d2d-183">In Visual Studio Code, the app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span> <span data-ttu-id="23d2d-184">Pomocí sady Visual Studio pro Mac, aplikace a serveru jsou spouštěny [ladicí program Mono konfigurace Soft-režim](http://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="23d2d-184">Using Visual Studio for Mac, the app and server are started by the [Mono Soft-Mode Debugger](http://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="23d2d-185">Při spuštění aplikace z příkazového řádku ve složce projektu [dotnet spustit](/dotnet/core/tools/dotnet-run) spustí aplikace a serveru (přes Kestrel a pouze HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="23d2d-185">When launching an app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="23d2d-186">Konfigurace je určena `-c|--configuration` možnost, který je nastaven na hodnotu `Debug` (výchozí) nebo `Release`.</span><span class="sxs-lookup"><span data-stu-id="23d2d-186">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span> <span data-ttu-id="23d2d-187">Pokud jsou k dispozici v profily spouštění *launchSettings.json* souboru, použijte `--launch-profile <NAME>` možnost nastavit profil spuštění (například `Development` nebo `Production`).</span><span class="sxs-lookup"><span data-stu-id="23d2d-187">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile <NAME>` option to set the launch profile (for example, `Development` or `Production`).</span></span> <span data-ttu-id="23d2d-188">Další informace najdete v tématu [dotnet spustit](/dotnet/core/tools/dotnet-run) a [vytváření distribučních balíčků .NET Core](/dotnet/core/build/distribution-packaging) témata.</span><span class="sxs-lookup"><span data-stu-id="23d2d-188">For more information, see the [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging) topics.</span></span>

## <a name="http2-support"></a><span data-ttu-id="23d2d-189">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="23d2d-189">HTTP/2 support</span></span>

<span data-ttu-id="23d2d-190">[HTTP/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení:</span><span class="sxs-lookup"><span data-stu-id="23d2d-190">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="23d2d-191">Kestrel</span><span class="sxs-lookup"><span data-stu-id="23d2d-191">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="23d2d-192">Operační systém</span><span class="sxs-lookup"><span data-stu-id="23d2d-192">Operating system</span></span>
    * <span data-ttu-id="23d2d-193">Windows Server 2016 nebo Windows 10 nebo novější&dagger;</span><span class="sxs-lookup"><span data-stu-id="23d2d-193">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="23d2d-194">Linux s OpenSSL 1.0.2 nebo novější (například Ubuntu 16.04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="23d2d-194">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="23d2d-195">HTTP/2 budou podporované v systému macOS v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="23d2d-195">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="23d2d-196">Cílová architektura: .NET Core 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="23d2d-196">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="23d2d-197">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="23d2d-197">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="23d2d-198">Windows Server 2016 nebo Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="23d2d-198">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="23d2d-199">Cílová architektura: není k dispozici pro nasazení souboru HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="23d2d-199">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="23d2d-200">Služba IIS (v procesu)</span><span class="sxs-lookup"><span data-stu-id="23d2d-200">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="23d2d-201">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="23d2d-201">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="23d2d-202">Cílová architektura: .NET Core 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="23d2d-202">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="23d2d-203">Služba IIS (out-of-process)</span><span class="sxs-lookup"><span data-stu-id="23d2d-203">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="23d2d-204">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="23d2d-204">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="23d2d-205">Připojení k serveru edge veřejně přístupných používat HTTP/2, ale připojení reverzního proxy serveru na Kestrel používá HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="23d2d-205">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="23d2d-206">Cílová architektura: není k dispozici pro nasazení na více instancí procesu služby IIS.</span><span class="sxs-lookup"><span data-stu-id="23d2d-206">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="23d2d-207">&dagger;Kestrel má omezenou podporu pro HTTP/2 pro systém Windows Server 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="23d2d-207">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="23d2d-208">Podpora je omezená, protože seznam podporovaných šifer TLS sady k dispozici v těchto operačních systémech je omezen.</span><span class="sxs-lookup"><span data-stu-id="23d2d-208">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="23d2d-209">Certifikát vytvořený pomocí křivky digitální podpis algoritmů ECDSA (Elliptic) může být nutné zabezpečení připojení protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="23d2d-209">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="23d2d-210">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="23d2d-210">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="23d2d-211">Windows Server 2016 nebo Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="23d2d-211">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="23d2d-212">Cílová architektura: není k dispozici pro nasazení souboru HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="23d2d-212">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="23d2d-213">Služba IIS (out-of-process)</span><span class="sxs-lookup"><span data-stu-id="23d2d-213">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="23d2d-214">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="23d2d-214">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="23d2d-215">Připojení k serveru edge veřejně přístupných používat HTTP/2, ale připojení reverzního proxy serveru na Kestrel používá HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="23d2d-215">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="23d2d-216">Cílová architektura: není k dispozici pro nasazení na více instancí procesu služby IIS.</span><span class="sxs-lookup"><span data-stu-id="23d2d-216">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="23d2d-217">Musíte použít připojení HTTP/2 [vyjednávání protokolu v aplikační vrstvě (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) a TLS 1.2 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="23d2d-217">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="23d2d-218">Další informace najdete v tématech, které se týkají scénářů nasazení serveru.</span><span class="sxs-lookup"><span data-stu-id="23d2d-218">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="23d2d-219">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="23d2d-219">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:fundamentals/servers/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <span data-ttu-id="23d2d-220"><xref:fundamentals/servers/httpsys> (pro technologii ASP.NET Core 1.x, přečtěte si téma <xref:fundamentals/servers/weblistener>)</span><span class="sxs-lookup"><span data-stu-id="23d2d-220"><xref:fundamentals/servers/httpsys> (for ASP.NET Core 1.x, see <xref:fundamentals/servers/weblistener>)</span></span>
