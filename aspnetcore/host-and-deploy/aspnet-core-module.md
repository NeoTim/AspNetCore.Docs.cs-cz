---
title: Modul ASP.NET Core
author: guardrex
description: Zjistěte, jak nakonfigurovat modul ASP.NET Core pro hostování aplikací ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/17/2019
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: d5392ff6b15eeb3a4502df578665538b936aae6f
ms.sourcegitcommit: 28a2874765cefe9eaa068dceb989a978ba2096aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67167061"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="2a275-103">Modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2a275-103">ASP.NET Core Module</span></span>

<span data-ttu-id="2a275-104">Podle [Petr Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [ Justin Kotalik](https://github.com/jkotalik), a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2a275-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), [Justin Kotalik](https://github.com/jkotalik), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2a275-105">Modul ASP.NET Core je nativní modul služby IIS, která zpřístupní kanálu IIS buď:</span><span class="sxs-lookup"><span data-stu-id="2a275-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="2a275-106">Hostování aplikace v ASP.NET Core v rámci pracovní proces služby IIS (`w3wp.exe`), označované jako [model hostingu v procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="2a275-106">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="2a275-107">Předání požadavku na webové aplikace ASP.NET Core s back-end systémem [Kestrel server](xref:fundamentals/servers/kestrel), označované jako [model hostingu mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="2a275-107">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="2a275-108">Podporované verze Windows:</span><span class="sxs-lookup"><span data-stu-id="2a275-108">Supported Windows versions:</span></span>

* <span data-ttu-id="2a275-109">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="2a275-109">Windows 7 or later</span></span>
* <span data-ttu-id="2a275-110">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="2a275-110">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="2a275-111">Při hostování v procesu, modul používá v procesu serveru implementace pro službu IIS, volá HTTP serveru služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="2a275-111">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="2a275-112">Při hostování mimo proces, modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2a275-112">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="2a275-113">Modul je nekompatibilní s [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="2a275-113">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="2a275-114">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="2a275-114">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="2a275-115">Model hostingu v procesu</span><span class="sxs-lookup"><span data-stu-id="2a275-115">In-process hosting model</span></span>

<span data-ttu-id="2a275-116">Jak nakonfigurovat aplikaci pro hostování v procesu, přidejte `<AspNetCoreHostingModel>` vlastnosti do souboru projektu vaší aplikace s hodnotou `InProcess` (hostování mimo proces se nastaví pomocí `OutOfProcess`):</span><span class="sxs-lookup"><span data-stu-id="2a275-116">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="2a275-117">Model hostingu v procesu není podporována pro aplikace ASP.NET Core, které jsou cíleny rozhraní .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="2a275-117">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="2a275-118">Pokud `<AspNetCoreHostingModel>` vlastnost není k dispozici v souboru, výchozí hodnota je `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="2a275-118">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="2a275-119">Při hostování v procesu platí následující vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2a275-119">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="2a275-120">Server služby IIS protokolu HTTP (`IISHttpServer`) se použije namísto [Kestrel](xref:fundamentals/servers/kestrel) serveru.</span><span class="sxs-lookup"><span data-stu-id="2a275-120">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="2a275-121">V procesu [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> na:</span><span class="sxs-lookup"><span data-stu-id="2a275-121">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="2a275-122">Zaregistrujte `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="2a275-122">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="2a275-123">Konfigurace portu a základní cesta server naslouchat požadavkům na při spuštění za modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a275-123">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="2a275-124">Nakonfigurujte hostitele tak, aby zachycení chyb při spuštění.</span><span class="sxs-lookup"><span data-stu-id="2a275-124">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="2a275-125">[RequestTimeout atribut](#attributes-of-the-aspnetcore-element) neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="2a275-125">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="2a275-126">Sdílení fondem aplikací mezi aplikacemi se nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="2a275-126">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="2a275-127">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2a275-127">Use one app pool per app.</span></span>

* <span data-ttu-id="2a275-128">Při použití [Webdeploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručně uvedení [soubor app_offline.htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files), aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="2a275-128">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="2a275-129">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="2a275-129">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="2a275-130">Architektura (bitové verze) (x64 nebo x86) nainstalovaný modul runtime a aplikace musí odpovídat architektuře fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="2a275-130">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="2a275-131">Pokud nastavení aplikace hostitele ručně pomocí `WebHostBuilder` (bez použití [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) a spuštění aplikace se nikdy přímo na serveru Kestrel (v místním prostředí), volání `UseKestrel` před voláním `UseIISIntegration`.</span><span class="sxs-lookup"><span data-stu-id="2a275-131">If setting up the app's host manually with `WebHostBuilder` (not using [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)) and the app is ever run directly on the Kestrel server (self-hosted), call `UseKestrel` before calling `UseIISIntegration`.</span></span> <span data-ttu-id="2a275-132">Pokud je obrácený pořadí, hostitel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="2a275-132">If the order is reversed, the host fails to start.</span></span>

* <span data-ttu-id="2a275-133">Odpojení klienta jsou zjištěny.</span><span class="sxs-lookup"><span data-stu-id="2a275-133">Client disconnects are detected.</span></span> <span data-ttu-id="2a275-134">[HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) token zrušení se zrušila, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="2a275-134">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="2a275-135">V ASP.NET Core 2.2.1 nebo starší, <xref:System.IO.Directory.GetCurrentDirectory*> vrátí adresáři pracovního procesu tím, že služba IIS spíše než adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *w3wp.exe*) .</span><span class="sxs-lookup"><span data-stu-id="2a275-135">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="2a275-136">Ukázkový kód, který nastaví aktuální adresář aplikace, najdete v článku [CurrentDirectoryHelpers třídy](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="2a275-136">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="2a275-137">Volání `SetCurrentDirectory` metody.</span><span class="sxs-lookup"><span data-stu-id="2a275-137">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="2a275-138">Následující volání <xref:System.IO.Directory.GetCurrentDirectory*> poskytují adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="2a275-138">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="2a275-139">Při hostování v procesu, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nevolá interně k inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="2a275-139">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="2a275-140">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaném k transformaci deklarací identity po každém ověření není ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="2a275-140">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="2a275-141">Při transformaci deklarací identity s <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace, volání <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> přidat ověřovací služby:</span><span class="sxs-lookup"><span data-stu-id="2a275-141">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="2a275-142">Model hostingu mimo proces</span><span class="sxs-lookup"><span data-stu-id="2a275-142">Out-of-process hosting model</span></span>

<span data-ttu-id="2a275-143">Jak nakonfigurovat aplikaci pro hostování mimo proces, použijte jednu z následujících postupů v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="2a275-143">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="2a275-144">Nezadávejte `<AspNetCoreHostingModel>` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="2a275-144">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="2a275-145">Pokud `<AspNetCoreHostingModel>` vlastnost není k dispozici v souboru, výchozí hodnota je `OutOfProcess`.</span><span class="sxs-lookup"><span data-stu-id="2a275-145">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="2a275-146">Nastavte hodnotu `<AspNetCoreHostingModel>` vlastnost `OutOfProcess` (hostování v procesu je nastavená pomocí `InProcess`):</span><span class="sxs-lookup"><span data-stu-id="2a275-146">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="2a275-147">[Kestrel](xref:fundamentals/servers/kestrel) server se používá místo protokolu HTTP serveru služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="2a275-147">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="2a275-148">Mimo proces [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> na:</span><span class="sxs-lookup"><span data-stu-id="2a275-148">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="2a275-149">Konfigurace portu a základní cesta server naslouchat požadavkům na při spuštění za modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a275-149">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="2a275-150">Nakonfigurujte hostitele tak, aby zachycení chyb při spuštění.</span><span class="sxs-lookup"><span data-stu-id="2a275-150">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="2a275-151">Hostování změny modelu</span><span class="sxs-lookup"><span data-stu-id="2a275-151">Hosting model changes</span></span>

<span data-ttu-id="2a275-152">Pokud `hostingModel` nastavení se změnilo v *web.config* souboru (podrobně [konfigurace pomocí souboru web.config](#configuration-with-webconfig) části), modul pro službu IIS recykluje pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="2a275-152">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="2a275-153">Pro službu IIS Express modul nebude recyklovat pracovní proces, ale místo toho aktivuje řádné vypnutí aktuální proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2a275-153">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="2a275-154">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="2a275-154">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="2a275-155">Název procesu</span><span class="sxs-lookup"><span data-stu-id="2a275-155">Process name</span></span>

<span data-ttu-id="2a275-156">`Process.GetCurrentProcess().ProcessName` sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (out-of-process).</span><span class="sxs-lookup"><span data-stu-id="2a275-156">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2a275-157">Modul ASP.NET Core je nativní modul služby IIS, která zpřístupní kanálu IIS, aby předával aplikace webové požadavky do back-endu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a275-157">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="2a275-158">Podporované verze Windows:</span><span class="sxs-lookup"><span data-stu-id="2a275-158">Supported Windows versions:</span></span>

* <span data-ttu-id="2a275-159">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="2a275-159">Windows 7 or later</span></span>
* <span data-ttu-id="2a275-160">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="2a275-160">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="2a275-161">Modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2a275-161">The module only works with Kestrel.</span></span> <span data-ttu-id="2a275-162">Modul je nekompatibilní s [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="2a275-162">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="2a275-163">Protože aplikace ASP.NET Core spuštění v procesu oddělit od pracovní proces služby IIS, zpracovává modul také procesu správy.</span><span class="sxs-lookup"><span data-stu-id="2a275-163">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="2a275-164">V modulu začne proces pro aplikace ASP.NET Core, když první žádost o přijetí a restartuje aplikaci, pokud ho dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="2a275-164">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="2a275-165">Toto je v podstatě stejné chování jako aplikace ASP.NET 4.x, na kterých běží v procesu ve službě IIS, která jsou spravována [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="2a275-165">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="2a275-166">Následující diagram znázorňuje vztah mezi službou IIS, že modul ASP.NET Core a aplikace:</span><span class="sxs-lookup"><span data-stu-id="2a275-166">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Modul ASP.NET Core](aspnet-core-module/_static/ancm-outofprocess.png)

<span data-ttu-id="2a275-168">Požadavky přicházejí z webu pro ovladač HTTP.sys režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="2a275-168">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="2a275-169">Ovladač směruje požadavky do služby IIS na webu nakonfigurovaný port, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2a275-169">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="2a275-170">V modulu předá požadavky Kestrel na náhodný port pro aplikaci, která není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="2a275-170">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="2a275-171">V modulu určuje port, přes proměnnou prostředí při spuštění a [Middleware pro integraci služby IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server tak, aby naslouchala na `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="2a275-171">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="2a275-172">Další kontroly jsou prováděny, a odmítne požadavky, které není pocházejí z modulu.</span><span class="sxs-lookup"><span data-stu-id="2a275-172">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="2a275-173">Modul nepodporuje předávání protokolu HTTPS, takže žádosti se předávají prostřednictvím protokolu HTTP i v případě, že byla přijata službou IIS přes protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2a275-173">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="2a275-174">Po Kestrel převezme žádosti z modulu, požadavek se vloží do kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="2a275-174">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="2a275-175">Zpracuje požadavek a předává je jako middleware kanálu `HttpContext` instanci aplikace logiky.</span><span class="sxs-lookup"><span data-stu-id="2a275-175">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="2a275-176">Middleware, které jsou přidány pomocí integrace služby IIS aktualizuje schéma, vzdálenou IP adresu a pathbase pro předání požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2a275-176">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="2a275-177">Odpověď aplikace je předán zpět do služby IIS, které nabízených oznámení je zpět klienta HTTP, který inicioval žádost.</span><span class="sxs-lookup"><span data-stu-id="2a275-177">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

<span data-ttu-id="2a275-178">Množství nativních modulů, jako je například ověřování Windows, zůstanou aktivní.</span><span class="sxs-lookup"><span data-stu-id="2a275-178">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="2a275-179">Další informace o moduly IIS aktivní s modul ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="2a275-179">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="2a275-180">Modul ASP.NET Core můžete:</span><span class="sxs-lookup"><span data-stu-id="2a275-180">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="2a275-181">Nastavení proměnných prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="2a275-181">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="2a275-182">Zaznamená stdout výstup do služby file storage pro řešení potíží při spuštění.</span><span class="sxs-lookup"><span data-stu-id="2a275-182">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="2a275-183">Předávání ověřovacích tokenů Windows.</span><span class="sxs-lookup"><span data-stu-id="2a275-183">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="2a275-184">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2a275-184">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="2a275-185">Pokyny o tom, jak nainstalovat modul ASP.NET Core najdete v tématu [instalaci sady .NET Core hostování](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="2a275-185">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="2a275-186">Konfigurace pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="2a275-186">Configuration with web.config</span></span>

<span data-ttu-id="2a275-187">Modul ASP.NET Core, nastavena `aspNetCore` část `system.webServer` uzlu na webu *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="2a275-187">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="2a275-188">Následující *web.config* soubor je publikován pro [nasazení závisí na architektuře](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="2a275-188">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="2a275-189">Následující *web.config* je publikována pro [samostatná nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="2a275-189">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="2a275-190"><xref:System.Configuration.SectionInformation.InheritInChildApplications*> Je nastavena na `false` k označení, že nastavení zadané v rámci [ \<umístění >](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element nedědí od neznámých aplikací, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="2a275-190">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

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

<span data-ttu-id="2a275-191">Když je aplikace nasazená na [služby Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` je nastavena cesta `\\?\%home%\LogFiles\stdout`.</span><span class="sxs-lookup"><span data-stu-id="2a275-191">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="2a275-192">Protokoly stdout a uloží ji *LogFiles* složce, která je na místě automaticky vytvořené službou.</span><span class="sxs-lookup"><span data-stu-id="2a275-192">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="2a275-193">Informace o konfiguraci dílčí aplikace služby IIS, naleznete v tématu <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="2a275-193">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="2a275-194">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="2a275-194">Attributes of the aspNetCore element</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="2a275-195">Atribut</span><span class="sxs-lookup"><span data-stu-id="2a275-195">Attribute</span></span> | <span data-ttu-id="2a275-196">Popis</span><span class="sxs-lookup"><span data-stu-id="2a275-196">Description</span></span> | <span data-ttu-id="2a275-197">Výchozí</span><span class="sxs-lookup"><span data-stu-id="2a275-197">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="2a275-198">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="2a275-198">Optional string attribute.</span></span></p><p><span data-ttu-id="2a275-199">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="2a275-199">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="2a275-200">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-200">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2a275-201">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="2a275-201">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="2a275-202">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-202">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2a275-203">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="2a275-203">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="2a275-204">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="2a275-204">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="2a275-205">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="2a275-205">Optional string attribute.</span></span></p><p><span data-ttu-id="2a275-206">Určuje model hostování jako v procesu (`InProcess`) nebo out-of-process (`OutOfProcess`).</span><span class="sxs-lookup"><span data-stu-id="2a275-206">Specifies the hosting model as in-process (`InProcess`) or out-of-process (`OutOfProcess`).</span></span></p> | `OutOfProcess` |
| `processesPerApplication` | <p><span data-ttu-id="2a275-207">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-207">Optional integer attribute.</span></span></p><p><span data-ttu-id="2a275-208">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="2a275-208">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="2a275-209">&dagger;Hostování v procesu, je omezena na hodnotu `1`.</span><span class="sxs-lookup"><span data-stu-id="2a275-209">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="2a275-210">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="2a275-210">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="2a275-211">Tento atribut bude v budoucí verzi odebrána.</span><span class="sxs-lookup"><span data-stu-id="2a275-211">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="2a275-212">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="2a275-212">Default: `1`</span></span><br><span data-ttu-id="2a275-213">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="2a275-213">Min: `1`</span></span><br><span data-ttu-id="2a275-214">Max: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="2a275-214">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="2a275-215">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="2a275-215">Required string attribute.</span></span></p><p><span data-ttu-id="2a275-216">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2a275-216">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="2a275-217">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="2a275-217">Relative paths are supported.</span></span> <span data-ttu-id="2a275-218">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="2a275-218">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="2a275-219">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-219">Optional integer attribute.</span></span></p><p><span data-ttu-id="2a275-220">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="2a275-220">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="2a275-221">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="2a275-221">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="2a275-222">Není podporováno s hostitelem v procesu.</span><span class="sxs-lookup"><span data-stu-id="2a275-222">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="2a275-223">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="2a275-223">Default: `10`</span></span><br><span data-ttu-id="2a275-224">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="2a275-224">Min: `0`</span></span><br><span data-ttu-id="2a275-225">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="2a275-225">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="2a275-226">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="2a275-226">Optional timespan attribute.</span></span></p><p><span data-ttu-id="2a275-227">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="2a275-227">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="2a275-228">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="2a275-228">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="2a275-229">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="2a275-229">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="2a275-230">Hostování v procesu, čeká modul app ke zpracování požadavku.</span><span class="sxs-lookup"><span data-stu-id="2a275-230">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="2a275-231">Platné hodnoty pro minuty a sekundy segmenty řetězce jsou v rozsahu 0 až 59.</span><span class="sxs-lookup"><span data-stu-id="2a275-231">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="2a275-232">Použití **60** v hodnotě pro minut nebo sekund výsledků *500 – Interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="2a275-232">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="2a275-233">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="2a275-233">Default: `00:02:00`</span></span><br><span data-ttu-id="2a275-234">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="2a275-234">Min: `00:00:00`</span></span><br><span data-ttu-id="2a275-235">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="2a275-235">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="2a275-236">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-236">Optional integer attribute.</span></span></p><p><span data-ttu-id="2a275-237">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="2a275-237">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="2a275-238">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="2a275-238">Default: `10`</span></span><br><span data-ttu-id="2a275-239">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="2a275-239">Min: `0`</span></span><br><span data-ttu-id="2a275-240">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="2a275-240">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="2a275-241">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-241">Optional integer attribute.</span></span></p><p><span data-ttu-id="2a275-242">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="2a275-242">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="2a275-243">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="2a275-243">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="2a275-244">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="2a275-244">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="2a275-245">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="2a275-245">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="2a275-246">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="2a275-246">Default: `120`</span></span><br><span data-ttu-id="2a275-247">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="2a275-247">Min: `0`</span></span><br><span data-ttu-id="2a275-248">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="2a275-248">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="2a275-249">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-249">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2a275-250">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="2a275-250">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="2a275-251">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="2a275-251">Optional string attribute.</span></span></p><p><span data-ttu-id="2a275-252">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="2a275-252">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="2a275-253">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="2a275-253">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="2a275-254">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="2a275-254">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="2a275-255">Všechny složky v cestě k dispozici jsou vytvořené v modulu při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="2a275-255">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="2a275-256">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru ( *.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="2a275-256">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="2a275-257">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="2a275-257">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| <span data-ttu-id="2a275-258">Atribut</span><span class="sxs-lookup"><span data-stu-id="2a275-258">Attribute</span></span> | <span data-ttu-id="2a275-259">Popis</span><span class="sxs-lookup"><span data-stu-id="2a275-259">Description</span></span> | <span data-ttu-id="2a275-260">Výchozí</span><span class="sxs-lookup"><span data-stu-id="2a275-260">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="2a275-261">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="2a275-261">Optional string attribute.</span></span></p><p><span data-ttu-id="2a275-262">Argumenty pro spustitelný soubor určený v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="2a275-262">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="2a275-263">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-263">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2a275-264">Při hodnotě true **502.5 – selhání procesu** Potlačené stránky a znakovou stránku 502 stav nakonfigurované v *web.config* má přednost před.</span><span class="sxs-lookup"><span data-stu-id="2a275-264">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="2a275-265">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-265">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2a275-266">Při hodnotě true se token předá podřízený proces naslouchání na ASPNETCORE_PORT % jako záhlaví "MS-ASPNETCORE-WINAUTHTOKEN" každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="2a275-266">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="2a275-267">Je odpovědností tento proces pro volání CloseHandle na tento token každý požadavek.</span><span class="sxs-lookup"><span data-stu-id="2a275-267">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="2a275-268">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-268">Optional integer attribute.</span></span></p><p><span data-ttu-id="2a275-269">Určuje počet instancí procesu zadaný v **processPath** nastavení, které lze se nespředený nahoru na app.</span><span class="sxs-lookup"><span data-stu-id="2a275-269">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="2a275-270">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="2a275-270">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="2a275-271">Tento atribut bude v budoucí verzi odebrána.</span><span class="sxs-lookup"><span data-stu-id="2a275-271">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="2a275-272">Výchozí hodnota: `1`</span><span class="sxs-lookup"><span data-stu-id="2a275-272">Default: `1`</span></span><br><span data-ttu-id="2a275-273">Min: `1`</span><span class="sxs-lookup"><span data-stu-id="2a275-273">Min: `1`</span></span><br><span data-ttu-id="2a275-274">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="2a275-274">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="2a275-275">Požadovaný atribut typu string.</span><span class="sxs-lookup"><span data-stu-id="2a275-275">Required string attribute.</span></span></p><p><span data-ttu-id="2a275-276">Cesta ke spustitelnému souboru, který spustí nějaký proces naslouchání požadavků protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2a275-276">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="2a275-277">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="2a275-277">Relative paths are supported.</span></span> <span data-ttu-id="2a275-278">Pokud cestu začíná `.`, cesta se považuje za kořeni webu.</span><span class="sxs-lookup"><span data-stu-id="2a275-278">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="2a275-279">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-279">Optional integer attribute.</span></span></p><p><span data-ttu-id="2a275-280">Určuje, kolikrát podle procesu **processPath** může při selhání za minutu.</span><span class="sxs-lookup"><span data-stu-id="2a275-280">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="2a275-281">Pokud je tento limit překročen, modulu zastaví spuštění procesu pro zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="2a275-281">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="2a275-282">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="2a275-282">Default: `10`</span></span><br><span data-ttu-id="2a275-283">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="2a275-283">Min: `0`</span></span><br><span data-ttu-id="2a275-284">Max: `100`</span><span class="sxs-lookup"><span data-stu-id="2a275-284">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="2a275-285">Atribut volitelný časový interval.</span><span class="sxs-lookup"><span data-stu-id="2a275-285">Optional timespan attribute.</span></span></p><p><span data-ttu-id="2a275-286">Určuje dobu, pro kterou modul ASP.NET Core čeká na odpověď z procesu naslouchání na ASPNETCORE_PORT %.</span><span class="sxs-lookup"><span data-stu-id="2a275-286">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="2a275-287">Ve verzích modul ASP.NET Core dodávané s verzí technologie ASP.NET Core 2.1 nebo novější `requestTimeout` je zadán v hodiny, minuty a sekundy.</span><span class="sxs-lookup"><span data-stu-id="2a275-287">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="2a275-288">Výchozí hodnota: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="2a275-288">Default: `00:02:00`</span></span><br><span data-ttu-id="2a275-289">Min: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="2a275-289">Min: `00:00:00`</span></span><br><span data-ttu-id="2a275-290">Max: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="2a275-290">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="2a275-291">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-291">Optional integer attribute.</span></span></p><p><span data-ttu-id="2a275-292">Doba trvání v sekundách, které modul čeká na spustitelný soubor na řádné vypnutí při *app_offline.htm* je detekován soubor.</span><span class="sxs-lookup"><span data-stu-id="2a275-292">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="2a275-293">Výchozí hodnota: `10`</span><span class="sxs-lookup"><span data-stu-id="2a275-293">Default: `10`</span></span><br><span data-ttu-id="2a275-294">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="2a275-294">Min: `0`</span></span><br><span data-ttu-id="2a275-295">Max: `600`</span><span class="sxs-lookup"><span data-stu-id="2a275-295">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="2a275-296">Volitelný celočíselný atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-296">Optional integer attribute.</span></span></p><p><span data-ttu-id="2a275-297">Doba trvání v sekundách, které modul čeká na spustitelný soubor ke spuštění procesu naslouchání na portu.</span><span class="sxs-lookup"><span data-stu-id="2a275-297">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="2a275-298">Pokud je překročen časový limit, modul ukončí proces.</span><span class="sxs-lookup"><span data-stu-id="2a275-298">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="2a275-299">Modul se pokusí znovu spustit proces, když ji dostane novou žádost a pokusit se restartovat proces na dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** málokdy za posledních minuta se zajištěním provozu.</span><span class="sxs-lookup"><span data-stu-id="2a275-299">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="2a275-300">Hodnota 0 (nula) je **ne** za neomezený časový limit.</span><span class="sxs-lookup"><span data-stu-id="2a275-300">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="2a275-301">Výchozí hodnota: `120`</span><span class="sxs-lookup"><span data-stu-id="2a275-301">Default: `120`</span></span><br><span data-ttu-id="2a275-302">Min: `0`</span><span class="sxs-lookup"><span data-stu-id="2a275-302">Min: `0`</span></span><br><span data-ttu-id="2a275-303">Max: `3600`</span><span class="sxs-lookup"><span data-stu-id="2a275-303">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="2a275-304">Volitelný logický atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-304">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="2a275-305">Při hodnotě true se **stdout** a **stderr** pro proces určený v **processPath** se přesměrují do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="2a275-305">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="2a275-306">Volitelný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="2a275-306">Optional string attribute.</span></span></p><p><span data-ttu-id="2a275-307">Určuje relativní nebo absolutní cestu, pro kterou **stdout** a **stderr** z procesu podle **processPath** přihlášeni.</span><span class="sxs-lookup"><span data-stu-id="2a275-307">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="2a275-308">Relativní cesty jsou relativní vzhledem k kořen webu.</span><span class="sxs-lookup"><span data-stu-id="2a275-308">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="2a275-309">Libovolnou cestu od `.` jsou relativní vzhledem k webu kořenové a všechny ostatní cesty jsou považovány za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="2a275-309">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="2a275-310">Všechny složky v cestě k dispozici musí existovat v pořadí pro modul pro vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="2a275-310">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="2a275-311">Pomocí oddělovače podtržítko, časové razítko, ID procesu a přípona souboru ( *.log*) jsou přidány na poslední segment **stdoutLogFile** cestu.</span><span class="sxs-lookup"><span data-stu-id="2a275-311">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="2a275-312">Pokud `.\logs\stdout` je zadaný jako hodnota, je uložen protokolu příklad stdout jako *stdout_20180205194132_1934.log* v *protokoly* složky při uložení 5 2. 2018 v 19:41:32 s ID procesu sady 1934.</span><span class="sxs-lookup"><span data-stu-id="2a275-312">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

::: moniker-end

### <a name="setting-environment-variables"></a><span data-ttu-id="2a275-313">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="2a275-313">Setting environment variables</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2a275-314">Proměnné prostředí se dá nastavit pro proces v `processPath` atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-314">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="2a275-315">Zadat proměnné prostředí s `<environmentVariable>` podřízený prvek `<environmentVariables>` prvek kolekce.</span><span class="sxs-lookup"><span data-stu-id="2a275-315">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="2a275-316">Proměnné prostředí nastavené v této části přednost systémové proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="2a275-316">Environment variables set in this section take precedence over system environment variables.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2a275-317">Proměnné prostředí se dá nastavit pro proces v `processPath` atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-317">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="2a275-318">Zadat proměnné prostředí s `<environmentVariable>` podřízený prvek `<environmentVariables>` prvek kolekce.</span><span class="sxs-lookup"><span data-stu-id="2a275-318">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="2a275-319">Nastavte proměnné prostředí nastavené v této části konflikt pomocí proměnných prostředí systému se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="2a275-319">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="2a275-320">Pokud proměnná prostředí je nastavena v obou *web.config* souborů a systému úroveň ve Windows, hodnotu z *web.config* souboru bude připojeno k hodnotu proměnné prostředí systému (pro například `ASPNETCORE_ENVIRONMENT: Development;Development`), která zabraňuje spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="2a275-320">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

::: moniker-end

<span data-ttu-id="2a275-321">Následující příklad nastaví dvou proměnných prostředí.</span><span class="sxs-lookup"><span data-stu-id="2a275-321">The following example sets two environment variables.</span></span> <span data-ttu-id="2a275-322">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace tak, aby `Development`.</span><span class="sxs-lookup"><span data-stu-id="2a275-322">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="2a275-323">Vývojáři mohou dočasně nastaví tuto hodnotu *web.config* souboru, aby bylo možné vynutit [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) načíst při ladění aplikace výjimky.</span><span class="sxs-lookup"><span data-stu-id="2a275-323">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="2a275-324">`CONFIG_DIR` je příkladem proměnné prostředí, kam má vývojář zapisovat kód, který čte hodnoty při spuštění tvoří cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="2a275-324">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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
> <span data-ttu-id="2a275-325">Alternativa k nastavení prostředí přímo v *web.config* se zahrnou `<EnvironmentName>` vlastnost v profilu publikování ( *.pubxml*) nebo soubor projektu.</span><span class="sxs-lookup"><span data-stu-id="2a275-325">An alternative to setting the environment directly in *web.config* is to include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="2a275-326">Tento přístup nastaví prostředí *web.config* při publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="2a275-326">This approach sets the environment in *web.config* when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

::: moniker-end

> [!WARNING]
> <span data-ttu-id="2a275-327">Nastavit pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí, aby `Development` na přípravy a testování serverů, které nejsou dostupné k nedůvěryhodným sítím, jako je Internet.</span><span class="sxs-lookup"><span data-stu-id="2a275-327">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="appofflinehtm"></a><span data-ttu-id="2a275-328">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="2a275-328">app_offline.htm</span></span>

<span data-ttu-id="2a275-329">Pokud soubor s názvem *app_offline.htm* je zjištěna v kořenovém adresáři aplikace, že modul ASP.NET Core se pokusí řádné vypnutí aplikace a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="2a275-329">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="2a275-330">Pokud aplikace pořád běží po dobu v sekundách podle `shutdownTimeLimit`, že modul ASP.NET Core ukončuje spuštěnému procesu.</span><span class="sxs-lookup"><span data-stu-id="2a275-330">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="2a275-331">Zatímco *app_offline.htm* soubor je k dispozici, že modul ASP.NET Core reaguje na požadavky odesílá zpět obsah *app_offline.htm* souboru.</span><span class="sxs-lookup"><span data-stu-id="2a275-331">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="2a275-332">Když *app_offline.htm* soubor bude odstraněn, příští žádosti o spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="2a275-332">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2a275-333">Pokud používáte model hostingu mimo proces, aplikace nemusí vypnout okamžitě při otevření připojení.</span><span class="sxs-lookup"><span data-stu-id="2a275-333">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="2a275-334">Připojení soketu websocket bylo třeba může způsobit prodlevu při ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="2a275-334">For example, a websocket connection may delay app shut down.</span></span>

::: moniker-end

## <a name="start-up-error-page"></a><span data-ttu-id="2a275-335">Spuštění chybovou stránku</span><span class="sxs-lookup"><span data-stu-id="2a275-335">Start-up error page</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2a275-336">Hostování v procesu a mimo proces vytvoření vlastní chybové stránky, když selžou a spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="2a275-336">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="2a275-337">Pokud se nepodaří najít buď nebo na více instancí procesu žádosti o obslužnou rutinu, že modul ASP.NET Core *500.0 – Chyba načtení obslužné rutiny v procesu/Out – proces* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="2a275-337">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="2a275-338">Pro hostování v procesu, pokud se nepodaří spustit aplikaci, že modul ASP.NET Core *500.30 - Start selhání* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="2a275-338">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="2a275-339">Pro hostování mimo proces, pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="2a275-339">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="2a275-340">Chcete-li potlačit tuto stránku a vrátit se na výchozí služby IIS 5xx stav znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-340">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="2a275-341">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="2a275-341">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2a275-342">Pokud se nespustí back-endový proces nebo back-endový proces spustí ale nebude moci poslouchat na konfigurovaném portu, že modul ASP.NET Core *502.5 – selhání procesu* se zobrazí stav znakovou stránku.</span><span class="sxs-lookup"><span data-stu-id="2a275-342">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="2a275-343">Chcete-li potlačit tuto stránku a vrátit se na výchozí stav služby IIS 502 znakovou stránku, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="2a275-343">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="2a275-344">Další informace o konfiguraci vlastních chybových zpráv, najdete v části [chyby protokolu HTTP \<httpErrors >](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="2a275-344">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

![502.5 proces selhání stav znaková stránka](aspnet-core-module/_static/ANCM-502_5.png)

::: moniker-end

## <a name="log-creation-and-redirection"></a><span data-ttu-id="2a275-346">Vytvoření protokolu a přesměrování</span><span class="sxs-lookup"><span data-stu-id="2a275-346">Log creation and redirection</span></span>

<span data-ttu-id="2a275-347">Modul ASP.NET Core přesměruje výstup stdout a stderr console na disk, pokud `stdoutLogEnabled` a `stdoutLogFile` atributy `aspNetCore` element jsou nastavené.</span><span class="sxs-lookup"><span data-stu-id="2a275-347">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="2a275-348">Všechny složky v `stdoutLogFile` cesta vytvářejí modulem, když se vytvoří soubor protokolu.</span><span class="sxs-lookup"><span data-stu-id="2a275-348">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="2a275-349">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="2a275-349">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="2a275-350">Protokoly nejsou střídán, pokud dojde k recyklování procesů/restartování.</span><span class="sxs-lookup"><span data-stu-id="2a275-350">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="2a275-351">Je odpovědností hostitel k omezení místa na disku, které využijete v protokolech.</span><span class="sxs-lookup"><span data-stu-id="2a275-351">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="2a275-352">Použití protokolu stdout se doporučuje jenom pro řešení potíží při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="2a275-352">Using the stdout log is only recommended for troubleshooting app startup issues.</span></span> <span data-ttu-id="2a275-353">Nepoužívejte stdout protokolu pro účely protokolování obecné aplikace.</span><span class="sxs-lookup"><span data-stu-id="2a275-353">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="2a275-354">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="2a275-354">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="2a275-355">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="2a275-355">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="2a275-356">Časové razítko a soubor rozšíření jsou přidány automaticky při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="2a275-356">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="2a275-357">Název souboru protokolu se skládá připojením časové razítko, ID procesu a přípona souboru ( *.log*) na poslední segment `stdoutLogFile` cestu (obvykle *stdout*) oddělené podtržítka.</span><span class="sxs-lookup"><span data-stu-id="2a275-357">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="2a275-358">Pokud `stdoutLogFile` cesta končí *stdout*, má název souboru protokolu pro aplikaci s PID 1934 vytvořili 5 2. 2018 v 19:42:32 *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="2a275-358">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2a275-359">Pokud `stdoutLogEnabled` má hodnotu false, chyby, ke kterým dochází při spuštění aplikace se zachytí a do protokolu událostí, protože ho až 30 KB.</span><span class="sxs-lookup"><span data-stu-id="2a275-359">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="2a275-360">Po spuštění se zahodí všechny další protokoly.</span><span class="sxs-lookup"><span data-stu-id="2a275-360">After startup, all additional logs are discarded.</span></span>

::: moniker-end

<span data-ttu-id="2a275-361">Následující ukázka `aspNetCore` element konfiguruje stdout protokolování pro aplikace hostované ve službě Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="2a275-361">The following sample `aspNetCore` element configures stdout logging for an app hosted in Azure App Service.</span></span> <span data-ttu-id="2a275-362">Místní cesta nebo cesta ke sdílené položce je přijatelné pro místní přihlášení.</span><span class="sxs-lookup"><span data-stu-id="2a275-362">A local path or network share path is acceptable for local logging.</span></span> <span data-ttu-id="2a275-363">Ověřte, jestli uživatel identita fondu aplikací má oprávnění k zápisu do zadaná výstupní cesta.</span><span class="sxs-lookup"><span data-stu-id="2a275-363">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

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

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="2a275-364">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="2a275-364">Enhanced diagnostic logs</span></span>

<span data-ttu-id="2a275-365">Modul ASP.NET Core je konfigurovat, a poskytují rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="2a275-365">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="2a275-366">Přidat `<handlerSettings>` elementu `<aspNetCore>` prvek *web.config*. Nastavení `debugLevel` k `TRACE` zpřístupňuje větší věrnost diagnostické informace:</span><span class="sxs-lookup"><span data-stu-id="2a275-366">Add the `<handlerSettings>` element to the `<aspNetCore>` element in *web.config*. Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2a275-367">Všechny složky v cestě (*protokoly* v předchozím příkladu) vytvořené modulem při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="2a275-367">Any folders in the path (*logs* in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="2a275-368">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="2a275-368">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2a275-369">Složky v cestě k dispozici na `<handlerSetting>` hodnotu (*protokoly* v předchozím příkladu) nebyly vytvořeny pomocí modulu automaticky a měla by existovat předem v nasazení.</span><span class="sxs-lookup"><span data-stu-id="2a275-369">Folders in the path provided to the `<handlerSetting>` value (*logs* in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="2a275-370">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou zapsány protokoly (použijte `IIS AppPool\<app_pool_name>` poskytnout oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="2a275-370">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2a275-371">Ladit úrovně (`debugLevel`) hodnoty může obsahovat úroveň a umístění.</span><span class="sxs-lookup"><span data-stu-id="2a275-371">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="2a275-372">Úrovně (v pořadí od nejméně na nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="2a275-372">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="2a275-373">CHYBA</span><span class="sxs-lookup"><span data-stu-id="2a275-373">ERROR</span></span>
* <span data-ttu-id="2a275-374">UPOZORNĚNÍ</span><span class="sxs-lookup"><span data-stu-id="2a275-374">WARNING</span></span>
* <span data-ttu-id="2a275-375">INFORMACE O</span><span class="sxs-lookup"><span data-stu-id="2a275-375">INFO</span></span>
* <span data-ttu-id="2a275-376">TRACE</span><span class="sxs-lookup"><span data-stu-id="2a275-376">TRACE</span></span>

<span data-ttu-id="2a275-377">Umístění (umístění více jsou povoleny):</span><span class="sxs-lookup"><span data-stu-id="2a275-377">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="2a275-378">KONZOLY</span><span class="sxs-lookup"><span data-stu-id="2a275-378">CONSOLE</span></span>
* <span data-ttu-id="2a275-379">PROTOKOL UDÁLOSTÍ</span><span class="sxs-lookup"><span data-stu-id="2a275-379">EVENTLOG</span></span>
* <span data-ttu-id="2a275-380">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="2a275-380">FILE</span></span>

<span data-ttu-id="2a275-381">Obslužná rutina nastavení se dá zadat i prostřednictvím proměnné prostředí:</span><span class="sxs-lookup"><span data-stu-id="2a275-381">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="2a275-382">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="2a275-382">`ASPNETCORE_MODULE_DEBUG_FILE` &ndash; Path to the debug log file.</span></span> <span data-ttu-id="2a275-383">(Výchozí: *aspnetcore-debug.log*)</span><span class="sxs-lookup"><span data-stu-id="2a275-383">(Default: *aspnetcore-debug.log*)</span></span>
* <span data-ttu-id="2a275-384">`ASPNETCORE_MODULE_DEBUG` &ndash; Ladit nastavení úrovně.</span><span class="sxs-lookup"><span data-stu-id="2a275-384">`ASPNETCORE_MODULE_DEBUG` &ndash; Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="2a275-385">Proveďte **není** protokolování ladění povoleno v nasazení pro déle, než se požaduje chcete vyřešit nějaký problém.</span><span class="sxs-lookup"><span data-stu-id="2a275-385">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="2a275-386">Velikost souboru protokolu není omezený.</span><span class="sxs-lookup"><span data-stu-id="2a275-386">The size of the log isn't limited.</span></span> <span data-ttu-id="2a275-387">Opuštění protokol ladění povoleno může vyčerpat dostupné místo na disku a chybách u aplikací na serveru nebo služby app service.</span><span class="sxs-lookup"><span data-stu-id="2a275-387">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

::: moniker-end

<span data-ttu-id="2a275-388">Naleznete v tématu [konfigurace pomocí souboru web.config](#configuration-with-webconfig) příklad `aspNetCore` prvek *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="2a275-388">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the *web.config* file.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="modify-the-stack-size"></a><span data-ttu-id="2a275-389">Změnit velikost zásobníku</span><span class="sxs-lookup"><span data-stu-id="2a275-389">Modify the stack size</span></span>

<span data-ttu-id="2a275-390">Nakonfigurujte velikost zásobníku spravovaného pomocí `stackSize` nastavení v bajtech.</span><span class="sxs-lookup"><span data-stu-id="2a275-390">Configure the managed stack size using the `stackSize` setting in bytes.</span></span> <span data-ttu-id="2a275-391">Výchozí velikost je `1048576` bajtů (1 MB).</span><span class="sxs-lookup"><span data-stu-id="2a275-391">The default size is `1048576` bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="InProcess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

::: moniker-end

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="2a275-392">Konfigurace proxy serveru používá protokol HTTP a token pro párování</span><span class="sxs-lookup"><span data-stu-id="2a275-392">Proxy configuration uses HTTP protocol and a pairing token</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2a275-393">*Platí jenom pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="2a275-393">*Only applies to out-of-process hosting.*</span></span>

::: moniker-end

<span data-ttu-id="2a275-394">Proxy server mezi modul ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="2a275-394">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="2a275-395">Pomocí protokolu HTTP je optimalizace výkonu, kdy přenos dat mezi modulu a Kestrel probíhá na adresu zpětné smyčky ze síťového rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2a275-395">Using HTTP is a performance optimization, where the traffic between the module and Kestrel takes place on a loopback address off of the network interface.</span></span> <span data-ttu-id="2a275-396">Neexistuje žádné riziko odposloucháváním provozu mezi modulu a Kestrel z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="2a275-396">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="2a275-397">Párování token se používá k zajištění, že požadavků přijatých službou Kestrel byly směrovány přes proxy server službou IIS a nebyl dodán z nějakého jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="2a275-397">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="2a275-398">Vytvoření a nastavení do proměnné prostředí párování token (`ASPNETCORE_TOKEN`) modulu.</span><span class="sxs-lookup"><span data-stu-id="2a275-398">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="2a275-399">Párování token byl nastavený i do záhlaví (`MS-ASPNETCORE-TOKEN`) na všechny požadavky směrovány přes proxy server.</span><span class="sxs-lookup"><span data-stu-id="2a275-399">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="2a275-400">Služba IIS Middleware kontroly požadavku že přijme potvrďte, že odpovídá párování hodnota tokenu hlavičky hodnotu proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="2a275-400">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="2a275-401">Pokud token hodnoty se neshodují, žádost je zaznamenána a odmítnut.</span><span class="sxs-lookup"><span data-stu-id="2a275-401">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="2a275-402">Párování proměnná tokenu prostředí a přenos dat mezi modulu a Kestrel nejsou dostupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="2a275-402">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="2a275-403">Nainstalovat bez mého párování hodnota tokenu nelze útočník odesílat požadavky, které obejít kontrolu v IIS middlewaru.</span><span class="sxs-lookup"><span data-stu-id="2a275-403">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="2a275-404">Modul ASP.NET Core s službu IIS sdílenou konfiguraci</span><span class="sxs-lookup"><span data-stu-id="2a275-404">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="2a275-405">Modul ASP.NET Core instalační program spustí s oprávněními **TrustedInstaller** účtu.</span><span class="sxs-lookup"><span data-stu-id="2a275-405">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="2a275-406">Vzhledem k tomu, že místní systémový účet mít nezmění oprávnění pro sdílenou složku cesta používaná systémem sdílené konfiguraci IIS, instalační program vyvolá chybu při pokusu o konfiguraci nastavení modulu v odepření přístupu *applicationHost.config*  soubor ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="2a275-406">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2a275-407">Pokud používáte sdílenou konfiguraci IIS na stejném počítači jako instalace služby IIS, spusťte instalační program sady hostování technologie ASP.NET Core s `OPT_NO_SHARED_CONFIG_CHECK` parametr nastaven na `1`:</span><span class="sxs-lookup"><span data-stu-id="2a275-407">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="2a275-408">Pokud cesta ke sdílené konfigurace není ve stejném počítači jako instalace služby IIS, postupujte takto:</span><span class="sxs-lookup"><span data-stu-id="2a275-408">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="2a275-409">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="2a275-409">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="2a275-410">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="2a275-410">Run the installer.</span></span>
1. <span data-ttu-id="2a275-411">Export aktualizovaný *applicationHost.config* souboru do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="2a275-411">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="2a275-412">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="2a275-412">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2a275-413">Pokud používáte sdílenou konfiguraci IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="2a275-413">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="2a275-414">Zakážete sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="2a275-414">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="2a275-415">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="2a275-415">Run the installer.</span></span>
1. <span data-ttu-id="2a275-416">Export aktualizovaný *applicationHost.config* souboru do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="2a275-416">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="2a275-417">Znovu povolte sdílenou konfiguraci IIS.</span><span class="sxs-lookup"><span data-stu-id="2a275-417">Re-enable the IIS Shared Configuration.</span></span>

::: moniker-end

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="2a275-418">Verze modulu a hostování sady Instalační protokoly</span><span class="sxs-lookup"><span data-stu-id="2a275-418">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="2a275-419">Chcete-li zjistit verzi nainstalovaný modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2a275-419">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="2a275-420">V hostitelském systému, přejděte na *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="2a275-420">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="2a275-421">Vyhledejte *aspnetcore.dll* souboru.</span><span class="sxs-lookup"><span data-stu-id="2a275-421">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="2a275-422">Klikněte pravým tlačítkem na soubor a vyberte **vlastnosti** z kontextové nabídky.</span><span class="sxs-lookup"><span data-stu-id="2a275-422">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="2a275-423">Vyberte **podrobnosti** kartu. **Verze souboru** a **verze produktu** představují nainstalovaná verze modulu.</span><span class="sxs-lookup"><span data-stu-id="2a275-423">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="2a275-424">Instalační protokoly hostování sady prostředků modulu se nacházejí v *C:\\uživatelé\\% UserName %\\AppData\\místní\\Temp*. Soubor *dd_DotNetCoreWinSvrHosting__\<časové razítko > _000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="2a275-424">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="2a275-425">Modul, schéma a konfiguraci umístění souborů</span><span class="sxs-lookup"><span data-stu-id="2a275-425">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="2a275-426">Modul</span><span class="sxs-lookup"><span data-stu-id="2a275-426">Module</span></span>

<span data-ttu-id="2a275-427">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="2a275-427">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="2a275-428">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2a275-428">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="2a275-429">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2a275-429">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="2a275-430">%ProgramFiles%\IIS\Asp.NET core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2a275-430">%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="2a275-431">% ProgramFiles (x86) %\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2a275-431">%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

<span data-ttu-id="2a275-432">**Služba IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="2a275-432">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="2a275-433">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2a275-433">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="2a275-434">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="2a275-434">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="2a275-435">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2a275-435">%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

* <span data-ttu-id="2a275-436">% ProgramFiles (x86) %\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span><span class="sxs-lookup"><span data-stu-id="2a275-436">%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll</span></span>

::: moniker-end

### <a name="schema"></a><span data-ttu-id="2a275-437">Schéma</span><span class="sxs-lookup"><span data-stu-id="2a275-437">Schema</span></span>

<span data-ttu-id="2a275-438">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="2a275-438">**IIS**</span></span>

* <span data-ttu-id="2a275-439">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2a275-439">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="2a275-440">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.XML</span><span class="sxs-lookup"><span data-stu-id="2a275-440">%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

<span data-ttu-id="2a275-441">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2a275-441">**IIS Express**</span></span>

* <span data-ttu-id="2a275-442">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="2a275-442">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="2a275-443">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span><span class="sxs-lookup"><span data-stu-id="2a275-443">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="2a275-444">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="2a275-444">Configuration</span></span>

<span data-ttu-id="2a275-445">**SLUŽBA IIS**</span><span class="sxs-lookup"><span data-stu-id="2a275-445">**IIS**</span></span>

* <span data-ttu-id="2a275-446">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2a275-446">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="2a275-447">**Služba IIS Express**</span><span class="sxs-lookup"><span data-stu-id="2a275-447">**IIS Express**</span></span>

* <span data-ttu-id="2a275-448">Visual Studio: {Kořenový adresář aplikace}\\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="2a275-448">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="2a275-449">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="2a275-449">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="2a275-450">Soubory můžete najít tak, že *aspnetcore* v *applicationHost.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="2a275-450">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a275-451">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2a275-451">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* [<span data-ttu-id="2a275-452">Úložiště GitHub modulu jádra ASP.NET (odkaz na zdroj)</span><span class="sxs-lookup"><span data-stu-id="2a275-452">ASP.NET Core Module GitHub repository (reference source)</span></span>](https://github.com/aspnet/AspNetCoreModule)
* <xref:host-and-deploy/iis/modules>
