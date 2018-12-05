---
title: Modul ASP.NET Core
author: guardrex
description: Zjistěte, jak modul ASP.NET Core umožňuje Kestrel webový server pro použití služby IIS nebo IIS Express.
ms.author: tdykstra
ms.custom: mvc
ms.date: 11/30/2018
uid: fundamentals/servers/aspnet-core-module
ms.openlocfilehash: d3f3a42dd7aebc425905b865376a584bcf0e5153
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861456"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="f4c1c-103">Modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f4c1c-103">ASP.NET Core Module</span></span>

<span data-ttu-id="f4c1c-104">Podle [Petr Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), a [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="f4c1c-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f4c1c-105">Modul ASP.NET Core umožňuje aplikacím pro spuštění v pracovním procesu služby IIS, ASP.NET Core (*vnitroprocesové*) nebo za služby IIS v konfigurace reverzního proxy serveru (*mimo proces*).</span><span class="sxs-lookup"><span data-stu-id="f4c1c-105">The ASP.NET Core Module allows ASP.NET Core apps to run in an IIS worker process (*in-process*) or behind IIS in a reverse proxy configuration (*out-of-process*).</span></span> <span data-ttu-id="f4c1c-106">Služba IIS poskytuje funkce zabezpečení a správy rozšířené webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-106">IIS provides advanced web app security and manageability features.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f4c1c-107">Modul ASP.NET Core umožňuje spouštění za služby IIS v konfigurace reverzního proxy aplikací pro ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-107">The ASP.NET Core Module allows ASP.NET Core apps to run behind IIS in a reverse proxy configuration.</span></span> <span data-ttu-id="f4c1c-108">Služba IIS poskytuje funkce zabezpečení a správy rozšířené webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-108">IIS provides advanced web app security and manageability features.</span></span>

::: moniker-end

<span data-ttu-id="f4c1c-109">Podporované verze Windows:</span><span class="sxs-lookup"><span data-stu-id="f4c1c-109">Supported Windows versions:</span></span>

* <span data-ttu-id="f4c1c-110">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="f4c1c-110">Windows 7 or later</span></span>
* <span data-ttu-id="f4c1c-111">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="f4c1c-111">Windows Server 2008 R2 or later</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f4c1c-112">Při hostování v procesu, modul používá implementaci v procesový server služby IIS, HTTP serveru služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="f4c1c-112">When hosting in-process, the module uses an IIS in-process server implementation, IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f4c1c-113">Při hostování mimo proces, modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-113">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="f4c1c-114">Modul je nekompatibilní s [HTTP.sys](xref:fundamentals/servers/httpsys) (dříve se označovaly jako [WebListener](xref:fundamentals/servers/weblistener)).</span><span class="sxs-lookup"><span data-stu-id="f4c1c-114">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys) (formerly called [WebListener](xref:fundamentals/servers/weblistener)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f4c1c-115">Modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-115">The module only works with Kestrel.</span></span> <span data-ttu-id="f4c1c-116">Modul je nekompatibilní s [HTTP.sys](xref:fundamentals/servers/httpsys) (dříve se označovaly jako [WebListener](xref:fundamentals/servers/weblistener)).</span><span class="sxs-lookup"><span data-stu-id="f4c1c-116">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys) (formerly called [WebListener](xref:fundamentals/servers/weblistener)).</span></span>

::: moniker-end

## <a name="aspnet-core-module-description"></a><span data-ttu-id="f4c1c-117">Popis modulu jádra ASP.NET</span><span class="sxs-lookup"><span data-stu-id="f4c1c-117">ASP.NET Core Module description</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f4c1c-118">Modul ASP.NET Core je nativní modul služby IIS, která zpřístupní kanálu IIS buď:</span><span class="sxs-lookup"><span data-stu-id="f4c1c-118">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="f4c1c-119">Hostování aplikace v ASP.NET Core v rámci pracovní proces služby IIS (`w3wp.exe`), označované jako [model hostingu v procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="f4c1c-119">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>

* <span data-ttu-id="f4c1c-120">Předání požadavku na webové aplikace ASP.NET Core s back-end systémem [Kestrel server](xref:fundamentals/servers/kestrel), označované jako [model hostingu mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="f4c1c-120">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="f4c1c-121">Model hostingu v procesu</span><span class="sxs-lookup"><span data-stu-id="f4c1c-121">In-process hosting model</span></span>

<span data-ttu-id="f4c1c-122">Proces hostování, ASP.NET Core pomocí app běží ve stejném procesu jako jeho pracovní proces služby IIS.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-122">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="f4c1c-123">Tím snížení výkonu v důsledku žádosti o připojení přes server proxy prostřednictvím adaptéru zpětné smyčky, při použití model hostingu mimo proces.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-123">This removes the performance penalty of proxying requests over the loopback adapter when using the out-of-process hosting model.</span></span> <span data-ttu-id="f4c1c-124">Služba IIS zpracovává proces správy pomocí [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="f4c1c-124">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="f4c1c-125">Modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f4c1c-125">The ASP.NET Core Module:</span></span>

* <span data-ttu-id="f4c1c-126">Provede inicializaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-126">Performs app initialization.</span></span>
  * <span data-ttu-id="f4c1c-127">Načtení [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="f4c1c-127">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="f4c1c-128">Volání `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-128">Calls `Program.Main`.</span></span>
* <span data-ttu-id="f4c1c-129">Zpracovává životnost nativní požadavků služby IIS.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-129">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="f4c1c-130">Následující diagram znázorňuje vztah mezi služby IIS, že modul ASP.NET Core a aplikace hostované v procesu:</span><span class="sxs-lookup"><span data-stu-id="f4c1c-130">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-inprocess.png)

<span data-ttu-id="f4c1c-132">Žádost o přijetí z webu pro ovladač HTTP.sys režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-132">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="f4c1c-133">Ovladač přesměruje požadavek na nativní služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f4c1c-133">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="f4c1c-134">Modul obdrží požadavek na nativní a předává je na serveru služby IIS protokolu HTTP (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="f4c1c-134">The module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="f4c1c-135">Server služby IIS protokolu HTTP je implementace v rámci procesu serveru služby IIS, která převede žádosti z nativní do spravované.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-135">IIS HTTP Server is an IIS in-process server implementation that converts the request from native to managed.</span></span>

<span data-ttu-id="f4c1c-136">Po zpracování požadavku HTTP Server služby IIS požadavek odesílají do kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-136">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="f4c1c-137">Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-137">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="f4c1c-138">Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta, který inicioval žádost.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-138">The app's response is passed back to IIS, which pushes it back out to the client that initiated the request.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="f4c1c-139">Model hostingu mimo proces</span><span class="sxs-lookup"><span data-stu-id="f4c1c-139">Out-of-process hosting model</span></span>

<span data-ttu-id="f4c1c-140">Protože aplikace ASP.NET Core spuštění v procesu oddělit od pracovní proces služby IIS, zpracovává modul Správa procesů.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-140">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="f4c1c-141">V modulu začne proces pro aplikace ASP.NET Core, když první žádost o přijetí a restartuje aplikaci, pokud se vypne nebo dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-141">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="f4c1c-142">Toto je v podstatě stejné chování jako aplikace, které běží v procesu, která jsou spravována [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="f4c1c-142">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="f4c1c-143">Následující diagram znázorňuje vztah mezi služby IIS, že modul ASP.NET Core a aplikace hostované na více instancí procesu:</span><span class="sxs-lookup"><span data-stu-id="f4c1c-143">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="f4c1c-145">Požadavky přicházejí z webu pro ovladač HTTP.sys režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-145">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="f4c1c-146">Ovladač směruje požadavky do služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f4c1c-146">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="f4c1c-147">V modulu předává požadavky Kestrel na náhodný port pro aplikaci, která není port 80 a 443.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-147">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80/443.</span></span>

<span data-ttu-id="f4c1c-148">V modulu určuje port, přes proměnnou prostředí při spuštění a Middleware pro integraci služby IIS nakonfiguruje server tak, aby naslouchala na `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-148">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="f4c1c-149">Další kontroly jsou prováděny, a odmítne požadavky, které není pocházejí z modulu.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-149">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="f4c1c-150">Modul nepodporuje předávání protokolu HTTPS, takže žádosti se předávají prostřednictvím protokolu HTTP i v případě, že byla přijata službou IIS přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-150">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="f4c1c-151">Po Kestrel převezme žádosti z modulu, požadavek se vloží do kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-151">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="f4c1c-152">Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-152">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="f4c1c-153">Middleware, které jsou přidány pomocí integrace služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase pro předání požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-153">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="f4c1c-154">Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta HTTP, který inicioval žádost.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-154">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f4c1c-155">Modul ASP.NET Core je nativní modul služby IIS, která zpřístupní kanálu IIS, aby předával aplikace webové požadavky do back-endu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-155">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="f4c1c-156">Protože aplikace ASP.NET Core spuštění v procesu oddělit od pracovní proces služby IIS, zpracovává modul také procesu správy.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-156">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="f4c1c-157">V modulu začne proces pro aplikace ASP.NET Core, když první žádost o přijetí a restartuje aplikaci, pokud ho dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-157">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="f4c1c-158">Toto je v podstatě stejné chování jako aplikace ASP.NET 4.x, na kterých běží v procesu ve službě IIS, která jsou spravována [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="f4c1c-158">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="f4c1c-159">Následující diagram znázorňuje vztah mezi službou IIS, že modul ASP.NET Core a aplikace:</span><span class="sxs-lookup"><span data-stu-id="f4c1c-159">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="f4c1c-161">Požadavky přicházejí z webu pro ovladač HTTP.sys režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-161">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="f4c1c-162">Ovladač směruje požadavky do služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f4c1c-162">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="f4c1c-163">V modulu předává požadavky Kestrel na náhodný port pro aplikaci, která není port 80 a 443.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-163">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80/443.</span></span>

<span data-ttu-id="f4c1c-164">V modulu určuje port, přes proměnnou prostředí při spuštění a Middleware pro integraci služby IIS nakonfiguruje server tak, aby naslouchala na `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-164">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="f4c1c-165">Další kontroly jsou prováděny, a odmítne požadavky, které není pocházejí z modulu.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-165">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="f4c1c-166">Modul nepodporuje předávání protokolu HTTPS, takže žádosti se předávají prostřednictvím protokolu HTTP i v případě, že byla přijata službou IIS přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-166">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="f4c1c-167">Po Kestrel převezme žádosti z modulu, požadavek se vloží do kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-167">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="f4c1c-168">Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-168">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="f4c1c-169">Middleware, které jsou přidány pomocí integrace služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase pro předání požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-169">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="f4c1c-170">Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta HTTP, který inicioval žádost.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-170">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

<span data-ttu-id="f4c1c-171">Množství nativních modulů, jako je například ověřování Windows, zůstanou aktivní.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-171">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="f4c1c-172">Další informace o moduly IIS aktivní s modulem, naleznete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-172">To learn more about IIS modules active with the module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="f4c1c-173">Modul ASP.NET Core má několik dalších funkcí.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-173">The ASP.NET Core Module has a few other functions.</span></span> <span data-ttu-id="f4c1c-174">Modul můžete:</span><span class="sxs-lookup"><span data-stu-id="f4c1c-174">The module can:</span></span>

* <span data-ttu-id="f4c1c-175">Nastavení proměnných prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-175">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="f4c1c-176">Zaznamená stdout výstup do služby file storage pro řešení potíží při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-176">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="f4c1c-177">Předávání ověřovacích tokenů Windows.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-177">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="f4c1c-178">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f4c1c-178">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="f4c1c-179">Podrobné pokyny o tom, jak nainstalovat a používat modul ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-179">For detailed instructions on how to install and use the ASP.NET Core Module, see <xref:host-and-deploy/iis/index>.</span></span> <span data-ttu-id="f4c1c-180">Informace o konfiguraci modulu najdete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="f4c1c-180">For information on configuring the module, see the <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f4c1c-181">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f4c1c-181">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* [<span data-ttu-id="f4c1c-182">Úložiště GitHub modulu jádra ASP.NET (zdrojového kódu)</span><span class="sxs-lookup"><span data-stu-id="f4c1c-182">ASP.NET Core Module GitHub repository (source code)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
