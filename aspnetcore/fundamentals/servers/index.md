---
title: Implementace webového serveru v ASP.NET Core
author: rick-anderson
description: Vyhledejte webové servery Kestrel a HTTP.sys pro ASP.NET Core. Naučte se, jak vybrat server a kdy použít reverzní proxy server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: fb9ba7cd4fe7ce805374dd802cc7ba4258d52527
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016749"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="133c6-104">Implementace webového serveru v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="133c6-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="133c6-105">Tím, že [Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen zastavení](https://twitter.com/halter73)a [Chris Rossův](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="133c6-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="133c6-106">Aplikace ASP.NET Core běží s implementací vnitroprocesového serveru HTTP.</span><span class="sxs-lookup"><span data-stu-id="133c6-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="133c6-107">Implementace serveru naslouchá požadavkům HTTP a rozloží je do aplikace jako sadu [funkcí požadavků](xref:fundamentals/request-features) složených do <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="133c6-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

## <a name="kestrel"></a><span data-ttu-id="133c6-108">Kestrel</span><span class="sxs-lookup"><span data-stu-id="133c6-108">Kestrel</span></span>

<span data-ttu-id="133c6-109">Kestrel je výchozí webový server určený ASP.NET Core šablonami projektů.</span><span class="sxs-lookup"><span data-stu-id="133c6-109">Kestrel is the default web server specified by the ASP.NET Core project templates.</span></span>

<span data-ttu-id="133c6-110">Použijte Kestrel:</span><span class="sxs-lookup"><span data-stu-id="133c6-110">Use Kestrel:</span></span>

* <span data-ttu-id="133c6-111">Sám sebe jako hraniční Server zpracovávající požadavky přímo ze sítě, včetně Internetu.</span><span class="sxs-lookup"><span data-stu-id="133c6-111">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="133c6-113">Pomocí *reverzní proxy server*, například [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="133c6-113">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="133c6-114">Reverzní proxy server přijímá požadavky HTTP z Internetu a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="133c6-114">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="133c6-116">Je podporována buď konfigurace hostování &mdash; s zpětným proxy serverm, nebo bez něj &mdash; .</span><span class="sxs-lookup"><span data-stu-id="133c6-116">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="133c6-117">Pokyny ke konfiguraci Kestrel a informace o tom, kdy použít Kestrel v konfiguraci reverzního proxy serveru, najdete v tématu <xref:fundamentals/servers/kestrel> .</span><span class="sxs-lookup"><span data-stu-id="133c6-117">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="133c6-118">Windows</span><span class="sxs-lookup"><span data-stu-id="133c6-118">Windows</span></span>](#tab/windows)

<span data-ttu-id="133c6-119">ASP.NET Core dodávána s následujícím:</span><span class="sxs-lookup"><span data-stu-id="133c6-119">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="133c6-120">[Kestrel Server](xref:fundamentals/servers/kestrel) je výchozí implementace serveru HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="133c6-120">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="133c6-121">HTTP server IIS je [vnitroprocesové Server](#hosting-models) pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="133c6-121">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="133c6-122">[HTTP.sys Server](xref:fundamentals/servers/httpsys) je server http, který je pouze Windows, založený na [ovladači jádraHTTP.sys a rozhraní API serveru http](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="133c6-122">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="133c6-123">Při použití [služby IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikace buď běží:</span><span class="sxs-lookup"><span data-stu-id="133c6-123">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="133c6-124">Ve stejném procesu jako pracovní proces služby IIS (v rámci [procesu hostování modelu](#hosting-models)) se serverem HTTP služby IIS.</span><span class="sxs-lookup"><span data-stu-id="133c6-124">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="133c6-125">Doporučená konfigurace je *vnitroprocesové* .</span><span class="sxs-lookup"><span data-stu-id="133c6-125">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="133c6-126">V procesu odděleném od pracovního procesu služby IIS ( [mimo proces hostující model](#hosting-models)) se [serverem Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="133c6-126">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="133c6-127">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) je nativní modul služby IIS, který zpracovává NATIVNÍ požadavky služby IIS mezi službou IIS a VNITROPROCESOVÉ serverem HTTP služby IIS nebo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="133c6-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="133c6-128">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="133c6-128">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="133c6-129">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="133c6-129">Hosting models</span></span>

<span data-ttu-id="133c6-130">Při použití hostování v rámci procesu ASP.NET Core aplikace běží ve stejném procesu jako jeho pracovní proces služby IIS.</span><span class="sxs-lookup"><span data-stu-id="133c6-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="133c6-131">Hostování v rámci procesů poskytují lepší výkon než hostování mimo procesy, protože požadavky nejsou proxy serverem přes adaptér zpětné smyčky, síťové rozhraní, které vrátí odchozí síťový provoz zpátky do stejného počítače.</span><span class="sxs-lookup"><span data-stu-id="133c6-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="133c6-132">Služba IIS zpracovává správu procesů pomocí [aktivační služby procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="133c6-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="133c6-133">Pomocí mimoprocesového hostování ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS a modul zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="133c6-133">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="133c6-134">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="133c6-134">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="133c6-135">To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="133c6-135">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="133c6-136">Další informace a pokyny k konfiguraci najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="133c6-136">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="133c6-137">macOS</span><span class="sxs-lookup"><span data-stu-id="133c6-137">macOS</span></span>](#tab/macos)

<span data-ttu-id="133c6-138">ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="133c6-138">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="133c6-139">Linux</span><span class="sxs-lookup"><span data-stu-id="133c6-139">Linux</span></span>](#tab/linux)

<span data-ttu-id="133c6-140">ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="133c6-140">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="133c6-141">Windows</span><span class="sxs-lookup"><span data-stu-id="133c6-141">Windows</span></span>](#tab/windows)

<span data-ttu-id="133c6-142">ASP.NET Core dodávána s následujícím:</span><span class="sxs-lookup"><span data-stu-id="133c6-142">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="133c6-143">[Kestrel Server](xref:fundamentals/servers/kestrel) je výchozím serverem HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="133c6-143">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="133c6-144">[HTTP.sys Server](xref:fundamentals/servers/httpsys) je server http, který je pouze Windows, založený na [ovladači jádraHTTP.sys a rozhraní API serveru http](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="133c6-144">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="133c6-145">Při použití [služby IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikace běží v procesu odděleně od pracovního procesu služby IIS (*mimo proces*) se [serverem Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="133c6-145">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="133c6-146">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="133c6-146">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="133c6-147">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="133c6-147">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="133c6-148">To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="133c6-148">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="133c6-149">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou mimo proces:</span><span class="sxs-lookup"><span data-stu-id="133c6-149">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Modul ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="133c6-151">Požadavky přicházející z webu do ovladače HTTP.sys režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="133c6-151">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="133c6-152">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="133c6-152">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="133c6-153">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="133c6-153">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="133c6-154">Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server, na kterém má naslouchat `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="133c6-154">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="133c6-155">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="133c6-155">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="133c6-156">Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.</span><span class="sxs-lookup"><span data-stu-id="133c6-156">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="133c6-157">Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="133c6-157">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="133c6-158">Kanál middlewaru zpracovává požadavek a předá ho jako `HttpContext` instanci do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="133c6-158">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="133c6-159">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="133c6-159">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="133c6-160">Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="133c6-160">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="133c6-161">Pokyny ke konfiguraci služby IIS a ASP.NET Core modulu najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="133c6-161">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="133c6-162">macOS</span><span class="sxs-lookup"><span data-stu-id="133c6-162">macOS</span></span>](#tab/macos)

<span data-ttu-id="133c6-163">ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="133c6-163">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="133c6-164">Linux</span><span class="sxs-lookup"><span data-stu-id="133c6-164">Linux</span></span>](#tab/linux)

<span data-ttu-id="133c6-165">ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="133c6-165">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="133c6-166">Nginx s Kestrel</span><span class="sxs-lookup"><span data-stu-id="133c6-166">Nginx with Kestrel</span></span>

<span data-ttu-id="133c6-167">Informace o tom, jak používat Nginx v systému Linux jako reverzní proxy server pro Kestrel, naleznete v tématu <xref:host-and-deploy/linux-nginx> .</span><span class="sxs-lookup"><span data-stu-id="133c6-167">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="133c6-168">Apache s Kestrel</span><span class="sxs-lookup"><span data-stu-id="133c6-168">Apache with Kestrel</span></span>

<span data-ttu-id="133c6-169">Informace o tom, jak používat Apache v systému Linux jako reverzní proxy server pro Kestrel, najdete v tématu <xref:host-and-deploy/linux-apache> .</span><span class="sxs-lookup"><span data-stu-id="133c6-169">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="133c6-170">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="133c6-170">HTTP.sys</span></span>

<span data-ttu-id="133c6-171">Pokud ASP.NET Core aplikace běží na Windows, HTTP.sys je alternativou k Kestrel.</span><span class="sxs-lookup"><span data-stu-id="133c6-171">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="133c6-172">Kestrel se obecně doporučuje pro nejlepší výkon.</span><span class="sxs-lookup"><span data-stu-id="133c6-172">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="133c6-173">HTTP.sys lze použít ve scénářích, kde je aplikace zpřístupněna pro Internet a požadované funkce jsou podporovány HTTP.sys ale nikoli Kestrel.</span><span class="sxs-lookup"><span data-stu-id="133c6-173">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="133c6-174">Další informace naleznete v tématu <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="133c6-174">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="133c6-176">HTTP.sys lze také použít pro aplikace, které jsou zpřístupněny pouze interní síti.</span><span class="sxs-lookup"><span data-stu-id="133c6-176">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="133c6-178">Pokyny ke konfiguraci HTTP.sys najdete v tématu <xref:fundamentals/servers/httpsys> .</span><span class="sxs-lookup"><span data-stu-id="133c6-178">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="133c6-179">Serverová infrastruktura ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="133c6-179">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="133c6-180"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>K dispozici v `Startup.Configure` metodě se zveřejňuje <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> vlastnost typu <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> .</span><span class="sxs-lookup"><span data-stu-id="133c6-180">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="133c6-181">Kestrel a HTTP.sys zveřejňují jenom jednu funkci, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ale jiné implementace serveru můžou vystavovat další funkce.</span><span class="sxs-lookup"><span data-stu-id="133c6-181">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="133c6-182">`IServerAddressesFeature`dá se použít k zjištění, který port má implementace serveru vázána za běhu.</span><span class="sxs-lookup"><span data-stu-id="133c6-182">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="133c6-183">Vlastní servery</span><span class="sxs-lookup"><span data-stu-id="133c6-183">Custom servers</span></span>

<span data-ttu-id="133c6-184">Pokud vestavěné servery nesplňují požadavky aplikace, je možné vytvořit vlastní implementaci serveru.</span><span class="sxs-lookup"><span data-stu-id="133c6-184">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="133c6-185">[Průvodce rozhraním Open Web Interface for .NET (Owin)](xref:fundamentals/owin) ukazuje, jak psát implementaci založenou na [Nowin](https://github.com/Bobris/Nowin) <xref:Microsoft.AspNetCore.Hosting.Server.IServer> .</span><span class="sxs-lookup"><span data-stu-id="133c6-185">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="133c6-186">Jenom rozhraní funkcí, které aplikace používá, vyžadují implementaci, ale minimálně <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> a <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> musí být podporovaná.</span><span class="sxs-lookup"><span data-stu-id="133c6-186">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="133c6-187">Spuštění serveru</span><span class="sxs-lookup"><span data-stu-id="133c6-187">Server startup</span></span>

<span data-ttu-id="133c6-188">Server se spustí, když integrované vývojové prostředí (IDE) nebo editor spustí aplikaci:</span><span class="sxs-lookup"><span data-stu-id="133c6-188">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="133c6-189">[Visual Studio](https://visualstudio.microsoft.com): spouštěcí profily lze použít ke spuštění aplikace a serveru pomocí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) / [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) nebo konzoly.</span><span class="sxs-lookup"><span data-stu-id="133c6-189">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="133c6-190">[Visual Studio Code](https://code.visualstudio.com/): aplikace a Server jsou spuštěné nástrojem [omnisharp](https://github.com/OmniSharp/omnisharp-vscode), který aktivuje ladicí program CoreCLR.</span><span class="sxs-lookup"><span data-stu-id="133c6-190">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="133c6-191">[Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/): aplikace a Server jsou spouštěny pomocí [ladicího programu mono měkkého režimu](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="133c6-191">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="133c6-192">Při spuštění aplikace z příkazového řádku ve složce projektu spustí [dotnet spuštění](/dotnet/core/tools/dotnet-run) aplikace a serveru (jenom Kestrel a HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="133c6-192">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="133c6-193">Konfigurace je určena `-c|--configuration` možností, která je nastavena na hodnotu `Debug` (výchozí) nebo `Release` .</span><span class="sxs-lookup"><span data-stu-id="133c6-193">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="133c6-194">*launchSettings.jsv* souboru poskytuje konfiguraci při spouštění aplikace pomocí `dotnet run` nebo s ladicím programem integrovaným do nástrojů, jako je například Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="133c6-194">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="133c6-195">Pokud jsou v *launchSettings.jssouboru k* dispozici profily spuštění, použijte `--launch-profile {PROFILE NAME}` možnost s `dotnet run` příkazem nebo vyberte profil v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="133c6-195">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="133c6-196">Další informace najdete v tématu věnovaném vytváření balíčků [dotnet](/dotnet/core/tools/dotnet-run) a [balíčku pro distribuci .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="133c6-196">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="133c6-197">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="133c6-197">HTTP/2 support</span></span>

<span data-ttu-id="133c6-198">[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení:</span><span class="sxs-lookup"><span data-stu-id="133c6-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="133c6-199">Kestrel</span><span class="sxs-lookup"><span data-stu-id="133c6-199">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="133c6-200">Operační systém</span><span class="sxs-lookup"><span data-stu-id="133c6-200">Operating system</span></span>
    * <span data-ttu-id="133c6-201">Windows Server 2016/Windows 10 nebo novější&dagger;</span><span class="sxs-lookup"><span data-stu-id="133c6-201">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="133c6-202">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="133c6-202">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="133c6-203">HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="133c6-203">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="133c6-204">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="133c6-204">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="133c6-205">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="133c6-205">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="133c6-206">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="133c6-206">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="133c6-207">Cílová architektura: neplatí pro nasazení HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="133c6-207">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="133c6-208">Služba IIS (v rámci procesu)</span><span class="sxs-lookup"><span data-stu-id="133c6-208">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="133c6-209">Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="133c6-209">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="133c6-210">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="133c6-210">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="133c6-211">Služba IIS (mimo jiné procesy)</span><span class="sxs-lookup"><span data-stu-id="133c6-211">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="133c6-212">Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="133c6-212">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="133c6-213">Veřejná připojení hraničních serverů používají HTTP/2, ale připojení reverzního proxy serveru k Kestrel používá protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="133c6-213">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="133c6-214">Cílová architektura: neplatí pro nasazení mimo procesy služby IIS.</span><span class="sxs-lookup"><span data-stu-id="133c6-214">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="133c6-215">&dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="133c6-215">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="133c6-216">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="133c6-216">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="133c6-217">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="133c6-217">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="133c6-218">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="133c6-218">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="133c6-219">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="133c6-219">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="133c6-220">Cílová architektura: neplatí pro nasazení HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="133c6-220">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="133c6-221">Služba IIS (mimo jiné procesy)</span><span class="sxs-lookup"><span data-stu-id="133c6-221">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="133c6-222">Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="133c6-222">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="133c6-223">Veřejná připojení hraničních serverů používají HTTP/2, ale připojení reverzního proxy serveru k Kestrel používá protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="133c6-223">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="133c6-224">Cílová architektura: neplatí pro nasazení mimo procesy služby IIS.</span><span class="sxs-lookup"><span data-stu-id="133c6-224">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="133c6-225">Připojení HTTP/2 musí používat [vyjednávání protokolu aplikační vrstvy (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) a TLS 1,2 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="133c6-225">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="133c6-226">Další informace najdete v tématech, která se týkají scénářů nasazení serveru.</span><span class="sxs-lookup"><span data-stu-id="133c6-226">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="133c6-227">Další materiály</span><span class="sxs-lookup"><span data-stu-id="133c6-227">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
