---
title: Implementace webového serveru v ASP.NET Core
author: rick-anderson
description: Objevte webové servery Kestrel a HTTP.sys pro ASP.NET Core. Přečtěte si, jak vybrat server a kdy použít reverzní proxy server.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/servers/index
ms.openlocfilehash: d46793ef54c99fe609b5983c5a658fb7b20032fa
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666339"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="feb1c-104">Implementace webového serveru v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="feb1c-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="feb1c-105">[Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), Stephen [Halter](https://twitter.com/halter73), a [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="feb1c-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="feb1c-106">Aplikace ASP.NET Core běží s implementací inprocesního http serveru.</span><span class="sxs-lookup"><span data-stu-id="feb1c-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="feb1c-107">Implementace serveru naslouchá požadavkům HTTP a zobrazuje je do aplikace jako sadu <xref:Microsoft.AspNetCore.Http.HttpContext>funkcí [požadavků](xref:fundamentals/request-features) složených do aplikace .</span><span class="sxs-lookup"><span data-stu-id="feb1c-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

## <a name="kestrel"></a><span data-ttu-id="feb1c-108">Kestrel</span><span class="sxs-lookup"><span data-stu-id="feb1c-108">Kestrel</span></span>

<span data-ttu-id="feb1c-109">Poštolka je výchozí webový server určený šablonami projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="feb1c-109">Kestrel is the default web server specified by the ASP.NET Core project templates.</span></span>

<span data-ttu-id="feb1c-110">Použijte Poštolka:</span><span class="sxs-lookup"><span data-stu-id="feb1c-110">Use Kestrel:</span></span>

* <span data-ttu-id="feb1c-111">Samo o sobě jako hraniční server zpracování požadavků přímo ze sítě, včetně Internetu.</span><span class="sxs-lookup"><span data-stu-id="feb1c-111">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Poštolka komunikuje přímo s Internetem bez reverzního proxy serveru](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="feb1c-113">S *reverzním proxy serverem*, například [Internetovou informační službou (IIS),](https://www.iis.net/) [Nginxem](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="feb1c-113">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="feb1c-114">Reverzní proxy server přijímá požadavky HTTP z Internetu a předá je ke strel.</span><span class="sxs-lookup"><span data-stu-id="feb1c-114">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Poštolka komunikuje nepřímo s Internetem prostřednictvím reverzního proxy serveru, jako je služba IIS, Nginx nebo Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="feb1c-116">Je podporována konfigurace&mdash;hostování s&mdash;reverzním proxy serverem nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="feb1c-116">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="feb1c-117">Pokyny ke konfiguraci kestrelu a informace o tom, kdy <xref:fundamentals/servers/kestrel>použít Kestrel v konfiguraci reverzního proxy serveru, naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="feb1c-117">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="feb1c-118">Windows</span><span class="sxs-lookup"><span data-stu-id="feb1c-118">Windows</span></span>](#tab/windows)

<span data-ttu-id="feb1c-119">ASP.NET Core dodává s těmito loděmi:</span><span class="sxs-lookup"><span data-stu-id="feb1c-119">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="feb1c-120">[Kestrel server](xref:fundamentals/servers/kestrel) je výchozí implementace http serveru napříč platformami.</span><span class="sxs-lookup"><span data-stu-id="feb1c-120">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="feb1c-121">Server HTTP služby IIS je pro službu IIS [neprocesový server.](#hosting-models)</span><span class="sxs-lookup"><span data-stu-id="feb1c-121">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="feb1c-122">[Server HTTP.sys](xref:fundamentals/servers/httpsys) je server HTTP pouze pro systém Windows založený na [ovladači jádra HTTP.sys a rozhraní HTTP Server API](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="feb1c-122">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="feb1c-123">Při používání [služby IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)se aplikace spustí buď:</span><span class="sxs-lookup"><span data-stu-id="feb1c-123">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="feb1c-124">Ve stejném procesu jako pracovní proces služby IIS [(model hostování v procesu)](#hosting-models)se serverem HTTP služby IIS.</span><span class="sxs-lookup"><span data-stu-id="feb1c-124">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="feb1c-125">*V průběhu procesu* je doporučená konfigurace.</span><span class="sxs-lookup"><span data-stu-id="feb1c-125">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="feb1c-126">V procesu odděleném od pracovního procesu služby IIS [(mimoprocesový model hostování)](#hosting-models)se [serverem Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="feb1c-126">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="feb1c-127">[Základní modul ASP.NET](xref:host-and-deploy/aspnet-core-module) je nativní modul služby IIS, který zpracovává nativní požadavky služby IIS mezi službou IIS a neprocesovým serverem HTTP služby IIS nebo poštolkou.</span><span class="sxs-lookup"><span data-stu-id="feb1c-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="feb1c-128">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="feb1c-128">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="feb1c-129">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="feb1c-129">Hosting models</span></span>

<span data-ttu-id="feb1c-130">Pomocí hostování v rámci procesu běží aplikace ASP.NET Core ve stejném procesu jako pracovní proces služby IIS.</span><span class="sxs-lookup"><span data-stu-id="feb1c-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="feb1c-131">Hostování v procesu poskytuje lepší výkon než mimoproceshosting, protože požadavky nejsou proxied přes adaptér zpětné smyčky, síťové rozhraní, které vrací odchozí síťový provoz zpět do stejného počítače.</span><span class="sxs-lookup"><span data-stu-id="feb1c-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="feb1c-132">Služba IIS zpracovává správu procesů pomocí [služby aktivace procesů systému Windows (WAS).](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)</span><span class="sxs-lookup"><span data-stu-id="feb1c-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="feb1c-133">Pomocí mimoprocesového hostingu ASP.NET základní aplikace spouštějí v procesu odděleném od pracovního procesu služby IIS a modul zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="feb1c-133">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="feb1c-134">Modul spustí proces pro aplikaci ASP.NET Core při příchodu prvního požadavku a restartuje aplikaci, pokud se vypne nebo dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="feb1c-134">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="feb1c-135">Toto je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány [službou aktivace procesu systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="feb1c-135">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="feb1c-136">Další informace a pokyny ke konfiguraci naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="feb1c-136">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="feb1c-137">macOS</span><span class="sxs-lookup"><span data-stu-id="feb1c-137">macOS</span></span>](#tab/macos)

<span data-ttu-id="feb1c-138">ASP.NET Core je dodáván se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí http server pro více platforem.</span><span class="sxs-lookup"><span data-stu-id="feb1c-138">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="feb1c-139">Linux</span><span class="sxs-lookup"><span data-stu-id="feb1c-139">Linux</span></span>](#tab/linux)

<span data-ttu-id="feb1c-140">ASP.NET Core je dodáván se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí http server pro více platforem.</span><span class="sxs-lookup"><span data-stu-id="feb1c-140">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="feb1c-141">Windows</span><span class="sxs-lookup"><span data-stu-id="feb1c-141">Windows</span></span>](#tab/windows)

<span data-ttu-id="feb1c-142">ASP.NET Core dodává s těmito loděmi:</span><span class="sxs-lookup"><span data-stu-id="feb1c-142">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="feb1c-143">[Kestrel server](xref:fundamentals/servers/kestrel) je výchozí, multiplatformní HTTP server.</span><span class="sxs-lookup"><span data-stu-id="feb1c-143">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="feb1c-144">[Server HTTP.sys](xref:fundamentals/servers/httpsys) je server HTTP pouze pro systém Windows založený na [ovladači jádra HTTP.sys a rozhraní HTTP Server API](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="feb1c-144">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="feb1c-145">Při použití [služby IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)je aplikace spuštěna v procesu odděleném od pracovního procesu služby IIS *(mimo proces)* se [serverem Kestrel](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="feb1c-145">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="feb1c-146">Vzhledem k tomu, ASP.NET základní aplikace spustit v procesu odděleném od pracovního procesu služby IIS, modul zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="feb1c-146">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="feb1c-147">Modul spustí proces pro aplikaci ASP.NET Core při příchodu prvního požadavku a restartuje aplikaci, pokud se vypne nebo dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="feb1c-147">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="feb1c-148">Toto je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány [službou aktivace procesu systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="feb1c-148">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="feb1c-149">Následující diagram znázorňuje vztah mezi iis, ASP.NET základní modul a aplikace hostované mimo proces:</span><span class="sxs-lookup"><span data-stu-id="feb1c-149">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Modul ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="feb1c-151">Požadavky dorazí z webu do ovladače HTTP.sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="feb1c-151">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="feb1c-152">Řidič směruje požadavky do služby IIS na nakonfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="feb1c-152">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="feb1c-153">Modul předá požadavky Kestrel na náhodný port pro aplikaci, která není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="feb1c-153">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="feb1c-154">Modul určuje port prostřednictvím proměnné prostředí při spuštění a [middleware integrace služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server tak, aby naslouchal . `http://localhost:{port}`</span><span class="sxs-lookup"><span data-stu-id="feb1c-154">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="feb1c-155">Jsou prováděny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="feb1c-155">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="feb1c-156">Modul nepodporuje předávání https, takže požadavky jsou předávány přes HTTP i v případě, že přijaté službou IIS přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="feb1c-156">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="feb1c-157">Poté, co Kestrel vyzvedne požadavek z modulu, je požadavek posunut do kanálu ASP.NET middleware.</span><span class="sxs-lookup"><span data-stu-id="feb1c-157">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="feb1c-158">Middleware kanálu zpracovává požadavek a předá `HttpContext` jej jako instanci logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="feb1c-158">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="feb1c-159">Middleware přidané integrací služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase tak, aby odpovídaly předání požadavku ke společnosti Kestrel.</span><span class="sxs-lookup"><span data-stu-id="feb1c-159">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="feb1c-160">Odpověď aplikace je předána zpět do služby IIS, která ji odešle zpět do klienta HTTP, který inicioval požadavek.</span><span class="sxs-lookup"><span data-stu-id="feb1c-160">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="feb1c-161">Pokyny ke konfiguraci služby IIS a ASP.NET základního modulu naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="feb1c-161">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="feb1c-162">macOS</span><span class="sxs-lookup"><span data-stu-id="feb1c-162">macOS</span></span>](#tab/macos)

<span data-ttu-id="feb1c-163">ASP.NET Core je dodáván se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí http server pro více platforem.</span><span class="sxs-lookup"><span data-stu-id="feb1c-163">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="feb1c-164">Linux</span><span class="sxs-lookup"><span data-stu-id="feb1c-164">Linux</span></span>](#tab/linux)

<span data-ttu-id="feb1c-165">ASP.NET Core je dodáván se [serverem Kestrel](xref:fundamentals/servers/kestrel), což je výchozí http server pro více platforem.</span><span class="sxs-lookup"><span data-stu-id="feb1c-165">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="feb1c-166">Nginx s Poštolem</span><span class="sxs-lookup"><span data-stu-id="feb1c-166">Nginx with Kestrel</span></span>

<span data-ttu-id="feb1c-167">Informace o tom, jak používat Nginx na Linuxu jako <xref:host-and-deploy/linux-nginx>reverzní proxy server pro Kestrel, viz .</span><span class="sxs-lookup"><span data-stu-id="feb1c-167">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="feb1c-168">Apache s Kestrelem</span><span class="sxs-lookup"><span data-stu-id="feb1c-168">Apache with Kestrel</span></span>

<span data-ttu-id="feb1c-169">Informace o tom, jak používat Apache na Linuxu jako <xref:host-and-deploy/linux-apache>reverzní proxy server pro Kestrel, viz .</span><span class="sxs-lookup"><span data-stu-id="feb1c-169">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="feb1c-170">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="feb1c-170">HTTP.sys</span></span>

<span data-ttu-id="feb1c-171">Pokud ASP.NET základní aplikace jsou spuštěny v systému Windows, HTTP.sys je alternativou ke Kestrel.</span><span class="sxs-lookup"><span data-stu-id="feb1c-171">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="feb1c-172">Kestrel se obecně doporučuje pro nejlepší výkon.</span><span class="sxs-lookup"><span data-stu-id="feb1c-172">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="feb1c-173">HTTP.sys lze použít ve scénářích, kde je aplikace vystavena internetu a požadované funkce jsou podporovány HTTP.sys, ale ne Kestrel.</span><span class="sxs-lookup"><span data-stu-id="feb1c-173">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="feb1c-174">Další informace naleznete v tématu <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="feb1c-174">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys komunikuje přímo s Internetem](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="feb1c-176">Http.sys lze také použít pro aplikace, které jsou vystaveny pouze interní síti.</span><span class="sxs-lookup"><span data-stu-id="feb1c-176">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys komunikuje přímo s interní sítí](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="feb1c-178">Pokyny ke konfiguraci http.sys naleznete v tématu <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="feb1c-178">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="feb1c-179">ASP.NET základní serverová infrastruktura</span><span class="sxs-lookup"><span data-stu-id="feb1c-179">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="feb1c-180">K <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> dispozici `Startup.Configure` v metodě <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> zpřístupňuje vlastnost typu <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span><span class="sxs-lookup"><span data-stu-id="feb1c-180">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="feb1c-181">Poštolky a HTTP.sys pouze vystavit <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>jednu funkci každý , , ale různé implementace serveru může vystavit další funkce.</span><span class="sxs-lookup"><span data-stu-id="feb1c-181">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="feb1c-182">`IServerAddressesFeature`lze zjistit, který port má implementace serveru vázána za běhu.</span><span class="sxs-lookup"><span data-stu-id="feb1c-182">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="feb1c-183">Vlastní servery</span><span class="sxs-lookup"><span data-stu-id="feb1c-183">Custom servers</span></span>

<span data-ttu-id="feb1c-184">Pokud integrované servery nesplňují požadavky aplikace, lze vytvořit vlastní implementaci serveru.</span><span class="sxs-lookup"><span data-stu-id="feb1c-184">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="feb1c-185">[Průvodce Open Web Interface for .NET (OWIN)](xref:fundamentals/owin) ukazuje, jak <xref:Microsoft.AspNetCore.Hosting.Server.IServer> napsat implementaci založenou na [aplikaci Nowin.](https://github.com/Bobris/Nowin)</span><span class="sxs-lookup"><span data-stu-id="feb1c-185">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="feb1c-186">Pouze rozhraní funkcí, které aplikace používá, vyžadují <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> implementaci, i když minimálně a <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> musí být podporována.</span><span class="sxs-lookup"><span data-stu-id="feb1c-186">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="feb1c-187">Spuštění serveru</span><span class="sxs-lookup"><span data-stu-id="feb1c-187">Server startup</span></span>

<span data-ttu-id="feb1c-188">Server se spustí, když integrované vývojové prostředí (IDE) nebo editor spustí aplikaci:</span><span class="sxs-lookup"><span data-stu-id="feb1c-188">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="feb1c-189">Profily spuštění [sady Visual Studio](https://visualstudio.microsoft.com) &ndash; lze použít ke spuštění aplikace a serveru pomocí aplikace a serveru pomocí[modulu](xref:host-and-deploy/aspnet-core-module) [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/ASP.NET Core Module nebo konzoly.</span><span class="sxs-lookup"><span data-stu-id="feb1c-189">[Visual Studio](https://visualstudio.microsoft.com) &ndash; Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="feb1c-190">[Visual Studio Code](https://code.visualstudio.com/) &ndash; Aplikace a server jsou schované [omnisharp](https://github.com/OmniSharp/omnisharp-vscode), který aktivuje ladicí program CoreCLR.</span><span class="sxs-lookup"><span data-stu-id="feb1c-190">[Visual Studio Code](https://code.visualstudio.com/) &ndash; The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="feb1c-191">[Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; Aplikace a server jsou schované [ladicím programem mono soft-mode](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="feb1c-191">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="feb1c-192">Při spuštění aplikace z příkazového řádku ve složce projektu [spustí dotnet spuštění](/dotnet/core/tools/dotnet-run) aplikace a serveru (pouze Kestrel a HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="feb1c-192">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="feb1c-193">Konfigurace je určena `-c|--configuration` možností, která je `Debug` nastavena na `Release`hodnotu buď (výchozí) nebo .</span><span class="sxs-lookup"><span data-stu-id="feb1c-193">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="feb1c-194">Soubor *launchSettings.json* poskytuje konfiguraci při `dotnet run` spouštění aplikace s ladicím programem integrovaným do nástrojů, jako je například Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="feb1c-194">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="feb1c-195">Pokud jsou profily spuštění k dispozici v souboru `--launch-profile {PROFILE NAME}` *launchSettings.json,* použijte možnost s příkazem `dotnet run` nebo vyberte profil v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="feb1c-195">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="feb1c-196">Další informace naleznete [v tématu dotnet run](/dotnet/core/tools/dotnet-run) a [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="feb1c-196">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="feb1c-197">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="feb1c-197">HTTP/2 support</span></span>

<span data-ttu-id="feb1c-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) je podporován ASP.NET jádrem v následujících scénářích nasazení:</span><span class="sxs-lookup"><span data-stu-id="feb1c-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="feb1c-199">Kestrel</span><span class="sxs-lookup"><span data-stu-id="feb1c-199">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="feb1c-200">Operační systém</span><span class="sxs-lookup"><span data-stu-id="feb1c-200">Operating system</span></span>
    * <span data-ttu-id="feb1c-201">Windows Server 2016/Windows 10 nebo novější&dagger;</span><span class="sxs-lookup"><span data-stu-id="feb1c-201">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="feb1c-202">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16.04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="feb1c-202">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="feb1c-203">HTTP/2 bude v budoucí verzi v systému macOS podporován.</span><span class="sxs-lookup"><span data-stu-id="feb1c-203">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="feb1c-204">Cílová architektura: .NET Core 2.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="feb1c-204">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="feb1c-205">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="feb1c-205">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="feb1c-206">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="feb1c-206">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="feb1c-207">Cílová architektura: Nevztahuje se na nasazení HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="feb1c-207">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="feb1c-208">IIS (v průběhu)</span><span class="sxs-lookup"><span data-stu-id="feb1c-208">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="feb1c-209">Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="feb1c-209">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="feb1c-210">Cílová architektura: .NET Core 2.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="feb1c-210">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="feb1c-211">IIS (mimo proces)</span><span class="sxs-lookup"><span data-stu-id="feb1c-211">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="feb1c-212">Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="feb1c-212">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="feb1c-213">Připojení serveru edge směřující mj.</span><span class="sxs-lookup"><span data-stu-id="feb1c-213">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="feb1c-214">Cílový rámec: Nevztahuje se na mimoprocesová nasazení služby IIS.</span><span class="sxs-lookup"><span data-stu-id="feb1c-214">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="feb1c-215">&dagger;Kestrel má omezenou podporu pro HTTP/2 v systémech Windows Server 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="feb1c-215">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="feb1c-216">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS dostupných v těchto operačních systémech je omezený.</span><span class="sxs-lookup"><span data-stu-id="feb1c-216">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="feb1c-217">K zabezpečení připojení TLS může být vyžadován certifikát generovaný pomocí algoritmu digitálního podpisu eliptické křivky (ECDSA).</span><span class="sxs-lookup"><span data-stu-id="feb1c-217">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="feb1c-218">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="feb1c-218">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="feb1c-219">Windows Server 2016/Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="feb1c-219">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="feb1c-220">Cílová architektura: Nevztahuje se na nasazení HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="feb1c-220">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="feb1c-221">IIS (mimo proces)</span><span class="sxs-lookup"><span data-stu-id="feb1c-221">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="feb1c-222">Windows Server 2016/Windows 10 nebo novější; IIS 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="feb1c-222">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="feb1c-223">Připojení serveru edge směřující mj.</span><span class="sxs-lookup"><span data-stu-id="feb1c-223">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="feb1c-224">Cílový rámec: Nevztahuje se na mimoprocesová nasazení služby IIS.</span><span class="sxs-lookup"><span data-stu-id="feb1c-224">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="feb1c-225">Připojení HTTP/2 musí používat [vyjednávání protokolu aplikační vrstvy (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) a TLS 1.2 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="feb1c-225">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="feb1c-226">Další informace naleznete v tématech, která se vztahuje k scénářům nasazení serveru.</span><span class="sxs-lookup"><span data-stu-id="feb1c-226">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="feb1c-227">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="feb1c-227">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
