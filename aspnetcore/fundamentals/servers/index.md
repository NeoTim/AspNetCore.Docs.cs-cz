---
title: Implementace webového serveru v ASP.NET Core
author: guardrex
description: Seznamte se s webovými servery Kestrel a HTTP. sys pro ASP.NET Core. Naučte se, jak vybrat server a kdy použít reverzní proxy server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/01/2019
uid: fundamentals/servers/index
ms.openlocfilehash: 404fec18409a675981fc0c068ee9a99001e06c16
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975545"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="58cdb-104">Implementace webového serveru v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58cdb-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="58cdb-105">Tím, že [Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen zastavení](https://twitter.com/halter73)a [Chris Rossův](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="58cdb-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="58cdb-106">Aplikace ASP.NET Core běží s implementací vnitroprocesového serveru HTTP.</span><span class="sxs-lookup"><span data-stu-id="58cdb-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="58cdb-107">Implementace serveru naslouchá požadavkům HTTP a rozloží je do aplikace jako sadu [funkcí požadavků](xref:fundamentals/request-features) složených do <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="58cdb-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

## <a name="kestrel"></a><span data-ttu-id="58cdb-108">Kestrel</span><span class="sxs-lookup"><span data-stu-id="58cdb-108">Kestrel</span></span>

<span data-ttu-id="58cdb-109">Kestrel je výchozí webový server zahrnutý v ASP.NET Core šablonách projektů.</span><span class="sxs-lookup"><span data-stu-id="58cdb-109">Kestrel is the default web server included in ASP.NET Core project templates.</span></span>

<span data-ttu-id="58cdb-110">Použijte Kestrel:</span><span class="sxs-lookup"><span data-stu-id="58cdb-110">Use Kestrel:</span></span>

* <span data-ttu-id="58cdb-111">Sám sebe jako hraniční Server zpracovávající požadavky přímo ze sítě, včetně Internetu.</span><span class="sxs-lookup"><span data-stu-id="58cdb-111">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="58cdb-113">Pomocí *reverzní proxy server*, například [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="58cdb-113">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="58cdb-114">Reverzní proxy server přijímá požadavky HTTP z Internetu a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="58cdb-114">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="58cdb-116">Konfigurace&mdash;hostování s nebo bez reverzního proxy server&mdash;se podporuje pro aplikace ASP.NET Core 2,1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="58cdb-116">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported for ASP.NET Core 2.1 or later apps.</span></span>

<span data-ttu-id="58cdb-117">Pokyny ke konfiguraci Kestrel a informace o tom, kdy použít Kestrel v konfiguraci reverzního proxy serveru <xref:fundamentals/servers/kestrel>, najdete v tématu.</span><span class="sxs-lookup"><span data-stu-id="58cdb-117">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="58cdb-118">Windows</span><span class="sxs-lookup"><span data-stu-id="58cdb-118">Windows</span></span>](#tab/windows)

<span data-ttu-id="58cdb-119">ASP.NET Core dodávána s následujícím:</span><span class="sxs-lookup"><span data-stu-id="58cdb-119">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="58cdb-120">[Kestrel Server](xref:fundamentals/servers/kestrel) je výchozí implementace serveru HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="58cdb-120">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="58cdb-121">HTTP server IIS je [vnitroprocesové Server](#hosting-models) pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="58cdb-121">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="58cdb-122">[Http. sys Server](xref:fundamentals/servers/httpsys) je server http pouze pro systém Windows založený na [ovladači jádra HTTP. sys a rozhraní API serveru http](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="58cdb-122">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="58cdb-123">Při použití [služby IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikace buď běží:</span><span class="sxs-lookup"><span data-stu-id="58cdb-123">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="58cdb-124">Ve stejném procesu jako pracovní proces služby IIS (v rámci [procesu hostování modelu](#hosting-models)) se serverem HTTP služby IIS.</span><span class="sxs-lookup"><span data-stu-id="58cdb-124">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="58cdb-125">Doporučená konfigurace je *vnitroprocesové* .</span><span class="sxs-lookup"><span data-stu-id="58cdb-125">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="58cdb-126">V procesu odděleném od pracovního procesu služby IIS ( [mimo proces hostující model](#hosting-models)) se [serverem Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="58cdb-126">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="58cdb-127">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) je nativní modul služby IIS, který zpracovává NATIVNÍ požadavky služby IIS mezi službou IIS a VNITROPROCESOVÉ serverem HTTP služby IIS nebo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="58cdb-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="58cdb-128">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="58cdb-128">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="58cdb-129">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="58cdb-129">Hosting models</span></span>

<span data-ttu-id="58cdb-130">Při použití hostování v rámci procesu ASP.NET Core aplikace běží ve stejném procesu jako jeho pracovní proces služby IIS.</span><span class="sxs-lookup"><span data-stu-id="58cdb-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="58cdb-131">Hostování v rámci procesů poskytují lepší výkon než hostování mimo procesy, protože požadavky nejsou proxy serverem přes adaptér zpětné smyčky, síťové rozhraní, které vrátí odchozí síťový provoz zpátky do stejného počítače.</span><span class="sxs-lookup"><span data-stu-id="58cdb-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="58cdb-132">Služba IIS zpracovává správu procesů pomocí [aktivační služby procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="58cdb-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="58cdb-133">Pomocí mimoprocesového hostování ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS a modul zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="58cdb-133">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="58cdb-134">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="58cdb-134">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="58cdb-135">To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="58cdb-135">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="58cdb-136">Další informace a pokyny k konfiguraci najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="58cdb-136">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[<span data-ttu-id="58cdb-137">macOS</span><span class="sxs-lookup"><span data-stu-id="58cdb-137">macOS</span></span>](#tab/macos)

<span data-ttu-id="58cdb-138">ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="58cdb-138">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="58cdb-139">Linux</span><span class="sxs-lookup"><span data-stu-id="58cdb-139">Linux</span></span>](#tab/linux)

<span data-ttu-id="58cdb-140">ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="58cdb-140">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="58cdb-141">Windows</span><span class="sxs-lookup"><span data-stu-id="58cdb-141">Windows</span></span>](#tab/windows)

<span data-ttu-id="58cdb-142">ASP.NET Core dodávána s následujícím:</span><span class="sxs-lookup"><span data-stu-id="58cdb-142">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="58cdb-143">[Kestrel Server](xref:fundamentals/servers/kestrel) je výchozím serverem HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="58cdb-143">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="58cdb-144">[Http. sys Server](xref:fundamentals/servers/httpsys) je server http pouze pro systém Windows založený na [ovladači jádra HTTP. sys a rozhraní API serveru http](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="58cdb-144">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="58cdb-145">Při použití [služby IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikace běží v procesu odděleně od pracovního procesu služby IIS (*mimo proces*) se [serverem Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="58cdb-145">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="58cdb-146">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="58cdb-146">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="58cdb-147">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="58cdb-147">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="58cdb-148">To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="58cdb-148">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="58cdb-149">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou mimo proces:</span><span class="sxs-lookup"><span data-stu-id="58cdb-149">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Modul ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="58cdb-151">Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="58cdb-151">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="58cdb-152">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="58cdb-152">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="58cdb-153">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="58cdb-153">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="58cdb-154">Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server, na kterém má naslouchat `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="58cdb-154">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="58cdb-155">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="58cdb-155">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="58cdb-156">Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.</span><span class="sxs-lookup"><span data-stu-id="58cdb-156">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="58cdb-157">Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="58cdb-157">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="58cdb-158">Kanál middlewaru zpracovává požadavek a předá ho jako `HttpContext` instanci do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="58cdb-158">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="58cdb-159">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="58cdb-159">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="58cdb-160">Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="58cdb-160">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="58cdb-161">Pokyny ke konfiguraci služby IIS a ASP.NET Core modulu najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="58cdb-161">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[<span data-ttu-id="58cdb-162">macOS</span><span class="sxs-lookup"><span data-stu-id="58cdb-162">macOS</span></span>](#tab/macos)

<span data-ttu-id="58cdb-163">ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="58cdb-163">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="58cdb-164">Linux</span><span class="sxs-lookup"><span data-stu-id="58cdb-164">Linux</span></span>](#tab/linux)

<span data-ttu-id="58cdb-165">ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="58cdb-165">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="58cdb-166">Nginx s Kestrel</span><span class="sxs-lookup"><span data-stu-id="58cdb-166">Nginx with Kestrel</span></span>

<span data-ttu-id="58cdb-167">Informace o tom, jak používat Nginx v systému Linux jako reverzní proxy server pro Kestrel, naleznete <xref:host-and-deploy/linux-nginx>v tématu.</span><span class="sxs-lookup"><span data-stu-id="58cdb-167">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="58cdb-168">Apache s Kestrel</span><span class="sxs-lookup"><span data-stu-id="58cdb-168">Apache with Kestrel</span></span>

<span data-ttu-id="58cdb-169">Informace o tom, jak používat Apache v systému Linux jako reverzní proxy server pro Kestrel, najdete <xref:host-and-deploy/linux-apache>v tématu.</span><span class="sxs-lookup"><span data-stu-id="58cdb-169">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="58cdb-170">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="58cdb-170">HTTP.sys</span></span>

<span data-ttu-id="58cdb-171">Pokud ASP.NET Core aplikace běží na Windows, HTTP. sys je alternativou k Kestrel.</span><span class="sxs-lookup"><span data-stu-id="58cdb-171">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="58cdb-172">Kestrel se obecně doporučuje pro nejlepší výkon.</span><span class="sxs-lookup"><span data-stu-id="58cdb-172">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="58cdb-173">HTTP. sys se dá použít ve scénářích, kde se aplikace zveřejňuje na internetu a požadované funkce podporuje HTTP. sys, ale ne Kestrel.</span><span class="sxs-lookup"><span data-stu-id="58cdb-173">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="58cdb-174">Další informace naleznete v tématu <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="58cdb-174">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP. sys komunikuje přímo s internetem.](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="58cdb-176">HTTP. sys se dá použít taky pro aplikace, které se zveřejňují jenom pro interní síť.</span><span class="sxs-lookup"><span data-stu-id="58cdb-176">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP. sys komunikuje přímo s interní sítí.](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="58cdb-178">Pokyny k konfiguraci HTTP. sys najdete v <xref:fundamentals/servers/httpsys>tématu.</span><span class="sxs-lookup"><span data-stu-id="58cdb-178">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="58cdb-179">Serverová infrastruktura ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58cdb-179">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="58cdb-180">K dispozici `Startup.Configure` v metodě se zveřejňuje <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> vlastnost typu <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>. <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder></span><span class="sxs-lookup"><span data-stu-id="58cdb-180">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="58cdb-181">Kestrel a http. sys zveřejňují jenom jednu funkci <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, ale různé implementace serveru můžou vystavovat další funkce.</span><span class="sxs-lookup"><span data-stu-id="58cdb-181">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="58cdb-182">`IServerAddressesFeature`dá se použít k zjištění, který port má implementace serveru vázána za běhu.</span><span class="sxs-lookup"><span data-stu-id="58cdb-182">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="58cdb-183">Vlastní servery</span><span class="sxs-lookup"><span data-stu-id="58cdb-183">Custom servers</span></span>

<span data-ttu-id="58cdb-184">Pokud vestavěné servery nesplňují požadavky aplikace, je možné vytvořit vlastní implementaci serveru.</span><span class="sxs-lookup"><span data-stu-id="58cdb-184">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="58cdb-185">[Průvodce rozhraním Open Web Interface for .NET (Owin)](xref:fundamentals/owin) ukazuje, jak psát implementaci založenou <xref:Microsoft.AspNetCore.Hosting.Server.IServer> na [Nowin](https://github.com/Bobris/Nowin).</span><span class="sxs-lookup"><span data-stu-id="58cdb-185">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="58cdb-186">Jenom rozhraní funkcí, které aplikace používá, vyžadují implementaci, ale <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> minimálně a <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> musí být podporovaná.</span><span class="sxs-lookup"><span data-stu-id="58cdb-186">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="58cdb-187">Spuštění serveru</span><span class="sxs-lookup"><span data-stu-id="58cdb-187">Server startup</span></span>

<span data-ttu-id="58cdb-188">Server se spustí, když integrované vývojové prostředí (IDE) nebo editor spustí aplikaci:</span><span class="sxs-lookup"><span data-stu-id="58cdb-188">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="58cdb-189">[Visual Studio](https://visualstudio.microsoft.com) Spouštěcí profily lze použít ke spuštění aplikace a serveru pomocí [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/ASP.NET Core modulu nebo konzoly.[](xref:host-and-deploy/aspnet-core-module) &ndash;</span><span class="sxs-lookup"><span data-stu-id="58cdb-189">[Visual Studio](https://visualstudio.microsoft.com) &ndash; Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="58cdb-190">[Visual Studio Code](https://code.visualstudio.com/) Aplikace a Server jsou spouštěny pomocí omnisharp, který aktivuje ladicí program CoreCLR. [](https://github.com/OmniSharp/omnisharp-vscode) &ndash;</span><span class="sxs-lookup"><span data-stu-id="58cdb-190">[Visual Studio Code](https://code.visualstudio.com/) &ndash; The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="58cdb-191">[Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/) Aplikace a Server jsou spouštěny pomocí [ladicího programu mono v režimu mono.](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/) &ndash;</span><span class="sxs-lookup"><span data-stu-id="58cdb-191">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="58cdb-192">Při spuštění aplikace z příkazového řádku ve složce projektu spustí [dotnet spuštění](/dotnet/core/tools/dotnet-run) aplikace a serveru (jenom Kestrel a http. sys).</span><span class="sxs-lookup"><span data-stu-id="58cdb-192">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="58cdb-193">Konfigurace je určena `-c|--configuration` možností, která je nastavena na hodnotu `Debug` (výchozí) nebo `Release`.</span><span class="sxs-lookup"><span data-stu-id="58cdb-193">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span> <span data-ttu-id="58cdb-194">Pokud jsou v souboru *launchSettings. JSON* k dispozici profily spuštění, použijte `--launch-profile <NAME>` možnost k nastavení profilu spuštění `Development` (například nebo `Production`).</span><span class="sxs-lookup"><span data-stu-id="58cdb-194">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile <NAME>` option to set the launch profile (for example, `Development` or `Production`).</span></span> <span data-ttu-id="58cdb-195">Další informace najdete v tématu věnovaném vytváření balíčků [dotnet](/dotnet/core/tools/dotnet-run) a [balíčku pro distribuci .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="58cdb-195">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="58cdb-196">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="58cdb-196">HTTP/2 support</span></span>

<span data-ttu-id="58cdb-197">[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení:</span><span class="sxs-lookup"><span data-stu-id="58cdb-197">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="58cdb-198">Kestrel</span><span class="sxs-lookup"><span data-stu-id="58cdb-198">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="58cdb-199">Operační systém</span><span class="sxs-lookup"><span data-stu-id="58cdb-199">Operating system</span></span>
    * <span data-ttu-id="58cdb-200">Windows Server 2016/Windows 10 nebo novější&dagger;</span><span class="sxs-lookup"><span data-stu-id="58cdb-200">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="58cdb-201">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="58cdb-201">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="58cdb-202">HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="58cdb-202">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="58cdb-203">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="58cdb-203">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="58cdb-204">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="58cdb-204">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="58cdb-205">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="58cdb-205">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="58cdb-206">Cílová architektura: Neplatí pro nasazení HTTP. sys.</span><span class="sxs-lookup"><span data-stu-id="58cdb-206">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="58cdb-207">Služba IIS (v rámci procesu)</span><span class="sxs-lookup"><span data-stu-id="58cdb-207">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="58cdb-208">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="58cdb-208">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="58cdb-209">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="58cdb-209">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="58cdb-210">Služba IIS (mimo jiné procesy)</span><span class="sxs-lookup"><span data-stu-id="58cdb-210">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="58cdb-211">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="58cdb-211">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="58cdb-212">Veřejná připojení hraničních serverů používají HTTP/2, ale připojení reverzního proxy serveru k Kestrel používá protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="58cdb-212">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="58cdb-213">Cílová architektura: Neplatí pro nasazení mimo procesy služby IIS.</span><span class="sxs-lookup"><span data-stu-id="58cdb-213">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="58cdb-214">&dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="58cdb-214">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="58cdb-215">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="58cdb-215">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="58cdb-216">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="58cdb-216">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="58cdb-217">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="58cdb-217">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="58cdb-218">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="58cdb-218">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="58cdb-219">Cílová architektura: Neplatí pro nasazení HTTP. sys.</span><span class="sxs-lookup"><span data-stu-id="58cdb-219">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="58cdb-220">Služba IIS (mimo jiné procesy)</span><span class="sxs-lookup"><span data-stu-id="58cdb-220">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="58cdb-221">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="58cdb-221">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="58cdb-222">Veřejná připojení hraničních serverů používají HTTP/2, ale připojení reverzního proxy serveru k Kestrel používá protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="58cdb-222">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="58cdb-223">Cílová architektura: Neplatí pro nasazení mimo procesy služby IIS.</span><span class="sxs-lookup"><span data-stu-id="58cdb-223">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="58cdb-224">Připojení HTTP/2 musí používat [vyjednávání protokolu aplikační vrstvy (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) a TLS 1,2 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="58cdb-224">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="58cdb-225">Další informace najdete v tématech, která se týkají scénářů nasazení serveru.</span><span class="sxs-lookup"><span data-stu-id="58cdb-225">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="58cdb-226">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="58cdb-226">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
