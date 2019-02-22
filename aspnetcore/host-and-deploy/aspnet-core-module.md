---
title: Modul ASP.NET Core
author: guardrex
description: Zjistěte, jak nakonfigurovat modul ASP.NET Core pro hostování aplikací ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/19/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: e7eed467a0f54df5d0e067efabf6f821b7647d70
ms.sourcegitcommit: 0945078a09c372f17e9b003758ed87e99c2449f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56647964"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="f14de-103">Modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f14de-103">ASP.NET Core Module</span></span>

<span data-ttu-id="f14de-104">Podle [Petr Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [ Justin Kotalik](https://github.com/jkotalik), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f14de-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f14de-105">Modul ASP.NET Core je nativní modul služby IIS, která zpřístupní kanálu IIS buď:</span><span class="sxs-lookup"><span data-stu-id="f14de-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="f14de-106">Hostování aplikace v ASP.NET Core v rámci pracovní proces služby IIS (`w3wp.exe`), označované jako [model hostingu v procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="f14de-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="f14de-107">Předání požadavku na webové aplikace ASP.NET Core s back-end systémem [Kestrel server](xref:fundamentals/servers/kestrel), označované jako [model hostingu mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="f14de-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="f14de-108">Podporované verze Windows:</span><span class="sxs-lookup"><span data-stu-id="f14de-108">Supported Windows versions:</span></span>

* <span data-ttu-id="f14de-109">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="f14de-109">Windows 7 or later</span></span>
* <span data-ttu-id="f14de-110">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="f14de-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f14de-111">Při hostování v procesu, modul používá v procesu serveru implementace pro službu IIS, volá HTTP serveru služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="f14de-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f14de-112">Při hostování mimo proces, modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f14de-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="f14de-113">Modul je nekompatibilní s [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="f14de-113">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="f14de-114">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="f14de-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="f14de-115">Model hostingu v procesu</span><span class="sxs-lookup"><span data-stu-id="f14de-115">In-process hosting model</span></span>

<span data-ttu-id="f14de-116">Jak nakonfigurovat aplikaci pro hostování v procesu, přidejte `<AspNetCoreHostingModel>` vlastnosti do souboru projektu vaší aplikace s hodnotou `InProcess` (hostování mimo proces se nastaví pomocí `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="f14de-116">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="f14de-117">Model hostingu v procesu není podporována pro aplikace ASP.NET Core, které jsou cíleny rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="f14de-117">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="f14de-118">Pokud `<AspNetCoreHostingModel>` vlastnost není k dispozici v souboru, výchozí hodnota je `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="f14de-118">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="f14de-119">Při hostování v procesu platí následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="f14de-119">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="f14de-120">Server služby IIS protokolu HTTP (`IISHttpServer`) se použije namísto [Kestrel](xref:fundamentals/servers/kestrel) serveru.</span><span class="sxs-lookup"><span data-stu-id="f14de-120">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="f14de-121">V procesu [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> na:</span><span class="sxs-lookup"><span data-stu-id="f14de-121">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="f14de-122">Zaregistrujte `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="f14de-122">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="f14de-123">Konfigurace portu a základní cesta server naslouchat požadavkům na při spuštění za modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f14de-123">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="f14de-124">Nakonfigurujte hostitele tak, aby zachycení chyb při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f14de-124">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="f14de-125">[RequestTimeout atribut](#attributes-of-the-aspnetcore-element) neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="f14de-125">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="f14de-126">Sdílení fondem aplikací mezi aplikacemi se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="f14de-126">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="f14de-127">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f14de-127">Use one app pool per app.</span></span>

* <span data-ttu-id="f14de-128">Při použití [Webdeploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručně uvedení [soubor app_offline.htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files), aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="f14de-128">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f14de-129">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f14de-129">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="f14de-130">Architektura (bitové verze) (x64 nebo x86) nainstalovaný modul runtime a aplikace musí odpovídat architektuře fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="f14de-130">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="f14de-131">Pokud nastavení aplikace hostitele ručně pomocí `WebHostBuilder` (bez použití [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) a spuštění aplikace se nikdy přímo na serveru Kestrel (v místním prostředí), volání `UseKestrel` před voláním `UseIISIntegration`.</span><span class="sxs-lookup"><span data-stu-id="f14de-131">If setting up the app's host manually with `WebHostBuilder` (not using [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) and the app is ever run directly on the Kestrel server (self-hosted), call `UseKestrel` before calling `UseIISIntegration`.</span></span> <span data-ttu-id="f14de-132">Pokud je obrácený pořadí, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="f14de-132">If the order is reversed, the host fails to start.</span></span>

* <span data-ttu-id="f14de-133">Odpojení klienta jsou zjištěny.</span><span class="sxs-lookup"><span data-stu-id="f14de-133">Client disconnects are detected.</span></span> <span data-ttu-id="f14de-134">[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) token zrušení se zrušila, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="f14de-134">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="f14de-135"><xref:System.IO.Directory.GetCurrentDirectory*> Vrátí adresáře pracovního procesu tím, že služba IIS spíše než adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="f14de-135"><xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="f14de-136">Ukázkový kód, který nastaví aktuální adresář aplikace, najdete v článku [CurrentDirectoryHelpers třídy](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="f14de-136">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="f14de-137">Volání `SetCurrentDirectory` metody.</span><span class="sxs-lookup"><span data-stu-id="f14de-137">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="f14de-138">Následující volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="f14de-138">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="f14de-139">Model hostingu mimo proces</span><span class="sxs-lookup"><span data-stu-id="f14de-139">Out-of-process hosting model</span></span>

<span data-ttu-id="f14de-140">Jak nakonfigurovat aplikaci pro hostování mimo proces, použijte jednu z následujících postupů v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="f14de-140">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="f14de-141">Nezadávejte `<AspNetCoreHostingModel>` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="f14de-141">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="f14de-142">Pokud `<AspNetCoreHostingModel>` vlastnost není k dispozici v souboru, výchozí hodnota je `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="f14de-142">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="f14de-143">Nastavte hodnotu `<AspNetCoreHostingModel>` vlastnost `OutOfProcess` (hostování v procesu je nastavená pomocí `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="f14de-143">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="f14de-144">[Kestrel](xref:fundamentals/servers/kestrel) server se používá místo protokolu HTTP serveru služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="f14de-144">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="f14de-145">Mimo proces [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> na:</span><span class="sxs-lookup"><span data-stu-id="f14de-145">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="f14de-146">Konfigurace portu a základní cesta server naslouchat požadavkům na při spuštění za modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f14de-146">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="f14de-147">Nakonfigurujte hostitele tak, aby zachycení chyb při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f14de-147">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="f14de-148">Hostování změny modelu</span><span class="sxs-lookup"><span data-stu-id="f14de-148">Hosting model changes</span></span>

<span data-ttu-id="f14de-149">Pokud `hostingModel` nastavení se změnilo v *web.config* souboru (podrobně [konfigurace pomocí souboru web.config](#configuration-with-webconfig) části), modul pro službu IIS recykluje pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="f14de-149">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="f14de-150">Pro službu IIS Express modul nebude recyklovat pracovní proces, ale místo toho aktivuje řádné vypnutí aktuální proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f14de-150">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="f14de-151">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="f14de-151">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="f14de-152">Název procesu</span><span class="sxs-lookup"><span data-stu-id="f14de-152">Process name</span></span>

<span data-ttu-id="f14de-153">`Process.GetCurrentProcess().ProcessName` sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (out-of-process).</span><span class="sxs-lookup"><span data-stu-id="f14de-153">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f14de-154">Modul ASP.NET Core je nativní modul služby IIS, která zpřístupní kanálu IIS, aby předával aplikace webové požadavky do back-endu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f14de-154">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="f14de-155">Podporované verze Windows:</span><span class="sxs-lookup"><span data-stu-id="f14de-155">Supported Windows versions:</span></span>

* <span data-ttu-id="f14de-156">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="f14de-156">Windows 7 or later</span></span>
* <span data-ttu-id="f14de-157">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="f14de-157">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="f14de-158">Modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f14de-158">The module only works with Kestrel.</span></span> <span data-ttu-id="f14de-159">Modul je nekompatibilní s [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="f14de-159">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="f14de-160">Protože aplikace ASP.NET Core spuštění v procesu oddělit od pracovní proces služby IIS, zpracovává modul také procesu správy.</span><span class="sxs-lookup"><span data-stu-id="f14de-160">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="f14de-161">V modulu začne proces pro aplikace ASP.NET Core, když první žádost o přijetí a restartuje aplikaci, pokud ho dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="f14de-161">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="f14de-162">Toto je v podstatě stejné chování jako aplikace ASP.NET 4.x, na kterých běží v procesu ve službě IIS, která jsou spravována [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="f14de-162">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="f14de-163">Následující diagram znázorňuje vztah mezi službou IIS, že modul ASP.NET Core a aplikace:</span><span class="sxs-lookup"><span data-stu-id="f14de-163">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="f14de-165">Požadavky přicházejí z webu pro ovladač HTTP.sys režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="f14de-165">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="f14de-166">Ovladač směruje požadavky do služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f14de-166">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="f14de-167">V modulu předá požadavky Kestrel na náhodný port pro aplikaci, která není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="f14de-167">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="f14de-168">V modulu určuje port, přes proměnnou prostředí při spuštění a Middleware pro integraci služby IIS nakonfiguruje server tak, aby naslouchala na `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="f14de-168">The module specifies the port via an environment variable at startup, and the IIS Integration Middleware configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="f14de-169">Další kontroly jsou prováděny, a odmítne požadavky, které není pocházejí z modulu.</span><span class="sxs-lookup"><span data-stu-id="f14de-169">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="f14de-170">Modul nepodporuje předávání protokolu HTTPS, takže žádosti se předávají prostřednictvím protokolu HTTP i v případě, že byla přijata službou IIS přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="f14de-170">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="f14de-171">Po Kestrel převezme žádosti z modulu, požadavek se vloží do kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="f14de-171">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="f14de-172">Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky.</span><span class="sxs-lookup"><span data-stu-id="f14de-172">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="f14de-173">Middleware, které jsou přidány pomocí integrace služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase pro předání požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="f14de-173">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="f14de-174">Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta HTTP, který inicioval žádost.</span><span class="sxs-lookup"><span data-stu-id="f14de-174">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

<span data-ttu-id="f14de-175">Množství nativních modulů, jako je například ověřování Windows, zůstanou aktivní.</span><span class="sxs-lookup"><span data-stu-id="f14de-175">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="f14de-176">Další informace o moduly IIS aktivní s modul ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="f14de-176">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="f14de-177">Modul ASP.NET Core můžete:</span><span class="sxs-lookup"><span data-stu-id="f14de-177">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="f14de-178">Nastavení proměnných prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="f14de-178">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="f14de-179">Zaznamená stdout výstup do služby file storage pro řešení potíží při spuštění.</span><span class="sxs-lookup"><span data-stu-id="f14de-179">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="f14de-180">Předávání ověřovacích tokenů Windows.</span><span class="sxs-lookup"><span data-stu-id="f14de-180">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="f14de-181">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f14de-181">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="f14de-182">Pokyny o tom, jak nainstalovat a používat modul ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="f14de-182">For instructions on how to install and use the ASP.NET Core Module, see <xref:host-and-deploy/iis/index>.</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="f14de-183">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="f14de-183">Configuration with web.config</span></span>

<span data-ttu-id="f14de-184">Modul ASP.NET Core, nastavena `aspNetCore` část `system.webServer` uzlu na webu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="f14de-184">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="f14de-185">Následující *web.config* soubor je publikován pro [nasazení závisí na architektuře](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="f14de-185">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="f14de-186">Následující *web.config* je publikována pro [samostatná nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="f14de-186">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="f14de-187"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> Je nastavena na `false` k označení, že nastavení zadané v rámci [ \<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nedědí od neznámých aplikací, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="f14de-187">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

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

<span data-ttu-id="f14de-188">Když je aplikace nasazená na [služby Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` je nastavena cesta `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="f14de-188">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="f14de-189">Protokoly stdout a uloží ji *LogFiles* složce, která je na místě automaticky vytvořené službou.</span><span class="sxs-lookup"><span data-stu-id="f14de-189">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="f14de-190">Informace o konfiguraci dílčí aplikace služby IIS, naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="f14de-190">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="f14de-191">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="f14de-191">Attributes of the aspNetCore element</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="f14de-192">Atribut</span><span class="sxs-lookup"><span data-stu-id="f14de-192">Attribute</span></span> | <span data-ttu-id="f14de-193">Popis</span><span class="sxs-lookup"><span data-stu-id="f14de-193">Description</span></span> | <span data-ttu-id="f14de-194">Výchozí</span><span class="sxs-lookup"><span data-stu-id="f14de-194">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="f14de-195">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="f14de-195">Optional string attribute.</span></span></p><p><span data-ttu-id="f14de-196">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="f14de-196">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="f14de-197">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-197">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f14de-198">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="f14de-198">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="f14de-199">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-199">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f14de-200">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="f14de-200">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="f14de-201">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="f14de-201">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="f14de-202">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="f14de-202">Optional string attribute.</span></span></p><p><span data-ttu-id="f14de-203">Určuje model hostování jako v procesu (`InProcess`) nebo out-of-process (`OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="f14de-203">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="f14de-204">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-204">Optional integer attribute.</span></span></p><p><span data-ttu-id="f14de-205">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="f14de-205">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="f14de-206">&dagger;Hostování v procesu, je omezena na hodnotu `1`.</span><span class="sxs-lookup"><span data-stu-id="f14de-206">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p> | <span data-ttu-id="f14de-207">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="f14de-207">Default: `1`</span></span><br><span data-ttu-id="f14de-208">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="f14de-208">Min: `1`</span></span><br><span data-ttu-id="f14de-209">Max: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="f14de-209">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="f14de-210">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="f14de-210">Required string attribute.</span></span></p><p><span data-ttu-id="f14de-211">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f14de-211">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="f14de-212">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="f14de-212">Relative paths are supported.</span></span> <span data-ttu-id="f14de-213">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="f14de-213">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="f14de-214">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-214">Optional integer attribute.</span></span></p><p><span data-ttu-id="f14de-215">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="f14de-215">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="f14de-216">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="f14de-216">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="f14de-217">Není podporováno s hostitelem v procesu.</span><span class="sxs-lookup"><span data-stu-id="f14de-217">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="f14de-218">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="f14de-218">Default: `10`</span></span><br><span data-ttu-id="f14de-219">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="f14de-219">Min: `0`</span></span><br><span data-ttu-id="f14de-220">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="f14de-220">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="f14de-221">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="f14de-221">Optional timespan attribute.</span></span></p><p><span data-ttu-id="f14de-222">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="f14de-222">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="f14de-223">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="f14de-223">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="f14de-224">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="f14de-224">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="f14de-225">Hostování v procesu, čeká modul app ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="f14de-225">For in-process hosting, the module waits for the app to process the request.</span></span></p> | <span data-ttu-id="f14de-226">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="f14de-226">Default: `00:02:00`</span></span><br><span data-ttu-id="f14de-227">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="f14de-227">Min: `00:00:00`</span></span><br><span data-ttu-id="f14de-228">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="f14de-228">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="f14de-229">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-229">Optional integer attribute.</span></span></p><p><span data-ttu-id="f14de-230">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="f14de-230">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="f14de-231">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="f14de-231">Default: `10`</span></span><br><span data-ttu-id="f14de-232">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="f14de-232">Min: `0`</span></span><br><span data-ttu-id="f14de-233">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="f14de-233">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="f14de-234">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-234">Optional integer attribute.</span></span></p><p><span data-ttu-id="f14de-235">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="f14de-235">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="f14de-236">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="f14de-236">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="f14de-237">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="f14de-237">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="f14de-238">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="f14de-238">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="f14de-239">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="f14de-239">Default: `120`</span></span><br><span data-ttu-id="f14de-240">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="f14de-240">Min: `0`</span></span><br><span data-ttu-id="f14de-241">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="f14de-241">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="f14de-242">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-242">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f14de-243">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="f14de-243">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="f14de-244">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="f14de-244">Optional string attribute.</span></span></p><p><span data-ttu-id="f14de-245">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="f14de-245">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="f14de-246">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="f14de-246">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="f14de-247">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="f14de-247">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="f14de-248">Všechny složky v cestě k dispozici jsou vytvořené v modulu při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="f14de-248">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="f14de-249">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru (*.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="f14de-249">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="f14de-250">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="f14de-250">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="f14de-251">Atribut</span><span class="sxs-lookup"><span data-stu-id="f14de-251">Attribute</span></span> | <span data-ttu-id="f14de-252">Popis</span><span class="sxs-lookup"><span data-stu-id="f14de-252">Description</span></span> | <span data-ttu-id="f14de-253">Výchozí</span><span class="sxs-lookup"><span data-stu-id="f14de-253">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="f14de-254">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="f14de-254">Optional string attribute.</span></span></p><p><span data-ttu-id="f14de-255">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="f14de-255">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="f14de-256">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-256">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f14de-257">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="f14de-257">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="f14de-258">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-258">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f14de-259">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="f14de-259">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="f14de-260">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="f14de-260">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="f14de-261">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-261">Optional integer attribute.</span></span></p><p><span data-ttu-id="f14de-262">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="f14de-262">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p> | <span data-ttu-id="f14de-263">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="f14de-263">Default: `1`</span></span><br><span data-ttu-id="f14de-264">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="f14de-264">Min: `1`</span></span><br><span data-ttu-id="f14de-265">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="f14de-265">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="f14de-266">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="f14de-266">Required string attribute.</span></span></p><p><span data-ttu-id="f14de-267">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f14de-267">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="f14de-268">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="f14de-268">Relative paths are supported.</span></span> <span data-ttu-id="f14de-269">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="f14de-269">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="f14de-270">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-270">Optional integer attribute.</span></span></p><p><span data-ttu-id="f14de-271">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="f14de-271">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="f14de-272">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="f14de-272">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="f14de-273">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="f14de-273">Default: `10`</span></span><br><span data-ttu-id="f14de-274">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="f14de-274">Min: `0`</span></span><br><span data-ttu-id="f14de-275">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="f14de-275">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="f14de-276">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="f14de-276">Optional timespan attribute.</span></span></p><p><span data-ttu-id="f14de-277">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="f14de-277">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="f14de-278">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="f14de-278">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="f14de-279">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="f14de-279">Default: `00:02:00`</span></span><br><span data-ttu-id="f14de-280">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="f14de-280">Min: `00:00:00`</span></span><br><span data-ttu-id="f14de-281">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="f14de-281">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="f14de-282">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-282">Optional integer attribute.</span></span></p><p><span data-ttu-id="f14de-283">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="f14de-283">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="f14de-284">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="f14de-284">Default: `10`</span></span><br><span data-ttu-id="f14de-285">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="f14de-285">Min: `0`</span></span><br><span data-ttu-id="f14de-286">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="f14de-286">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="f14de-287">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-287">Optional integer attribute.</span></span></p><p><span data-ttu-id="f14de-288">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="f14de-288">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="f14de-289">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="f14de-289">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="f14de-290">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="f14de-290">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="f14de-291">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="f14de-291">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="f14de-292">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="f14de-292">Default: `120`</span></span><br><span data-ttu-id="f14de-293">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="f14de-293">Min: `0`</span></span><br><span data-ttu-id="f14de-294">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="f14de-294">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="f14de-295">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-295">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f14de-296">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="f14de-296">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="f14de-297">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="f14de-297">Optional string attribute.</span></span></p><p><span data-ttu-id="f14de-298">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="f14de-298">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="f14de-299">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="f14de-299">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="f14de-300">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="f14de-300">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="f14de-301">Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="f14de-301">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="f14de-302">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru (*.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="f14de-302">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="f14de-303">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="f14de-303">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

| <span data-ttu-id="f14de-304">Atribut</span><span class="sxs-lookup"><span data-stu-id="f14de-304">Attribute</span></span> | <span data-ttu-id="f14de-305">Popis</span><span class="sxs-lookup"><span data-stu-id="f14de-305">Description</span></span> | <span data-ttu-id="f14de-306">Výchozí</span><span class="sxs-lookup"><span data-stu-id="f14de-306">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="f14de-307">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="f14de-307">Optional string attribute.</span></span></p><p><span data-ttu-id="f14de-308">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="f14de-308">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="f14de-309">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-309">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f14de-310">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="f14de-310">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="f14de-311">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-311">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f14de-312">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="f14de-312">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="f14de-313">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="f14de-313">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="f14de-314">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-314">Optional integer attribute.</span></span></p><p><span data-ttu-id="f14de-315">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="f14de-315">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p> | <span data-ttu-id="f14de-316">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="f14de-316">Default: `1`</span></span><br><span data-ttu-id="f14de-317">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="f14de-317">Min: `1`</span></span><br><span data-ttu-id="f14de-318">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="f14de-318">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="f14de-319">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="f14de-319">Required string attribute.</span></span></p><p><span data-ttu-id="f14de-320">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f14de-320">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="f14de-321">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="f14de-321">Relative paths are supported.</span></span> <span data-ttu-id="f14de-322">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="f14de-322">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="f14de-323">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-323">Optional integer attribute.</span></span></p><p><span data-ttu-id="f14de-324">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="f14de-324">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="f14de-325">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="f14de-325">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="f14de-326">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="f14de-326">Default: `10`</span></span><br><span data-ttu-id="f14de-327">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="f14de-327">Min: `0`</span></span><br><span data-ttu-id="f14de-328">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="f14de-328">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="f14de-329">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="f14de-329">Optional timespan attribute.</span></span></p><p><span data-ttu-id="f14de-330">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="f14de-330">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="f14de-331">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.0 nebo starší `requestTimeout` musí být zadán v celých minutách, v opačném případě výchozí hodnota 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="f14de-331">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.0 or earlier, the `requestTimeout` must be specified in whole minutes only, otherwise it defaults to 2 minutes.</span></span></p> | <span data-ttu-id="f14de-332">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="f14de-332">Default: `00:02:00`</span></span><br><span data-ttu-id="f14de-333">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="f14de-333">Min: `00:00:00`</span></span><br><span data-ttu-id="f14de-334">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="f14de-334">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="f14de-335">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-335">Optional integer attribute.</span></span></p><p><span data-ttu-id="f14de-336">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="f14de-336">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="f14de-337">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="f14de-337">Default: `10`</span></span><br><span data-ttu-id="f14de-338">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="f14de-338">Min: `0`</span></span><br><span data-ttu-id="f14de-339">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="f14de-339">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="f14de-340">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-340">Optional integer attribute.</span></span></p><p><span data-ttu-id="f14de-341">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="f14de-341">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="f14de-342">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="f14de-342">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="f14de-343">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="f14de-343">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p> | <span data-ttu-id="f14de-344">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="f14de-344">Default: `120`</span></span><br><span data-ttu-id="f14de-345">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="f14de-345">Min: `0`</span></span><br><span data-ttu-id="f14de-346">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="f14de-346">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="f14de-347">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-347">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="f14de-348">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="f14de-348">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="f14de-349">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="f14de-349">Optional string attribute.</span></span></p><p><span data-ttu-id="f14de-350">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="f14de-350">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="f14de-351">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="f14de-351">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="f14de-352">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="f14de-352">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="f14de-353">Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="f14de-353">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="f14de-354">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru (*.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="f14de-354">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="f14de-355">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="f14de-355">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a><span data-ttu-id="f14de-356">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="f14de-356">Setting environment variables</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f14de-357">Proměnné prostředí se dá nastavit pro proces v `processPath` atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-357">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="f14de-358">Zadat proměnné prostředí s `<environmentVariable>` podřízený prvek `<environmentVariables>` prvek kolekce.</span><span class="sxs-lookup"><span data-stu-id="f14de-358">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="f14de-359">Proměnné prostředí nastavené v této části přednost systémové proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f14de-359">Environment variables set in this section take precedence over system environment variables.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f14de-360">Proměnné prostředí se dá nastavit pro proces v `processPath` atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-360">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="f14de-361">Zadat proměnné prostředí s `<environmentVariable>` podřízený prvek `<environmentVariables>` prvek kolekce.</span><span class="sxs-lookup"><span data-stu-id="f14de-361">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="f14de-362">Nastavte proměnné prostředí nastavené v této části konflikt pomocí proměnných prostředí systému se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="f14de-362">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="f14de-363">Pokud proměnná prostředí je nastavena v obou *web.config* souborů a systému úroveň ve Windows, hodnotu z *web.config* souboru bude připojeno k hodnotu proměnné prostředí systému (pro například `ASPNETCORE_ENVIRONMENT: Development;Development`), která zabraňuje spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="f14de-363">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

::: moniker-end

<span data-ttu-id="f14de-364">Následující příklad nastaví dvou proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="f14de-364">The following example sets two environment variables.</span></span> <span data-ttu-id="f14de-365">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby `Development`.</span><span class="sxs-lookup"><span data-stu-id="f14de-365">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="f14de-366">Vývojáři mohou dočasně nastaví tuto hodnotu *web.config* souboru, aby bylo možné vynutit [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) načíst při ladění aplikace výjimky.</span><span class="sxs-lookup"><span data-stu-id="f14de-366">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="f14de-367">`CONFIG_DIR` je příkladem proměnné prostředí, kam má vývojář zapisovat kód, který čte hodnoty při spuštění tvoří cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="f14de-367">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="f14de-368">Alternativa k nastavení prostředí přímo v *web.config* se zahrnou `<EnvironmentName>` vlastnost v profilu publikování (*.pubxml*) nebo soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="f14de-368">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="f14de-369">Tento přístup nastaví prostředí *web.config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="f14de-369">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

::: moniker-end

> [!WARNING]
> <span data-ttu-id="f14de-370">Nastavit pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí, aby `Development` na přípravy a testování serverů, které nejsou dostupné k nedůvěryhodným sítím, jako je Internet.</span><span class="sxs-lookup"><span data-stu-id="f14de-370">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="appofflinehtm"></a><span data-ttu-id="f14de-371">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="f14de-371">app_offline.htm</span></span>

<span data-ttu-id="f14de-372">Pokud soubor s názvem *app_offline.htm* je zjištěna v kořenovém adresáři aplikace, že modul ASP.NET Core se pokusí řádné vypnutí aplikace a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="f14de-372">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="f14de-373">Pokud aplikace pořád běží po dobu v sekundách podle `shutdownTimeLimit`, že modul ASP.NET Core ukončuje spuštěnému procesu.</span><span class="sxs-lookup"><span data-stu-id="f14de-373">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="f14de-374">Zatímco *app_offline.htm* soubor je k dispozici, že modul ASP.NET Core reaguje na požadavky odesílá zpět obsah *app_offline.htm* souboru.</span><span class="sxs-lookup"><span data-stu-id="f14de-374">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="f14de-375">Když *app_offline.htm* soubor bude odstraněn, příští žádosti o spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="f14de-375">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f14de-376">Pokud používáte model hostingu mimo proces, aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="f14de-376">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="f14de-377">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="f14de-377">For example, a websocket connection may delay app shut down.</span></span>

::: moniker-end

## <a name="start-up-error-page"></a><span data-ttu-id="f14de-378">Spuštění chybovou stránku</span><span class="sxs-lookup"><span data-stu-id="f14de-378">Start-up error page</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f14de-379">Hostování v procesu a mimo proces vytvoření vlastní chybové stránky, když selžou a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f14de-379">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="f14de-380">Pokud se nepodaří najít buď nebo na více instancí procesu žádosti o obslužnou rutinu, že modul ASP.NET Core *500.0 – Chyba načtení obslužné rutiny v procesu/Out – proces* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="f14de-380">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="f14de-381">Pro hostování v procesu, pokud se nepodaří spustit aplikaci, že modul ASP.NET Core *500.30 - Start selhání* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="f14de-381">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="f14de-382">Pro hostování mimo proces, pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="f14de-382">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="f14de-383">Chcete-li potlačit tuto stránku a vrátit se na výchozí služby IIS 5xx stav znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-383">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="f14de-384">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="f14de-384">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f14de-385">Pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="f14de-385">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="f14de-386">Chcete-li potlačit tuto stránku a vrátit se na výchozí stav služby IIS 502 znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="f14de-386">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="f14de-387">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="f14de-387">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 proces selhání stav znaková stránka](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a><span data-ttu-id="f14de-389">Vytvoření protokolu a přesměrování</span><span class="sxs-lookup"><span data-stu-id="f14de-389">Log creation and redirection</span></span>

<span data-ttu-id="f14de-390">Modul ASP.NET Core přesměruje výstup stdout a stderr console na disk, pokud `stdoutLogEnabled` a `stdoutLogFile` atributy `aspNetCore` element jsou nastavené.</span><span class="sxs-lookup"><span data-stu-id="f14de-390">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="f14de-391">Všechny složky v `stdoutLogFile` cesta vytvářejí modulem, když se vytvoří soubor protokolu.</span><span class="sxs-lookup"><span data-stu-id="f14de-391">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="f14de-392">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="f14de-392">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="f14de-393">Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování.</span><span class="sxs-lookup"><span data-stu-id="f14de-393">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="f14de-394">Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.</span><span class="sxs-lookup"><span data-stu-id="f14de-394">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="f14de-395">Použití protokolu stdout se doporučuje jenom pro řešení potíží při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="f14de-395">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="f14de-396">Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace.</span><span class="sxs-lookup"><span data-stu-id="f14de-396">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="f14de-397">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="f14de-397">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="f14de-398">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="f14de-398">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="f14de-399">Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="f14de-399">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="f14de-400">Název souboru protokolu se skládá připojením časové razítko, ID procesu a přípona souboru (*.log*) na poslední segment `stdoutLogFile` cestu (obvykle *stdout*) oddělené podtržítka.</span><span class="sxs-lookup"><span data-stu-id="f14de-400">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="f14de-401">Pokud `stdoutLogFile` cesta končí *stdout*, má název souboru protokolu pro aplikaci s PID 1934 vytvořili 5 2. 2018 v 19:42:32 *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="f14de-401">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f14de-402">Pokud `stdoutLogEnabled` má hodnotu false, chyby, ke kterým dochází při spuštění aplikace se zachytí a do protokolu událostí, protože ho až 30 KB.</span><span class="sxs-lookup"><span data-stu-id="f14de-402">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="f14de-403">Po spuštění se zahodí všechny další protokoly.</span><span class="sxs-lookup"><span data-stu-id="f14de-403">After startup, all additional logs are discarded.</span></span>

::: moniker-end

<span data-ttu-id="f14de-404">Následující ukázka `aspNetCore` element konfiguruje stdout protokolování pro aplikace hostované ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="f14de-404">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="f14de-405">Místní cesta nebo cesta ke sdílené položce je přijatelné pro místní přihlášení.</span><span class="sxs-lookup"><span data-stu-id="f14de-405">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="f14de-406">Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.</span><span class="sxs-lookup"><span data-stu-id="f14de-406">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

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

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="f14de-407">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="f14de-407">Enhanced diagnostic logs</span></span>

<span data-ttu-id="f14de-408">Modul ASP.NET Core je konfigurovat, a poskytují rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="f14de-408">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="f14de-409">Přidat `<handlerSettings>` elementu `<aspNetCore>` prvek *web.config*. Nastavení `debugLevel` k `TRACE` zpřístupňuje větší věrnost diagnostické informace:</span><span class="sxs-lookup"><span data-stu-id="f14de-409">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

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

<span data-ttu-id="f14de-410">Ladit úrovně (`debugLevel`) hodnoty může obsahovat úroveň a umístění.</span><span class="sxs-lookup"><span data-stu-id="f14de-410">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="f14de-411">Úrovně (v pořadí od nejméně na nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="f14de-411">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="f14de-412">CHYBA</span><span class="sxs-lookup"><span data-stu-id="f14de-412">ERROR</span></span>
* <span data-ttu-id="f14de-413">UPOZORNĚNÍ</span><span class="sxs-lookup"><span data-stu-id="f14de-413">WARNING</span></span>
* <span data-ttu-id="f14de-414">INFORMACE O</span><span class="sxs-lookup"><span data-stu-id="f14de-414">INFO</span></span>
* <span data-ttu-id="f14de-415">TRACE</span><span class="sxs-lookup"><span data-stu-id="f14de-415">TRACE</span></span>

<span data-ttu-id="f14de-416">Umístění (umístění více jsou povoleny):</span><span class="sxs-lookup"><span data-stu-id="f14de-416">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="f14de-417">KONZOLY</span><span class="sxs-lookup"><span data-stu-id="f14de-417">CONSOLE</span></span>
* <span data-ttu-id="f14de-418">PROTOKOL UDÁLOSTÍ</span><span class="sxs-lookup"><span data-stu-id="f14de-418">EVENTLOG</span></span>
* <span data-ttu-id="f14de-419">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="f14de-419">FILE</span></span>

<span data-ttu-id="f14de-420">Obslužná rutina nastavení se dá zadat i prostřednictvím proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="f14de-420">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="f14de-421">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="f14de-421">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="f14de-422">(Výchozí: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="f14de-422">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="f14de-423">`ASPNETCORE_MODULE_DEBUG` &ndash; Ladit nastavení úrovně.</span><span class="sxs-lookup"><span data-stu-id="f14de-423">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="f14de-424">Proveďte **není** protokolování ladění povoleno v nasazení pro déle, než se požaduje chcete vyřešit nějaký problém.</span><span class="sxs-lookup"><span data-stu-id="f14de-424">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="f14de-425">Velikost souboru protokolu není omezený.</span><span class="sxs-lookup"><span data-stu-id="f14de-425">The size of the log isn't limited.</span></span> <span data-ttu-id="f14de-426">Opuštění protokol ladění povoleno může vyčerpat dostupné místo na disku a chybách u aplikací na serveru nebo služby app service.</span><span class="sxs-lookup"><span data-stu-id="f14de-426">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

::: moniker-end

<span data-ttu-id="f14de-427">Naleznete v tématu [konfigurace pomocí souboru web.config](#configuration-with-webconfig) příklad `aspNetCore` prvek *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="f14de-427">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="f14de-428">Konfigurace proxy serveru používá protokol HTTP a token pro párování</span><span class="sxs-lookup"><span data-stu-id="f14de-428">Proxy configuration uses HTTP protocol and a pairing token</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f14de-429">*Platí jenom pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="f14de-429">*Only applies to out-of-process hosting.*</span></span>

::: moniker-end

<span data-ttu-id="f14de-430">Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="f14de-430">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="f14de-431">Pomocí protokolu HTTP je optimalizace výkonu, kdy přenos dat mezi modulu a Kestrel probíhá na adresu zpětné smyčky ze síťového rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f14de-431">Using HTTP is a performance optimization, where the traffic between the module and Kestrel takes place on a loopback address off of the network interface.</span></span> <span data-ttu-id="f14de-432">Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="f14de-432">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="f14de-433">Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="f14de-433">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="f14de-434">Vytvoření a nastavení do proměnné prostředí párování token (`ASPNETCORE_TOKEN`) modulu.</span><span class="sxs-lookup"><span data-stu-id="f14de-434">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="f14de-435">Párování token byl nastavený i do záhlaví (`MS-ASPNETCORE-TOKEN`) na všechny požadavky směrovány přes proxy server.</span><span class="sxs-lookup"><span data-stu-id="f14de-435">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="f14de-436">Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="f14de-436">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="f14de-437">Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut.</span><span class="sxs-lookup"><span data-stu-id="f14de-437">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="f14de-438">Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="f14de-438">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="f14de-439">Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.</span><span class="sxs-lookup"><span data-stu-id="f14de-439">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="f14de-440">Modul ASP.NET Core s službu IIS sdílenou konfiguraci</span><span class="sxs-lookup"><span data-stu-id="f14de-440">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="f14de-441">Modul ASP.NET Core instalační program spustí s oprávněními **TrustedInstaller** účtu.</span><span class="sxs-lookup"><span data-stu-id="f14de-441">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="f14de-442">Vzhledem k tomu, že místní systémový účet mít nezmění oprávnění pro sdílenou složku cesta používaná systémem sdílené konfiguraci IIS, instalační program vyvolá chybu při pokusu o konfiguraci nastavení modulu v odepření přístupu *applicationHost.config*  soubor ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="f14de-442">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f14de-443">Pokud používáte sdílenou konfiguraci IIS na stejném počítači jako instalace služby IIS, spusťte instalační program sady hostování technologie ASP.NET Core s `OPT_NO_SHARED_CONFIG_CHECK` parametr nastaven na `1`:</span><span class="sxs-lookup"><span data-stu-id="f14de-443">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="f14de-444">Pokud cesta ke sdílené konfigurace není ve stejném počítači jako instalace služby IIS, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="f14de-444">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="f14de-445">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="f14de-445">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="f14de-446">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="f14de-446">Run the installer.</span></span>
1. <span data-ttu-id="f14de-447">Export aktualizovaný *applicationHost.config* souboru do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="f14de-447">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="f14de-448">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="f14de-448">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="f14de-449">Pokud používáte sdílenou konfiguraci IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="f14de-449">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="f14de-450">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="f14de-450">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="f14de-451">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="f14de-451">Run the installer.</span></span>
1. <span data-ttu-id="f14de-452">Export aktualizovaný *applicationHost.config* souboru do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="f14de-452">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="f14de-453">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="f14de-453">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="application-initialization"></a><span data-ttu-id="f14de-454">Inicializace aplikací</span><span class="sxs-lookup"><span data-stu-id="f14de-454">Application Initialization</span></span>

<span data-ttu-id="f14de-455">[Inicializace aplikací služby IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) je funkce IIS, který odešle požadavek HTTP do aplikace při spuštění fondu aplikací nebo recykluje.</span><span class="sxs-lookup"><span data-stu-id="f14de-455">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="f14de-456">Žádost se aktivuje spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="f14de-456">The request triggers the app to start.</span></span> <span data-ttu-id="f14de-457">Inicializace aplikace může využívat i [model hostingu v procesu](xref:fundamentals/servers/index#in-process-hosting-model) a [model hostingu mimo proces](xref:fundamentals/servers/index#out-of-process-hosting-model) s modul ASP.NET Core verze 2.</span><span class="sxs-lookup"><span data-stu-id="f14de-457">Application Initialization can be used by both the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model) and [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model) with the ASP.NET Core Module version 2.</span></span>

<span data-ttu-id="f14de-458">Chcete-li povolit inicializaci aplikace:</span><span class="sxs-lookup"><span data-stu-id="f14de-458">To enable Application Initialization:</span></span>

1. <span data-ttu-id="f14de-459">Potvrďte, že funkce inicializace aplikace služby IIS role v povoleno:</span><span class="sxs-lookup"><span data-stu-id="f14de-459">Confirm that the IIS Application Initialization role feature in enabled:</span></span>
   * <span data-ttu-id="f14de-460">Ve Windows 7 nebo novější: Přejděte do **ovládací panely** > **programy** > **programy a funkce** > **zapnout Windows funkce na nebo vypnout** (levé straně obrazovky).</span><span class="sxs-lookup"><span data-stu-id="f14de-460">On Windows 7 or later: Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span> <span data-ttu-id="f14de-461">Otevřít **Internetová informační služba** > **webové služby** > **funkce pro vývoj aplikací**.</span><span class="sxs-lookup"><span data-stu-id="f14de-461">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="f14de-462">Zaškrtněte políčko pro **inicializace aplikace**.</span><span class="sxs-lookup"><span data-stu-id="f14de-462">Select the check box for **Application Initialization**.</span></span>
   * <span data-ttu-id="f14de-463">V systému Windows Server 2008 R2 nebo novější, otevřete **Průvodce přidání rolí a funkcí**.</span><span class="sxs-lookup"><span data-stu-id="f14de-463">On Windows Server 2008 R2 or later, open the **Add Roles and Features Wizard**.</span></span> <span data-ttu-id="f14de-464">Když se dostanete **vybrat služby rolí** panelů, otevřete **vývoj aplikací** uzel a vyberte **inicializace aplikace** zaškrtávací políčko.</span><span class="sxs-lookup"><span data-stu-id="f14de-464">When you reach the **Select role services** panel, open the **Application Development** node and select the **Application Initialization** check box.</span></span>
1. <span data-ttu-id="f14de-465">Ve Správci služby IIS vyberte **fondy aplikací** v **připojení** panelu.</span><span class="sxs-lookup"><span data-stu-id="f14de-465">In IIS Manager, select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="f14de-466">V seznamu vyberte fond aplikací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f14de-466">Select the app's app pool in the list.</span></span>
1. <span data-ttu-id="f14de-467">Vyberte **Upřesnit nastavení** pod **upravit fond aplikací** v **akce** panelu.</span><span class="sxs-lookup"><span data-stu-id="f14de-467">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="f14de-468">Nastavte **spustit režim** k **AlwaysRunning**.</span><span class="sxs-lookup"><span data-stu-id="f14de-468">Set **Start Mode** to **AlwaysRunning**.</span></span>
1. <span data-ttu-id="f14de-469">Otevřít **lokality** uzlu v **připojení** panelu.</span><span class="sxs-lookup"><span data-stu-id="f14de-469">Open the **Sites** node in the **Connections** panel.</span></span>
1. <span data-ttu-id="f14de-470">Vyberte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f14de-470">Select the app.</span></span>
1. <span data-ttu-id="f14de-471">Vyberte **Upřesnit nastavení** pod **spravovat web** v **akce** panelu.</span><span class="sxs-lookup"><span data-stu-id="f14de-471">Select **Advanced Settings** under **Manage Website** in the **Actions** panel.</span></span>
1. <span data-ttu-id="f14de-472">Nastavte **předběžné načtení povoleno** k **True**.</span><span class="sxs-lookup"><span data-stu-id="f14de-472">Set **Preload Enabled** to **True**.</span></span>

<span data-ttu-id="f14de-473">Další informace najdete v tématu [inicializace aplikace služby IIS 8.0](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization).</span><span class="sxs-lookup"><span data-stu-id="f14de-473">For more information, see [IIS 8.0 Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization).</span></span>

<span data-ttu-id="f14de-474">Aplikace, které používají [model hostingu mimo proces](xref:fundamentals/servers/index#out-of-process-hosting-model) musíte použít externí služby pravidelně k udržení příkazem ping otestovat aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f14de-474">Apps that use the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model) must use an external service to periodically ping the app in order to keep it running.</span></span>

::: moniker-end

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="f14de-475">Verze modulu a hostování sady Instalační protokoly</span><span class="sxs-lookup"><span data-stu-id="f14de-475">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="f14de-476">Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f14de-476">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="f14de-477">V hostitelském systému, přejděte na *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="f14de-477">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="f14de-478">Vyhledejte *aspnetcore.dll* souboru.</span><span class="sxs-lookup"><span data-stu-id="f14de-478">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="f14de-479">Klikněte pravým tlačítkem na soubor a vyberte **vlastnosti** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="f14de-479">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="f14de-480">Vyberte **podrobnosti** kartu. **Verze souboru** a **verze produktu** představují nainstalovaná verze modulu.</span><span class="sxs-lookup"><span data-stu-id="f14de-480">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="f14de-481">Instalační protokoly hostování sady prostředků modulu se nacházejí v *C:\\uživatelé\\% UserName %\\AppData\\místní\\Temp*. Soubor *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="f14de-481">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="f14de-482">Modul, schéma a konfiguraci umístění souborů</span><span class="sxs-lookup"><span data-stu-id="f14de-482">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="f14de-483">Modul</span><span class="sxs-lookup"><span data-stu-id="f14de-483">Module</span></span>

<span data-ttu-id="f14de-484">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f14de-484">**IIS (x86/amd64):**</span></span>

   * <span data-ttu-id="f14de-485">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f14de-485">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

   * <span data-ttu-id="f14de-486">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f14de-486">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="f14de-487">%ProgramFiles%\IIS\Asp.NET core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f14de-487">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="f14de-488">% ProgramFiles (x86) %\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f14de-488">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

<span data-ttu-id="f14de-489">**Služba IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="f14de-489">**IIS Express (x86/amd64):**</span></span>

   * <span data-ttu-id="f14de-490">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f14de-490">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

   * <span data-ttu-id="f14de-491">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="f14de-491">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="f14de-492">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f14de-492">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

   * <span data-ttu-id="f14de-493">% ProgramFiles (x86) %\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="f14de-493">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

### <a name="schema"></a><span data-ttu-id="f14de-494">Schéma</span><span class="sxs-lookup"><span data-stu-id="f14de-494">Schema</span></span>

<span data-ttu-id="f14de-495">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="f14de-495">**IIS**</span></span>

   * <span data-ttu-id="f14de-496">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f14de-496">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="f14de-497">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.XML</span><span class="sxs-lookup"><span data-stu-id="f14de-497">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end
<span data-ttu-id="f14de-498">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f14de-498">**IIS Express**</span></span>

   * <span data-ttu-id="f14de-499">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="f14de-499">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

   * <span data-ttu-id="f14de-500">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="f14de-500">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="f14de-501">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="f14de-501">Configuration</span></span>

<span data-ttu-id="f14de-502">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="f14de-502">**IIS**</span></span>

   * <span data-ttu-id="f14de-503">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f14de-503">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="f14de-504">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="f14de-504">**IIS Express**</span></span>

   * <span data-ttu-id="f14de-505">Visual Studio: {Kořenový adresář aplikace}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="f14de-505">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>
   
   * <span data-ttu-id="f14de-506">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="f14de-506">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="f14de-507">Soubory můžete najít tak, že *aspnetcore* v *applicationHost.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="f14de-507">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f14de-508">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="f14de-508">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="f14de-509">Úložiště GitHub modulu jádra ASP.NET (odkaz na zdroj)</span><span class="sxs-lookup"><span data-stu-id="f14de-509">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
