---
title: Modul ASP.NET Core
author: guardrex
description: Zjistěte, jak nakonfigurovat modul ASP.NET Core pro hostování aplikací ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: dee4fe7a498d211cb8ef6a3c49017c3cc8a56847
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637856"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="986c3-103">Modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="986c3-103">ASP.NET Core Module</span></span>

<span data-ttu-id="986c3-104">Podle [Petr Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [ Justin Kotalik](https://github.com/jkotalik), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="986c3-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="986c3-105">Modul ASP.NET Core je nativní modul služby IIS, která zpřístupní kanálu IIS buď:</span><span class="sxs-lookup"><span data-stu-id="986c3-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="986c3-106">Hostování aplikace v ASP.NET Core v rámci pracovní proces služby IIS (`w3wp.exe`), označované jako [model hostingu v procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="986c3-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="986c3-107">Předání požadavku na webové aplikace ASP.NET Core s back-end systémem [Kestrel server](xref:fundamentals/servers/kestrel), označované jako [model hostingu mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="986c3-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="986c3-108">Podporované verze Windows:</span><span class="sxs-lookup"><span data-stu-id="986c3-108">Supported Windows versions:</span></span>

* <span data-ttu-id="986c3-109">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="986c3-109">Windows 7 or later</span></span>
* <span data-ttu-id="986c3-110">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="986c3-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="986c3-111">Při hostování v procesu, modul používá v procesu serveru implementace pro službu IIS, volá HTTP serveru služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="986c3-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="986c3-112">Při hostování mimo proces, modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="986c3-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="986c3-113">Modul je nekompatibilní s [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="986c3-113">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="986c3-114">Modelech hostování</span><span class="sxs-lookup"><span data-stu-id="986c3-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="986c3-115">Model hostingu v procesu</span><span class="sxs-lookup"><span data-stu-id="986c3-115">In-process hosting model</span></span>

<span data-ttu-id="986c3-116">Jak nakonfigurovat aplikaci pro hostování v procesu, přidejte `<AspNetCoreHostingModel>` vlastnosti do souboru projektu vaší aplikace s hodnotou `InProcess` (hostování mimo proces se nastaví pomocí `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="986c3-116">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="986c3-117">Pokud `<AspNetCoreHostingModel>` vlastnost není k dispozici v souboru, výchozí hodnota je `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="986c3-117">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="986c3-118">Při hostování v procesu platí následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="986c3-118">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="986c3-119">Server služby IIS protokolu HTTP (`IISHttpServer`) se použije namísto [Kestrel](xref:fundamentals/servers/kestrel) serveru.</span><span class="sxs-lookup"><span data-stu-id="986c3-119">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

* <span data-ttu-id="986c3-120">[RequestTimeout atribut](#attributes-of-the-aspnetcore-element) neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="986c3-120">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="986c3-121">Sdílení fondem aplikací mezi aplikacemi se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="986c3-121">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="986c3-122">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="986c3-122">Use one app pool per app.</span></span>

* <span data-ttu-id="986c3-123">Při použití [Webdeploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručně uvedení [soubor app_offline.htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files), aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="986c3-123">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="986c3-124">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="986c3-124">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="986c3-125">Architektura (bitové verze) (x64 nebo x86) nainstalovaný modul runtime a aplikace musí odpovídat architektuře fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="986c3-125">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="986c3-126">Pokud nastavení aplikace hostitele ručně pomocí `WebHostBuilder` (bez použití [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) a spuštění aplikace se nikdy přímo na serveru Kestrel (v místním prostředí), volání `UseKestrel` před voláním `UseIISIntegration`.</span><span class="sxs-lookup"><span data-stu-id="986c3-126">If setting up the app's host manually with `WebHostBuilder` (not using [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) and the app is ever run directly on the Kestrel server (self-hosted), call `UseKestrel` before calling `UseIISIntegration`.</span></span> <span data-ttu-id="986c3-127">Pokud je obrácený pořadí, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="986c3-127">If the order is reversed, the host fails to start.</span></span>

* <span data-ttu-id="986c3-128">Odpojení klienta jsou zjištěny.</span><span class="sxs-lookup"><span data-stu-id="986c3-128">Client disconnects are detected.</span></span> <span data-ttu-id="986c3-129">[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) token zrušení se zrušila, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="986c3-129">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="986c3-130"><xref:System.IO.Directory.GetCurrentDirectory*> Vrátí adresáře pracovního procesu tím, že služba IIS spíše než adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="986c3-130"><xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="986c3-131">Ukázkový kód, který nastaví aktuální adresář aplikace, najdete v článku [CurrentDirectoryHelpers třídy](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="986c3-131">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="986c3-132">Volání `SetCurrentDirectory` metody.</span><span class="sxs-lookup"><span data-stu-id="986c3-132">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="986c3-133">Následující volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="986c3-133">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="986c3-134">Model hostingu mimo proces</span><span class="sxs-lookup"><span data-stu-id="986c3-134">Out-of-process hosting model</span></span>

<span data-ttu-id="986c3-135">Jak nakonfigurovat aplikaci pro hostování mimo proces, použijte jednu z následujících postupů v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="986c3-135">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="986c3-136">Nezadávejte `<AspNetCoreHostingModel>` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="986c3-136">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="986c3-137">Pokud `<AspNetCoreHostingModel>` vlastnost není k dispozici v souboru, výchozí hodnota je `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="986c3-137">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="986c3-138">Nastavte hodnotu `<AspNetCoreHostingModel>` vlastnost `OutOfProcess` (hostování v procesu je nastavená pomocí `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="986c3-138">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="986c3-139">[Kestrel](xref:fundamentals/servers/kestrel) server se používá místo protokolu HTTP serveru služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="986c3-139">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="986c3-140">Hostování změny modelu</span><span class="sxs-lookup"><span data-stu-id="986c3-140">Hosting model changes</span></span>

<span data-ttu-id="986c3-141">Pokud `hostingModel` nastavení se změnilo v *web.config* souboru (podrobně [konfigurace pomocí souboru web.config](#configuration-with-webconfig) části), modul pro službu IIS recykluje pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="986c3-141">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="986c3-142">Pro službu IIS Express modul nebude recyklovat pracovní proces, ale místo toho aktivuje řádné vypnutí aktuální proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="986c3-142">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="986c3-143">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="986c3-143">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="986c3-144">Název procesu</span><span class="sxs-lookup"><span data-stu-id="986c3-144">Process name</span></span>

<span data-ttu-id="986c3-145">`Process.GetCurrentProcess().ProcessName` sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (out-of-process).</span><span class="sxs-lookup"><span data-stu-id="986c3-145">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="986c3-146">Modul ASP.NET Core je nativní modul služby IIS, která zpřístupní kanálu IIS, aby předával aplikace webové požadavky do back-endu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="986c3-146">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="986c3-147">Podporované verze Windows:</span><span class="sxs-lookup"><span data-stu-id="986c3-147">Supported Windows versions:</span></span>

* <span data-ttu-id="986c3-148">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="986c3-148">Windows 7 or later</span></span>
* <span data-ttu-id="986c3-149">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="986c3-149">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="986c3-150">Modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="986c3-150">The module only works with Kestrel.</span></span> <span data-ttu-id="986c3-151">Modul je nekompatibilní s [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="986c3-151">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="986c3-152">Protože aplikace ASP.NET Core spuštění v procesu oddělit od pracovní proces služby IIS, zpracovává modul také procesu správy.</span><span class="sxs-lookup"><span data-stu-id="986c3-152">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="986c3-153">V modulu začne proces pro aplikace ASP.NET Core, když první žádost o přijetí a restartuje aplikaci, pokud ho dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="986c3-153">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="986c3-154">Toto je v podstatě stejné chování jako aplikace ASP.NET 4.x, na kterých běží v procesu ve službě IIS, která jsou spravována [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="986c3-154">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="986c3-155">Následující diagram znázorňuje vztah mezi službou IIS, že modul ASP.NET Core a aplikace:</span><span class="sxs-lookup"><span data-stu-id="986c3-155">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="986c3-157">Požadavky přicházejí z webu pro ovladač HTTP.sys režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="986c3-157">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="986c3-158">Ovladač směruje požadavky do služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="986c3-158">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="986c3-159">V modulu předá požadavky Kestrel na náhodný port pro aplikaci, která není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="986c3-159">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="986c3-160">V modulu určuje port, přes proměnnou prostředí při spuštění a Middleware pro integraci služby IIS nakonfiguruje server tak, aby naslouchala na `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="986c3-160">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="986c3-161">Další kontroly jsou prováděny, a odmítne požadavky, které není pocházejí z modulu.</span><span class="sxs-lookup"><span data-stu-id="986c3-161">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="986c3-162">Modul nepodporuje předávání protokolu HTTPS, takže žádosti se předávají prostřednictvím protokolu HTTP i v případě, že byla přijata službou IIS přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="986c3-162">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="986c3-163">Po Kestrel převezme žádosti z modulu, požadavek se vloží do kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="986c3-163">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="986c3-164">Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky.</span><span class="sxs-lookup"><span data-stu-id="986c3-164">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="986c3-165">Middleware, které jsou přidány pomocí integrace služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase pro předání požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="986c3-165">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="986c3-166">Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta HTTP, který inicioval žádost.</span><span class="sxs-lookup"><span data-stu-id="986c3-166">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

<span data-ttu-id="986c3-167">Množství nativních modulů, jako je například ověřování Windows, zůstanou aktivní.</span><span class="sxs-lookup"><span data-stu-id="986c3-167">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="986c3-168">Další informace o moduly IIS aktivní s modul ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="986c3-168">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="986c3-169">Modul ASP.NET Core můžete:</span><span class="sxs-lookup"><span data-stu-id="986c3-169">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="986c3-170">Nastavení proměnných prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="986c3-170">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="986c3-171">Zaznamená stdout výstup do služby file storage pro řešení potíží při spuštění.</span><span class="sxs-lookup"><span data-stu-id="986c3-171">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="986c3-172">Předávání ověřovacích tokenů Windows.</span><span class="sxs-lookup"><span data-stu-id="986c3-172">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="986c3-173">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="986c3-173">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="986c3-174">Pokyny o tom, jak nainstalovat a používat modul ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="986c3-174">For instructions on how to install and use the ASP.NET Core Module, see <xref:host-and-deploy/iis/index>.</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="986c3-175">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="986c3-175">Configuration with web.config</span></span>

<span data-ttu-id="986c3-176">Modul ASP.NET Core, nastavena `aspNetCore` část `system.webServer` uzlu na webu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="986c3-176">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="986c3-177">Následující *web.config* soubor je publikován pro [nasazení závisí na architektuře](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="986c3-177">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet" 
                  arguments=".\MyApp.dll" 
                  stdoutLogEnabled="false" 
                  stdoutLogFile=".\logs\stdout" 
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet" 
                arguments=".\MyApp.dll" 
                stdoutLogEnabled="false" 
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

<span data-ttu-id="986c3-178">Následující *web.config* je publikována pro [samostatná nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="986c3-178">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe" 
                  stdoutLogEnabled="false" 
                  stdoutLogFile=".\logs\stdout" 
                  hostingModel="InProcess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="986c3-179"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> Je nastavena na `false` k označení, že nastavení zadané v rámci [ \<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nedědí od neznámých aplikací, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="986c3-179">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe" 
                stdoutLogEnabled="false" 
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

<span data-ttu-id="986c3-180">Když je aplikace nasazená na [služby Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` je nastavena cesta `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="986c3-180">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="986c3-181">Protokoly stdout a uloží ji *LogFiles* složce, která je na místě automaticky vytvořené službou.</span><span class="sxs-lookup"><span data-stu-id="986c3-181">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="986c3-182">Informace o konfiguraci dílčí aplikace služby IIS, naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="986c3-182">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="986c3-183">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="986c3-183">Attributes of the aspNetCore element</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="986c3-184">Atribut</span><span class="sxs-lookup"><span data-stu-id="986c3-184">Attribute</span></span> | <span data-ttu-id="986c3-185">Popis</span><span class="sxs-lookup"><span data-stu-id="986c3-185">Description</span></span> | <span data-ttu-id="986c3-186">Výchozí</span><span class="sxs-lookup"><span data-stu-id="986c3-186">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="986c3-187">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="986c3-187">Optional string attribute.</span></span></p><p><span data-ttu-id="986c3-188">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="986c3-188">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="986c3-189">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-189">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="986c3-190">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="986c3-190">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="986c3-191">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-191">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="986c3-192">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="986c3-192">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="986c3-193">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="986c3-193">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="986c3-194">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="986c3-194">Optional string attribute.</span></span></p><p><span data-ttu-id="986c3-195">Určuje model hostování jako v procesu (`InProcess`) nebo out-of-process (`OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="986c3-195">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="986c3-196">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-196">Optional integer attribute.</span></span></p><p><span data-ttu-id="986c3-197">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="986c3-197">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="986c3-198">&dagger;Hostování v procesu, je omezena na hodnotu `1`.</span><span class="sxs-lookup"><span data-stu-id="986c3-198">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p> | <span data-ttu-id="986c3-199">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="986c3-199">Default: `1`</span></span><br><span data-ttu-id="986c3-200">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="986c3-200">Min: `1`</span></span><br><span data-ttu-id="986c3-201">Max: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="986c3-201">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="986c3-202">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="986c3-202">Required string attribute.</span></span></p><p><span data-ttu-id="986c3-203">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="986c3-203">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="986c3-204">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="986c3-204">Relative paths are supported.</span></span> <span data-ttu-id="986c3-205">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="986c3-205">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="986c3-206">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-206">Optional integer attribute.</span></span></p><p><span data-ttu-id="986c3-207">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="986c3-207">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="986c3-208">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="986c3-208">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="986c3-209">Není podporováno s hostitelem v procesu.</span><span class="sxs-lookup"><span data-stu-id="986c3-209">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="986c3-210">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="986c3-210">Default: `10`</span></span><br><span data-ttu-id="986c3-211">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="986c3-211">Min: `0`</span></span><br><span data-ttu-id="986c3-212">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="986c3-212">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="986c3-213">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="986c3-213">Optional timespan attribute.</span></span></p><p><span data-ttu-id="986c3-214">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="986c3-214">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="986c3-215">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="986c3-215">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="986c3-216">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="986c3-216">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="986c3-217">Hostování v procesu, čeká modul app ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="986c3-217">For in-process hosting, the module waits for the app to process the request.</span></span></p> | <span data-ttu-id="986c3-218">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="986c3-218">Default: `00:02:00`</span></span><br><span data-ttu-id="986c3-219">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="986c3-219">Min: `00:00:00`</span></span><br><span data-ttu-id="986c3-220">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="986c3-220">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="986c3-221">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-221">Optional integer attribute.</span></span></p><p><span data-ttu-id="986c3-222">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="986c3-222">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="986c3-223">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="986c3-223">Default: `10`</span></span><br><span data-ttu-id="986c3-224">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="986c3-224">Min: `0`</span></span><br><span data-ttu-id="986c3-225">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="986c3-225">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="986c3-226">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-226">Optional integer attribute.</span></span></p><p><span data-ttu-id="986c3-227">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="986c3-227">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="986c3-228">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="986c3-228">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="986c3-229">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="986c3-229">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="986c3-230">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="986c3-230">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="986c3-231">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="986c3-231">Default: `120`</span></span><br><span data-ttu-id="986c3-232">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="986c3-232">Min: `0`</span></span><br><span data-ttu-id="986c3-233">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="986c3-233">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="986c3-234">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-234">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="986c3-235">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="986c3-235">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="986c3-236">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="986c3-236">Optional string attribute.</span></span></p><p><span data-ttu-id="986c3-237">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="986c3-237">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="986c3-238">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="986c3-238">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="986c3-239">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="986c3-239">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="986c3-240">Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="986c3-240">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="986c3-241">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru (*.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="986c3-241">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="986c3-242">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="986c3-242">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="986c3-243">Atribut</span><span class="sxs-lookup"><span data-stu-id="986c3-243">Attribute</span></span> | <span data-ttu-id="986c3-244">Popis</span><span class="sxs-lookup"><span data-stu-id="986c3-244">Description</span></span> | <span data-ttu-id="986c3-245">Výchozí</span><span class="sxs-lookup"><span data-stu-id="986c3-245">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="986c3-246">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="986c3-246">Optional string attribute.</span></span></p><p><span data-ttu-id="986c3-247">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="986c3-247">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="986c3-248">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-248">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="986c3-249">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="986c3-249">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="986c3-250">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-250">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="986c3-251">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="986c3-251">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="986c3-252">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="986c3-252">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="986c3-253">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-253">Optional integer attribute.</span></span></p><p><span data-ttu-id="986c3-254">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="986c3-254">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p> | <span data-ttu-id="986c3-255">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="986c3-255">Default: `1`</span></span><br><span data-ttu-id="986c3-256">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="986c3-256">Min: `1`</span></span><br><span data-ttu-id="986c3-257">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="986c3-257">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="986c3-258">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="986c3-258">Required string attribute.</span></span></p><p><span data-ttu-id="986c3-259">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="986c3-259">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="986c3-260">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="986c3-260">Relative paths are supported.</span></span> <span data-ttu-id="986c3-261">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="986c3-261">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="986c3-262">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-262">Optional integer attribute.</span></span></p><p><span data-ttu-id="986c3-263">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="986c3-263">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="986c3-264">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="986c3-264">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="986c3-265">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="986c3-265">Default: `10`</span></span><br><span data-ttu-id="986c3-266">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="986c3-266">Min: `0`</span></span><br><span data-ttu-id="986c3-267">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="986c3-267">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="986c3-268">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="986c3-268">Optional timespan attribute.</span></span></p><p><span data-ttu-id="986c3-269">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="986c3-269">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="986c3-270">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="986c3-270">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="986c3-271">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="986c3-271">Default: `00:02:00`</span></span><br><span data-ttu-id="986c3-272">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="986c3-272">Min: `00:00:00`</span></span><br><span data-ttu-id="986c3-273">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="986c3-273">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="986c3-274">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-274">Optional integer attribute.</span></span></p><p><span data-ttu-id="986c3-275">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="986c3-275">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="986c3-276">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="986c3-276">Default: `10`</span></span><br><span data-ttu-id="986c3-277">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="986c3-277">Min: `0`</span></span><br><span data-ttu-id="986c3-278">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="986c3-278">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="986c3-279">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-279">Optional integer attribute.</span></span></p><p><span data-ttu-id="986c3-280">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="986c3-280">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="986c3-281">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="986c3-281">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="986c3-282">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="986c3-282">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="986c3-283">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="986c3-283">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="986c3-284">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="986c3-284">Default: `120`</span></span><br><span data-ttu-id="986c3-285">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="986c3-285">Min: `0`</span></span><br><span data-ttu-id="986c3-286">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="986c3-286">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="986c3-287">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-287">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="986c3-288">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="986c3-288">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="986c3-289">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="986c3-289">Optional string attribute.</span></span></p><p><span data-ttu-id="986c3-290">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="986c3-290">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="986c3-291">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="986c3-291">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="986c3-292">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="986c3-292">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="986c3-293">Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="986c3-293">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="986c3-294">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru (*.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="986c3-294">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="986c3-295">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="986c3-295">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| <span data-ttu-id="986c3-296">Atribut</span><span class="sxs-lookup"><span data-stu-id="986c3-296">Attribute</span></span> | <span data-ttu-id="986c3-297">Popis</span><span class="sxs-lookup"><span data-stu-id="986c3-297">Description</span></span> | <span data-ttu-id="986c3-298">Výchozí</span><span class="sxs-lookup"><span data-stu-id="986c3-298">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="986c3-299">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="986c3-299">Optional string attribute.</span></span></p><p><span data-ttu-id="986c3-300">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="986c3-300">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="986c3-301">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-301">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="986c3-302">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="986c3-302">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="986c3-303">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-303">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="986c3-304">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="986c3-304">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="986c3-305">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="986c3-305">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="986c3-306">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-306">Optional integer attribute.</span></span></p><p><span data-ttu-id="986c3-307">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="986c3-307">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p> | <span data-ttu-id="986c3-308">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="986c3-308">Default: `1`</span></span><br><span data-ttu-id="986c3-309">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="986c3-309">Min: `1`</span></span><br><span data-ttu-id="986c3-310">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="986c3-310">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="986c3-311">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="986c3-311">Required string attribute.</span></span></p><p><span data-ttu-id="986c3-312">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="986c3-312">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="986c3-313">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="986c3-313">Relative paths are supported.</span></span> <span data-ttu-id="986c3-314">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="986c3-314">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="986c3-315">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-315">Optional integer attribute.</span></span></p><p><span data-ttu-id="986c3-316">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="986c3-316">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="986c3-317">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="986c3-317">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="986c3-318">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="986c3-318">Default: `10`</span></span><br><span data-ttu-id="986c3-319">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="986c3-319">Min: `0`</span></span><br><span data-ttu-id="986c3-320">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="986c3-320">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="986c3-321">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="986c3-321">Optional timespan attribute.</span></span></p><p><span data-ttu-id="986c3-322">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="986c3-322">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="986c3-323">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.0 nebo starší `requestTimeout` musí být zadán v celých minutách, v opačném případě výchozí hodnota 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="986c3-323">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.0 or earlier, the `requestTimeout` must be specified in whole minutes only, otherwise it defaults to 2 minutes.</span></span></p> | <span data-ttu-id="986c3-324">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="986c3-324">Default: `00:02:00`</span></span><br><span data-ttu-id="986c3-325">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="986c3-325">Min: `00:00:00`</span></span><br><span data-ttu-id="986c3-326">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="986c3-326">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="986c3-327">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-327">Optional integer attribute.</span></span></p><p><span data-ttu-id="986c3-328">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="986c3-328">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="986c3-329">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="986c3-329">Default: `10`</span></span><br><span data-ttu-id="986c3-330">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="986c3-330">Min: `0`</span></span><br><span data-ttu-id="986c3-331">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="986c3-331">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="986c3-332">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-332">Optional integer attribute.</span></span></p><p><span data-ttu-id="986c3-333">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="986c3-333">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="986c3-334">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="986c3-334">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="986c3-335">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="986c3-335">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p> | <span data-ttu-id="986c3-336">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="986c3-336">Default: `120`</span></span><br><span data-ttu-id="986c3-337">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="986c3-337">Min: `0`</span></span><br><span data-ttu-id="986c3-338">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="986c3-338">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="986c3-339">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-339">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="986c3-340">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="986c3-340">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="986c3-341">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="986c3-341">Optional string attribute.</span></span></p><p><span data-ttu-id="986c3-342">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="986c3-342">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="986c3-343">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="986c3-343">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="986c3-344">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="986c3-344">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="986c3-345">Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="986c3-345">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="986c3-346">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru (*.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="986c3-346">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="986c3-347">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="986c3-347">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a><span data-ttu-id="986c3-348">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="986c3-348">Setting environment variables</span></span>

<span data-ttu-id="986c3-349">Proměnné prostředí se dá nastavit pro proces v `processPath` atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-349">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="986c3-350">Zadat proměnné prostředí s `environmentVariable` podřízený prvek `environmentVariables` prvek kolekce.</span><span class="sxs-lookup"><span data-stu-id="986c3-350">Specify an environment variable with the `environmentVariable` child element of an `environmentVariables` collection element.</span></span> <span data-ttu-id="986c3-351">Proměnné prostředí nastavené v této části přednost systémové proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="986c3-351">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="986c3-352">Následující příklad nastaví dvou proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="986c3-352">The following example sets two environment variables.</span></span> <span data-ttu-id="986c3-353">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby `Development`.</span><span class="sxs-lookup"><span data-stu-id="986c3-353">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="986c3-354">Vývojáři mohou dočasně nastaví tuto hodnotu *web.config* souboru, aby bylo možné vynutit [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) načíst při ladění aplikace výjimky.</span><span class="sxs-lookup"><span data-stu-id="986c3-354">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="986c3-355">`CONFIG_DIR` je příkladem proměnné prostředí, kam má vývojář zapisovat kód, který čte hodnoty při spuštění tvoří cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="986c3-355">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

> [!WARNING]
> <span data-ttu-id="986c3-356">Nastavit pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí, aby `Development` na přípravy a testování serverů, které nejsou dostupné k nedůvěryhodným sítím, jako je Internet.</span><span class="sxs-lookup"><span data-stu-id="986c3-356">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="appofflinehtm"></a><span data-ttu-id="986c3-357">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="986c3-357">app_offline.htm</span></span>

<span data-ttu-id="986c3-358">Pokud soubor s názvem *app_offline.htm* je zjištěna v kořenovém adresáři aplikace, že modul ASP.NET Core se pokusí řádné vypnutí aplikace a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="986c3-358">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="986c3-359">Pokud aplikace pořád běží po dobu v sekundách podle `shutdownTimeLimit`, že modul ASP.NET Core ukončuje spuštěnému procesu.</span><span class="sxs-lookup"><span data-stu-id="986c3-359">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="986c3-360">Zatímco *app_offline.htm* soubor je k dispozici, že modul ASP.NET Core reaguje na požadavky odesílá zpět obsah *app_offline.htm* souboru.</span><span class="sxs-lookup"><span data-stu-id="986c3-360">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="986c3-361">Když *app_offline.htm* soubor bude odstraněn, příští žádosti o spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="986c3-361">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="986c3-362">Pokud používáte model hostingu mimo proces, aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="986c3-362">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="986c3-363">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="986c3-363">For example, a websocket connection may delay app shut down.</span></span>

::: moniker-end

## <a name="start-up-error-page"></a><span data-ttu-id="986c3-364">Spuštění chybovou stránku</span><span class="sxs-lookup"><span data-stu-id="986c3-364">Start-up error page</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="986c3-365">Hostování v procesu a mimo proces vytvoření vlastní chybové stránky, když selžou a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="986c3-365">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="986c3-366">Pokud se nepodaří najít buď nebo na více instancí procesu žádosti o obslužnou rutinu, že modul ASP.NET Core *500.0 – Chyba načtení obslužné rutiny v procesu/Out – proces* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="986c3-366">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="986c3-367">Pro hostování v procesu, pokud se nepodaří spustit aplikaci, že modul ASP.NET Core *500.30 - Start selhání* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="986c3-367">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="986c3-368">Pro hostování mimo proces, pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="986c3-368">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="986c3-369">Chcete-li potlačit tuto stránku a vrátit se na výchozí služby IIS 5xx stav znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-369">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="986c3-370">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="986c3-370">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="986c3-371">Pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="986c3-371">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="986c3-372">Chcete-li potlačit tuto stránku a vrátit se na výchozí stav služby IIS 502 znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="986c3-372">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="986c3-373">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="986c3-373">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 proces selhání stav znaková stránka](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a><span data-ttu-id="986c3-375">Vytvoření protokolu a přesměrování</span><span class="sxs-lookup"><span data-stu-id="986c3-375">Log creation and redirection</span></span>

<span data-ttu-id="986c3-376">Modul ASP.NET Core přesměruje výstup stdout a stderr console na disk, pokud `stdoutLogEnabled` a `stdoutLogFile` atributy `aspNetCore` element jsou nastavené.</span><span class="sxs-lookup"><span data-stu-id="986c3-376">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="986c3-377">Všechny složky v `stdoutLogFile` cesta musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="986c3-377">Any folders in the `stdoutLogFile` path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="986c3-378">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="986c3-378">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="986c3-379">Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování.</span><span class="sxs-lookup"><span data-stu-id="986c3-379">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="986c3-380">Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.</span><span class="sxs-lookup"><span data-stu-id="986c3-380">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="986c3-381">Použití protokolu stdout se doporučuje jenom pro řešení potíží při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="986c3-381">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="986c3-382">Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace.</span><span class="sxs-lookup"><span data-stu-id="986c3-382">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="986c3-383">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="986c3-383">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="986c3-384">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="986c3-384">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="986c3-385">Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="986c3-385">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="986c3-386">Název souboru protokolu se skládá připojením časové razítko, ID procesu a přípona souboru (*.log*) na poslední segment `stdoutLogFile` cestu (obvykle *stdout*) oddělené podtržítka.</span><span class="sxs-lookup"><span data-stu-id="986c3-386">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="986c3-387">Pokud `stdoutLogFile` cesta končí *stdout*, má název souboru protokolu pro aplikaci s PID 1934 vytvořili 5 2. 2018 v 19:42:32 *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="986c3-387">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="986c3-388">Pokud `stdoutLogEnabled` má hodnotu false, chyby, ke kterým dochází při spuštění aplikace se zachytí a do protokolu událostí, protože ho až 30 KB.</span><span class="sxs-lookup"><span data-stu-id="986c3-388">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="986c3-389">Po spuštění se zahodí všechny další protokoly.</span><span class="sxs-lookup"><span data-stu-id="986c3-389">After startup, all additional logs are discarded.</span></span>

::: moniker-end

<span data-ttu-id="986c3-390">Následující ukázka `aspNetCore` element konfiguruje stdout protokolování pro aplikace hostované ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="986c3-390">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="986c3-391">Místní cesta nebo cesta ke sdílené položce je přijatelné pro místní přihlášení.</span><span class="sxs-lookup"><span data-stu-id="986c3-391">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="986c3-392">Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.</span><span class="sxs-lookup"><span data-stu-id="986c3-392">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile="\\?\%home%\LogFiles\stdout">
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="986c3-393">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="986c3-393">Enhanced diagnostic logs</span></span>

<span data-ttu-id="986c3-394">Poskytuje modul ASP.NET Core je konfigurovat, a poskytují rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="986c3-394">The ASP.NET Core Module provides is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="986c3-395">Přidat `<handlerSettings>` elementu `<aspNetCore>` prvek *web.config*. Nastavení `debugLevel` k `TRACE` zpřístupňuje větší věrnost diagnostické informace:</span><span class="sxs-lookup"><span data-stu-id="986c3-395">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value="aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="986c3-396">Ladit úrovně (`debugLevel`) hodnoty může obsahovat úroveň a umístění.</span><span class="sxs-lookup"><span data-stu-id="986c3-396">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="986c3-397">Úrovně (v pořadí od nejméně na nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="986c3-397">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="986c3-398">CHYBA</span><span class="sxs-lookup"><span data-stu-id="986c3-398">ERROR</span></span>
* <span data-ttu-id="986c3-399">UPOZORNĚNÍ</span><span class="sxs-lookup"><span data-stu-id="986c3-399">WARNING</span></span>
* <span data-ttu-id="986c3-400">INFORMACE O</span><span class="sxs-lookup"><span data-stu-id="986c3-400">INFO</span></span>
* <span data-ttu-id="986c3-401">TRACE</span><span class="sxs-lookup"><span data-stu-id="986c3-401">TRACE</span></span>

<span data-ttu-id="986c3-402">Umístění (umístění více jsou povoleny):</span><span class="sxs-lookup"><span data-stu-id="986c3-402">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="986c3-403">KONZOLY</span><span class="sxs-lookup"><span data-stu-id="986c3-403">CONSOLE</span></span>
* <span data-ttu-id="986c3-404">PROTOKOL UDÁLOSTÍ</span><span class="sxs-lookup"><span data-stu-id="986c3-404">EVENTLOG</span></span>
* <span data-ttu-id="986c3-405">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="986c3-405">FILE</span></span>

<span data-ttu-id="986c3-406">Obslužná rutina nastavení se dá zadat i prostřednictvím proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="986c3-406">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="986c3-407">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="986c3-407">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="986c3-408">(Výchozí: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="986c3-408">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="986c3-409">`ASPNETCORE_MODULE_DEBUG` &ndash; Ladit nastavení úrovně.</span><span class="sxs-lookup"><span data-stu-id="986c3-409">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="986c3-410">Proveďte **není** protokolování ladění povoleno v nasazení pro déle, než se požaduje chcete vyřešit nějaký problém.</span><span class="sxs-lookup"><span data-stu-id="986c3-410">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="986c3-411">Velikost souboru protokolu není omezený.</span><span class="sxs-lookup"><span data-stu-id="986c3-411">The size of the log isn't limited.</span></span> <span data-ttu-id="986c3-412">Opuštění protokol ladění povoleno může vyčerpat dostupné místo na disku a chybách u aplikací na serveru nebo služby app service.</span><span class="sxs-lookup"><span data-stu-id="986c3-412">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

::: moniker-end

<span data-ttu-id="986c3-413">Naleznete v tématu [konfigurace pomocí souboru web.config](#configuration-with-webconfig) příklad `aspNetCore` prvek *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="986c3-413">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="986c3-414">Konfigurace proxy serveru používá protokol HTTP a token pro párování</span><span class="sxs-lookup"><span data-stu-id="986c3-414">Proxy configuration uses HTTP protocol and a pairing token</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="986c3-415">*Platí jenom pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="986c3-415">*Only applies to out-of-process hosting.*</span></span>

::: moniker-end

<span data-ttu-id="986c3-416">Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="986c3-416">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="986c3-417">Pomocí protokolu HTTP je optimalizace výkonu, kdy přenos dat mezi modulu a Kestrel probíhá na adresu zpětné smyčky ze síťového rozhraní.</span><span class="sxs-lookup"><span data-stu-id="986c3-417">Using HTTP is a performance optimization, where the traffic between the module and Kestrel takes place on a loopback address off of the network interface.</span></span> <span data-ttu-id="986c3-418">Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="986c3-418">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="986c3-419">Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="986c3-419">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="986c3-420">Vytvoření a nastavení do proměnné prostředí párování token (`ASPNETCORE_TOKEN`) modulu.</span><span class="sxs-lookup"><span data-stu-id="986c3-420">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="986c3-421">Párování token byl nastavený i do záhlaví (`MS-ASPNETCORE-TOKEN`) na všechny požadavky směrovány přes proxy server.</span><span class="sxs-lookup"><span data-stu-id="986c3-421">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="986c3-422">Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="986c3-422">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="986c3-423">Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut.</span><span class="sxs-lookup"><span data-stu-id="986c3-423">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="986c3-424">Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="986c3-424">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="986c3-425">Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.</span><span class="sxs-lookup"><span data-stu-id="986c3-425">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="986c3-426">Modul ASP.NET Core s službu IIS sdílenou konfiguraci</span><span class="sxs-lookup"><span data-stu-id="986c3-426">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="986c3-427">Modul ASP.NET Core instalační program spustí s oprávněními **systému** účtu.</span><span class="sxs-lookup"><span data-stu-id="986c3-427">The ASP.NET Core Module installer runs with the privileges of the **SYSTEM** account.</span></span> <span data-ttu-id="986c3-428">Vzhledem k tomu, že místní systémový účet mít nezmění oprávnění pro sdílenou složku cesta používaná systémem sdílené konfiguraci IIS, instalační program narazí na chybu při pokusu o konfiguraci nastavení modulu v odepření přístupu *applicationHost.config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="986c3-428">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer hits an access denied error when attempting to configure the module settings in *applicationHost.config* on the share.</span></span> <span data-ttu-id="986c3-429">Pokud používáte sdílenou konfiguraci IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="986c3-429">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="986c3-430">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="986c3-430">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="986c3-431">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="986c3-431">Run the installer.</span></span>
1. <span data-ttu-id="986c3-432">Export aktualizovaný *applicationHost.config* souboru do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="986c3-432">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="986c3-433">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="986c3-433">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="986c3-434">Verze modulu a hostování sady Instalační protokoly</span><span class="sxs-lookup"><span data-stu-id="986c3-434">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="986c3-435">Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="986c3-435">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="986c3-436">V hostitelském systému, přejděte na *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="986c3-436">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="986c3-437">Vyhledejte *aspnetcore.dll* souboru.</span><span class="sxs-lookup"><span data-stu-id="986c3-437">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="986c3-438">Klikněte pravým tlačítkem na soubor a vyberte **vlastnosti** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="986c3-438">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="986c3-439">Vyberte **podrobnosti** kartu. **Verze souboru** a **verze produktu** představují nainstalovaná verze modulu.</span><span class="sxs-lookup"><span data-stu-id="986c3-439">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="986c3-440">Instalační protokoly hostování sady prostředků modulu se nacházejí v *C:\\uživatelé\\% UserName %\\AppData\\místní\\Temp*. Soubor *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="986c3-440">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="986c3-441">Modul, schéma a konfiguraci umístění souborů</span><span class="sxs-lookup"><span data-stu-id="986c3-441">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="986c3-442">Modul</span><span class="sxs-lookup"><span data-stu-id="986c3-442">Module</span></span>

<span data-ttu-id="986c3-443">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="986c3-443">**IIS (x86/amd64):**</span></span>

   * <span data-ttu-id="986c3-444">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="986c3-444">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

   * <span data-ttu-id="986c3-445">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="986c3-445">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="986c3-446">%ProgramFiles%\IIS\Asp.NET core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="986c3-446">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="986c3-447">% ProgramFiles (x86) %\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="986c3-447">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

<span data-ttu-id="986c3-448">**Služba IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="986c3-448">**IIS Express (x86/amd64):**</span></span>

   * <span data-ttu-id="986c3-449">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="986c3-449">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

   * <span data-ttu-id="986c3-450">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="986c3-450">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="986c3-451">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="986c3-451">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="986c3-452">% ProgramFiles (x86) %\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="986c3-452">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

### <a name="schema"></a><span data-ttu-id="986c3-453">Schéma</span><span class="sxs-lookup"><span data-stu-id="986c3-453">Schema</span></span>

<span data-ttu-id="986c3-454">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="986c3-454">**IIS**</span></span>

   * <span data-ttu-id="986c3-455">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="986c3-455">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="986c3-456">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.XML</span><span class="sxs-lookup"><span data-stu-id="986c3-456">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end
<span data-ttu-id="986c3-457">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="986c3-457">**IIS Express**</span></span>

   * <span data-ttu-id="986c3-458">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="986c3-458">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="986c3-459">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="986c3-459">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="986c3-460">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="986c3-460">Configuration</span></span>

<span data-ttu-id="986c3-461">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="986c3-461">**IIS**</span></span>

   * <span data-ttu-id="986c3-462">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="986c3-462">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="986c3-463">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="986c3-463">**IIS Express**</span></span>

   * <span data-ttu-id="986c3-464">%ProgramFiles%\IIS Express\config\templates\PersonalWebServer\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="986c3-464">%ProgramFiles%\IIS Express\config\templates\PersonalWebServer\applicationHost.config</span></span>

<span data-ttu-id="986c3-465">Soubory můžete najít tak, že *aspnetcore* v *applicationHost.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="986c3-465">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="986c3-466">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="986c3-466">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="986c3-467">Úložiště GitHub modulu jádra ASP.NET (odkaz na zdroj)</span><span class="sxs-lookup"><span data-stu-id="986c3-467">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>