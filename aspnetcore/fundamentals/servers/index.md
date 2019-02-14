---
title: Implementací webového serveru v ASP.NET Core
author: guardrex
description: Zjišťování webové servery přes Kestrel a HTTP.sys pro ASP.NET Core. Zjistěte, jak vybrat server a kdy použít reverzní proxy server.
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/13/2019
uid: fundamentals/servers/index
ms.openlocfilehash: 672fe2ce6fd0adae09c380fe508344a254f1a9fe
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248131"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="b28ff-104">Implementací webového serveru v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b28ff-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="b28ff-105">Podle [Petr Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), a [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="b28ff-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="b28ff-106">Aplikace ASP.NET Core spouští implementaci serveru HTTP v procesu.</span><span class="sxs-lookup"><span data-stu-id="b28ff-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="b28ff-107">Implementace server přijímá požadavky protokolu HTTP a poskytuje je na aplikaci jako sada [funkce požadavků](xref:fundamentals/request-features) složený do <xref:Microsoft.AspNetCore.Http.HttpContext>.</span><span class="sxs-lookup"><span data-stu-id="b28ff-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="b28ff-108">Windows</span><span class="sxs-lookup"><span data-stu-id="b28ff-108">Windows</span></span>](#tab/windows)

<span data-ttu-id="b28ff-109">ASP.NET Core se dodává s následujícími možnostmi:</span><span class="sxs-lookup"><span data-stu-id="b28ff-109">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="b28ff-110">[Kestrel server](xref:fundamentals/servers/kestrel) je výchozí, implementaci serveru HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="b28ff-110">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="b28ff-111">Server služby IIS protokolu HTTP [v procesový server](#in-process-hosting-model) pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="b28ff-111">IIS HTTP Server is an [in-process server](#in-process-hosting-model) for IIS.</span></span>
* <span data-ttu-id="b28ff-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) je na základě protokolu HTTP jenom pro Windows server [ovladač HTTP.sys jádra a rozhraní API serveru HTTP](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="b28ff-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="b28ff-113">Při použití [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), buď spuštění aplikace:</span><span class="sxs-lookup"><span data-stu-id="b28ff-113">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="b28ff-114">Ve stejném procesu jako pracovní proces služby IIS ( [model hostingu v procesu](#in-process-hosting-model)) se [HTTP serveru služby IIS](#iis-http-server).</span><span class="sxs-lookup"><span data-stu-id="b28ff-114">In the same process as the IIS worker process (the [in-process hosting model](#in-process-hosting-model)) with the [IIS HTTP Server](#iis-http-server).</span></span> <span data-ttu-id="b28ff-115">*V procesu* je doporučená konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b28ff-115">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="b28ff-116">V procesu oddělit od pracovní proces služby IIS ( [model hostingu mimo proces](#out-of-process-hosting-model)) se [Kestrel server](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="b28ff-116">In a process separate from the IIS worker process (the [out-of-process hosting model](#out-of-process-hosting-model)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="b28ff-117">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) je nativní modul služby IIS, která zpracovává nativní požadavků služby IIS mezi služby IIS a v rámci procesu serveru služby IIS protokolu HTTP nebo Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b28ff-117">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="b28ff-118">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="b28ff-118">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="b28ff-119">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="b28ff-119">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="b28ff-120">Model hostingu v procesu</span><span class="sxs-lookup"><span data-stu-id="b28ff-120">In-process hosting model</span></span>

<span data-ttu-id="b28ff-121">Proces hostování, ASP.NET Core pomocí app běží ve stejném procesu jako jeho pracovní proces služby IIS.</span><span class="sxs-lookup"><span data-stu-id="b28ff-121">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="b28ff-122">Hostování v procesu poskytuje Vylepšený výkon oproti mimo proces hostování, protože žádosti nejsou směrovány přes proxy server prostřednictvím adaptéru zpětné smyčky, síťové rozhraní, které vrátí odchozí síťový provoz do stejného počítače.</span><span class="sxs-lookup"><span data-stu-id="b28ff-122">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="b28ff-123">Služba IIS zpracovává proces správy pomocí [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="b28ff-123">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="b28ff-124">Modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b28ff-124">The ASP.NET Core Module:</span></span>

* <span data-ttu-id="b28ff-125">Provede inicializaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="b28ff-125">Performs app initialization.</span></span>
  * <span data-ttu-id="b28ff-126">Načtení [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="b28ff-126">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="b28ff-127">Volání `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="b28ff-127">Calls `Program.Main`.</span></span>
* <span data-ttu-id="b28ff-128">Zpracovává životnost nativní požadavků služby IIS.</span><span class="sxs-lookup"><span data-stu-id="b28ff-128">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="b28ff-129">Model hostingu v procesu není podporována pro aplikace ASP.NET Core, které jsou cíleny rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="b28ff-129">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="b28ff-130">Následující diagram znázorňuje vztah mezi služby IIS, že modul ASP.NET Core a aplikace hostované v procesu:</span><span class="sxs-lookup"><span data-stu-id="b28ff-130">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Modul ASP.NET Core](_static/ancm-inprocess.png)

<span data-ttu-id="b28ff-132">Žádost o přijetí z webu pro ovladač HTTP.sys režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="b28ff-132">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="b28ff-133">Ovladač přesměruje požadavek na nativní služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="b28ff-133">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="b28ff-134">Modul obdrží požadavek na nativní a předává je na serveru služby IIS protokolu HTTP (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="b28ff-134">The module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="b28ff-135">HTTP serveru služby IIS je implementace v rámci procesu serveru pro službu IIS, který převede žádosti z nativní do spravované.</span><span class="sxs-lookup"><span data-stu-id="b28ff-135">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="b28ff-136">Po zpracování požadavku HTTP Server služby IIS požadavek odesílají do kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b28ff-136">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="b28ff-137">Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky.</span><span class="sxs-lookup"><span data-stu-id="b28ff-137">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="b28ff-138">Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta, který inicioval žádost.</span><span class="sxs-lookup"><span data-stu-id="b28ff-138">The app's response is passed back to IIS, which pushes it back out to the client that initiated the request.</span></span>

<span data-ttu-id="b28ff-139">Hostování v procesu je vyjádřit výslovný souhlas pro existující aplikace, ale [dotnet nové](/dotnet/core/tools/dotnet-new) šablony ve výchozím nastavení model hostingu v procesu pro všechny scénáře pro službu IIS a služby IIS Express.</span><span class="sxs-lookup"><span data-stu-id="b28ff-139">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="b28ff-140">Model hostingu mimo proces</span><span class="sxs-lookup"><span data-stu-id="b28ff-140">Out-of-process hosting model</span></span>

<span data-ttu-id="b28ff-141">Protože aplikace ASP.NET Core spuštění v procesu oddělit od pracovní proces služby IIS, zpracovává modul Správa procesů.</span><span class="sxs-lookup"><span data-stu-id="b28ff-141">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="b28ff-142">V modulu začne proces pro aplikace ASP.NET Core, když první žádost o přijetí a restartuje aplikaci, pokud se vypne nebo dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="b28ff-142">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="b28ff-143">Toto je v podstatě stejné chování jako aplikace, které běží v procesu, která jsou spravována [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="b28ff-143">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="b28ff-144">Následující diagram znázorňuje vztah mezi služby IIS, že modul ASP.NET Core a aplikace hostované na více instancí procesu:</span><span class="sxs-lookup"><span data-stu-id="b28ff-144">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Modul ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="b28ff-146">Požadavky přicházejí z webu pro ovladač HTTP.sys režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="b28ff-146">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="b28ff-147">Ovladač směruje požadavky do služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="b28ff-147">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="b28ff-148">V modulu předá požadavky Kestrel na náhodný port pro aplikaci, která není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="b28ff-148">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="b28ff-149">V modulu určuje port, přes proměnnou prostředí při spuštění a Middleware pro integraci služby IIS nakonfiguruje server tak, aby naslouchala na `http://localhost:{PORT}`.</span><span class="sxs-lookup"><span data-stu-id="b28ff-149">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="b28ff-150">Další kontroly jsou prováděny, a odmítne požadavky, které není pocházejí z modulu.</span><span class="sxs-lookup"><span data-stu-id="b28ff-150">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="b28ff-151">Modul nepodporuje předávání protokolu HTTPS, takže žádosti se předávají prostřednictvím protokolu HTTP i v případě, že byla přijata službou IIS přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b28ff-151">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="b28ff-152">Po Kestrel převezme žádosti z modulu, požadavek se vloží do kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b28ff-152">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="b28ff-153">Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky.</span><span class="sxs-lookup"><span data-stu-id="b28ff-153">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="b28ff-154">Middleware, které jsou přidány pomocí integrace služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase pro předání požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b28ff-154">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="b28ff-155">Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta HTTP, který inicioval žádost.</span><span class="sxs-lookup"><span data-stu-id="b28ff-155">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="b28ff-156">Pro službu IIS a ASP.NET Core modulu pokyny ke konfiguraci naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="b28ff-156">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[<span data-ttu-id="b28ff-157">macOS</span><span class="sxs-lookup"><span data-stu-id="b28ff-157">macOS</span></span>](#tab/macos)

<span data-ttu-id="b28ff-158">ASP.NET Core se dodává s [Kestrel server](xref:fundamentals/servers/kestrel), což je výchozí, server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="b28ff-158">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="b28ff-159">Linux</span><span class="sxs-lookup"><span data-stu-id="b28ff-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="b28ff-160">ASP.NET Core se dodává s [Kestrel server](xref:fundamentals/servers/kestrel), což je výchozí, server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="b28ff-160">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="b28ff-161">Windows</span><span class="sxs-lookup"><span data-stu-id="b28ff-161">Windows</span></span>](#tab/windows)

<span data-ttu-id="b28ff-162">ASP.NET Core se dodává s následujícími možnostmi:</span><span class="sxs-lookup"><span data-stu-id="b28ff-162">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="b28ff-163">[Kestrel server](xref:fundamentals/servers/kestrel) je výchozí, server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="b28ff-163">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="b28ff-164">[HTTP.sys server](xref:fundamentals/servers/httpsys) je na základě protokolu HTTP jenom pro Windows server [ovladač HTTP.sys jádra a rozhraní API serveru HTTP](/windows/desktop/Http/http-api-start-page).</span><span class="sxs-lookup"><span data-stu-id="b28ff-164">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="b28ff-165">Při použití [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), aplikace běží v procesu nezávisle na pracovní proces služby IIS (*mimo proces*) se [Kestrel server](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="b28ff-165">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="b28ff-166">Protože aplikace ASP.NET Core spuštění v procesu oddělit od pracovní proces služby IIS, zpracovává modul Správa procesů.</span><span class="sxs-lookup"><span data-stu-id="b28ff-166">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="b28ff-167">V modulu začne proces pro aplikace ASP.NET Core, když první žádost o přijetí a restartuje aplikaci, pokud se vypne nebo dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="b28ff-167">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="b28ff-168">Toto je v podstatě stejné chování jako aplikace, které běží v procesu, která jsou spravována [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="b28ff-168">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="b28ff-169">Následující diagram znázorňuje vztah mezi služby IIS, že modul ASP.NET Core a aplikace hostované na více instancí procesu:</span><span class="sxs-lookup"><span data-stu-id="b28ff-169">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Modul ASP.NET Core](_static/ancm-outofprocess.png)

<span data-ttu-id="b28ff-171">Požadavky přicházejí z webu pro ovladač HTTP.sys režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="b28ff-171">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="b28ff-172">Ovladač směruje požadavky do služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="b28ff-172">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="b28ff-173">V modulu předá požadavky Kestrel na náhodný port pro aplikaci, která není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="b28ff-173">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="b28ff-174">V modulu určuje port, přes proměnnou prostředí při spuštění a Middleware pro integraci služby IIS nakonfiguruje server tak, aby naslouchala na `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="b28ff-174">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="b28ff-175">Další kontroly jsou prováděny, a odmítne požadavky, které není pocházejí z modulu.</span><span class="sxs-lookup"><span data-stu-id="b28ff-175">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="b28ff-176">Modul nepodporuje předávání protokolu HTTPS, takže žádosti se předávají prostřednictvím protokolu HTTP i v případě, že byla přijata službou IIS přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b28ff-176">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="b28ff-177">Po Kestrel převezme žádosti z modulu, požadavek se vloží do kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b28ff-177">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="b28ff-178">Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky.</span><span class="sxs-lookup"><span data-stu-id="b28ff-178">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="b28ff-179">Middleware, které jsou přidány pomocí integrace služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase pro předání požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b28ff-179">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="b28ff-180">Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta HTTP, který inicioval žádost.</span><span class="sxs-lookup"><span data-stu-id="b28ff-180">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="b28ff-181">Pro službu IIS a ASP.NET Core modulu pokyny ke konfiguraci naleznete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="b28ff-181">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[<span data-ttu-id="b28ff-182">macOS</span><span class="sxs-lookup"><span data-stu-id="b28ff-182">macOS</span></span>](#tab/macos)

<span data-ttu-id="b28ff-183">ASP.NET Core se dodává s [Kestrel server](xref:fundamentals/servers/kestrel), což je výchozí, server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="b28ff-183">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="b28ff-184">Linux</span><span class="sxs-lookup"><span data-stu-id="b28ff-184">Linux</span></span>](#tab/linux)

<span data-ttu-id="b28ff-185">ASP.NET Core se dodává s [Kestrel server](xref:fundamentals/servers/kestrel), což je výchozí, server HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="b28ff-185">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

## <a name="kestrel"></a><span data-ttu-id="b28ff-186">Kestrel</span><span class="sxs-lookup"><span data-stu-id="b28ff-186">Kestrel</span></span>

<span data-ttu-id="b28ff-187">Kestrel je výchozí webový server, která je součástí šablony projektů ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b28ff-187">Kestrel is the default web server included in ASP.NET Core project templates.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b28ff-188">Dá se kestrel:</span><span class="sxs-lookup"><span data-stu-id="b28ff-188">Kestrel can be used:</span></span>

* <span data-ttu-id="b28ff-189">Samostatně jako hraniční server zpracování požadavků přímo ze sítě, včetně Internetu.</span><span class="sxs-lookup"><span data-stu-id="b28ff-189">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel komunikuje přímo s Internetu bez reverzní proxy server](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="b28ff-191">S *reverzní proxy server*, jako například [Internetové informační služby (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="b28ff-191">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="b28ff-192">Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b28ff-192">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel nepřímo komunikuje přes Internet prostřednictvím reverzního proxy serveru, jako je například Apache, IIS nebo Nginx](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="b28ff-194">Konfigurace buď hostování&mdash;s nebo bez něj reverzní proxy server&mdash;se podporuje pro ASP.NET Core 2.1 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="b28ff-194">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported for ASP.NET Core 2.1 or later apps.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="b28ff-195">Pokud aplikace přijímá jenom požadavky z interní sítě, je možné Kestrel samostatně.</span><span class="sxs-lookup"><span data-stu-id="b28ff-195">If the app only accepts requests from an internal network, Kestrel can be used by itself.</span></span>

![Kestrel komunikuje přímo s interní sítě](kestrel/_static/kestrel-to-internal.png)

<span data-ttu-id="b28ff-197">Pokud aplikace je přístupný z Internetu, musíte použít Kestrel *reverzní proxy server*, jako například [Internetové informační služby (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), nebo [Apache ](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="b28ff-197">If the app is exposed to the Internet, Kestrel must use a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](http://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="b28ff-198">Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b28ff-198">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

![Kestrel nepřímo komunikuje přes Internet prostřednictvím reverzního proxy serveru, jako je například Apache, IIS nebo Nginx](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="b28ff-200">Nejdůležitější důvod pomocí reverzního proxy serveru pro veřejnou hraniční server nasazení, které jsou vystaveny přímo k Internetu je zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="b28ff-200">The most important reason for using a reverse proxy for public-facing edge server deployments that are exposed directly the Internet is security.</span></span> <span data-ttu-id="b28ff-201">Verze 1.x Kestrel nezahrnují funkcí důležité zabezpečení, ochranu před útoky z Internetu.</span><span class="sxs-lookup"><span data-stu-id="b28ff-201">The 1.x versions of Kestrel don't include important security features to defend against attacks from the Internet.</span></span> <span data-ttu-id="b28ff-202">To zahrnuje, ale není omezený na, odpovídající časové limity, žádost o velikosti omezení a omezení počtu souběžných připojení.</span><span class="sxs-lookup"><span data-stu-id="b28ff-202">This includes, but isn't limited to, appropriate timeouts, request size limits, and concurrent connection limits.</span></span>

::: moniker-end

<span data-ttu-id="b28ff-203">Pokyny ke konfiguraci Kestrel a informace o použití Kestrel v konfigurace reverzního proxy serveru najdete v tématu <xref:fundamentals/servers/kestrel>.</span><span class="sxs-lookup"><span data-stu-id="b28ff-203">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

### <a name="nginx-with-kestrel"></a><span data-ttu-id="b28ff-204">Server Nginx s Kestrel</span><span class="sxs-lookup"><span data-stu-id="b28ff-204">Nginx with Kestrel</span></span>

<span data-ttu-id="b28ff-205">Informace o tom, jak použít Nginx jako reverzní proxy server pro Kestrel v Linuxu najdete v tématu <xref:host-and-deploy/linux-nginx>.</span><span class="sxs-lookup"><span data-stu-id="b28ff-205">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="b28ff-206">Apache s Kestrel</span><span class="sxs-lookup"><span data-stu-id="b28ff-206">Apache with Kestrel</span></span>

<span data-ttu-id="b28ff-207">Informace o tom, jak používat Apache na platformě Linux jako reverzní proxy server pro Kestrel najdete v tématu <xref:host-and-deploy/linux-apache>.</span><span class="sxs-lookup"><span data-stu-id="b28ff-207">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="iis-http-server"></a><span data-ttu-id="b28ff-208">IIS HTTP Server</span><span class="sxs-lookup"><span data-stu-id="b28ff-208">IIS HTTP Server</span></span>

<span data-ttu-id="b28ff-209">Server služby IIS protokolu HTTP [v procesový server](#in-process-hosting-model) pro službu IIS a požadované pro nasazení v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="b28ff-209">IIS HTTP Server is an [in-process server](#in-process-hosting-model) for IIS and required for in-process deployments.</span></span> <span data-ttu-id="b28ff-210">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) zpracovává nativní služby IIS požadavky mezi službou IIS a Server služby IIS protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b28ff-210">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) handles native IIS requests between IIS and IIS HTTP Server.</span></span> <span data-ttu-id="b28ff-211">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="b28ff-211">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="b28ff-212">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b28ff-212">HTTP.sys</span></span>

<span data-ttu-id="b28ff-213">Pokud aplikace ASP.NET Core běží na Windows, ovladač HTTP.sys se o alternativu k Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b28ff-213">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="b28ff-214">Kestrel obecně se doporučuje pro zajištění nejlepšího výkonu.</span><span class="sxs-lookup"><span data-stu-id="b28ff-214">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="b28ff-215">Ovladač HTTP.sys lze použít v situacích, kdy aplikace je přístupný z Internetu a požadované funkce jsou podporovány, ale ne Kestrel HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="b28ff-215">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="b28ff-216">Další informace naleznete v tématu <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="b28ff-216">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![Ovladač HTTP.sys komunikuje přímo s Internetem](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="b28ff-218">Ovladač HTTP.sys lze použít také pro aplikace, které jsou přístupné pouze k interní síti.</span><span class="sxs-lookup"><span data-stu-id="b28ff-218">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![Ovladač HTTP.sys komunikuje přímo s interní sítě](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="b28ff-220">Pokyny ke konfiguraci HTTP.sys, naleznete v tématu <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="b28ff-220">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="b28ff-221">ASP.NET Core server infrastruktury</span><span class="sxs-lookup"><span data-stu-id="b28ff-221">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="b28ff-222"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> k dispozici v `Startup.Configure` zpřístupňuje metodu <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> vlastnost typu <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span><span class="sxs-lookup"><span data-stu-id="b28ff-222">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="b28ff-223">Kestrel a HTTP.sys vystavují jenom jednu funkci, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, ale implementace jiný server může vystavit další funkce.</span><span class="sxs-lookup"><span data-stu-id="b28ff-223">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="b28ff-224">`IServerAddressesFeature` umožňuje zjistit, port, který obsahuje implementaci serveru vázán v době běhu.</span><span class="sxs-lookup"><span data-stu-id="b28ff-224">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="b28ff-225">Vlastní servery</span><span class="sxs-lookup"><span data-stu-id="b28ff-225">Custom servers</span></span>

<span data-ttu-id="b28ff-226">Pokud integrované servery nesplňují požadavky aplikace, implementace vlastního serveru vytvořit.</span><span class="sxs-lookup"><span data-stu-id="b28ff-226">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="b28ff-227">[Open Web Interface pro .NET (OWIN) průvodce](xref:fundamentals/owin) ukazuje, jak psát [Nowin](https://github.com/Bobris/Nowin)– na základě <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementace.</span><span class="sxs-lookup"><span data-stu-id="b28ff-227">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="b28ff-228">Pouze funkce rozhraní, které aplikace používá potřeba provádění, když minimálně <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> a <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> musí podporovat.</span><span class="sxs-lookup"><span data-stu-id="b28ff-228">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="b28ff-229">Při spuštění serveru</span><span class="sxs-lookup"><span data-stu-id="b28ff-229">Server startup</span></span>

<span data-ttu-id="b28ff-230">Server se spustí, když je integrované vývojové prostředí (IDE) nebo editor spustí aplikaci:</span><span class="sxs-lookup"><span data-stu-id="b28ff-230">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="b28ff-231">[Visual Studio](https://www.visualstudio.com/vs/) &ndash; profily spouštění můžete použít ke spuštění aplikace a serveru s oběma [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) nebo konzoly.</span><span class="sxs-lookup"><span data-stu-id="b28ff-231">[Visual Studio](https://www.visualstudio.com/vs/) &ndash; Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="b28ff-232">[Visual Studio Code](https://code.visualstudio.com/) &ndash; aplikace a serveru jsou spouštěny [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), která aktivuje CoreCLR ladicího programu.</span><span class="sxs-lookup"><span data-stu-id="b28ff-232">[Visual Studio Code](https://code.visualstudio.com/) &ndash; The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="b28ff-233">[Visual Studio pro Mac](https://www.visualstudio.com/vs/mac/) &ndash; aplikace a serveru jsou spouštěny [ladicí program Mono konfigurace Soft-režim](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span><span class="sxs-lookup"><span data-stu-id="b28ff-233">[Visual Studio for Mac](https://www.visualstudio.com/vs/mac/) &ndash; The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="b28ff-234">Při spuštění aplikace z příkazového řádku ve složce projektu [dotnet spustit](/dotnet/core/tools/dotnet-run) spustí aplikace a serveru (přes Kestrel a pouze HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="b28ff-234">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="b28ff-235">Konfigurace je určena `-c|--configuration` možnost, který je nastaven na hodnotu `Debug` (výchozí) nebo `Release`.</span><span class="sxs-lookup"><span data-stu-id="b28ff-235">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span> <span data-ttu-id="b28ff-236">Pokud jsou k dispozici v profily spouštění *launchSettings.json* souboru, použijte `--launch-profile <NAME>` možnost nastavit profil spuštění (například `Development` nebo `Production`).</span><span class="sxs-lookup"><span data-stu-id="b28ff-236">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile <NAME>` option to set the launch profile (for example, `Development` or `Production`).</span></span> <span data-ttu-id="b28ff-237">Další informace najdete v tématu [dotnet spustit](/dotnet/core/tools/dotnet-run) a [vytváření distribučních balíčků .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="b28ff-237">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="b28ff-238">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="b28ff-238">HTTP/2 support</span></span>

<span data-ttu-id="b28ff-239">[HTTP/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení:</span><span class="sxs-lookup"><span data-stu-id="b28ff-239">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="b28ff-240">Kestrel</span><span class="sxs-lookup"><span data-stu-id="b28ff-240">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="b28ff-241">Operační systém</span><span class="sxs-lookup"><span data-stu-id="b28ff-241">Operating system</span></span>
    * <span data-ttu-id="b28ff-242">Windows Server 2016 nebo Windows 10 nebo novější&dagger;</span><span class="sxs-lookup"><span data-stu-id="b28ff-242">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="b28ff-243">Linux s OpenSSL 1.0.2 nebo novější (například Ubuntu 16.04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="b28ff-243">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="b28ff-244">HTTP/2 budou podporované v systému macOS v budoucí verzi.</span><span class="sxs-lookup"><span data-stu-id="b28ff-244">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="b28ff-245">Cílová architektura: .NET Core 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="b28ff-245">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="b28ff-246">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b28ff-246">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="b28ff-247">Windows Server 2016 nebo Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="b28ff-247">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="b28ff-248">Cílová architektura: Není k dispozici pro nasazení souboru HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="b28ff-248">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="b28ff-249">Služba IIS (v procesu)</span><span class="sxs-lookup"><span data-stu-id="b28ff-249">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="b28ff-250">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="b28ff-250">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="b28ff-251">Cílová architektura: .NET Core 2.2 nebo vyšší</span><span class="sxs-lookup"><span data-stu-id="b28ff-251">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="b28ff-252">Služba IIS (out-of-process)</span><span class="sxs-lookup"><span data-stu-id="b28ff-252">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="b28ff-253">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="b28ff-253">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="b28ff-254">Připojení k serveru edge veřejně přístupných používat HTTP/2, ale připojení reverzního proxy serveru na Kestrel používá HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="b28ff-254">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="b28ff-255">Cílová architektura: Není k dispozici pro nasazení na více instancí procesu služby IIS.</span><span class="sxs-lookup"><span data-stu-id="b28ff-255">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="b28ff-256">&dagger;Kestrel má omezenou podporu pro HTTP/2 pro systém Windows Server 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="b28ff-256">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="b28ff-257">Podpora je omezená, protože seznam podporovaných šifer TLS sady k dispozici v těchto operačních systémech je omezen.</span><span class="sxs-lookup"><span data-stu-id="b28ff-257">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="b28ff-258">Certifikát vytvořený pomocí křivky digitální podpis algoritmů ECDSA (Elliptic) může být nutné zabezpečení připojení protokol TLS.</span><span class="sxs-lookup"><span data-stu-id="b28ff-258">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="b28ff-259">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b28ff-259">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="b28ff-260">Windows Server 2016 nebo Windows 10 nebo novější</span><span class="sxs-lookup"><span data-stu-id="b28ff-260">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="b28ff-261">Cílová architektura: Není k dispozici pro nasazení souboru HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="b28ff-261">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="b28ff-262">Služba IIS (out-of-process)</span><span class="sxs-lookup"><span data-stu-id="b28ff-262">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="b28ff-263">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="b28ff-263">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="b28ff-264">Připojení k serveru edge veřejně přístupných používat HTTP/2, ale připojení reverzního proxy serveru na Kestrel používá HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="b28ff-264">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="b28ff-265">Cílová architektura: Není k dispozici pro nasazení na více instancí procesu služby IIS.</span><span class="sxs-lookup"><span data-stu-id="b28ff-265">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="b28ff-266">Musíte použít připojení HTTP/2 [vyjednávání protokolu v aplikační vrstvě (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) a TLS 1.2 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="b28ff-266">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="b28ff-267">Další informace najdete v tématech, které se týkají scénářů nasazení serveru.</span><span class="sxs-lookup"><span data-stu-id="b28ff-267">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b28ff-268">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b28ff-268">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
