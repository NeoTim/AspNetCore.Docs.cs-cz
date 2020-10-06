---
title: Modul ASP.NET Core
author: rick-anderson
description: Přečtěte si o modulu ASP.NET Core pro hostování ASP.NET Corech aplikací pomocí služby IIS.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: 8ee9ab2b598bc8ff62faa45a5666615ee7ab239b
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754668"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="0c641-103">Modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0c641-103">ASP.NET Core Module</span></span>

<span data-ttu-id="0c641-104">Díky [Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Novák Rossův](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [sourabh Shirhatti](https://twitter.com/sshirhatti)a [Justin](https://github.com/jkotalik) Kotalik</span><span class="sxs-lookup"><span data-stu-id="0c641-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0c641-105">Modul ASP.NET Core je nativní modul služby IIS, který se připojuje k kanálu služby IIS a umožňuje ASP.NET Core aplikacím pracovat se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="0c641-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline, allowing ASP.NET Core applications to work with IIS.</span></span> <span data-ttu-id="0c641-106">Spusťte ASP.NET Core aplikace pomocí služby IIS pomocí těchto akcí:</span><span class="sxs-lookup"><span data-stu-id="0c641-106">Run ASP.NET Core apps with IIS by either:</span></span> 

* <span data-ttu-id="0c641-107">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS ( `w3wp.exe` ), který se nazývá [model hostování v rámci procesu](xref:host-and-deploy/iis/in-process-hosting).</span><span class="sxs-lookup"><span data-stu-id="0c641-107">Hosting an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](xref:host-and-deploy/iis/in-process-hosting).</span></span>
* <span data-ttu-id="0c641-108">Přesměrování webových požadavků do back-endu ASP.NET Core aplikace, na které běží server Kestrel, se nazývá [model hostování mimo proces](xref:host-and-deploy/iis/out-of-process-hosting).</span><span class="sxs-lookup"><span data-stu-id="0c641-108">Forwarding web requests to a backend ASP.NET Core app running the Kestrel server, called the [out-of-process hosting model](xref:host-and-deploy/iis/out-of-process-hosting).</span></span>

<span data-ttu-id="0c641-109">Existují kompromisy mezi jednotlivými hostujícími modely.</span><span class="sxs-lookup"><span data-stu-id="0c641-109">There are trade-offs between each of the hosting models.</span></span> <span data-ttu-id="0c641-110">Ve výchozím nastavení se model hostování v rámci procesu používá kvůli lepšímu výkonu a diagnostice.</span><span class="sxs-lookup"><span data-stu-id="0c641-110">By default, the in-process hosting model is used due to better performance and diagnostics.</span></span>

## <a name="install-aspnet-core-module"></a><span data-ttu-id="0c641-111">Nainstalovat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0c641-111">Install ASP.NET Core Module</span></span>

<span data-ttu-id="0c641-112">Stáhněte instalační program pomocí následujícího odkazu:</span><span class="sxs-lookup"><span data-stu-id="0c641-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="0c641-113">Aktuální instalační program sady hostujících sad .NET Core (přímé stahování)</span><span class="sxs-lookup"><span data-stu-id="0c641-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="0c641-114">Další podrobnosti o tom, jak nainstalovat modul ASP.NET Core nebo instalovat jiné verze modulu, najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="0c641-114">For more details instructions on how to install the ASP.NET Core Module, or installing different versions of the module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

<span data-ttu-id="0c641-115">Kurz týkající se publikování ASP.NET Core aplikace na server služby IIS najdete v tématu <xref:tutorials/publish-to-iis> .</span><span class="sxs-lookup"><span data-stu-id="0c641-115">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="0c641-116">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="0c641-116">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="0c641-117">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS ( `w3wp.exe` ), který se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="0c641-117">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="0c641-118">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="0c641-118">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="0c641-119">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="0c641-119">Supported Windows versions:</span></span>

* <span data-ttu-id="0c641-120">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="0c641-120">Windows 7 or later</span></span>
* <span data-ttu-id="0c641-121">Windows Server 2012 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="0c641-121">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="0c641-122">Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS nazvanou IIS HTTP Server ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="0c641-122">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="0c641-123">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0c641-123">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="0c641-124">Modul nefunguje s [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="0c641-124">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="0c641-125">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="0c641-125">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="0c641-126">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="0c641-126">In-process hosting model</span></span>

<span data-ttu-id="0c641-127">ASP.NET Core aplikace jako výchozí pro model hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="0c641-127">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="0c641-128">Následující vlastnosti platí při hostování v procesu:</span><span class="sxs-lookup"><span data-stu-id="0c641-128">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="0c641-129">`IISHttpServer`Místo serveru [Kestrel](xref:fundamentals/servers/kestrel) se používá server HTTP služby IIS ().</span><span class="sxs-lookup"><span data-stu-id="0c641-129">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="0c641-130">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="0c641-130">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="0c641-131">Zaregistrujte `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="0c641-131">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="0c641-132">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0c641-132">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="0c641-133">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="0c641-133">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="0c641-134">[Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="0c641-134">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="0c641-135">Sdílení fondu aplikací mezi aplikacemi není podporováno.</span><span class="sxs-lookup"><span data-stu-id="0c641-135">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="0c641-136">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0c641-136">Use one app pool per app.</span></span>

* <span data-ttu-id="0c641-137">Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručního umístění [ `app_offline.htm` souboru do nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="0c641-137">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [`app_offline.htm` file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="0c641-138">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-138">For example, a WebSocket connection may delay app shut down.</span></span>

* <span data-ttu-id="0c641-139">Architektura (bitová verze) aplikace a nainstalovaného modulu runtime (x64 nebo x86) se musí shodovat s architekturou fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="0c641-139">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="0c641-140">Zjistila se odpojení klienta.</span><span class="sxs-lookup"><span data-stu-id="0c641-140">Client disconnects are detected.</span></span> <span data-ttu-id="0c641-141">[`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*)Token zrušení se zruší, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="0c641-141">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="0c641-142">V ASP.NET Core 2.2.1 nebo starším <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například `C:\Windows\System32\inetsrv` pro `w3wp.exe` ).</span><span class="sxs-lookup"><span data-stu-id="0c641-142">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, `C:\Windows\System32\inetsrv` for `w3wp.exe`).</span></span>

  <span data-ttu-id="0c641-143">Vzorový kód, který nastaví aktuální adresář aplikace, naleznete v tématu [ `CurrentDirectoryHelpers` Třída](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="0c641-143">For sample code that sets the app's current directory, see the [`CurrentDirectoryHelpers` class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="0c641-144">Zavolejte `SetCurrentDirectory` metodu.</span><span class="sxs-lookup"><span data-stu-id="0c641-144">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="0c641-145">Následná volání pro <xref:System.IO.Directory.GetCurrentDirectory*> poskytnutí adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-145">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="0c641-146">Při hostování v procesu <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> není volána interně pro inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="0c641-146">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="0c641-147">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="0c641-147">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="0c641-148">Při transformaci deklarací s <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementací zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> na přidat ověřovací služby:</span><span class="sxs-lookup"><span data-stu-id="0c641-148">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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
  
  * <span data-ttu-id="0c641-149">[Nasazení webového balíčku (v jednom souboru)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) nejsou podporována.</span><span class="sxs-lookup"><span data-stu-id="0c641-149">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="0c641-150">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="0c641-150">Out-of-process hosting model</span></span>

<span data-ttu-id="0c641-151">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti na `OutOfProcess` soubor projektu ( `.csproj` ):</span><span class="sxs-lookup"><span data-stu-id="0c641-151">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="0c641-152">Hostování v rámci procesu je nastaveno na `InProcess` , což je výchozí hodnota.</span><span class="sxs-lookup"><span data-stu-id="0c641-152">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="0c641-153">Hodnota rozlišuje `<AspNetCoreHostingModel>` malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="0c641-153">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="0c641-154">[Kestrel](xref:fundamentals/servers/kestrel) Místo http serveru IIS () se používá server Kestrel `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="0c641-154">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="0c641-155">Pro procesy mimo proces [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> :</span><span class="sxs-lookup"><span data-stu-id="0c641-155">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="0c641-156">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0c641-156">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="0c641-157">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="0c641-157">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="0c641-158">Změny modelu hostování</span><span class="sxs-lookup"><span data-stu-id="0c641-158">Hosting model changes</span></span>

<span data-ttu-id="0c641-159">Pokud se `hostingModel` nastavení v `web.config` souboru změní (vysvětluje v části [konfigurace s `web.config` ](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="0c641-159">If the `hostingModel` setting is changed in the `web.config` file (explained in the [Configuration with `web.config`](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="0c641-160">V případě IIS Express modul neprovádí recyklaci pracovního procesu, ale místo toho aktivuje bezproblémové vypnutí aktuálního procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="0c641-160">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="0c641-161">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="0c641-161">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="0c641-162">Název procesu</span><span class="sxs-lookup"><span data-stu-id="0c641-162">Process name</span></span>

<span data-ttu-id="0c641-163">`Process.GetCurrentProcess().ProcessName`sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (mimo proces).</span><span class="sxs-lookup"><span data-stu-id="0c641-163">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="0c641-164">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="0c641-164">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="0c641-165">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="0c641-165">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="0c641-166">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="0c641-166">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="0c641-167">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="0c641-167">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="0c641-168">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="0c641-168">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="0c641-169">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="0c641-169">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="0c641-170">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0c641-170">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="0c641-171">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="0c641-171">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="0c641-172">Konfigurace s web.config</span><span class="sxs-lookup"><span data-stu-id="0c641-172">Configuration with web.config</span></span>

<span data-ttu-id="0c641-173">Modul ASP.NET Core je nakonfigurován s `aspNetCore` částí `system.webServer` uzlu v souboru *web.config* webu.</span><span class="sxs-lookup"><span data-stu-id="0c641-173">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="0c641-174">Následující `web.config` soubor je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core pro zpracování požadavků lokality:</span><span class="sxs-lookup"><span data-stu-id="0c641-174">The following `web.config` file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="0c641-175">Následující *web.config* jsou publikovány pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="0c641-175">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="0c641-176"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Vlastnost je nastavena na hodnotu `false` , která označuje, že nastavení zadaná v rámci [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elementu nejsou děděna aplikacemi, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-176">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="0c641-177">Když je aplikace nasazená na [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` cesta je nastavená na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="0c641-177">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="0c641-178">Cesta uloží protokoly stdout do `LogFiles` složky, což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="0c641-178">The path saves stdout logs to the `LogFiles` folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="0c641-179">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications> .</span><span class="sxs-lookup"><span data-stu-id="0c641-179">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="0c641-180">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="0c641-180">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="0c641-181">Atribut</span><span class="sxs-lookup"><span data-stu-id="0c641-181">Attribute</span></span> | <span data-ttu-id="0c641-182">Popis</span><span class="sxs-lookup"><span data-stu-id="0c641-182">Description</span></span> | <span data-ttu-id="0c641-183">Výchozí</span><span class="sxs-lookup"><span data-stu-id="0c641-183">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="0c641-184">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="0c641-184">Optional string attribute.</span></span></p><p><span data-ttu-id="0c641-185">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="0c641-185">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="0c641-186">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="0c641-186">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0c641-187">Pokud je nastaveno na true, stránka **502,5-procesového selhání** se potlačí a stavová stránka 502, která je nakonfigurovaná ve *web.config* , má přednost.</span><span class="sxs-lookup"><span data-stu-id="0c641-187">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="0c641-188">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="0c641-188">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0c641-189">Pokud je nastaveno na true, token se přepošle podřízenému procesu `%ASPNETCORE_PORT%` , který naslouchá jako záhlaví na `'MS-ASPNETCORE-WINAUTHTOKEN'` žádost.</span><span class="sxs-lookup"><span data-stu-id="0c641-189">If true, the token is forwarded to the child process listening on `%ASPNETCORE_PORT%` as a header `'MS-ASPNETCORE-WINAUTHTOKEN'` per request.</span></span> <span data-ttu-id="0c641-190">Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</span><span class="sxs-lookup"><span data-stu-id="0c641-190">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="0c641-191">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="0c641-191">Optional string attribute.</span></span></p><p><span data-ttu-id="0c641-192">Určuje model hostování jako vnitroprocesové ( `InProcess` / `inprocess` ) nebo mimo proces ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="0c641-192">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="0c641-193">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="0c641-193">Optional integer attribute.</span></span></p><p><span data-ttu-id="0c641-194">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="0c641-194">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="0c641-195">&dagger;Pro hostování v rámci procesu je hodnota omezená na `1` .</span><span class="sxs-lookup"><span data-stu-id="0c641-195">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="0c641-196">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="0c641-196">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="0c641-197">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="0c641-197">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="0c641-198">Výchozí `1`</span><span class="sxs-lookup"><span data-stu-id="0c641-198">Default: `1`</span></span><br><span data-ttu-id="0c641-199">Dlouhé `1`</span><span class="sxs-lookup"><span data-stu-id="0c641-199">Min: `1`</span></span><br><span data-ttu-id="0c641-200">Počet `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="0c641-200">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="0c641-201">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="0c641-201">Required string attribute.</span></span></p><p><span data-ttu-id="0c641-202">Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="0c641-202">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="0c641-203">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="0c641-203">Relative paths are supported.</span></span> <span data-ttu-id="0c641-204">Pokud cesta začíná `.` , bude tato cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="0c641-204">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="0c641-205">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="0c641-205">Optional integer attribute.</span></span></p><p><span data-ttu-id="0c641-206">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="0c641-206">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="0c641-207">Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="0c641-207">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="0c641-208">Nepodporováno v hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="0c641-208">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="0c641-209">Výchozí `10`</span><span class="sxs-lookup"><span data-stu-id="0c641-209">Default: `10`</span></span><br><span data-ttu-id="0c641-210">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="0c641-210">Min: `0`</span></span><br><span data-ttu-id="0c641-211">Počet `100`</span><span class="sxs-lookup"><span data-stu-id="0c641-211">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="0c641-212">Volitelný atribut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="0c641-212">Optional timespan attribute.</span></span></p><p><span data-ttu-id="0c641-213">Určuje dobu, po kterou modul ASP.NET Core čeká na odpověď od procesu, který naslouchá na% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="0c641-213">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="0c641-214">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, `requestTimeout` je určena v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="0c641-214">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="0c641-215">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="0c641-215">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="0c641-216">Pro hostování v rámci procesu modul čeká, až aplikace zpracuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="0c641-216">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="0c641-217">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="0c641-217">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="0c641-218">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="0c641-218">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="0c641-219">Výchozí `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="0c641-219">Default: `00:02:00`</span></span><br><span data-ttu-id="0c641-220">Dlouhé `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="0c641-220">Min: `00:00:00`</span></span><br><span data-ttu-id="0c641-221">Počet `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="0c641-221">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="0c641-222">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="0c641-222">Optional integer attribute.</span></span></p><p><span data-ttu-id="0c641-223">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="0c641-223">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="0c641-224">Výchozí `10`</span><span class="sxs-lookup"><span data-stu-id="0c641-224">Default: `10`</span></span><br><span data-ttu-id="0c641-225">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="0c641-225">Min: `0`</span></span><br><span data-ttu-id="0c641-226">Počet `600`</span><span class="sxs-lookup"><span data-stu-id="0c641-226">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="0c641-227">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="0c641-227">Optional integer attribute.</span></span></p><p><span data-ttu-id="0c641-228">Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá.</span><span class="sxs-lookup"><span data-stu-id="0c641-228">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="0c641-229">Pokud je tento časový limit překročen, modul proces ukončuje.</span><span class="sxs-lookup"><span data-stu-id="0c641-229">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="0c641-230">Při hostování *v procesu*: proces **není restartován a** **nepoužívá nastavení** **rapidFailsPerMinute** .</span><span class="sxs-lookup"><span data-stu-id="0c641-230">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="0c641-231">Při hostování *mimo proces*: modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se aplikaci nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="0c641-231">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="0c641-232">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="0c641-232">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="0c641-233">Výchozí `120`</span><span class="sxs-lookup"><span data-stu-id="0c641-233">Default: `120`</span></span><br><span data-ttu-id="0c641-234">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="0c641-234">Min: `0`</span></span><br><span data-ttu-id="0c641-235">Počet `3600`</span><span class="sxs-lookup"><span data-stu-id="0c641-235">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="0c641-236">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="0c641-236">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0c641-237">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="0c641-237">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="0c641-238">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="0c641-238">Optional string attribute.</span></span></p><p><span data-ttu-id="0c641-239">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="0c641-239">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="0c641-240">Relativní cesty jsou relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="0c641-240">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="0c641-241">Každá cesta začínající řetězcem `.` je relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="0c641-241">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="0c641-242">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="0c641-242">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="0c641-243">Pomocí oddělovačů podtržítka se `.log` do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru ().</span><span class="sxs-lookup"><span data-stu-id="0c641-243">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="0c641-244">Pokud `.\logs\stdout` je zadán jako hodnota, je ukázkový protokol stdout uložen jako `stdout_20180205194132_1934.log` ve `logs` složce při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="0c641-244">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="0c641-245">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="0c641-245">Set environment variables</span></span>

<span data-ttu-id="0c641-246">Proměnné prostředí lze zadat pro proces v `processPath` atributu.</span><span class="sxs-lookup"><span data-stu-id="0c641-246">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="0c641-247">Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem `<environmentVariables>` elementu kolekce.</span><span class="sxs-lookup"><span data-stu-id="0c641-247">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="0c641-248">Proměnné prostředí nastavené v této části mají přednost před proměnnými prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="0c641-248">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="0c641-249">Následující příklad nastaví dvě proměnné prostředí v `web.config` .</span><span class="sxs-lookup"><span data-stu-id="0c641-249">The following example sets two environment variables in `web.config`.</span></span> <span data-ttu-id="0c641-250">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace na `Development` .</span><span class="sxs-lookup"><span data-stu-id="0c641-250">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="0c641-251">Vývojář může tuto hodnotu dočasně nastavit v souboru, aby se `web.config` vynutila načítání [stránky výjimky pro vývojáře](xref:fundamentals/error-handling) při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-251">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="0c641-252">`CONFIG_DIR` je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-252">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="0c641-253">Alternativou k nastavení prostředí přímo v `web.config` je zahrnutí `<EnvironmentName>` vlastnosti do [publikačního profilu ( `.pubxml` )](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="0c641-253">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="0c641-254">Tento přístup nastaví prostředí v `web.config` okamžiku publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="0c641-254">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="0c641-255">Nastavte pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí na `Development` pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="0c641-255">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## `app_offline.htm`

<span data-ttu-id="0c641-256">Pokud `app_offline.htm` se v kořenovém adresáři aplikace zjistí soubor s názvem, modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastaví zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="0c641-256">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="0c641-257">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit` , modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="0c641-257">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="0c641-258">I když `app_offline.htm` je soubor přítomen, ASP.NET Core modul reaguje na požadavky odesláním obsahu `app_offline.htm` souboru zpět.</span><span class="sxs-lookup"><span data-stu-id="0c641-258">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="0c641-259">Po `app_offline.htm` Odebrání souboru se aplikace spustí dalším požadavkem.</span><span class="sxs-lookup"><span data-stu-id="0c641-259">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="0c641-260">Při použití nezpracovaného modelu hostování se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="0c641-260">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="0c641-261">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-261">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="0c641-262">Spouštěcí chybová stránka</span><span class="sxs-lookup"><span data-stu-id="0c641-262">Start-up error page</span></span>

<span data-ttu-id="0c641-263">Hostování v procesu i mimo procesy vytváří vlastní chybové stránky, když se nepodaří aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="0c641-263">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="0c641-264">Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.</span><span class="sxs-lookup"><span data-stu-id="0c641-264">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="0c641-265">Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .</span><span class="sxs-lookup"><span data-stu-id="0c641-265">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="0c641-266">Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="0c641-266">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="0c641-267">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="0c641-267">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="0c641-268">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby `<httpErrors>` protokolu HTTP ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="0c641-268">For more information on configuring custom error messages, see [HTTP Errors `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="0c641-269">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="0c641-269">Log creation and redirection</span></span>

<span data-ttu-id="0c641-270">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` jsou nastaveny atributy a elementu.</span><span class="sxs-lookup"><span data-stu-id="0c641-270">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="0c641-271">`stdoutLogFile`Při vytvoření souboru protokolu jsou všechny složky v této cestě vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="0c641-271">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="0c641-272">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\<app_pool_name>` k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="0c641-272">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="0c641-273">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="0c641-273">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="0c641-274">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="0c641-274">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="0c641-275">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="0c641-275">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="0c641-276">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-276">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="0c641-277">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="0c641-277">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="0c641-278">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="0c641-278">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="0c641-279">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="0c641-279">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="0c641-280">Název souboru protokolu se skládá tak, že se připojí časové razítko, ID procesu a Přípona souboru ( `.log` ) k poslednímu segmentu `stdoutLogFile` cesty (obvykle `stdout` ) oddělené podtržítky.</span><span class="sxs-lookup"><span data-stu-id="0c641-280">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="0c641-281">Pokud `stdoutLogFile` cesta končí `stdout` , protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru `stdout_20180205194132_1934.log` .</span><span class="sxs-lookup"><span data-stu-id="0c641-281">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="0c641-282">Pokud `stdoutLogEnabled` je hodnota false, chyby, ke kterým dochází při spuštění aplikace, se zaznamenávají a generují do protokolu událostí až do 30 kB.</span><span class="sxs-lookup"><span data-stu-id="0c641-282">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="0c641-283">Po spuštění budou všechny další protokoly zahozeny.</span><span class="sxs-lookup"><span data-stu-id="0c641-283">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="0c641-284">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="0c641-284">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="0c641-285">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="0c641-285">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="0c641-286">Když publikujete aplikaci pro nasazení Azure App Service, sada web SDK nastaví `stdoutLogFile` hodnotu na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="0c641-286">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="0c641-287">`%home`Proměnná prostředí je předdefinovaná pro aplikace hostované v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="0c641-287">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="0c641-288">Chcete-li vytvořit pravidla filtru protokolování, přečtěte si část [Konfigurace](xref:fundamentals/logging/index#log-filtering) a [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci ASP.NET Core protokolování.</span><span class="sxs-lookup"><span data-stu-id="0c641-288">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="0c641-289">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="0c641-289">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="0c641-290">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="0c641-290">Enhanced diagnostic logs</span></span>

<span data-ttu-id="0c641-291">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="0c641-291">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="0c641-292">Přidejte `<handlerSettings>` element do `<aspNetCore>` prvku v `web.config` .</span><span class="sxs-lookup"><span data-stu-id="0c641-292">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="0c641-293">Nastavení `debugLevel` pro `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:</span><span class="sxs-lookup"><span data-stu-id="0c641-293">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="0c641-294">Všechny složky v cestě ( `logs` v předchozím příkladu) se vytvoří modulem, když se vytvoří soubor protokolu.</span><span class="sxs-lookup"><span data-stu-id="0c641-294">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="0c641-295">Fond aplikací musí mít oprávnění k zápisu do umístění, ve kterém jsou protokoly zapsané (použijte `IIS AppPool\{APP POOL NAME}` , kde zástupný symbol `{APP POOL NAME}` je název fondu aplikací, který poskytuje oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="0c641-295">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, to provide write permission).</span></span>

<span data-ttu-id="0c641-296">Hodnoty úrovně ladění ( `debugLevel` ) mohou zahrnovat jak úroveň, tak i umístění.</span><span class="sxs-lookup"><span data-stu-id="0c641-296">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="0c641-297">Úrovně (v pořadí od nejméně po nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="0c641-297">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="0c641-298">CHYBA</span><span class="sxs-lookup"><span data-stu-id="0c641-298">ERROR</span></span>
* <span data-ttu-id="0c641-299">WARNING</span><span class="sxs-lookup"><span data-stu-id="0c641-299">WARNING</span></span>
* <span data-ttu-id="0c641-300">PŘÍJEMCE</span><span class="sxs-lookup"><span data-stu-id="0c641-300">INFO</span></span>
* <span data-ttu-id="0c641-301">TRACE</span><span class="sxs-lookup"><span data-stu-id="0c641-301">TRACE</span></span>

<span data-ttu-id="0c641-302">Umístění (je povoleno více umístění):</span><span class="sxs-lookup"><span data-stu-id="0c641-302">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="0c641-303">STROMU</span><span class="sxs-lookup"><span data-stu-id="0c641-303">CONSOLE</span></span>
* <span data-ttu-id="0c641-304">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="0c641-304">EVENTLOG</span></span>
* <span data-ttu-id="0c641-305">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="0c641-305">FILE</span></span>

<span data-ttu-id="0c641-306">Nastavení obslužné rutiny lze také poskytnout pomocí proměnných prostředí:</span><span class="sxs-lookup"><span data-stu-id="0c641-306">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="0c641-307">`ASPNETCORE_MODULE_DEBUG_FILE`: Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="0c641-307">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="0c641-308">(Výchozí: `aspnetcore-debug.log` )</span><span class="sxs-lookup"><span data-stu-id="0c641-308">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="0c641-309">`ASPNETCORE_MODULE_DEBUG`: Nastavení úrovně ladění.</span><span class="sxs-lookup"><span data-stu-id="0c641-309">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="0c641-310">Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="0c641-310">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="0c641-311">Velikost protokolu není omezená.</span><span class="sxs-lookup"><span data-stu-id="0c641-311">The size of the log isn't limited.</span></span> <span data-ttu-id="0c641-312">Když necháte protokol ladění povolený, může dojít k vyčerpání dostupného místa na disku a selhání serveru nebo služby App Service.</span><span class="sxs-lookup"><span data-stu-id="0c641-312">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="0c641-313">Příklad prvku v souboru naleznete v tématu [konfigurace s web.config](#configuration-with-webconfig) `aspNetCore` `web.config` .</span><span class="sxs-lookup"><span data-stu-id="0c641-313">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="0c641-314">Úprava velikosti zásobníku</span><span class="sxs-lookup"><span data-stu-id="0c641-314">Modify the stack size</span></span>

<span data-ttu-id="0c641-315">*Platí pouze při použití modelu hostování v rámci procesu.*</span><span class="sxs-lookup"><span data-stu-id="0c641-315">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="0c641-316">Nakonfigurujte velikost spravovaného zásobníku pomocí `stackSize` nastavení v bajtech v `web.config` .</span><span class="sxs-lookup"><span data-stu-id="0c641-316">Configure the managed stack size using the `stackSize` setting in bytes in `web.config`.</span></span> <span data-ttu-id="0c641-317">Výchozí velikost je 1 048 576 bajtů (1 MB).</span><span class="sxs-lookup"><span data-stu-id="0c641-317">The default size is 1,048,576 bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="0c641-318">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="0c641-318">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="0c641-319">*Platí pouze pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="0c641-319">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="0c641-320">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="0c641-320">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="0c641-321">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="0c641-321">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="0c641-322">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="0c641-322">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="0c641-323">Token párování se vytvoří a nastaví na proměnnou prostředí ( `ASPNETCORE_TOKEN` ) modulem.</span><span class="sxs-lookup"><span data-stu-id="0c641-323">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="0c641-324">Párovací token je také nastaven na hlavičku ( `MS-ASPNETCORE-TOKEN` ) pro každý požadavek proxy.</span><span class="sxs-lookup"><span data-stu-id="0c641-324">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="0c641-325">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0c641-325">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="0c641-326">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="0c641-326">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="0c641-327">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="0c641-327">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="0c641-328">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="0c641-328">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="0c641-329">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="0c641-329">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="0c641-330">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="0c641-330">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="0c641-331">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v `applicationHost.config` souboru ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="0c641-331">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="0c641-332">Pokud používáte konfiguraci IIS Shared na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s `OPT_NO_SHARED_CONFIG_CHECK` parametrem nastaveným na `1` :</span><span class="sxs-lookup"><span data-stu-id="0c641-332">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="0c641-333">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="0c641-333">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="0c641-334">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="0c641-334">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="0c641-335">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="0c641-335">Run the installer.</span></span>
1. <span data-ttu-id="0c641-336">Exportujte aktualizovaný `applicationHost.config` soubor do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="0c641-336">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="0c641-337">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="0c641-337">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="0c641-338">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="0c641-338">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="0c641-339">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0c641-339">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="0c641-340">V hostitelském systému přejděte na `%windir%\System32\inetsrv` .</span><span class="sxs-lookup"><span data-stu-id="0c641-340">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="0c641-341">Vyhledejte `aspnetcore.dll` soubor.</span><span class="sxs-lookup"><span data-stu-id="0c641-341">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="0c641-342">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="0c641-342">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="0c641-343">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="0c641-343">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="0c641-344">V umístění se nacházejí protokoly instalačního balíčku pro modul `C:\Users\%UserName%\AppData\Local\Temp` .</span><span class="sxs-lookup"><span data-stu-id="0c641-344">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="0c641-345">Soubor má název `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` .</span><span class="sxs-lookup"><span data-stu-id="0c641-345">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="0c641-346">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="0c641-346">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="0c641-347">Modul</span><span class="sxs-lookup"><span data-stu-id="0c641-347">Module</span></span>

<span data-ttu-id="0c641-348">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="0c641-348">**IIS (x86/amd64):**</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="0c641-349">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="0c641-349">**IIS Express (x86/amd64):**</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="0c641-350">Schéma</span><span class="sxs-lookup"><span data-stu-id="0c641-350">Schema</span></span>

<span data-ttu-id="0c641-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="0c641-351">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="0c641-352">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="0c641-352">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="0c641-353">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="0c641-353">Configuration</span></span>

<span data-ttu-id="0c641-354">**IIS**</span><span class="sxs-lookup"><span data-stu-id="0c641-354">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="0c641-355">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="0c641-355">**IIS Express**</span></span>

* <span data-ttu-id="0c641-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="0c641-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="0c641-357">*iisexpress.exe* CLI `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="0c641-357">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="0c641-358">Soubory lze najít hledáním `aspnetcore` v `applicationHost.config` souboru.</span><span class="sxs-lookup"><span data-stu-id="0c641-358">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="0c641-359">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS na jednu z těchto:</span><span class="sxs-lookup"><span data-stu-id="0c641-359">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="0c641-360">Hostování aplikace ASP.NET Core uvnitř pracovního procesu služby IIS ( `w3wp.exe` ), který se nazývá [model hostování v rámci procesu](#in-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="0c641-360">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="0c641-361">Dopředné webové požadavky do back-endu ASP.NET Core aplikace, na které běží [Server Kestrel](xref:fundamentals/servers/kestrel), se nazývá [model hostování mimo proces](#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="0c641-361">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="0c641-362">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="0c641-362">Supported Windows versions:</span></span>

* <span data-ttu-id="0c641-363">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="0c641-363">Windows 7 or later</span></span>
* <span data-ttu-id="0c641-364">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="0c641-364">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="0c641-365">Při hostování v procesu používá modul implementaci vnitroprocesového serveru pro službu IIS nazvanou IIS HTTP Server ( `IISHttpServer` ).</span><span class="sxs-lookup"><span data-stu-id="0c641-365">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="0c641-366">Při hostování mimo proces funguje modul pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0c641-366">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="0c641-367">Modul nefunguje s [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="0c641-367">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="0c641-368">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="0c641-368">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="0c641-369">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="0c641-369">In-process hosting model</span></span>

<span data-ttu-id="0c641-370">Chcete-li nakonfigurovat aplikaci pro hostování v rámci procesu, přidejte do `<AspNetCoreHostingModel>` souboru projektu aplikace vlastnost s hodnotou `InProcess` (hostování mimo proces je nastavená na `OutOfProcess` ):</span><span class="sxs-lookup"><span data-stu-id="0c641-370">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="0c641-371">Model hostování v rámci procesu není podporován pro aplikace ASP.NET Core, které cílí na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="0c641-371">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="0c641-372">Hodnota rozlišuje `<AspNetCoreHostingModel>` malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="0c641-372">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="0c641-373">Pokud `<AspNetCoreHostingModel>` vlastnost není v souboru přítomna, výchozí hodnota je `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="0c641-373">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="0c641-374">Následující vlastnosti platí při hostování v procesu:</span><span class="sxs-lookup"><span data-stu-id="0c641-374">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="0c641-375">`IISHttpServer`Místo serveru [Kestrel](xref:fundamentals/servers/kestrel) se používá server HTTP služby IIS ().</span><span class="sxs-lookup"><span data-stu-id="0c641-375">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="0c641-376">Pro vnitroprocesové [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> :</span><span class="sxs-lookup"><span data-stu-id="0c641-376">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="0c641-377">Zaregistrujte `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="0c641-377">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="0c641-378">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0c641-378">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="0c641-379">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="0c641-379">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="0c641-380">[Atribut RequestTimeout](#attributes-of-the-aspnetcore-element) se nevztahuje na hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="0c641-380">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="0c641-381">Sdílení fondu aplikací mezi aplikacemi není podporováno.</span><span class="sxs-lookup"><span data-stu-id="0c641-381">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="0c641-382">Použijte jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="0c641-382">Use one app pool per app.</span></span>

* <span data-ttu-id="0c641-383">Při použití [nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) nebo ručního umístění [ souboruapp_offline.htm v nasazení](xref:host-and-deploy/iis/index#locked-deployment-files)se aplikace nemusí vypnout okamžitě, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="0c641-383">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="0c641-384">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-384">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="0c641-385">Architektura (bitová verze) aplikace a nainstalovaného modulu runtime (x64 nebo x86) se musí shodovat s architekturou fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="0c641-385">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="0c641-386">Zjistila se odpojení klienta.</span><span class="sxs-lookup"><span data-stu-id="0c641-386">Client disconnects are detected.</span></span> <span data-ttu-id="0c641-387">Token zrušení [HttpContext. RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) je zrušený, když se klient odpojí.</span><span class="sxs-lookup"><span data-stu-id="0c641-387">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="0c641-388">V ASP.NET Core 2.2.1 nebo dřívější <xref:System.IO.Directory.GetCurrentDirectory*> vrátí pracovní adresář procesu spuštěného službou IIS místo adresáře aplikace (například *C:\Windows\System32\inetsrv* pro *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="0c641-388">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="0c641-389">Vzorový kód, který nastaví aktuální adresář aplikace, najdete v tématu [Třída CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="0c641-389">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="0c641-390">Zavolejte `SetCurrentDirectory` metodu.</span><span class="sxs-lookup"><span data-stu-id="0c641-390">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="0c641-391">Následná volání pro <xref:System.IO.Directory.GetCurrentDirectory*> poskytnutí adresáře aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-391">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="0c641-392">Při hostování v procesu <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> není volána interně pro inicializaci uživatele.</span><span class="sxs-lookup"><span data-stu-id="0c641-392">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="0c641-393">Proto <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementace používaná k transformaci deklarací po každém ověření není ve výchozím nastavení aktivována.</span><span class="sxs-lookup"><span data-stu-id="0c641-393">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="0c641-394">Při transformaci deklarací s <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementací zavolejte <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> na přidat ověřovací služby:</span><span class="sxs-lookup"><span data-stu-id="0c641-394">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

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

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="0c641-395">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="0c641-395">Out-of-process hosting model</span></span>

<span data-ttu-id="0c641-396">Chcete-li nakonfigurovat aplikaci pro hostování mimo procesy, použijte některý z následujících přístupů v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="0c641-396">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="0c641-397">Nezadávejte `<AspNetCoreHostingModel>` vlastnost.</span><span class="sxs-lookup"><span data-stu-id="0c641-397">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="0c641-398">Pokud `<AspNetCoreHostingModel>` vlastnost není v souboru přítomna, výchozí hodnota je `OutOfProcess` .</span><span class="sxs-lookup"><span data-stu-id="0c641-398">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="0c641-399">Nastavte hodnotu `<AspNetCoreHostingModel>` vlastnosti na `OutOfProcess` (vnitroprocesové hostování v procesu je nastaveno na `InProcess` ):</span><span class="sxs-lookup"><span data-stu-id="0c641-399">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="0c641-400">Hodnota rozlišuje malá a velká písmena, takže `inprocess` a `outofprocess` jsou platné hodnoty.</span><span class="sxs-lookup"><span data-stu-id="0c641-400">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="0c641-401">[Kestrel](xref:fundamentals/servers/kestrel) Místo http serveru IIS () se používá server Kestrel `IISHttpServer` .</span><span class="sxs-lookup"><span data-stu-id="0c641-401">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="0c641-402">Pro mimoprocesové [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> do:</span><span class="sxs-lookup"><span data-stu-id="0c641-402">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="0c641-403">Nakonfigurujte port a základní cestu, na kterých má Server naslouchat při spuštění za modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0c641-403">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="0c641-404">Nakonfigurujte hostitele tak, aby zachytával chyby při spuštění.</span><span class="sxs-lookup"><span data-stu-id="0c641-404">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="0c641-405">Změny modelu hostování</span><span class="sxs-lookup"><span data-stu-id="0c641-405">Hosting model changes</span></span>

<span data-ttu-id="0c641-406">Pokud se `hostingModel` změní nastavení v souboru *web.config* (vysvětleno v části [konfigurace s web.config](#configuration-with-webconfig) ), modul recykluje pracovní proces pro službu IIS.</span><span class="sxs-lookup"><span data-stu-id="0c641-406">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="0c641-407">V případě IIS Express modul neprovádí recyklaci pracovního procesu, ale místo toho aktivuje bezproblémové vypnutí aktuálního procesu IIS Express.</span><span class="sxs-lookup"><span data-stu-id="0c641-407">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="0c641-408">Další požadavek na aplikaci vytvoří nový proces IIS Express.</span><span class="sxs-lookup"><span data-stu-id="0c641-408">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="0c641-409">Název procesu</span><span class="sxs-lookup"><span data-stu-id="0c641-409">Process name</span></span>

<span data-ttu-id="0c641-410">`Process.GetCurrentProcess().ProcessName`sestavy `w3wp` / `iisexpress` (v procesu) nebo `dotnet` (mimo proces).</span><span class="sxs-lookup"><span data-stu-id="0c641-410">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="0c641-411">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="0c641-411">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="0c641-412">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="0c641-412">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="0c641-413">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="0c641-413">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="0c641-414">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="0c641-414">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="0c641-415">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="0c641-415">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="0c641-416">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="0c641-416">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="0c641-417">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0c641-417">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="0c641-418">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="0c641-418">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="0c641-419">Konfigurace s web.config</span><span class="sxs-lookup"><span data-stu-id="0c641-419">Configuration with web.config</span></span>

<span data-ttu-id="0c641-420">Modul ASP.NET Core je nakonfigurován s `aspNetCore` částí `system.webServer` uzlu v souboru *web.config* webu.</span><span class="sxs-lookup"><span data-stu-id="0c641-420">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="0c641-421">Následující *web.config* soubor je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core, aby zpracovával požadavky webu:</span><span class="sxs-lookup"><span data-stu-id="0c641-421">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="0c641-422">Následující *web.config* jsou publikovány pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="0c641-422">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="0c641-423"><xref:System.Configuration.SectionInformation.InheritInChildApplications*>Vlastnost je nastavena na hodnotu `false` , která označuje, že nastavení zadaná v rámci [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) elementu nejsou děděna aplikacemi, které jsou umístěny v podadresáři aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-423">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="0c641-424">Když je aplikace nasazená na [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` cesta je nastavená na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="0c641-424">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="0c641-425">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="0c641-425">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="0c641-426">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications> .</span><span class="sxs-lookup"><span data-stu-id="0c641-426">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="0c641-427">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="0c641-427">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="0c641-428">Atribut</span><span class="sxs-lookup"><span data-stu-id="0c641-428">Attribute</span></span> | <span data-ttu-id="0c641-429">Popis</span><span class="sxs-lookup"><span data-stu-id="0c641-429">Description</span></span> | <span data-ttu-id="0c641-430">Výchozí</span><span class="sxs-lookup"><span data-stu-id="0c641-430">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="0c641-431">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="0c641-431">Optional string attribute.</span></span></p><p><span data-ttu-id="0c641-432">Argumenty ke spustitelnému souboru určenému v `processPath` .</span><span class="sxs-lookup"><span data-stu-id="0c641-432">Arguments to the executable specified in `processPath`.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="0c641-433">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="0c641-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0c641-434">Pokud je nastaveno na true, stránka **502,5-procesového selhání** se potlačí a stavová stránka 502, která je nakonfigurovaná ve *web.config* , má přednost.</span><span class="sxs-lookup"><span data-stu-id="0c641-434">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="0c641-435">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="0c641-435">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0c641-436">Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako záhlaví MS-ASPNETCORE-WINAUTHTOKEN na žádost.</span><span class="sxs-lookup"><span data-stu-id="0c641-436">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="0c641-437">Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</span><span class="sxs-lookup"><span data-stu-id="0c641-437">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="0c641-438">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="0c641-438">Optional string attribute.</span></span></p><p><span data-ttu-id="0c641-439">Určuje model hostování jako vnitroprocesové ( `InProcess` / `inprocess` ) nebo mimo proces ( `OutOfProcess` / `outofprocess` ).</span><span class="sxs-lookup"><span data-stu-id="0c641-439">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="0c641-440">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="0c641-440">Optional integer attribute.</span></span></p><p><span data-ttu-id="0c641-441">Určuje počet instancí procesu zadaných v `processPath` nastavení, které je možné vystavit pro jednotlivé aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-441">Specifies the number of instances of the process specified in the `processPath` setting that can be spun up per app.</span></span></p><p><span data-ttu-id="0c641-442">&dagger;Pro hostování v rámci procesu je hodnota omezená na `1` .</span><span class="sxs-lookup"><span data-stu-id="0c641-442">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="0c641-443">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="0c641-443">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="0c641-444">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="0c641-444">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="0c641-445">Výchozí `1`</span><span class="sxs-lookup"><span data-stu-id="0c641-445">Default: `1`</span></span><br><span data-ttu-id="0c641-446">Dlouhé `1`</span><span class="sxs-lookup"><span data-stu-id="0c641-446">Min: `1`</span></span><br><span data-ttu-id="0c641-447">Počet `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="0c641-447">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="0c641-448">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="0c641-448">Required string attribute.</span></span></p><p><span data-ttu-id="0c641-449">Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="0c641-449">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="0c641-450">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="0c641-450">Relative paths are supported.</span></span> <span data-ttu-id="0c641-451">Pokud cesta začíná `.` , bude tato cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="0c641-451">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="0c641-452">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="0c641-452">Optional integer attribute.</span></span></p><p><span data-ttu-id="0c641-453">Určuje počet pokusů, kolikrát `processPath` je povoleno selhání procesu určeného v rámci jedné minuty.</span><span class="sxs-lookup"><span data-stu-id="0c641-453">Specifies the number of times the process specified in `processPath` is allowed to crash per minute.</span></span> <span data-ttu-id="0c641-454">Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="0c641-454">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="0c641-455">Nepodporováno v hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="0c641-455">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="0c641-456">Výchozí `10`</span><span class="sxs-lookup"><span data-stu-id="0c641-456">Default: `10`</span></span><br><span data-ttu-id="0c641-457">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="0c641-457">Min: `0`</span></span><br><span data-ttu-id="0c641-458">Počet `100`</span><span class="sxs-lookup"><span data-stu-id="0c641-458">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="0c641-459">Volitelný atribut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="0c641-459">Optional timespan attribute.</span></span></p><p><span data-ttu-id="0c641-460">Určuje dobu, po kterou modul ASP.NET Core čeká na odpověď od procesu, který naslouchá na% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="0c641-460">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="0c641-461">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, `requestTimeout` je určena v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="0c641-461">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="0c641-462">Neplatí pro hostování v procesu.</span><span class="sxs-lookup"><span data-stu-id="0c641-462">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="0c641-463">Pro hostování v rámci procesu modul čeká, až aplikace zpracuje požadavek.</span><span class="sxs-lookup"><span data-stu-id="0c641-463">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="0c641-464">Platné hodnoty segmentů minut a sekund řetězce jsou v rozsahu 0-59.</span><span class="sxs-lookup"><span data-stu-id="0c641-464">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="0c641-465">Při použití hodnoty **60** v hodnotě minut nebo sekund dojde k chybě systému *500 – interní chyba serveru*.</span><span class="sxs-lookup"><span data-stu-id="0c641-465">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="0c641-466">Výchozí `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="0c641-466">Default: `00:02:00`</span></span><br><span data-ttu-id="0c641-467">Dlouhé `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="0c641-467">Min: `00:00:00`</span></span><br><span data-ttu-id="0c641-468">Počet `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="0c641-468">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="0c641-469">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="0c641-469">Optional integer attribute.</span></span></p><p><span data-ttu-id="0c641-470">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když `app_offline.htm` se zjistí soubor</span><span class="sxs-lookup"><span data-stu-id="0c641-470">Duration in seconds that the module waits for the executable to gracefully shutdown when the `app_offline.htm` file is detected.</span></span></p> | <span data-ttu-id="0c641-471">Výchozí `10`</span><span class="sxs-lookup"><span data-stu-id="0c641-471">Default: `10`</span></span><br><span data-ttu-id="0c641-472">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="0c641-472">Min: `0`</span></span><br><span data-ttu-id="0c641-473">Počet `600`</span><span class="sxs-lookup"><span data-stu-id="0c641-473">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="0c641-474">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="0c641-474">Optional integer attribute.</span></span></p><p><span data-ttu-id="0c641-475">Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá.</span><span class="sxs-lookup"><span data-stu-id="0c641-475">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="0c641-476">Pokud je tento časový limit překročen, modul proces ukončuje.</span><span class="sxs-lookup"><span data-stu-id="0c641-476">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="0c641-477">Při hostování *v procesu*: proces **není restartován a** **nepoužívá** `rapidFailsPerMinute` nastavení.</span><span class="sxs-lookup"><span data-stu-id="0c641-477">When hosting *in-process*: The process is **not** restarted and does **not** use the `rapidFailsPerMinute` setting.</span></span></p><p><span data-ttu-id="0c641-478">Při hostování *mimo proces*: modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se aplikaci nepodaří spustit `rapidFailsPerMinute` několikrát během poslední minuty za minutu.</span><span class="sxs-lookup"><span data-stu-id="0c641-478">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start `rapidFailsPerMinute` number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="0c641-479">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="0c641-479">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="0c641-480">Výchozí `120`</span><span class="sxs-lookup"><span data-stu-id="0c641-480">Default: `120`</span></span><br><span data-ttu-id="0c641-481">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="0c641-481">Min: `0`</span></span><br><span data-ttu-id="0c641-482">Počet `3600`</span><span class="sxs-lookup"><span data-stu-id="0c641-482">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="0c641-483">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="0c641-483">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0c641-484">Pokud je nastavené na true, **stdout** a **stderr** pro proces zadaný v `processPath` se přesměrují na soubor určený v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="0c641-484">If true, **stdout** and **stderr** for the process specified in `processPath` are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="0c641-485">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="0c641-485">Optional string attribute.</span></span></p><p><span data-ttu-id="0c641-486">Určuje relativní nebo absolutní cestu k souboru, pro který `stdout` a `stderr` z procesu určeného v části `processPath` jsou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="0c641-486">Specifies the relative or absolute file path for which `stdout` and `stderr` from the process specified in `processPath` are logged.</span></span> <span data-ttu-id="0c641-487">Relativní cesty jsou relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="0c641-487">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="0c641-488">Každá cesta začínající řetězcem `.` je relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="0c641-488">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="0c641-489">Při vytvoření souboru protokolu se v modulu vytvoří všechny složky, které jsou v cestě zadané.</span><span class="sxs-lookup"><span data-stu-id="0c641-489">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="0c641-490">Pomocí oddělovačů podtržítka se `.log` do posledního segmentu cesty přidají časové razítko, ID procesu a Přípona souboru () `stdoutLogFile` .</span><span class="sxs-lookup"><span data-stu-id="0c641-490">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the `stdoutLogFile` path.</span></span> <span data-ttu-id="0c641-491">Pokud `.\logs\stdout` je zadán jako hodnota, je ukázkový protokol stdout uložen jako `stdout_20180205194132_1934.log` ve `logs` složce při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="0c641-491">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="0c641-492">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="0c641-492">Setting environment variables</span></span>

<span data-ttu-id="0c641-493">Proměnné prostředí lze zadat pro proces v `processPath` atributu.</span><span class="sxs-lookup"><span data-stu-id="0c641-493">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="0c641-494">Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem `<environmentVariables>` elementu kolekce.</span><span class="sxs-lookup"><span data-stu-id="0c641-494">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="0c641-495">Proměnné prostředí nastavené v této části mají přednost před proměnnými prostředí systému.</span><span class="sxs-lookup"><span data-stu-id="0c641-495">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="0c641-496">Následující příklad nastaví dvě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0c641-496">The following example sets two environment variables.</span></span> <span data-ttu-id="0c641-497">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace na `Development` .</span><span class="sxs-lookup"><span data-stu-id="0c641-497">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="0c641-498">Vývojář může tuto hodnotu dočasně nastavit v souboru, aby se `web.config` vynutila načítání [stránky výjimky pro vývojáře](xref:fundamentals/error-handling) při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-498">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="0c641-499">`CONFIG_DIR` je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-499">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="0c641-500">Alternativou k nastavení prostředí přímo v `web.config` je zahrnutí `<EnvironmentName>` vlastnosti do [profilu publikování (. pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) nebo souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="0c641-500">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="0c641-501">Tento přístup nastaví prostředí v `web.config` okamžiku publikování projektu:</span><span class="sxs-lookup"><span data-stu-id="0c641-501">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="0c641-502">Nastavte pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí na `Development` pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="0c641-502">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="0c641-503">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="0c641-503">app_offline.htm</span></span>

<span data-ttu-id="0c641-504">Pokud `app_offline.htm` se v kořenovém adresáři aplikace zjistí soubor s názvem, modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastaví zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="0c641-504">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="0c641-505">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit` , modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="0c641-505">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="0c641-506">I když `app_offline.htm` je soubor přítomen, ASP.NET Core modul reaguje na požadavky odesláním obsahu `app_offline.htm` souboru zpět.</span><span class="sxs-lookup"><span data-stu-id="0c641-506">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="0c641-507">Po `app_offline.htm` Odebrání souboru se aplikace spustí dalším požadavkem.</span><span class="sxs-lookup"><span data-stu-id="0c641-507">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="0c641-508">Při použití nezpracovaného modelu hostování se aplikace nemusí okamžitě vypnout, pokud dojde k otevřenému připojení.</span><span class="sxs-lookup"><span data-stu-id="0c641-508">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="0c641-509">Připojení k protokolu WebSocket může například zpozdit ukončení aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-509">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="0c641-510">Spouštěcí chybová stránka</span><span class="sxs-lookup"><span data-stu-id="0c641-510">Start-up error page</span></span>

<span data-ttu-id="0c641-511">Hostování v procesu i mimo procesy vytváří vlastní chybové stránky, když se nepodaří aplikaci spustit.</span><span class="sxs-lookup"><span data-stu-id="0c641-511">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="0c641-512">Pokud modul ASP.NET Core nenalezne obslužnou rutinu žádosti v procesu nebo mimo proces 500,0, zobrazí se stránka s kódem stavu *selhání načtení obslužné rutiny v* procesu nebo mimo proces.</span><span class="sxs-lookup"><span data-stu-id="0c641-512">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="0c641-513">Pro vnitroprocesové hostování v případě, že se modul ASP.NET Core nepodaří spustit aplikaci, zobrazí se stránka s kódem stavu *chyby při spuštění 500,30* .</span><span class="sxs-lookup"><span data-stu-id="0c641-513">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="0c641-514">Pro hostování mimo procesy, pokud se ASP.NET Core modulu nespustí back-end proces, nebo dojde k selhání procesu back-endu, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="0c641-514">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="0c641-515">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 5xx, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="0c641-515">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="0c641-516">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby \<httpErrors> protokolu HTTP ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="0c641-516">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="0c641-517">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="0c641-517">Log creation and redirection</span></span>

<span data-ttu-id="0c641-518">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` jsou nastaveny atributy a elementu.</span><span class="sxs-lookup"><span data-stu-id="0c641-518">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="0c641-519">`stdoutLogFile`Při vytvoření souboru protokolu jsou všechny složky v této cestě vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="0c641-519">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="0c641-520">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\{APP POOL NAME}` k poskytnutí oprávnění k zápisu, kde zástupný symbol `{APP POOL NAME}` je název fondu aplikací).</span><span class="sxs-lookup"><span data-stu-id="0c641-520">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="0c641-521">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="0c641-521">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="0c641-522">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="0c641-522">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="0c641-523">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="0c641-523">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="0c641-524">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-524">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="0c641-525">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="0c641-525">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="0c641-526">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="0c641-526">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="0c641-527">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="0c641-527">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="0c641-528">Název souboru protokolu se skládá tak, že se připojí časové razítko, ID procesu a Přípona souboru ( `.log` ) k poslednímu segmentu `stdoutLogFile` cesty (obvykle `stdout` ) oddělené podtržítky.</span><span class="sxs-lookup"><span data-stu-id="0c641-528">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="0c641-529">Pokud `stdoutLogFile` cesta končí `stdout` , protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru `stdout_20180205194132_1934.log` .</span><span class="sxs-lookup"><span data-stu-id="0c641-529">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="0c641-530">Pokud `stdoutLogEnabled` je hodnota false, chyby, ke kterým dochází při spuštění aplikace, se zaznamenávají a generují do protokolu událostí až do 30 kB.</span><span class="sxs-lookup"><span data-stu-id="0c641-530">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="0c641-531">Po spuštění budou všechny další protokoly zahozeny.</span><span class="sxs-lookup"><span data-stu-id="0c641-531">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="0c641-532">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="0c641-532">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="0c641-533">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="0c641-533">Confirm that the app pool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="0c641-534">Když publikujete aplikaci pro nasazení Azure App Service, sada web SDK nastaví `stdoutLogFile` hodnotu na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="0c641-534">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="0c641-535">`%home`Proměnná prostředí je předdefinovaná pro aplikace hostované v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="0c641-535">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="0c641-536">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="0c641-536">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="0c641-537">Rozšířené diagnostické protokoly</span><span class="sxs-lookup"><span data-stu-id="0c641-537">Enhanced diagnostic logs</span></span>

<span data-ttu-id="0c641-538">Modul ASP.NET Core lze nakonfigurovat tak, aby poskytoval rozšířené diagnostické protokoly.</span><span class="sxs-lookup"><span data-stu-id="0c641-538">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="0c641-539">Přidejte `<handlerSettings>` element do `<aspNetCore>` prvku v `web.config` .</span><span class="sxs-lookup"><span data-stu-id="0c641-539">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="0c641-540">Nastavení `debugLevel` pro `TRACE` zpřístupňuje vyšší věrnost diagnostických informací:</span><span class="sxs-lookup"><span data-stu-id="0c641-540">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="0c641-541">Složky v cestě poskytnuté `<handlerSetting>` hodnotě ( `logs` v předchozím příkladu) nejsou vytvářeny modulem automaticky a měly by být v nasazení předběžně existující.</span><span class="sxs-lookup"><span data-stu-id="0c641-541">Folders in the path provided to the `<handlerSetting>` value (`logs` in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="0c641-542">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\{APP POOL NAME}` k poskytnutí oprávnění k zápisu, kde zástupný symbol `{APP POOL NAME}` je název fondu aplikací).</span><span class="sxs-lookup"><span data-stu-id="0c641-542">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="0c641-543">Hodnoty úrovně ladění ( `debugLevel` ) mohou zahrnovat jak úroveň, tak i umístění.</span><span class="sxs-lookup"><span data-stu-id="0c641-543">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="0c641-544">Úrovně (v pořadí od nejméně po nejpodrobnější):</span><span class="sxs-lookup"><span data-stu-id="0c641-544">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="0c641-545">CHYBA</span><span class="sxs-lookup"><span data-stu-id="0c641-545">ERROR</span></span>
* <span data-ttu-id="0c641-546">WARNING</span><span class="sxs-lookup"><span data-stu-id="0c641-546">WARNING</span></span>
* <span data-ttu-id="0c641-547">PŘÍJEMCE</span><span class="sxs-lookup"><span data-stu-id="0c641-547">INFO</span></span>
* <span data-ttu-id="0c641-548">TRACE</span><span class="sxs-lookup"><span data-stu-id="0c641-548">TRACE</span></span>

<span data-ttu-id="0c641-549">Umístění (je povoleno více umístění):</span><span class="sxs-lookup"><span data-stu-id="0c641-549">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="0c641-550">STROMU</span><span class="sxs-lookup"><span data-stu-id="0c641-550">CONSOLE</span></span>
* <span data-ttu-id="0c641-551">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="0c641-551">EVENTLOG</span></span>
* <span data-ttu-id="0c641-552">SOUBOR</span><span class="sxs-lookup"><span data-stu-id="0c641-552">FILE</span></span>

<span data-ttu-id="0c641-553">Nastavení obslužné rutiny lze také poskytnout pomocí proměnných prostředí:</span><span class="sxs-lookup"><span data-stu-id="0c641-553">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="0c641-554">`ASPNETCORE_MODULE_DEBUG_FILE`: Cesta k souboru protokolu ladění.</span><span class="sxs-lookup"><span data-stu-id="0c641-554">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="0c641-555">(Výchozí: `aspnetcore-debug.log` )</span><span class="sxs-lookup"><span data-stu-id="0c641-555">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="0c641-556">`ASPNETCORE_MODULE_DEBUG`: Nastavení úrovně ladění.</span><span class="sxs-lookup"><span data-stu-id="0c641-556">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="0c641-557">Nenechávejte **protokolování** ladění povolené v nasazení po delší dobu, než je potřeba k řešení problému.</span><span class="sxs-lookup"><span data-stu-id="0c641-557">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="0c641-558">Velikost protokolu není omezená.</span><span class="sxs-lookup"><span data-stu-id="0c641-558">The size of the log isn't limited.</span></span> <span data-ttu-id="0c641-559">Když necháte protokol ladění povolený, může dojít k vyčerpání dostupného místa na disku a selhání serveru nebo služby App Service.</span><span class="sxs-lookup"><span data-stu-id="0c641-559">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="0c641-560">Příklad prvku v souboru naleznete v tématu [konfigurace s web.config](#configuration-with-webconfig) `aspNetCore` `web.config` .</span><span class="sxs-lookup"><span data-stu-id="0c641-560">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="0c641-561">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="0c641-561">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="0c641-562">*Platí pouze pro hostování mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="0c641-562">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="0c641-563">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="0c641-563">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="0c641-564">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="0c641-564">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="0c641-565">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="0c641-565">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="0c641-566">Token párování se vytvoří a nastaví na proměnnou prostředí ( `ASPNETCORE_TOKEN` ) modulem.</span><span class="sxs-lookup"><span data-stu-id="0c641-566">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="0c641-567">Párovací token je také nastaven na hlavičku ( `MS-ASPNETCORE-TOKEN` ) pro každý požadavek proxy.</span><span class="sxs-lookup"><span data-stu-id="0c641-567">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="0c641-568">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0c641-568">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="0c641-569">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="0c641-569">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="0c641-570">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="0c641-570">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="0c641-571">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="0c641-571">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="0c641-572">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="0c641-572">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="0c641-573">Instalační služba modulu ASP.NET Core se spouští s oprávněními `TrustedInstaller` účtu.</span><span class="sxs-lookup"><span data-stu-id="0c641-573">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="0c641-574">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v `applicationHost.config` souboru ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="0c641-574">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="0c641-575">Pokud používáte konfiguraci IIS Shared na stejném počítači jako instalaci služby IIS, spusťte instalační program sady prostředků ASP.NET Core s `OPT_NO_SHARED_CONFIG_CHECK` parametrem nastaveným na `1` :</span><span class="sxs-lookup"><span data-stu-id="0c641-575">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="0c641-576">Pokud cesta ke sdílené konfiguraci není ve stejném počítači jako instalace služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="0c641-576">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="0c641-577">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="0c641-577">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="0c641-578">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="0c641-578">Run the installer.</span></span>
1. <span data-ttu-id="0c641-579">Exportujte aktualizovaný `applicationHost.config` soubor do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="0c641-579">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="0c641-580">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="0c641-580">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="0c641-581">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="0c641-581">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="0c641-582">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0c641-582">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="0c641-583">V hostitelském systému přejděte na `%windir%\System32\inetsrv` .</span><span class="sxs-lookup"><span data-stu-id="0c641-583">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="0c641-584">Vyhledejte `aspnetcore.dll` soubor.</span><span class="sxs-lookup"><span data-stu-id="0c641-584">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="0c641-585">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="0c641-585">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="0c641-586">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="0c641-586">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="0c641-587">V umístění se nacházejí protokoly instalačního balíčku pro modul `C:\\Users\\%UserName%\\AppData\\Local\\Temp` .</span><span class="sxs-lookup"><span data-stu-id="0c641-587">The Hosting Bundle installer logs for the module are found at `C:\\Users\\%UserName%\\AppData\\Local\\Temp`.</span></span> <span data-ttu-id="0c641-588">Soubor má název `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log` , kde zástupný symbol `{TIMESTAMP}` je časové razítko.</span><span class="sxs-lookup"><span data-stu-id="0c641-588">The file is named `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="0c641-589">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="0c641-589">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="0c641-590">Modul</span><span class="sxs-lookup"><span data-stu-id="0c641-590">Module</span></span>

<span data-ttu-id="0c641-591">**Služba IIS (x86/amd64)**:</span><span class="sxs-lookup"><span data-stu-id="0c641-591">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="0c641-592">**IIS Express (x86/amd64)**:</span><span class="sxs-lookup"><span data-stu-id="0c641-592">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="0c641-593">Schéma</span><span class="sxs-lookup"><span data-stu-id="0c641-593">Schema</span></span>

<span data-ttu-id="0c641-594">**IIS**</span><span class="sxs-lookup"><span data-stu-id="0c641-594">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="0c641-595">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="0c641-595">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="0c641-596">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="0c641-596">Configuration</span></span>

<span data-ttu-id="0c641-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="0c641-597">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="0c641-598">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="0c641-598">**IIS Express**</span></span>

* <span data-ttu-id="0c641-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="0c641-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="0c641-600">*iisexpress.exe* CLI `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="0c641-600">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="0c641-601">Soubory lze najít hledáním `aspnetcore` v `applicationHost.config` souboru.</span><span class="sxs-lookup"><span data-stu-id="0c641-601">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="0c641-602">Modul ASP.NET Core je nativním modulem služby IIS, který se připojuje k kanálu IIS a přesměrovává webové požadavky na back-endové aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0c641-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="0c641-603">Podporované verze systému Windows:</span><span class="sxs-lookup"><span data-stu-id="0c641-603">Supported Windows versions:</span></span>

* <span data-ttu-id="0c641-604">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="0c641-604">Windows 7 or later</span></span>
* <span data-ttu-id="0c641-605">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="0c641-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="0c641-606">Modul funguje pouze s Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0c641-606">The module only works with Kestrel.</span></span> <span data-ttu-id="0c641-607">Modul není kompatibilní s [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="0c641-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="0c641-608">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul také zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="0c641-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="0c641-609">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="0c641-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="0c641-610">To je v podstatě stejné chování jako u aplikací ASP.NET 4. x spouštěných vnitroprocesově ve službě IIS, které spravuje [Aktivační služba procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="0c641-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="0c641-611">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací:</span><span class="sxs-lookup"><span data-stu-id="0c641-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![Modul ASP.NET Core](iis/index/_static/ancm-outofprocess.png)

<span data-ttu-id="0c641-613">Požadavky přicházející z webu do ovladače HTTP.sys režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="0c641-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="0c641-614">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="0c641-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="0c641-615">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="0c641-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="0c641-616">Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server, na kterém má naslouchat `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="0c641-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="0c641-617">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="0c641-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="0c641-618">Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.</span><span class="sxs-lookup"><span data-stu-id="0c641-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="0c641-619">Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0c641-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="0c641-620">Kanál middlewaru zpracovává požadavek a předá ho jako `HttpContext` instanci do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="0c641-621">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="0c641-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="0c641-622">Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="0c641-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="0c641-623">Mnoho nativních modulů, jako je ověřování systému Windows, zůstane aktivní.</span><span class="sxs-lookup"><span data-stu-id="0c641-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="0c641-624">Další informace o aktivních modulech služby IIS pomocí modulu ASP.NET Core najdete v tématu <xref:host-and-deploy/iis/modules> .</span><span class="sxs-lookup"><span data-stu-id="0c641-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="0c641-625">Modul ASP.NET Core může také:</span><span class="sxs-lookup"><span data-stu-id="0c641-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="0c641-626">Nastavte proměnné prostředí pro pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="0c641-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="0c641-627">Protokoluje výstup stdout do úložiště souborů pro řešení problémů při spuštění.</span><span class="sxs-lookup"><span data-stu-id="0c641-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="0c641-628">Předejte tokeny ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="0c641-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="0c641-629">Jak nainstalovat a používat modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0c641-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="0c641-630">Pokyny k instalaci modulu ASP.NET Core najdete v tématu [instalace hostující sady .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="0c641-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="0c641-631">Konfigurace s web.config</span><span class="sxs-lookup"><span data-stu-id="0c641-631">Configuration with web.config</span></span>

<span data-ttu-id="0c641-632">Modul ASP.NET Core je nakonfigurován s `aspNetCore` částí `system.webServer` uzlu v souboru *web.config* webu.</span><span class="sxs-lookup"><span data-stu-id="0c641-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="0c641-633">Následující *web.config* soubor je publikován pro [nasazení závislé na rozhraní](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) a konfiguruje modul ASP.NET Core, aby zpracovával požadavky webu:</span><span class="sxs-lookup"><span data-stu-id="0c641-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

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

<span data-ttu-id="0c641-634">Následující *web.config* jsou publikovány pro [samostatně uzavřené nasazení](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="0c641-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

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

<span data-ttu-id="0c641-635">Když je aplikace nasazená na [Azure App Service](https://azure.microsoft.com/services/app-service/), `stdoutLogFile` cesta je nastavená na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="0c641-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="0c641-636">Cesta ukládá protokoly stdout do složky *soubory protokolů* , což je umístění, které služba automaticky vytvořila.</span><span class="sxs-lookup"><span data-stu-id="0c641-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="0c641-637">Informace o konfiguraci dílčí aplikace IIS najdete v tématu <xref:host-and-deploy/iis/index#sub-applications> .</span><span class="sxs-lookup"><span data-stu-id="0c641-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="0c641-638">Atributy elementu aspNetCore</span><span class="sxs-lookup"><span data-stu-id="0c641-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="0c641-639">Atribut</span><span class="sxs-lookup"><span data-stu-id="0c641-639">Attribute</span></span> | <span data-ttu-id="0c641-640">Popis</span><span class="sxs-lookup"><span data-stu-id="0c641-640">Description</span></span> | <span data-ttu-id="0c641-641">Výchozí</span><span class="sxs-lookup"><span data-stu-id="0c641-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="0c641-642">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="0c641-642">Optional string attribute.</span></span></p><p><span data-ttu-id="0c641-643">Argumenty ke spustitelnému souboru určenému v **processPath**.</span><span class="sxs-lookup"><span data-stu-id="0c641-643">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="0c641-644">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="0c641-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0c641-645">Pokud je nastaveno na true, stránka **502,5-procesového selhání** se potlačí a stavová stránka 502, která je nakonfigurovaná ve *web.config* , má přednost.</span><span class="sxs-lookup"><span data-stu-id="0c641-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="0c641-646">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="0c641-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0c641-647">Pokud je nastaveno na true, token se přepošle podřízenému procesu, který naslouchá na% ASPNETCORE_PORT% jako záhlaví MS-ASPNETCORE-WINAUTHTOKEN na žádost.</span><span class="sxs-lookup"><span data-stu-id="0c641-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="0c641-648">Je zodpovědností za tento proces volání CloseHandle na tento token na žádost.</span><span class="sxs-lookup"><span data-stu-id="0c641-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="0c641-649">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="0c641-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="0c641-650">Určuje počet instancí procesu určeného v nastavení **processPath** , které lze v rámci aplikace vystavit.</span><span class="sxs-lookup"><span data-stu-id="0c641-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="0c641-651">Nastavení `processesPerApplication` se nedoporučuje.</span><span class="sxs-lookup"><span data-stu-id="0c641-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="0c641-652">Tento atribut bude v budoucí verzi odebrán.</span><span class="sxs-lookup"><span data-stu-id="0c641-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="0c641-653">Výchozí `1`</span><span class="sxs-lookup"><span data-stu-id="0c641-653">Default: `1`</span></span><br><span data-ttu-id="0c641-654">Dlouhé `1`</span><span class="sxs-lookup"><span data-stu-id="0c641-654">Min: `1`</span></span><br><span data-ttu-id="0c641-655">Počet `100`</span><span class="sxs-lookup"><span data-stu-id="0c641-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="0c641-656">Povinný atribut řetězce.</span><span class="sxs-lookup"><span data-stu-id="0c641-656">Required string attribute.</span></span></p><p><span data-ttu-id="0c641-657">Cesta ke spustitelnému souboru, který spouští proces naslouchající na požadavky HTTP.</span><span class="sxs-lookup"><span data-stu-id="0c641-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="0c641-658">Jsou podporovány relativní cesty.</span><span class="sxs-lookup"><span data-stu-id="0c641-658">Relative paths are supported.</span></span> <span data-ttu-id="0c641-659">Pokud cesta začíná `.` , bude tato cesta považována za relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="0c641-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="0c641-660">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="0c641-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="0c641-661">Určuje, kolikrát je povoleno selhání procesu určeného v **processPath** za minutu.</span><span class="sxs-lookup"><span data-stu-id="0c641-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="0c641-662">Pokud je tento limit překročen, modul ukončí spuštění procesu po zbytek minuty.</span><span class="sxs-lookup"><span data-stu-id="0c641-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="0c641-663">Výchozí `10`</span><span class="sxs-lookup"><span data-stu-id="0c641-663">Default: `10`</span></span><br><span data-ttu-id="0c641-664">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="0c641-664">Min: `0`</span></span><br><span data-ttu-id="0c641-665">Počet `100`</span><span class="sxs-lookup"><span data-stu-id="0c641-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="0c641-666">Volitelný atribut TimeSpan.</span><span class="sxs-lookup"><span data-stu-id="0c641-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="0c641-667">Určuje dobu, po kterou modul ASP.NET Core čeká na odpověď od procesu, který naslouchá na% ASPNETCORE_PORT%.</span><span class="sxs-lookup"><span data-stu-id="0c641-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="0c641-668">Ve verzích modulu ASP.NET Core, které byly dodávány s vydáním ASP.NET Core 2,1 nebo vyšší, `requestTimeout` je určena v hodinách, minutách a sekundách.</span><span class="sxs-lookup"><span data-stu-id="0c641-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="0c641-669">Výchozí `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="0c641-669">Default: `00:02:00`</span></span><br><span data-ttu-id="0c641-670">Dlouhé `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="0c641-670">Min: `00:00:00`</span></span><br><span data-ttu-id="0c641-671">Počet `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="0c641-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="0c641-672">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="0c641-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="0c641-673">Doba v sekundách, po kterou modul čeká na řádné vypnutí spustitelného souboru, když se zjistí soubor *app_offline.htm* .</span><span class="sxs-lookup"><span data-stu-id="0c641-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="0c641-674">Výchozí `10`</span><span class="sxs-lookup"><span data-stu-id="0c641-674">Default: `10`</span></span><br><span data-ttu-id="0c641-675">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="0c641-675">Min: `0`</span></span><br><span data-ttu-id="0c641-676">Počet `600`</span><span class="sxs-lookup"><span data-stu-id="0c641-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="0c641-677">Volitelný celočíselný atribut</span><span class="sxs-lookup"><span data-stu-id="0c641-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="0c641-678">Doba v sekundách, kterou modul počká, než spustitelný soubor spustí proces, který na portu naslouchá.</span><span class="sxs-lookup"><span data-stu-id="0c641-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="0c641-679">Pokud je tento časový limit překročen, modul proces ukončuje.</span><span class="sxs-lookup"><span data-stu-id="0c641-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="0c641-680">Modul se pokusí znovu spustit proces, když obdrží novou žádost, a pokračuje v pokusu o restartování procesu u dalších příchozích požadavků, pokud se nepodaří spustit **rapidFailsPerMinute** počet pokusů během poslední minuty.</span><span class="sxs-lookup"><span data-stu-id="0c641-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="0c641-681">Hodnota 0 (nula **) není považována za** nekonečný časový limit.</span><span class="sxs-lookup"><span data-stu-id="0c641-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="0c641-682">Výchozí `120`</span><span class="sxs-lookup"><span data-stu-id="0c641-682">Default: `120`</span></span><br><span data-ttu-id="0c641-683">Dlouhé `0`</span><span class="sxs-lookup"><span data-stu-id="0c641-683">Min: `0`</span></span><br><span data-ttu-id="0c641-684">Počet `3600`</span><span class="sxs-lookup"><span data-stu-id="0c641-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="0c641-685">Volitelný atribut typu Boolean.</span><span class="sxs-lookup"><span data-stu-id="0c641-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="0c641-686">Je-li nastavena hodnota true, **stdout** a **stderr** pro proces zadaný v **processPath** budou přesměrovány do souboru zadaného v **stdoutLogFile**.</span><span class="sxs-lookup"><span data-stu-id="0c641-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="0c641-687">Volitelný řetězcový atribut.</span><span class="sxs-lookup"><span data-stu-id="0c641-687">Optional string attribute.</span></span></p><p><span data-ttu-id="0c641-688">Určuje relativní nebo absolutní cestu k souboru, pro který je **stdout** a **stderr** z procesu určeného v **processPath** protokolováno.</span><span class="sxs-lookup"><span data-stu-id="0c641-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="0c641-689">Relativní cesty jsou relativní vzhledem k kořenu webu.</span><span class="sxs-lookup"><span data-stu-id="0c641-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="0c641-690">Každá cesta začínající řetězcem `.` je relativní vzhledem k kořenu webu a všechny ostatní cesty se považují za absolutní cesty.</span><span class="sxs-lookup"><span data-stu-id="0c641-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="0c641-691">Aby modul vytvořil soubor protokolu, musí existovat všechny složky, které jsou v cestě k dispozici.</span><span class="sxs-lookup"><span data-stu-id="0c641-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="0c641-692">Pomocí oddělovačů podtržítka se do posledního segmentu cesty **stdoutLogFile** přidá časové razítko, ID procesu a Přípona souboru (*. log*).</span><span class="sxs-lookup"><span data-stu-id="0c641-692">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="0c641-693">Pokud `.\logs\stdout` je zadán jako hodnota, je ukázkový protokol stdout uložen jako *stdout_20180205194132_1934. log* ve složce *logs* při uložení na 2/5/2018 v 19:41:32 s ID procesu 1934.</span><span class="sxs-lookup"><span data-stu-id="0c641-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="0c641-694">Nastavení proměnných prostředí</span><span class="sxs-lookup"><span data-stu-id="0c641-694">Setting environment variables</span></span>

<span data-ttu-id="0c641-695">Proměnné prostředí lze zadat pro proces v `processPath` atributu.</span><span class="sxs-lookup"><span data-stu-id="0c641-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="0c641-696">Zadejte proměnnou prostředí s `<environmentVariable>` podřízeným elementem `<environmentVariables>` elementu kolekce.</span><span class="sxs-lookup"><span data-stu-id="0c641-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="0c641-697">Proměnné prostředí nastavené v této části jsou v konfliktu se sadou proměnných prostředí systému se stejným názvem.</span><span class="sxs-lookup"><span data-stu-id="0c641-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="0c641-698">Pokud je proměnná prostředí nastavena v souboru *web.config* i na úrovni systému systému Windows, bude hodnota ze souboru *web.config* připojena k hodnotě proměnné prostředí systému (například `ASPNETCORE_ENVIRONMENT: Development;Development` ), což zabrání spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="0c641-699">Následující příklad nastaví dvě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0c641-699">The following example sets two environment variables.</span></span> <span data-ttu-id="0c641-700">`ASPNETCORE_ENVIRONMENT` nakonfiguruje prostředí aplikace na `Development` .</span><span class="sxs-lookup"><span data-stu-id="0c641-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="0c641-701">Vývojář může tuto hodnotu dočasně nastavit v souboru *web.config* , aby bylo možné vynutit, aby se [Stránka s výjimkou vývojářů](xref:fundamentals/error-handling) načetla při ladění výjimky aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="0c641-702">`CONFIG_DIR` je příkladem uživatelsky definované proměnné prostředí, kde vývojář napsal kód, který přečte hodnotu při spuštění, aby vytvořil cestu pro načtení konfiguračního souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

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

> [!WARNING]
> <span data-ttu-id="0c641-703">Nastavte pouze `ASPNETCORE_ENVIRONMENT` proměnnou prostředí na `Development` pracovní a testovací servery, které nejsou přístupné nedůvěryhodným sítím, jako je například Internet.</span><span class="sxs-lookup"><span data-stu-id="0c641-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="0c641-704">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="0c641-704">app_offline.htm</span></span>

<span data-ttu-id="0c641-705">Pokud se v kořenovém adresáři aplikace zjistí soubor s názvem *app_offline.htm* , modul ASP.NET Core se pokusí aplikaci řádně vypnout a zastavit zpracování příchozích požadavků.</span><span class="sxs-lookup"><span data-stu-id="0c641-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="0c641-706">Pokud aplikace běží i po uplynutí počtu sekund definovaného v `shutdownTimeLimit` , modul ASP.NET Core ukončuje běžící proces.</span><span class="sxs-lookup"><span data-stu-id="0c641-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="0c641-707">Když je přítomen soubor *app_offline.htm* , ASP.NET Core modul reaguje na požadavky odesláním zpět obsahu *app_offline.htm* souboru.</span><span class="sxs-lookup"><span data-stu-id="0c641-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="0c641-708">Po odebrání souboru *app_offline.htm* spustí aplikace další požadavek.</span><span class="sxs-lookup"><span data-stu-id="0c641-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="0c641-709">Spouštěcí chybová stránka</span><span class="sxs-lookup"><span data-stu-id="0c641-709">Start-up error page</span></span>

<span data-ttu-id="0c641-710">Pokud se v modulu ASP.NET Core nepodaří spustit back-end proces nebo se spustí back-end proces, ale nenaslouchá na konfigurovaném portu, zobrazí se stránka s kódem stavu *selhání procesu 502,5* .</span><span class="sxs-lookup"><span data-stu-id="0c641-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="0c641-711">Chcete-li tuto stránku potlačit a vrátit se na výchozí stavovou stránku služby IIS 502, použijte `disableStartUpErrorPage` atribut.</span><span class="sxs-lookup"><span data-stu-id="0c641-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="0c641-712">Další informace o konfiguraci vlastních chybových zpráv najdete v tématu [chyby \<httpErrors> protokolu HTTP ](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="0c641-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="0c641-713">Vytvoření a přesměrování protokolu</span><span class="sxs-lookup"><span data-stu-id="0c641-713">Log creation and redirection</span></span>

<span data-ttu-id="0c641-714">Modul ASP.NET Core přesměruje výstup konzoly stdout a stderr na disk, pokud `stdoutLogEnabled` `stdoutLogFile` `aspNetCore` jsou nastaveny atributy a elementu.</span><span class="sxs-lookup"><span data-stu-id="0c641-714">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="0c641-715">`stdoutLogFile`Při vytvoření souboru protokolu jsou všechny složky v této cestě vytvářeny modulem.</span><span class="sxs-lookup"><span data-stu-id="0c641-715">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="0c641-716">Fond aplikací musí mít oprávnění k zápisu do umístění, kam se protokoly zapisují (použijte `IIS AppPool\<app_pool_name>` k poskytnutí oprávnění k zápisu).</span><span class="sxs-lookup"><span data-stu-id="0c641-716">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="0c641-717">Protokoly nejsou otočeny, pokud nedochází k recyklování/restartování procesu.</span><span class="sxs-lookup"><span data-stu-id="0c641-717">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="0c641-718">Je zodpovědností hostitele omezit místo na disku, které používají protokoly.</span><span class="sxs-lookup"><span data-stu-id="0c641-718">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="0c641-719">Použití protokolu stdout se doporučuje pouze při řešení potíží se spouštěním aplikací při hostování služby IIS nebo při použití [podpory v době vývoje služby IIS se sadou Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), nikoli při ladění místně a při spuštění aplikace s IIS Express.</span><span class="sxs-lookup"><span data-stu-id="0c641-719">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="0c641-720">Nepoužívejte protokol stdout pro obecné účely protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="0c641-720">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="0c641-721">Pro rutinu protokolování v aplikaci ASP.NET Core použijte knihovnu protokolování, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="0c641-721">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="0c641-722">Další informace najdete v tématu [Zprostředkovatelé protokolování třetích stran](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="0c641-722">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="0c641-723">Časové razítko a Přípona souboru se automaticky přidají při vytvoření souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="0c641-723">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="0c641-724">Název souboru protokolu se skládá připojením časového razítka, ID procesu a přípony souboru (*. log*) k poslednímu segmentu `stdoutLogFile` cesty (obvykle *stdout*), oddělený podtržítky.</span><span class="sxs-lookup"><span data-stu-id="0c641-724">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="0c641-725">Pokud `stdoutLogFile` cesta končí na *stdout*, protokol pro aplikaci s číslem PID 1934 vytvořeným v 2/5/2018 v 19:42:32 má název souboru *stdout_20180205194132_1934. log*.</span><span class="sxs-lookup"><span data-stu-id="0c641-725">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="0c641-726">Následující vzorový `aspNetCore` prvek nakonfiguruje protokolování stdout na relativní cestě `.\log\` .</span><span class="sxs-lookup"><span data-stu-id="0c641-726">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="0c641-727">Potvrďte, že identita uživatele fondu aplikací má oprávnění k zápisu do zadané cesty.</span><span class="sxs-lookup"><span data-stu-id="0c641-727">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="0c641-728">Když publikujete aplikaci pro nasazení Azure App Service, sada web SDK nastaví `stdoutLogFile` hodnotu na `\\?\%home%\LogFiles\stdout` .</span><span class="sxs-lookup"><span data-stu-id="0c641-728">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="0c641-729">`%home`Proměnná prostředí je předdefinovaná pro aplikace hostované v Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="0c641-729">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="0c641-730">Chcete-li vytvořit pravidla filtru protokolování, přečtěte si část [Konfigurace](xref:fundamentals/logging/index#log-filtering) a [filtrování protokolů](xref:fundamentals/logging/index#log-filtering) v dokumentaci ASP.NET Core protokolování.</span><span class="sxs-lookup"><span data-stu-id="0c641-730">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="0c641-731">Další informace o formátech cest najdete v tématu [formáty souborů cest v systémech Windows](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="0c641-731">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="0c641-732">Konfigurace proxy serveru používá protokol HTTP a token párování.</span><span class="sxs-lookup"><span data-stu-id="0c641-732">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="0c641-733">Proxy vytvořený mezi modulem ASP.NET Core a Kestrel používá protokol HTTP.</span><span class="sxs-lookup"><span data-stu-id="0c641-733">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="0c641-734">Nehrozí riziko odposlouchávání provozu mezi modulem a Kestrel z umístění od serveru.</span><span class="sxs-lookup"><span data-stu-id="0c641-734">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="0c641-735">Párovací token se používá k zajištění, že žádosti přijaté službou Kestrel byly proxy službou IIS a nepocházejí z jiného zdroje.</span><span class="sxs-lookup"><span data-stu-id="0c641-735">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="0c641-736">Token párování se vytvoří a nastaví na proměnnou prostředí ( `ASPNETCORE_TOKEN` ) modulem.</span><span class="sxs-lookup"><span data-stu-id="0c641-736">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="0c641-737">Párovací token je také nastaven na hlavičku ( `MS-ASPNETCORE-TOKEN` ) pro každý požadavek proxy.</span><span class="sxs-lookup"><span data-stu-id="0c641-737">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="0c641-738">Middleware služby IIS kontroluje každý požadavek, který obdrží, a potvrdí, že hodnota hlavičky párového tokenu odpovídá hodnotě proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="0c641-738">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="0c641-739">Pokud se hodnoty tokenu neshodují, požadavek se zaprotokoluje a odmítne.</span><span class="sxs-lookup"><span data-stu-id="0c641-739">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="0c641-740">Proměnná prostředí párového tokenu a přenos dat mezi modulem a Kestrel nejsou přístupné z umístění mimo server.</span><span class="sxs-lookup"><span data-stu-id="0c641-740">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="0c641-741">Bez znalosti hodnoty párovacího tokenu nemůže útočník odeslat žádosti, které obcházejí kontrolu v Middlewari IIS.</span><span class="sxs-lookup"><span data-stu-id="0c641-741">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="0c641-742">ASP.NET Core modul se sdílenou konfigurací služby IIS</span><span class="sxs-lookup"><span data-stu-id="0c641-742">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="0c641-743">Instalační služba modulu ASP.NET Core se spouští s oprávněními účtu **TrustedInstaller** .</span><span class="sxs-lookup"><span data-stu-id="0c641-743">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="0c641-744">Vzhledem k tomu, že účet místního systému nemá oprávnění k úpravám pro cestu ke sdílené složce, kterou používá sdílená konfigurace služby IIS, vyvolá Instalační program chybu odepření přístupu při pokusu o konfiguraci nastavení modulu v souboru *applicationHost.config* ve sdílené složce.</span><span class="sxs-lookup"><span data-stu-id="0c641-744">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="0c641-745">Pokud používáte sdílenou konfiguraci služby IIS, postupujte podle těchto kroků:</span><span class="sxs-lookup"><span data-stu-id="0c641-745">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="0c641-746">Zakažte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="0c641-746">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="0c641-747">Spusťte instalační program.</span><span class="sxs-lookup"><span data-stu-id="0c641-747">Run the installer.</span></span>
1. <span data-ttu-id="0c641-748">Exportujte aktualizovaný soubor *applicationHost.config* do sdílené složky.</span><span class="sxs-lookup"><span data-stu-id="0c641-748">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="0c641-749">Znovu povolte sdílenou konfiguraci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="0c641-749">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="0c641-750">Verze modulu a instalační protokoly hostitelské sady</span><span class="sxs-lookup"><span data-stu-id="0c641-750">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="0c641-751">Postup určení verze nainstalovaného modulu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="0c641-751">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="0c641-752">V hostitelském systému přejděte na *%windir%\system32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="0c641-752">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="0c641-753">Vyhledejte soubor *aspnetcore.dll* .</span><span class="sxs-lookup"><span data-stu-id="0c641-753">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="0c641-754">Klikněte na soubor pravým tlačítkem a v místní nabídce vyberte **vlastnosti** .</span><span class="sxs-lookup"><span data-stu-id="0c641-754">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="0c641-755">Vyberte kartu **Podrobnosti** . **Verze souboru** a **verze produktu** reprezentují nainstalovanou verzi modulu.</span><span class="sxs-lookup"><span data-stu-id="0c641-755">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="0c641-756">Protokoly instalačních balíčků pro modul se nacházejí v *C: \\ Uživatelé \\ % UserName% \\ data \\ Local \\ TEMP*. Soubor má název *dd_DotNetCoreWinSvrHosting__ \<timestamp> _000_AspNetCoreModule_x64. log*.</span><span class="sxs-lookup"><span data-stu-id="0c641-756">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="0c641-757">Umístění souboru modulu, schématu a konfiguračního souboru</span><span class="sxs-lookup"><span data-stu-id="0c641-757">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="0c641-758">Modul</span><span class="sxs-lookup"><span data-stu-id="0c641-758">Module</span></span>

<span data-ttu-id="0c641-759">**Služba IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="0c641-759">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="0c641-760">% windir% \System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="0c641-760">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="0c641-761">% windir% \SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="0c641-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="0c641-762">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="0c641-762">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="0c641-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="0c641-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="0c641-764">% ProgramFiles (x86)% \ IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="0c641-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="0c641-765">Schéma</span><span class="sxs-lookup"><span data-stu-id="0c641-765">Schema</span></span>

<span data-ttu-id="0c641-766">**IIS**</span><span class="sxs-lookup"><span data-stu-id="0c641-766">**IIS**</span></span>

* <span data-ttu-id="0c641-767">% windir% \System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="0c641-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="0c641-768">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="0c641-768">**IIS Express**</span></span>

* <span data-ttu-id="0c641-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="0c641-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="0c641-770">Konfigurace</span><span class="sxs-lookup"><span data-stu-id="0c641-770">Configuration</span></span>

<span data-ttu-id="0c641-771">**IIS**</span><span class="sxs-lookup"><span data-stu-id="0c641-771">**IIS**</span></span>

* <span data-ttu-id="0c641-772">% windir% \System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="0c641-772">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="0c641-773">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="0c641-773">**IIS Express**</span></span>

* <span data-ttu-id="0c641-774">Visual Studio: {ROOT aplikace} \\.vs\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="0c641-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="0c641-775">*iisexpress.exe* CLI:% USERPROFILE% \Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="0c641-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="0c641-776">Soubory můžete najít hledáním *aspnetcore* v souboru *applicationHost.config* .</span><span class="sxs-lookup"><span data-stu-id="0c641-776">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="0c641-777">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="0c641-777">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="0c641-778">[Zdroj odkazu na modul ASP.NET Core (hlavní větev)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): k výběru konkrétní verze (například) použijte rozevírací seznam **větev** `release/3.1` .</span><span class="sxs-lookup"><span data-stu-id="0c641-778">[ASP.NET Core Module reference source (master branch)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
