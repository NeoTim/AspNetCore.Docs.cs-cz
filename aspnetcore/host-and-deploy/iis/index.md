---
title: Hostitele ASP.NET Core ve Windows se službou IIS
author: guardrex
description: Zjistěte, jak hostovat aplikace ASP.NET Core na Windows serveru Internetové informační služby (IIS).
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: host-and-deploy/iis/index
ms.openlocfilehash: 146a204509856186a2696b770cae2249d348fa34
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726836"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a><span data-ttu-id="57fe9-103">Hostitele ASP.NET Core ve Windows se službou IIS</span><span class="sxs-lookup"><span data-stu-id="57fe9-103">Host ASP.NET Core on Windows with IIS</span></span>

<span data-ttu-id="57fe9-104">Od [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="57fe9-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="57fe9-105">Kurz týkající se publikování ASP.NET Core aplikace na server služby IIS najdete v tématu <xref:tutorials/publish-to-iis>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-105">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

[<span data-ttu-id="57fe9-106">Instalace hostující sady .NET Core</span><span class="sxs-lookup"><span data-stu-id="57fe9-106">Install the .NET Core Hosting Bundle</span></span>](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a><span data-ttu-id="57fe9-107">Podporované operační systémy</span><span class="sxs-lookup"><span data-stu-id="57fe9-107">Supported operating systems</span></span>

<span data-ttu-id="57fe9-108">Podporovány jsou následující operační systémy:</span><span class="sxs-lookup"><span data-stu-id="57fe9-108">The following operating systems are supported:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="57fe9-109">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="57fe9-109">Windows 7 or later</span></span>
* <span data-ttu-id="57fe9-110">Windows Server 2012 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="57fe9-110">Windows Server 2012 R2 or later</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="57fe9-111">Windows 7 nebo novější</span><span class="sxs-lookup"><span data-stu-id="57fe9-111">Windows 7 or later</span></span>
* <span data-ttu-id="57fe9-112">Windows Server 2008 R2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="57fe9-112">Windows Server 2008 R2 or later</span></span>

::: moniker-end

<span data-ttu-id="57fe9-113">[Http. sys Server](xref:fundamentals/servers/httpsys) (dříve označovaný jako weblisten) nefunguje v konfiguraci reverzního proxy serveru se službou IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-113">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener) doesn't work in a reverse proxy configuration with IIS.</span></span> <span data-ttu-id="57fe9-114">Použijte [Server Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="57fe9-114">Use the [Kestrel server](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="57fe9-115">Informace o hostování v Azure najdete v tématu <xref:host-and-deploy/azure-apps/index>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-115">For information on hosting in Azure, see <xref:host-and-deploy/azure-apps/index>.</span></span>

<span data-ttu-id="57fe9-116">Pokyny k řešení potíží najdete v tématu <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-116">For troubleshooting guidance, see <xref:test/troubleshoot>.</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="57fe9-117">Podporované platformy</span><span class="sxs-lookup"><span data-stu-id="57fe9-117">Supported platforms</span></span>

<span data-ttu-id="57fe9-118">Podporují se aplikace publikované pro nasazení 32 (x86) nebo 64-bit (x64).</span><span class="sxs-lookup"><span data-stu-id="57fe9-118">Apps published for 32-bit (x86) or 64-bit (x64) deployment are supported.</span></span> <span data-ttu-id="57fe9-119">Nasazení 32 aplikace s 32 (x86) .NET Core SDK, pokud aplikace:</span><span class="sxs-lookup"><span data-stu-id="57fe9-119">Deploy a 32-bit app with a 32-bit (x86) .NET Core SDK unless the app:</span></span>

* <span data-ttu-id="57fe9-120">Vyžaduje větší dostupný adresní prostor virtuální paměti pro 64 aplikaci.</span><span class="sxs-lookup"><span data-stu-id="57fe9-120">Requires the larger virtual memory address space available to a 64-bit app.</span></span>
* <span data-ttu-id="57fe9-121">Vyžaduje větší velikost zásobníku služby IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-121">Requires the larger IIS stack size.</span></span>
* <span data-ttu-id="57fe9-122">Má 64 nativní závislosti.</span><span class="sxs-lookup"><span data-stu-id="57fe9-122">Has 64-bit native dependencies.</span></span>

<span data-ttu-id="57fe9-123">K publikování 64 aplikace použijte 64 .NET Core SDK (x64).</span><span class="sxs-lookup"><span data-stu-id="57fe9-123">Use a 64-bit (x64) .NET Core SDK to publish a 64-bit app.</span></span> <span data-ttu-id="57fe9-124">V hostitelském systému musí být nainstalován 64 modul runtime.</span><span class="sxs-lookup"><span data-stu-id="57fe9-124">A 64-bit runtime must be present on the host system.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="hosting-models"></a><span data-ttu-id="57fe9-125">Modely hostingu</span><span class="sxs-lookup"><span data-stu-id="57fe9-125">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="57fe9-126">Model hostování v procesu</span><span class="sxs-lookup"><span data-stu-id="57fe9-126">In-process hosting model</span></span>

<span data-ttu-id="57fe9-127">Při použití hostování v rámci procesu ASP.NET Core aplikace běží ve stejném procesu jako jeho pracovní proces služby IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-127">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="57fe9-128">Hostování v rámci procesů poskytují lepší výkon než hostování mimo procesy, protože požadavky nejsou proxy serverem přes adaptér zpětné smyčky, síťové rozhraní, které vrátí odchozí síťový provoz zpátky do stejného počítače.</span><span class="sxs-lookup"><span data-stu-id="57fe9-128">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="57fe9-129">Služba IIS zpracovává správu procesů pomocí [aktivační služby procesů systému Windows (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="57fe9-129">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="57fe9-130">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module):</span><span class="sxs-lookup"><span data-stu-id="57fe9-130">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module):</span></span>

* <span data-ttu-id="57fe9-131">Provede inicializaci aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-131">Performs app initialization.</span></span>
  * <span data-ttu-id="57fe9-132">Načte [CoreCLR](/dotnet/standard/glossary#coreclr).</span><span class="sxs-lookup"><span data-stu-id="57fe9-132">Loads the [CoreCLR](/dotnet/standard/glossary#coreclr).</span></span>
  * <span data-ttu-id="57fe9-133">Volá `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-133">Calls `Program.Main`.</span></span>
* <span data-ttu-id="57fe9-134">Zpracovává životnost nativního požadavku služby IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-134">Handles the lifetime of the IIS native request.</span></span>

<span data-ttu-id="57fe9-135">Model hostování v rámci procesu není podporován pro aplikace ASP.NET Core, které cílí na .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="57fe9-135">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="57fe9-136">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou v procesu:</span><span class="sxs-lookup"><span data-stu-id="57fe9-136">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![ASP.NET Core modul ve scénáři hostování v rámci procesu](index/_static/ancm-inprocess.png)

<span data-ttu-id="57fe9-138">Požadavek přijde z webu do ovladače HTTP. sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="57fe9-138">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="57fe9-139">Ovladač směruje nativní požadavek na IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="57fe9-139">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="57fe9-140">Modul ASP.NET Core obdrží nativní požadavek a předá ho k serveru HTTP služby IIS (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="57fe9-140">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="57fe9-141">HTTP server IIS je vnitroprocesové implementace v rámci procesového serveru pro službu IIS, která převádí požadavek z nativního na spravovanou.</span><span class="sxs-lookup"><span data-stu-id="57fe9-141">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="57fe9-142">Poté, co server HTTP služby IIS požadavek zpracuje, je požadavek vložen do kanálu middleware ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57fe9-142">After the IIS HTTP Server processes the request, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="57fe9-143">Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-143">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="57fe9-144">Odpověď aplikace se předává zpět službě IIS prostřednictvím serveru IIS HTTP.</span><span class="sxs-lookup"><span data-stu-id="57fe9-144">The app's response is passed back to IIS through IIS HTTP Server.</span></span> <span data-ttu-id="57fe9-145">Služba IIS odešle odpověď klientovi, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="57fe9-145">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="57fe9-146">Vnitroprocesové hostování v rámci procesu je výslovný souhlas pro existující aplikace, ale [dotnet nové](/dotnet/core/tools/dotnet-new) šablony jsou výchozí pro všechny scénáře hostování v rámci procesu pro všechny služby IIS a IIS Express.</span><span class="sxs-lookup"><span data-stu-id="57fe9-146">In-process hosting is opt-in for existing apps, but [dotnet new](/dotnet/core/tools/dotnet-new) templates default to the in-process hosting model for all IIS and IIS Express scenarios.</span></span>

<span data-ttu-id="57fe9-147">`CreateDefaultBuilder` přidá instanci <xref:Microsoft.AspNetCore.Hosting.Server.IServer> voláním metody <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> pro spuštění [CoreCLR](/dotnet/standard/glossary#coreclr) a hostování aplikace uvnitř pracovního procesu služby IIS (*W3wp. exe* nebo *IISExpress. exe*).</span><span class="sxs-lookup"><span data-stu-id="57fe9-147">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (*w3wp.exe* or *iisexpress.exe*).</span></span> <span data-ttu-id="57fe9-148">Testy výkonu označují, že hostování aplikace .NET Core v rámci procesu přináší výrazně vyšší propustnost žádostí v porovnání s hostováním aplikací mimo proces a požadavky na proxy serveru na [Kestrel](xref:fundamentals/servers/kestrel) Server.</span><span class="sxs-lookup"><span data-stu-id="57fe9-148">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

> [!NOTE]
> <span data-ttu-id="57fe9-149">Aplikace publikované jako spustitelný soubor s jedním souborem nejde načíst pomocí modelu hostování v rámci procesu.</span><span class="sxs-lookup"><span data-stu-id="57fe9-149">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="57fe9-150">Model hostování mimo proces</span><span class="sxs-lookup"><span data-stu-id="57fe9-150">Out-of-process hosting model</span></span>

<span data-ttu-id="57fe9-151">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul ASP.NET Core zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="57fe9-151">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="57fe9-152">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="57fe9-152">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="57fe9-153">To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="57fe9-153">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="57fe9-154">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou mimo proces:</span><span class="sxs-lookup"><span data-stu-id="57fe9-154">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core modul ve scénáři hostování mimo proces](index/_static/ancm-outofprocess.png)

<span data-ttu-id="57fe9-156">Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="57fe9-156">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="57fe9-157">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="57fe9-157">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="57fe9-158">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="57fe9-158">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="57fe9-159">Modul Určuje port prostřednictvím proměnné prostředí při spuštění a rozšíření <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> nakonfiguruje server tak, aby naslouchal `http://localhost:{PORT}`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-159">The module specifies the port via an environment variable at startup, and the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="57fe9-160">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="57fe9-160">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="57fe9-161">Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.</span><span class="sxs-lookup"><span data-stu-id="57fe9-161">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="57fe9-162">Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57fe9-162">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="57fe9-163">Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-163">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="57fe9-164">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="57fe9-164">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="57fe9-165">Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="57fe9-165">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="57fe9-166">ASP.NET Core se dodává se [serverem Kestrel](xref:fundamentals/servers/kestrel), výchozím serverem HTTP pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="57fe9-166">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), a default, cross-platform HTTP server.</span></span>

<span data-ttu-id="57fe9-167">Při použití [služby IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) nebo [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)aplikace běží v procesu odděleně od pracovního procesu služby IIS (*mimo proces*) se [serverem Kestrel](xref:fundamentals/servers/index#kestrel).</span><span class="sxs-lookup"><span data-stu-id="57fe9-167">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](xref:fundamentals/servers/index#kestrel).</span></span>

<span data-ttu-id="57fe9-168">Vzhledem k tomu, že ASP.NET Core aplikace běží v procesu odděleném od pracovního procesu služby IIS, modul zpracovává správu procesů.</span><span class="sxs-lookup"><span data-stu-id="57fe9-168">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="57fe9-169">Modul spustí proces pro aplikaci ASP.NET Core, když první požadavek dorazí a restartuje aplikaci, pokud se ukončí nebo dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="57fe9-169">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="57fe9-170">To je v podstatě stejné chování jako u aplikací, které běží v procesu, které jsou spravovány službou was [(Windows Process Activation Service)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="57fe9-170">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="57fe9-171">Následující diagram znázorňuje vztah mezi službou IIS, modulem ASP.NET Core a aplikací hostovanou mimo proces:</span><span class="sxs-lookup"><span data-stu-id="57fe9-171">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Modul ASP.NET Core](index/_static/ancm-outofprocess.png)

<span data-ttu-id="57fe9-173">Požadavky přicházející z webu do ovladače HTTP. sys v režimu jádra.</span><span class="sxs-lookup"><span data-stu-id="57fe9-173">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="57fe9-174">Ovladač směruje požadavky do služby IIS na konfigurovaném portu webu, obvykle 80 (HTTP) nebo 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="57fe9-174">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="57fe9-175">Modul předá požadavky do Kestrel na náhodném portu pro aplikaci, což není port 80 nebo 443.</span><span class="sxs-lookup"><span data-stu-id="57fe9-175">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="57fe9-176">Modul Určuje port prostřednictvím proměnné prostředí při spuštění a [Služba IIS Integration middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) nakonfiguruje server tak, aby naslouchal `http://localhost:{port}`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-176">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="57fe9-177">Budou provedeny další kontroly a požadavky, které nepocházejí z modulu, jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="57fe9-177">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="57fe9-178">Modul nepodporuje předávání HTTPS, takže požadavky se předávají přes protokol HTTP i v případě, že je služba IIS prostřednictvím protokolu HTTPS přijímá.</span><span class="sxs-lookup"><span data-stu-id="57fe9-178">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="57fe9-179">Po Kestrel žádosti z modulu se požadavek odešle do kanálu middlewaru ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57fe9-179">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="57fe9-180">Kanál middlewaru zpracovává požadavek a předá ho jako instanci `HttpContext` do logiky aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-180">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="57fe9-181">Middleware přidaný integrací služby IIS: aktualizace schématu, vzdálené IP adresy a pathbase pro přesměrování požadavku do Kestrel.</span><span class="sxs-lookup"><span data-stu-id="57fe9-181">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="57fe9-182">Odpověď aplikace se předává zpátky do služby IIS, která ji přenáší zpátky do klienta HTTP, který žádost inicioval.</span><span class="sxs-lookup"><span data-stu-id="57fe9-182">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="57fe9-183">`CreateDefaultBuilder` nakonfiguruje server [Kestrel](xref:fundamentals/servers/kestrel) jako webový server a povolí integraci služby IIS konfigurací základní cesty a portu pro [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="57fe9-183">`CreateDefaultBuilder` configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server and enables IIS Integration by configuring the base path and port for the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="57fe9-184">Modul ASP.NET Core generuje dynamický port přiřadit k procesu back-endu.</span><span class="sxs-lookup"><span data-stu-id="57fe9-184">The ASP.NET Core Module generates a dynamic port to assign to the backend process.</span></span> <span data-ttu-id="57fe9-185">`CreateDefaultBuilder` volá metodu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-185">`CreateDefaultBuilder` calls the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> method.</span></span> <span data-ttu-id="57fe9-186">`UseIISIntegration` nakonfiguruje Kestrel k naslouchání na dynamickém portu na IP adrese localhost (`127.0.0.1`).</span><span class="sxs-lookup"><span data-stu-id="57fe9-186">`UseIISIntegration` configures Kestrel to listen on the dynamic port at the localhost IP address (`127.0.0.1`).</span></span> <span data-ttu-id="57fe9-187">Pokud je dynamický port 1234, Kestrel naslouchá na `127.0.0.1:1234`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-187">If the dynamic port is 1234, Kestrel listens at `127.0.0.1:1234`.</span></span> <span data-ttu-id="57fe9-188">Tato konfigurace nahrazuje jiné konfigurace adresy URL poskytnuté:</span><span class="sxs-lookup"><span data-stu-id="57fe9-188">This configuration replaces other URL configurations provided by:</span></span>

* `UseUrls`
* [<span data-ttu-id="57fe9-189">Rozhraní API pro naslouchání Kestrel</span><span class="sxs-lookup"><span data-stu-id="57fe9-189">Kestrel's Listen API</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* <span data-ttu-id="57fe9-190">[Konfigurace](xref:fundamentals/configuration/index) (nebo [příkazového řádku – možnost adresy URL](xref:fundamentals/host/web-host#override-configuration))</span><span class="sxs-lookup"><span data-stu-id="57fe9-190">[Configuration](xref:fundamentals/configuration/index) (or [command-line --urls option](xref:fundamentals/host/web-host#override-configuration))</span></span>

<span data-ttu-id="57fe9-191">Při použití modulu se nevyžadují volání `UseUrls` nebo rozhraní API pro `Listen` Kestrel.</span><span class="sxs-lookup"><span data-stu-id="57fe9-191">Calls to `UseUrls` or Kestrel's `Listen` API aren't required when using the module.</span></span> <span data-ttu-id="57fe9-192">Pokud je zavolána `UseUrls` nebo `Listen`, Kestrel naslouchá na portu určeném pouze při spuštění aplikace bez služby IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-192">If `UseUrls` or `Listen` is called, Kestrel listens on the port specified only when running the app without IIS.</span></span>

::: moniker-end

<span data-ttu-id="57fe9-193">Pokyny ke konfiguraci ASP.NET Core modulu najdete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-193">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="57fe9-194">Další informace o hostování najdete v tématu [hostitel v ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="57fe9-194">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="57fe9-195">Konfigurace aplikací</span><span class="sxs-lookup"><span data-stu-id="57fe9-195">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="57fe9-196">Povolit IISIntegration součásti</span><span class="sxs-lookup"><span data-stu-id="57fe9-196">Enable the IISIntegration components</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="57fe9-197">Při sestavování hostitele v `CreateHostBuilder` (*program.cs*) volejte <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> a povolte integraci služby IIS:</span><span class="sxs-lookup"><span data-stu-id="57fe9-197">When building a host in `CreateHostBuilder` (*Program.cs*), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="57fe9-198">Další informace o `CreateDefaultBuilder`najdete v tématu <xref:fundamentals/host/generic-host#default-builder-settings>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-198">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="57fe9-199">Při sestavování hostitele v `CreateWebHostBuilder` (*program.cs*) volejte <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> a povolte integraci služby IIS:</span><span class="sxs-lookup"><span data-stu-id="57fe9-199">When building a host in `CreateWebHostBuilder` (*Program.cs*), call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to enable IIS integration:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="57fe9-200">Další informace o `CreateDefaultBuilder`najdete v tématu <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-200">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

::: moniker-end

### <a name="iis-options"></a><span data-ttu-id="57fe9-201">Možnosti služby IIS</span><span class="sxs-lookup"><span data-stu-id="57fe9-201">IIS options</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="57fe9-202">**Model hostování v procesu**</span><span class="sxs-lookup"><span data-stu-id="57fe9-202">**In-process hosting model**</span></span>

<span data-ttu-id="57fe9-203">Pokud chcete nakonfigurovat možnosti serveru IIS, zahrňte do <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-203">To configure IIS Server options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="57fe9-204">Následující příklad zakazuje AutomaticAuthentication:</span><span class="sxs-lookup"><span data-stu-id="57fe9-204">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="57fe9-205">Možnost</span><span class="sxs-lookup"><span data-stu-id="57fe9-205">Option</span></span>                         | <span data-ttu-id="57fe9-206">Výchozí</span><span class="sxs-lookup"><span data-stu-id="57fe9-206">Default</span></span> | <span data-ttu-id="57fe9-207">Nastavení</span><span class="sxs-lookup"><span data-stu-id="57fe9-207">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="57fe9-208">Pokud `true`, server IIS nastaví `HttpContext.User` ověřený [ověřováním systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="57fe9-208">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="57fe9-209">Pokud `false`, Server poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, které jsou výslovně požadovány `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-209">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="57fe9-210">Aby mohla funkce `AutomaticAuthentication` fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="57fe9-210">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="57fe9-211">Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="57fe9-211">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="57fe9-212">Nastaví zobrazovaný název, který se uživatelům na přihlašovací stránky zobrazí.</span><span class="sxs-lookup"><span data-stu-id="57fe9-212">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO`           | `false` | <span data-ttu-id="57fe9-213">Zda je povolena synchronní v/v pro `HttpContext.Request` a `HttpContext.Response`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-213">Whether synchronous IO is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize`           | `30000000`  | <span data-ttu-id="57fe9-214">Získá nebo nastaví maximální velikost textu požadavku pro `HttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-214">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="57fe9-215">Všimněte si, že služba IIS má limit `maxAllowedContentLength`, který bude zpracován před `MaxRequestBodySize` nastavenou v `IISServerOptions`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-215">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="57fe9-216">Změna `MaxRequestBodySize` nebude mít vliv na `maxAllowedContentLength`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-216">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="57fe9-217">Chcete-li zvýšit `maxAllowedContentLength`, přidejte položku do *souboru Web. config* a nastavte `maxAllowedContentLength` na vyšší hodnotu.</span><span class="sxs-lookup"><span data-stu-id="57fe9-217">To increase `maxAllowedContentLength`, add an entry in the *web.config* to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="57fe9-218">Další podrobnosti najdete v tématu [Konfigurace](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="57fe9-218">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

<span data-ttu-id="57fe9-219">**Model hostování mimo proces**</span><span class="sxs-lookup"><span data-stu-id="57fe9-219">**Out-of-process hosting model**</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="57fe9-220">Možnost</span><span class="sxs-lookup"><span data-stu-id="57fe9-220">Option</span></span>                         | <span data-ttu-id="57fe9-221">Výchozí</span><span class="sxs-lookup"><span data-stu-id="57fe9-221">Default</span></span> | <span data-ttu-id="57fe9-222">Nastavení</span><span class="sxs-lookup"><span data-stu-id="57fe9-222">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="57fe9-223">Pokud `true`, server IIS nastaví `HttpContext.User` ověřený [ověřováním systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="57fe9-223">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="57fe9-224">Pokud `false`, Server poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, které jsou výslovně požadovány `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-224">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="57fe9-225">Aby mohla funkce `AutomaticAuthentication` fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="57fe9-225">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="57fe9-226">Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="57fe9-226">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="57fe9-227">Nastaví zobrazovaný název, který se uživatelům na přihlašovací stránky zobrazí.</span><span class="sxs-lookup"><span data-stu-id="57fe9-227">Sets the display name shown to users on login pages.</span></span> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="57fe9-228">**Model hostování mimo proces**</span><span class="sxs-lookup"><span data-stu-id="57fe9-228">**Out-of-process hosting model**</span></span>

::: moniker-end

<span data-ttu-id="57fe9-229">Pokud chcete nakonfigurovat možnosti služby IIS, zahrňte do <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>konfiguraci služby pro <xref:Microsoft.AspNetCore.Builder.IISOptions>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-229">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*>.</span></span> <span data-ttu-id="57fe9-230">Následující příklad zabrání aplikaci v naplnění `HttpContext.Connection.ClientCertificate`:</span><span class="sxs-lookup"><span data-stu-id="57fe9-230">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="57fe9-231">Možnost</span><span class="sxs-lookup"><span data-stu-id="57fe9-231">Option</span></span>                         | <span data-ttu-id="57fe9-232">Výchozí</span><span class="sxs-lookup"><span data-stu-id="57fe9-232">Default</span></span> | <span data-ttu-id="57fe9-233">Nastavení</span><span class="sxs-lookup"><span data-stu-id="57fe9-233">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="57fe9-234">Pokud `true`, [middleware integrace služby IIS](#enable-the-iisintegration-components) nastaví `HttpContext.User` ověřováno [ověřováním systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="57fe9-234">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="57fe9-235">Pokud `false`, middleware poskytuje pouze identitu pro `HttpContext.User` a reaguje na výzvy, které jsou explicitně požadovány `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-235">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="57fe9-236">Aby mohla funkce `AutomaticAuthentication` fungovat, musí být ve službě IIS povolená možnost ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="57fe9-236">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="57fe9-237">Další informace najdete v tématu [ověřování systému Windows](xref:security/authentication/windowsauth) .</span><span class="sxs-lookup"><span data-stu-id="57fe9-237">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="57fe9-238">Nastaví zobrazovaný název, který se uživatelům na přihlašovací stránky zobrazí.</span><span class="sxs-lookup"><span data-stu-id="57fe9-238">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="57fe9-239">Pokud se `true` a nachází se hlavička žádosti `MS-ASPNETCORE-CLIENTCERT`, `HttpContext.Connection.ClientCertificate` se naplní.</span><span class="sxs-lookup"><span data-stu-id="57fe9-239">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |

### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="57fe9-240">Proxy server a scénáře pro nástroj pro vyrovnávání zatížení</span><span class="sxs-lookup"><span data-stu-id="57fe9-240">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="57fe9-241">[Služba IIS Integration middleware](#enable-the-iisintegration-components), která konfiguruje middleware předávaných hlaviček, a modul ASP.NET Core je nakonfigurován tak, aby přenesl schéma (http/https) a vzdálenou IP adresu, kam pochází požadavek.</span><span class="sxs-lookup"><span data-stu-id="57fe9-241">The [IIS Integration Middleware](#enable-the-iisintegration-components), which configures Forwarded Headers Middleware, and the ASP.NET Core Module are configured to forward the scheme (HTTP/HTTPS) and the remote IP address where the request originated.</span></span> <span data-ttu-id="57fe9-242">Další konfigurace může být nezbytný pro aplikací hostovaných za službou další proxy servery a nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="57fe9-242">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="57fe9-243">Další informace najdete v tématu [konfigurace ASP.NET Core pro práci se servery proxy a nástroji pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="57fe9-243">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

### <a name="webconfig-file"></a><span data-ttu-id="57fe9-244">soubor Web.config</span><span class="sxs-lookup"><span data-stu-id="57fe9-244">web.config file</span></span>

<span data-ttu-id="57fe9-245">Soubor *Web. config* konfiguruje [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="57fe9-245">The *web.config* file configures the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="57fe9-246">Vytváření, transformace a publikování souboru *Web. config* je zpracováno cílem MSBuild (`_TransformWebConfig`) při publikování projektu.</span><span class="sxs-lookup"><span data-stu-id="57fe9-246">Creating, transforming, and publishing the *web.config* file is handled by an MSBuild target (`_TransformWebConfig`) when the project is published.</span></span> <span data-ttu-id="57fe9-247">Tento cíl je k dispozici v rámci cílů web SDK (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="57fe9-247">This target is present in the Web SDK targets (`Microsoft.NET.Sdk.Web`).</span></span> <span data-ttu-id="57fe9-248">V horní části souboru projektu je sada SDK:</span><span class="sxs-lookup"><span data-stu-id="57fe9-248">The SDK is set at the top of the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="57fe9-249">Pokud soubor *Web. config* není v projektu přítomen, je vytvořen soubor se správným *processPath* a *argumenty* pro konfiguraci modulu ASP.NET Core a přesunutí do [publikovaného výstupu](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="57fe9-249">If a *web.config* file isn't present in the project, the file is created with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to [published output](xref:host-and-deploy/directory-structure).</span></span>

<span data-ttu-id="57fe9-250">Pokud je soubor *Web. config* přítomen v projektu, soubor je transformován se správným *processPath* a *argumenty* pro konfiguraci modulu ASP.NET Core a přesunutí do publikovaného výstupu.</span><span class="sxs-lookup"><span data-stu-id="57fe9-250">If a *web.config* file is present in the project, the file is transformed with the correct *processPath* and *arguments* to configure the ASP.NET Core Module and moved to published output.</span></span> <span data-ttu-id="57fe9-251">Transformace nezmění nastavení konfigurace služby IIS v souboru.</span><span class="sxs-lookup"><span data-stu-id="57fe9-251">The transformation doesn't modify IIS configuration settings in the file.</span></span>

<span data-ttu-id="57fe9-252">Soubor *Web. config* může poskytovat další nastavení konfigurace služby IIS, která ovládají aktivní moduly IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-252">The *web.config* file may provide additional IIS configuration settings that control active IIS modules.</span></span> <span data-ttu-id="57fe9-253">Informace o modulech služby IIS, které jsou schopné zpracovávat požadavky s ASP.NET Core aplikacemi, najdete v tématu [IIS modules](xref:host-and-deploy/iis/modules) .</span><span class="sxs-lookup"><span data-stu-id="57fe9-253">For information on IIS modules that are capable of processing requests with ASP.NET Core apps, see the [IIS modules](xref:host-and-deploy/iis/modules) topic.</span></span>

<span data-ttu-id="57fe9-254">Chcete-li webové sadě SDK zabránit ve transformaci souboru *Web. config* , použijte vlastnost **\<IsTransformWebConfigDisabled >** v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="57fe9-254">To prevent the Web SDK from transforming the *web.config* file, use the **\<IsTransformWebConfigDisabled>** property in the project file:</span></span>

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

<span data-ttu-id="57fe9-255">Při zakazování webové sady SDK z transformace souboru by měl vývojář *processPath* a *argumenty* ručně nastavit.</span><span class="sxs-lookup"><span data-stu-id="57fe9-255">When disabling the Web SDK from transforming the file, the *processPath* and *arguments* should be manually set by the developer.</span></span> <span data-ttu-id="57fe9-256">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-256">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

### <a name="webconfig-file-location"></a><span data-ttu-id="57fe9-257">Umístění souboru Web.config</span><span class="sxs-lookup"><span data-stu-id="57fe9-257">web.config file location</span></span>

<span data-ttu-id="57fe9-258">Aby bylo možné správně nastavit [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) , musí být soubor *Web. config* přítomen v [kořenové cestě obsahu](xref:fundamentals/index#content-root) (obvykle v základní cestě aplikace) nasazené aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-258">In order to set up the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) correctly, the *web.config* file must be present at the [content root](xref:fundamentals/index#content-root) path (typically the app base path) of the deployed app.</span></span> <span data-ttu-id="57fe9-259">Jedná se o stejné umístění jako fyzická cesta webu služby IIS k dispozici.</span><span class="sxs-lookup"><span data-stu-id="57fe9-259">This is the same location as the website physical path provided to IIS.</span></span> <span data-ttu-id="57fe9-260">Soubor *Web. config* je vyžadován v kořenu aplikace, aby bylo možné publikovat více aplikací pomocí nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="57fe9-260">The *web.config* file is required at the root of the app to enable the publishing of multiple apps using Web Deploy.</span></span>

<span data-ttu-id="57fe9-261">Citlivé soubory existují na fyzické cestě aplikace, jako je například *\<sestavení >. runtimeconfig. JSON*, *\<Assembly >. XML* (dokumentační dokumentace xml), a *\<sestavení >. DEPS. JSON*.</span><span class="sxs-lookup"><span data-stu-id="57fe9-261">Sensitive files exist on the app's physical path, such as *\<assembly>.runtimeconfig.json*, *\<assembly>.xml* (XML Documentation comments), and *\<assembly>.deps.json*.</span></span> <span data-ttu-id="57fe9-262">Když je přítomen soubor *Web. config* a lokalita se spouští normálně, služba IIS tyto citlivé soubory po vyžádání neobsluhuje.</span><span class="sxs-lookup"><span data-stu-id="57fe9-262">When the *web.config* file is present and the site starts normally, IIS doesn't serve these sensitive files if they're requested.</span></span> <span data-ttu-id="57fe9-263">Pokud soubor *Web. config* chybí, je nesprávně pojmenovaný nebo nemůže nakonfigurovat lokalitu pro normální spuštění, služba IIS může obsluhovat citlivé soubory veřejně.</span><span class="sxs-lookup"><span data-stu-id="57fe9-263">If the *web.config* file is missing, incorrectly named, or unable to configure the site for normal startup, IIS may serve sensitive files publicly.</span></span>

<span data-ttu-id="57fe9-264">**Soubor *Web. config* musí být současně přítomen v nasazení, správně pojmenován a může nakonfigurovat lokalitu pro normální spuštění. Nikdy neodstraňujte soubor *Web. config* z produkčního nasazení.**</span><span class="sxs-lookup"><span data-stu-id="57fe9-264">**The *web.config* file must be present in the deployment at all times, correctly named, and able to configure the site for normal start up. Never remove the *web.config* file from a production deployment.**</span></span>

### <a name="transform-webconfig"></a><span data-ttu-id="57fe9-265">Transformace souboru web.config</span><span class="sxs-lookup"><span data-stu-id="57fe9-265">Transform web.config</span></span>

<span data-ttu-id="57fe9-266">Pokud potřebujete transformovat *Web. config* při publikování (například nastavit proměnné prostředí na základě konfigurace, profilu nebo prostředí), přečtěte si téma <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-266">If you need to transform *web.config* on publish (for example, set environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="57fe9-267">Konfigurace služby IIS</span><span class="sxs-lookup"><span data-stu-id="57fe9-267">IIS configuration</span></span>

<span data-ttu-id="57fe9-268">**Operační systémy Windows Server**</span><span class="sxs-lookup"><span data-stu-id="57fe9-268">**Windows Server operating systems**</span></span>

<span data-ttu-id="57fe9-269">Povolte roli serveru **webový server (IIS)** a vytvořte služby rolí.</span><span class="sxs-lookup"><span data-stu-id="57fe9-269">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="57fe9-270">Použijte průvodce **přidáním rolí a funkcí** z nabídky **Správa** nebo odkazu v **Správce serveru**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-270">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="57fe9-271">V kroku **role serveru** zaškrtněte políčko **webový server (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-271">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![V kroku výběr serveru role je vybrána role webového serveru IIS.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="57fe9-273">Po kroku **funkce** se krok **služby rolí** načte pro webový server (IIS).</span><span class="sxs-lookup"><span data-stu-id="57fe9-273">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="57fe9-274">Vyberte požadovaných služeb role služby IIS nebo přijměte výchozí nastavení role služby za předpokladu.</span><span class="sxs-lookup"><span data-stu-id="57fe9-274">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Výchozí služby rolí jsou vybrané v kroku vybrat roli služby.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="57fe9-276">**Ověřování systému Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="57fe9-276">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="57fe9-277">Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: **webový Server** > **zabezpečení**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-277">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="57fe9-278">Vyberte funkci **ověřování systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-278">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="57fe9-279">Další informace najdete v tématu [ověřování systému windows \<windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="57fe9-279">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="57fe9-280">**WebSockets (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="57fe9-280">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="57fe9-281">Protokoly Websocket je podporována s ASP.NET Core 1.1 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="57fe9-281">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="57fe9-282">Pokud chcete povolit objekty WebSockets, rozbalte následující uzly: **webový Server** > **vývoj aplikací**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-282">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="57fe9-283">Vyberte funkci **protokolu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-283">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="57fe9-284">Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="57fe9-284">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="57fe9-285">Chcete-li nainstalovat roli a služby webového serveru, postupujte podle kroků pro **potvrzení** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-285">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="57fe9-286">Po instalaci role **webový server (IIS)** není nutné restartovat server nebo službu IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-286">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="57fe9-287">**Desktopové operační systémy Windows**</span><span class="sxs-lookup"><span data-stu-id="57fe9-287">**Windows desktop operating systems**</span></span>

<span data-ttu-id="57fe9-288">Povolte **konzolu pro správu služby IIS** a **webové služby**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-288">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="57fe9-289">Přejděte na **Ovládací panely** > **programy** > **programy a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="57fe9-289">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="57fe9-290">Otevřete uzel **Internetová informační služba** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-290">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="57fe9-291">Otevřete uzel **Nástroje webové správy** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-291">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="57fe9-292">Zaškrtněte políčko pro **konzolu pro správu služby IIS**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-292">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="57fe9-293">Zaškrtněte políčko u **webových služeb**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-293">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="57fe9-294">Přijměte výchozí funkce pro **webové služby** nebo upravte funkce služby IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-294">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="57fe9-295">**Ověřování systému Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="57fe9-295">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="57fe9-296">Chcete-li povolit ověřování systému Windows, rozbalte následující uzly: **webové služby** > **zabezpečení**webu.</span><span class="sxs-lookup"><span data-stu-id="57fe9-296">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="57fe9-297">Vyberte funkci **ověřování systému Windows** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-297">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="57fe9-298">Další informace najdete v tématu [ověřování systému windows \<windowsAuthentication >](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) a [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="57fe9-298">For more information, see [Windows Authentication \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="57fe9-299">**WebSockets (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="57fe9-299">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="57fe9-300">Protokoly Websocket je podporována s ASP.NET Core 1.1 nebo vyšší.</span><span class="sxs-lookup"><span data-stu-id="57fe9-300">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="57fe9-301">Chcete-li povolit objekty WebSockets, rozbalte následující uzly: **webové služby** > **funkce pro vývoj aplikací**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-301">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="57fe9-302">Vyberte funkci **protokolu WebSocket** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-302">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="57fe9-303">Další informace najdete v tématu [objekty WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="57fe9-303">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="57fe9-304">Pokud instalace služby IIS vyžaduje restartování, restartujte systém.</span><span class="sxs-lookup"><span data-stu-id="57fe9-304">If the IIS installation requires a restart, restart the system.</span></span>

![Konzola pro správu služby IIS a webové služby jsou vybrány v funkce Windows.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="57fe9-306">Instalace .NET Core, který je hostitelem svazku</span><span class="sxs-lookup"><span data-stu-id="57fe9-306">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="57fe9-307">Nainstalujte *hostující sadu .NET Core* do hostitelského systému.</span><span class="sxs-lookup"><span data-stu-id="57fe9-307">Install the *.NET Core Hosting Bundle* on the hosting system.</span></span> <span data-ttu-id="57fe9-308">Svazek nainstaluje modul runtime .NET Core, knihovnu .NET Core a [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="57fe9-308">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="57fe9-309">Povolí modul ASP.NET Core aplikací ke spuštění za služby IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-309">The module allows ASP.NET Core apps to run behind IIS.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="57fe9-310">Pokud před službou IIS instalovanou sadou hostování, je nutné opravit instalaci sady.</span><span class="sxs-lookup"><span data-stu-id="57fe9-310">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="57fe9-311">Spusťte instalační program sady hostování znovu po instalaci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-311">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="57fe9-312">Pokud se hostující sada nainstaluje po instalaci 64 (x64) verze .NET Core, můžou se zdát, že sady SDK chybí ([nezjistily se žádné sady .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="57fe9-312">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="57fe9-313">Chcete-li tento problém vyřešit, přečtěte si téma <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-313">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="57fe9-314">Přímé stažení (aktuální verze)</span><span class="sxs-lookup"><span data-stu-id="57fe9-314">Direct download (current version)</span></span>

<span data-ttu-id="57fe9-315">Stažení instalačního programu pomocí následujícího odkazu:</span><span class="sxs-lookup"><span data-stu-id="57fe9-315">Download the installer using the following link:</span></span>

[<span data-ttu-id="57fe9-316">Aktuální instalační program sady hostujících sad .NET Core (přímé stahování)</span><span class="sxs-lookup"><span data-stu-id="57fe9-316">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="57fe9-317">Starší verze instalačního programu</span><span class="sxs-lookup"><span data-stu-id="57fe9-317">Earlier versions of the installer</span></span>

<span data-ttu-id="57fe9-318">Chcete-li získat starší verzi instalačního programu:</span><span class="sxs-lookup"><span data-stu-id="57fe9-318">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="57fe9-319">Přejděte do [archivu stahování v rozhraní .NET](https://www.microsoft.com/net/download/archives).</span><span class="sxs-lookup"><span data-stu-id="57fe9-319">Navigate to the [.NET download archives](https://www.microsoft.com/net/download/archives).</span></span>
1. <span data-ttu-id="57fe9-320">V části **.NET Core**vyberte verzi .NET Core.</span><span class="sxs-lookup"><span data-stu-id="57fe9-320">Under **.NET Core**, select the .NET Core version.</span></span>
1. <span data-ttu-id="57fe9-321">Ve sloupci **Spustit aplikace – modul runtime** vyhledejte řádek požadované verze modulu runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="57fe9-321">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="57fe9-322">Stáhněte instalační program pomocí odkazu **& hostování sady prostředků modulu runtime** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-322">Download the installer using the **Runtime & Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="57fe9-323">Některé instalační programy obsahují verze, které bylo dosaženo jejich konci životnosti (konce řádku) a již nejsou podporovány společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="57fe9-323">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="57fe9-324">Další informace najdete v tématu [zásady podpory](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="57fe9-324">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="57fe9-325">Instalaci sady hostování</span><span class="sxs-lookup"><span data-stu-id="57fe9-325">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="57fe9-326">Spusťte instalační program na serveru.</span><span class="sxs-lookup"><span data-stu-id="57fe9-326">Run the installer on the server.</span></span> <span data-ttu-id="57fe9-327">Při spuštění instalačního programu z příkazového prostředí správce jsou k dispozici následující parametry:</span><span class="sxs-lookup"><span data-stu-id="57fe9-327">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="57fe9-328">`OPT_NO_ANCM=1` &ndash; Přeskočit instalaci modulu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57fe9-328">`OPT_NO_ANCM=1` &ndash; Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="57fe9-329">`OPT_NO_RUNTIME=1` &ndash; přeskočení instalace modulu runtime .NET Core.</span><span class="sxs-lookup"><span data-stu-id="57fe9-329">`OPT_NO_RUNTIME=1` &ndash; Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="57fe9-330">Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="57fe9-330">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="57fe9-331">`OPT_NO_SHAREDFX=1` &ndash; Přeskočit instalaci sdílené architektury ASP.NET (modul runtime ASP.NET).</span><span class="sxs-lookup"><span data-stu-id="57fe9-331">`OPT_NO_SHAREDFX=1` &ndash; Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="57fe9-332">Používá se, když Server hostuje jenom [samostatná nasazení (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="57fe9-332">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="57fe9-333">`OPT_NO_X86=1` &ndash; Přeskočit instalaci běhových prostředí x86.</span><span class="sxs-lookup"><span data-stu-id="57fe9-333">`OPT_NO_X86=1` &ndash; Skip installing x86 runtimes.</span></span> <span data-ttu-id="57fe9-334">Tento parametr použijte, pokud víte, že nebudete hostovat 32 aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-334">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="57fe9-335">Pokud existuje možnost, že v budoucnosti budete hostovat jak 32, tak i 64 aplikace, tento parametr nepoužívejte a nainstalujete oba moduly runtime.</span><span class="sxs-lookup"><span data-stu-id="57fe9-335">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="57fe9-336">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; zakázat kontrolu použití sdílené konfigurace služby IIS, pokud je sdílená konfigurace (*ApplicationHost. config*) ve stejném počítači jako instalace služby IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-336">`OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Disable the check for using an IIS Shared Configuration when the shared configuration (*applicationHost.config*) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="57fe9-337">*K dispozici jenom pro ASP.NET Core 2,2 nebo novější instalační programy hostujících prostředků.*</span><span class="sxs-lookup"><span data-stu-id="57fe9-337">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="57fe9-338">Další informace naleznete v tématu <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-338">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="57fe9-339">Restartujte systém nebo v příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="57fe9-339">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="57fe9-340">Restartování služby IIS příjmem změnu systému provedené CESTU, která je proměnná prostředí, instalační služby.</span><span class="sxs-lookup"><span data-stu-id="57fe9-340">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="57fe9-341">ASP.NET Core nesplňuje chování při přeposílání pro vydání oprav pro balíčky sdílených rozhraní.</span><span class="sxs-lookup"><span data-stu-id="57fe9-341">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="57fe9-342">Po upgradu sdíleného rozhraní instalací nové hostitelské sady restartujte systém nebo v příkazovém prostředí spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="57fe9-342">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="57fe9-343">Při instalaci hostujícího balíčku není nutné ručně zastavit jednotlivé weby ve službě IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-343">It isn't necessary to manually stop individual sites in IIS when installing the Hosting Bundle.</span></span> <span data-ttu-id="57fe9-344">Hostované aplikace (weby IIS) se restartují po restartování služby IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-344">Hosted apps (IIS sites) restart when IIS restarts.</span></span> <span data-ttu-id="57fe9-345">Aplikace se po přijetí první žádosti spustí znovu, včetně [modulu inicializace aplikace](#application-initialization-module-and-idle-timeout).</span><span class="sxs-lookup"><span data-stu-id="57fe9-345">Apps start up again when they receive their first request, including from the [Application Initialization Module](#application-initialization-module-and-idle-timeout).</span></span>

<span data-ttu-id="57fe9-346">ASP.NET Core přijímá postup při přeposílání pro vydání oprav pro balíčky sdílených balíčků rozhraní.</span><span class="sxs-lookup"><span data-stu-id="57fe9-346">ASP.NET Core adopts roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="57fe9-347">Když se aplikace hostované službou IIS restartují se službou IIS, aplikace při přijetí jejich prvního požadavku načtou nejnovější verze oprav jejich odkazovaných balíčků.</span><span class="sxs-lookup"><span data-stu-id="57fe9-347">When apps hosted by IIS restart with IIS, the apps load with the latest patch releases of their referenced packages when they receive their first request.</span></span> <span data-ttu-id="57fe9-348">Pokud se služba IIS nerestartuje, aplikace se restartují a projeví se chování při přeposílání, když se jejich pracovní procesy recyklují a obdrží první požadavek.</span><span class="sxs-lookup"><span data-stu-id="57fe9-348">If IIS isn't restarted, apps restart and exhibit roll-forward behavior when their worker processes are recycled and they receive their first request.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="57fe9-349">Informace o sdílené konfiguraci služby IIS najdete v tématu [modul ASP.NET Core se sdílenou konfigurací služby IIS](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="57fe9-349">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a><span data-ttu-id="57fe9-350">Nainstalujte nástroj nasazení webu při publikování pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57fe9-350">Install Web Deploy when publishing with Visual Studio</span></span>

<span data-ttu-id="57fe9-351">Při nasazování aplikací na servery s [nasazení webu](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)nainstalujte na server nejnovější verzi nasazení webu.</span><span class="sxs-lookup"><span data-stu-id="57fe9-351">When deploying apps to servers with [Web Deploy](/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later), install the latest version of Web Deploy on the server.</span></span> <span data-ttu-id="57fe9-352">Chcete-li nainstalovat Nasazení webu, použijte [instalační program webové platformy (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) nebo si instalační program Získejte přímo z webu [služby Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=43717).</span><span class="sxs-lookup"><span data-stu-id="57fe9-352">To install Web Deploy, use the [Web Platform Installer (WebPI)](https://www.microsoft.com/web/downloads/platform.aspx) or obtain an installer directly from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717).</span></span> <span data-ttu-id="57fe9-353">Upřednostňovanou metodou je použití instalace webové platformy.</span><span class="sxs-lookup"><span data-stu-id="57fe9-353">The preferred method is to use WebPI.</span></span> <span data-ttu-id="57fe9-354">Instalace webové platformy nabízí samostatné instalace a konfigurace pro poskytovatele hostingu.</span><span class="sxs-lookup"><span data-stu-id="57fe9-354">WebPI offers a standalone setup and a configuration for hosting providers.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="57fe9-355">Vytvořte web služby IIS</span><span class="sxs-lookup"><span data-stu-id="57fe9-355">Create the IIS site</span></span>

1. <span data-ttu-id="57fe9-356">V hostitelském systému vytvořte složku obsahující soubory a složky publikované aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-356">On the hosting system, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="57fe9-357">V následujícím kroku je jako fyzická cesta k aplikaci služba IIS poskytována jako cesta k této složce.</span><span class="sxs-lookup"><span data-stu-id="57fe9-357">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="57fe9-358">Další informace o složce nasazení aplikace a rozložení souborů naleznete v tématu <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-358">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="57fe9-359">Ve Správci služby IIS otevřete uzel serveru na panelu **připojení** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-359">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="57fe9-360">Klikněte pravým tlačítkem na složku **weby** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-360">Right-click the **Sites** folder.</span></span> <span data-ttu-id="57fe9-361">V místní nabídce vyberte **Přidat web** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-361">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="57fe9-362">Zadejte **název lokality** a nastavte **fyzickou cestu** ke složce pro nasazení aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-362">Provide a **Site name** and set the **Physical path** to the app's deployment folder.</span></span> <span data-ttu-id="57fe9-363">Zadejte konfiguraci **vazby** a vytvořte web výběrem **OK**:</span><span class="sxs-lookup"><span data-stu-id="57fe9-363">Provide the **Binding** configuration and create the website by selecting **OK**:</span></span>

   ![Zadejte název lokality, fyzickou cestu a název hostitele v kroku přidat web.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > <span data-ttu-id="57fe9-365">**Nesmí** být použita vazba zástupných znaků nejvyšší úrovně (`http://*:80/` a `http://+:80`).</span><span class="sxs-lookup"><span data-stu-id="57fe9-365">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="57fe9-366">Vazby nejvyšší úrovně zástupný znak můžete otevřít aplikaci tak, aby slabá místa zabezpečení.</span><span class="sxs-lookup"><span data-stu-id="57fe9-366">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="57fe9-367">To platí pro silné a slabé zástupné znaky.</span><span class="sxs-lookup"><span data-stu-id="57fe9-367">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="57fe9-368">Používejte explicitní hostitele názvy místo zástupných znaků.</span><span class="sxs-lookup"><span data-stu-id="57fe9-368">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="57fe9-369">Vazba zástupných znaků subdomény (například `*.mysub.com`) nemá toto bezpečnostní riziko, pokud ovládáte celou nadřazenou doménu (na rozdíl od `*.com`, která je zranitelná).</span><span class="sxs-lookup"><span data-stu-id="57fe9-369">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="57fe9-370">Další informace najdete v [části rfc7230 část-5,4](https://tools.ietf.org/html/rfc7230#section-5.4) .</span><span class="sxs-lookup"><span data-stu-id="57fe9-370">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="57fe9-371">V uzlu serveru vyberte **fondy aplikací**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-371">Under the server's node, select **Application Pools**.</span></span>

1. <span data-ttu-id="57fe9-372">Klikněte pravým tlačítkem myši na fond aplikací webu a v místní nabídce vyberte **základní nastavení** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-372">Right-click the site's app pool and select **Basic Settings** from the contextual menu.</span></span>

1. <span data-ttu-id="57fe9-373">V okně **Upravit fond aplikací** nastavte **verzi .NET CLR** na **bez spravovaného kódu**:</span><span class="sxs-lookup"><span data-stu-id="57fe9-373">In the **Edit Application Pool** window, set the **.NET CLR version** to **No Managed Code**:</span></span>

   ![Nastavit bez spravovaného kódu pro verze .NET CLR.](index/_static/edit-apppool-ws2016.png)

    <span data-ttu-id="57fe9-375">ASP.NET Core běží v samostatném procesu a spravuje modulu runtime.</span><span class="sxs-lookup"><span data-stu-id="57fe9-375">ASP.NET Core runs in a separate process and manages the runtime.</span></span> <span data-ttu-id="57fe9-376">ASP.NET Core nespoléhá na načítání CLR desktopu (.NET CLR)&mdash;se modul CLR (Common Language Runtime) pro .NET Core spouští k hostování aplikace v pracovním procesu.</span><span class="sxs-lookup"><span data-stu-id="57fe9-376">ASP.NET Core doesn't rely on loading the desktop CLR (.NET CLR)&mdash;the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process.</span></span> <span data-ttu-id="57fe9-377">Nastavení **verze .NET CLR** na **žádný spravovaný kód** není volitelné, ale doporučuje se.</span><span class="sxs-lookup"><span data-stu-id="57fe9-377">Setting the **.NET CLR version** to **No Managed Code** is optional but recommended.</span></span>

1. <span data-ttu-id="57fe9-378">*ASP.NET Core 2,2 nebo novější*: 64 u samostatného [nasazeného nasazení](/dotnet/core/deploying/#self-contained-deployments-scd) (x64), které používá [model hostování v rámci procesu](#in-process-hosting-model), zakažte fond aplikací pro procesy 32 (x86).</span><span class="sxs-lookup"><span data-stu-id="57fe9-378">*ASP.NET Core 2.2 or later*: For a 64-bit (x64) [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) that uses the [in-process hosting model](#in-process-hosting-model), disable the app pool for 32-bit (x86) processes.</span></span>

   <span data-ttu-id="57fe9-379">V bočním panelu **Akce** Správce služby IIS > **fondy aplikací**vyberte možnost **nastavit výchozí hodnoty fondu aplikací** nebo **Upřesnit nastavení**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-379">In the **Actions** sidebar of IIS Manager > **Application Pools**, select **Set Application Pool Defaults** or **Advanced Settings**.</span></span> <span data-ttu-id="57fe9-380">Vyhledejte **možnost povolit 32-bitové aplikace** a nastavte hodnotu na `False`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-380">Locate **Enable 32-Bit Applications** and set the value to `False`.</span></span> <span data-ttu-id="57fe9-381">Toto nastavení nemá vliv na aplikace nasazené pro [hostování mimo proces](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span><span class="sxs-lookup"><span data-stu-id="57fe9-381">This setting doesn't affect apps deployed for [out-of-process hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model).</span></span>

1. <span data-ttu-id="57fe9-382">Potvrďte, že identita model procesu má příslušná oprávnění.</span><span class="sxs-lookup"><span data-stu-id="57fe9-382">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="57fe9-383">Pokud se výchozí identita fondu aplikací (**model procesu** > **identity**) změní z **ApplicationPoolIdentity** na jinou identitu, ověřte, že Nová identita má požadovaná oprávnění pro přístup ke složce, databázi a dalším požadovaným prostředkům aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-383">If the default identity of the app pool (**Process Model** > **Identity**) is changed from **ApplicationPoolIdentity** to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="57fe9-384">Například fond aplikací vyžaduje čtení a zápisu do složky, kde aplikace čte a zapisuje soubory.</span><span class="sxs-lookup"><span data-stu-id="57fe9-384">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

<span data-ttu-id="57fe9-385">**Konfigurace ověřování systému Windows (volitelné)**</span><span class="sxs-lookup"><span data-stu-id="57fe9-385">**Windows Authentication configuration (Optional)**</span></span>  
<span data-ttu-id="57fe9-386">Další informace najdete v tématu [Konfigurace ověřování systému Windows](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="57fe9-386">For more information, see [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

## <a name="deploy-the-app"></a><span data-ttu-id="57fe9-387">Nasazení aplikace</span><span class="sxs-lookup"><span data-stu-id="57fe9-387">Deploy the app</span></span>

<span data-ttu-id="57fe9-388">Nasaďte aplikaci do složky **fyzické cesty** služby IIS, která byla navázána v části [Vytvoření webu služby IIS](#create-the-iis-site) .</span><span class="sxs-lookup"><span data-stu-id="57fe9-388">Deploy the app to the IIS **Physical path** folder that was established in the [Create the IIS site](#create-the-iis-site) section.</span></span> <span data-ttu-id="57fe9-389">[Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) je doporučeným mechanismem pro nasazení, ale existuje několik možností pro přesun aplikace ze složky *publikování* projektu do složky pro nasazení hostitelského systému.</span><span class="sxs-lookup"><span data-stu-id="57fe9-389">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) is the recommended mechanism for deployment, but several options exist for moving the app from the project's *publish* folder to the hosting system's deployment folder.</span></span>

### <a name="web-deploy-with-visual-studio"></a><span data-ttu-id="57fe9-390">Nasazení webu pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57fe9-390">Web Deploy with Visual Studio</span></span>

<span data-ttu-id="57fe9-391">Informace o tom, jak vytvořit profil publikování pro použití s Nasazení webu, najdete v tématu [publikační profily sady Visual Studio pro ASP.NET Core nasazení aplikací](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) .</span><span class="sxs-lookup"><span data-stu-id="57fe9-391">See the [Visual Studio publish profiles for ASP.NET Core app deployment](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) topic to learn how to create a publish profile for use with Web Deploy.</span></span> <span data-ttu-id="57fe9-392">Pokud poskytovatel hostingu poskytuje profil publikování nebo podporu pro jeho vytvoření, Stáhněte si jeho profil a importujte ho pomocí dialogového okna pro **publikování** sady Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="57fe9-392">If the hosting provider provides a Publish Profile or support for creating one, download their profile and import it using the Visual Studio **Publish** dialog:</span></span>

![Publikovat stránku dialogového okna](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a><span data-ttu-id="57fe9-394">Nasazení webu mimo sadu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57fe9-394">Web Deploy outside of Visual Studio</span></span>

<span data-ttu-id="57fe9-395">[Nasazení webu](/iis/publish/using-web-deploy/introduction-to-web-deploy) lze použít také mimo sadu Visual Studio z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="57fe9-395">[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) can also be used outside of Visual Studio from the command line.</span></span> <span data-ttu-id="57fe9-396">Další informace najdete v tématu [Nástroj pro nasazení webu](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span><span class="sxs-lookup"><span data-stu-id="57fe9-396">For more information, see [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool).</span></span>

### <a name="alternatives-to-web-deploy"></a><span data-ttu-id="57fe9-397">Alternativy k webové nasazení</span><span class="sxs-lookup"><span data-stu-id="57fe9-397">Alternatives to Web Deploy</span></span>

<span data-ttu-id="57fe9-398">K přesunu aplikace do hostitelského systému, jako je ruční kopírování, [xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy)nebo [PowerShell](/powershell/), použijte libovolný z několika způsobů.</span><span class="sxs-lookup"><span data-stu-id="57fe9-398">Use any of several methods to move the app to the hosting system, such as manual copy, [Xcopy](/windows-server/administration/windows-commands/xcopy), [Robocopy](/windows-server/administration/windows-commands/robocopy), or [PowerShell](/powershell/).</span></span>

<span data-ttu-id="57fe9-399">Další informace o nasazení ASP.NET Core do služby IIS najdete v části [prostředky nasazení pro správce služby IIS](#deployment-resources-for-iis-administrators) .</span><span class="sxs-lookup"><span data-stu-id="57fe9-399">For more information on ASP.NET Core deployment to IIS, see the [Deployment resources for IIS administrators](#deployment-resources-for-iis-administrators) section.</span></span>

## <a name="browse-the-website"></a><span data-ttu-id="57fe9-400">Přejděte na web</span><span class="sxs-lookup"><span data-stu-id="57fe9-400">Browse the website</span></span>

<span data-ttu-id="57fe9-401">Po nasazení aplikace do hostitelského systému vytvořte žádost jednomu z veřejných koncových bodů aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-401">After the app is deployed to the hosting system, make a request to one of the app's public endpoints.</span></span>

<span data-ttu-id="57fe9-402">V následujícím příkladu je lokalita svázána s **názvem hostitele** služby IIS `www.mysite.com` na **portu** `80`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-402">In the following example, the site is bound to an IIS **Host name** of `www.mysite.com` on **Port** `80`.</span></span> <span data-ttu-id="57fe9-403">Je učiněna žádost o `http://www.mysite.com`:</span><span class="sxs-lookup"><span data-stu-id="57fe9-403">A request is made to `http://www.mysite.com`:</span></span>

![Prohlížeč Microsoft Edge načetl úvodní stránka služby IIS.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a><span data-ttu-id="57fe9-405">Soubory uzamčené nasazení</span><span class="sxs-lookup"><span data-stu-id="57fe9-405">Locked deployment files</span></span>

<span data-ttu-id="57fe9-406">Při spuštění aplikace jsou zamknuté soubory ve složce pro nasazení.</span><span class="sxs-lookup"><span data-stu-id="57fe9-406">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="57fe9-407">Uzamčené soubory nemohou být přepsána během nasazení.</span><span class="sxs-lookup"><span data-stu-id="57fe9-407">Locked files can't be overwritten during deployment.</span></span> <span data-ttu-id="57fe9-408">Chcete-li uvolnit uzamčené soubory v nasazení, zastavte fond aplikací pomocí **jednoho** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="57fe9-408">To release locked files in a deployment, stop the app pool using **one** of the following approaches:</span></span>

* <span data-ttu-id="57fe9-409">Použijte Nasazení webu a referenční `Microsoft.NET.Sdk.Web` v souboru projektu.</span><span class="sxs-lookup"><span data-stu-id="57fe9-409">Use Web Deploy and reference `Microsoft.NET.Sdk.Web` in the project file.</span></span> <span data-ttu-id="57fe9-410">Soubor *App_offline. htm* se umístí do kořenové složky adresáře webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-410">An *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="57fe9-411">Když je soubor přítomen, modul ASP.NET Core aplikaci korektně ukončí a během nasazování zachová soubor *App_offline. htm* .</span><span class="sxs-lookup"><span data-stu-id="57fe9-411">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="57fe9-412">Další informace najdete v referenčních informacích k [konfiguraci modulu ASP.NET Core](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="57fe9-412">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>
* <span data-ttu-id="57fe9-413">Ručně zastavte fond aplikací ve Správci služby IIS na serveru.</span><span class="sxs-lookup"><span data-stu-id="57fe9-413">Manually stop the app pool in the IIS Manager on the server.</span></span>
* <span data-ttu-id="57fe9-414">Použití PowerShellu k vyřazení *App_offline. htm* (vyžaduje PowerShell 5 nebo novější):</span><span class="sxs-lookup"><span data-stu-id="57fe9-414">Use PowerShell to drop *app_offline.htm* (requires PowerShell 5 or later):</span></span>

  ```PowerShell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a><span data-ttu-id="57fe9-415">Ochrana dat</span><span class="sxs-lookup"><span data-stu-id="57fe9-415">Data protection</span></span>

<span data-ttu-id="57fe9-416">[Sada ASP.NET Core Data Protection Stack](xref:security/data-protection/introduction) je používána několika ASP.NET Core [middlewary](xref:fundamentals/middleware/index), včetně middlewaru používaného při ověřování.</span><span class="sxs-lookup"><span data-stu-id="57fe9-416">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="57fe9-417">I v případě, že rozhraní API ochrany dat není voláno uživatelským kódem, je třeba nakonfigurovat ochranu dat pomocí skriptu nasazení nebo v uživatelském kódu, aby bylo možné vytvořit trvalé úložiště kryptografických [klíčů](xref:security/data-protection/implementation/key-management).</span><span class="sxs-lookup"><span data-stu-id="57fe9-417">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="57fe9-418">Pokud není nakonfigurovaná ochrana dat, jsou klíče uložené v paměti a při restartování aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-418">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="57fe9-419">Pokud kanál klíče jsou uloženy v paměti, při restartování aplikace:</span><span class="sxs-lookup"><span data-stu-id="57fe9-419">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="57fe9-420">Všechny tokeny ověřování na základě souborů cookie nejsou zneplatněny.</span><span class="sxs-lookup"><span data-stu-id="57fe9-420">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="57fe9-421">Uživatelé se musí znovu přihlásit v jejich další požadavek.</span><span class="sxs-lookup"><span data-stu-id="57fe9-421">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="57fe9-422">Všechna data chráněná pomocí aktualizační kanál, který klíč můžete už nebude možné dešifrovat.</span><span class="sxs-lookup"><span data-stu-id="57fe9-422">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="57fe9-423">To může zahrnovat [CSRF tokeny](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) a [ASP.NET Core soubory cookie TempData MVC](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="57fe9-423">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="57fe9-424">Pokud chcete v rámci služby IIS nakonfigurovat ochranu dat a zachovat přitom klíčového prstence, použijte **některý** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="57fe9-424">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="57fe9-425">**Vytvoření klíčů registru ochrany dat**</span><span class="sxs-lookup"><span data-stu-id="57fe9-425">**Create Data Protection Registry Keys**</span></span>

  <span data-ttu-id="57fe9-426">Používá aplikace ASP.NET Core klíče ochrany dat jsou uložené v registru, které jsou externí vzhledem k aplikacím.</span><span class="sxs-lookup"><span data-stu-id="57fe9-426">Data protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="57fe9-427">Pokud chcete zachovat klíče pro danou aplikaci, vytvořte klíče registru pro fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="57fe9-427">To persist the keys for a given app, create registry keys for the app pool.</span></span>

  <span data-ttu-id="57fe9-428">Pro samostatnou instalaci služby IIS, která není součástí webfarmu, se dá [skript PowerShellu pro ochranu dat provision-AutoGenKeys. ps1](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) použít pro každý fond aplikací, který se používá v aplikaci ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57fe9-428">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="57fe9-429">Tento skript vytvoří klíč registru HKLM registru, který je přístupný pouze pro účet pracovního procesu fondu aplikací aplikaci.</span><span class="sxs-lookup"><span data-stu-id="57fe9-429">This script creates a registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="57fe9-430">Klíče se zašifrují neaktivní uložená data pomocí rozhraní DPAPI klíčem celý počítač.</span><span class="sxs-lookup"><span data-stu-id="57fe9-430">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="57fe9-431">Ve webových farem lze nastavit aplikaci pro použití cesty UNC pro ukládání jeho data protection klíč kanál.</span><span class="sxs-lookup"><span data-stu-id="57fe9-431">In web farm scenarios, an app can be configured to use a UNC path to store its data protection key ring.</span></span> <span data-ttu-id="57fe9-432">Ve výchozím nastavení klíče ochrany dat nejsou šifrovány.</span><span class="sxs-lookup"><span data-stu-id="57fe9-432">By default, the data protection keys aren't encrypted.</span></span> <span data-ttu-id="57fe9-433">Zajistěte, aby byly omezené na účet Windows, které aplikace běží pod oprávnění pro sdílené síťové složce.</span><span class="sxs-lookup"><span data-stu-id="57fe9-433">Ensure that the file permissions for the network share are limited to the Windows account the app runs under.</span></span> <span data-ttu-id="57fe9-434">X X509 certifikát můžete použít k ochraně klíčů v klidovém stavu.</span><span class="sxs-lookup"><span data-stu-id="57fe9-434">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="57fe9-435">Vezměte v úvahu mechanismus pro uživatelům umožní nahrát certifikáty: místo certifikátů do důvěryhodného certifikátu uživatele ukládat a ujistěte se, jsou k dispozici na všech počítačích, ve kterém běží aplikace uživatele.</span><span class="sxs-lookup"><span data-stu-id="57fe9-435">Consider a mechanism to allow users to upload certificates: Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="57fe9-436">Podrobnosti najdete v tématu [Konfigurace ochrany ASP.NET Core dat](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="57fe9-436">See [Configure ASP.NET Core Data Protection](xref:security/data-protection/configuration/overview) for details.</span></span>

* <span data-ttu-id="57fe9-437">**Konfigurace fondu aplikací služby IIS pro načtení profilu uživatele**</span><span class="sxs-lookup"><span data-stu-id="57fe9-437">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="57fe9-438">Toto nastavení se nachází v části **model procesu** v části **Rozšířená nastavení** fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="57fe9-438">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="57fe9-439">Nastavte **načíst profil uživatele** na `True`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-439">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="57fe9-440">Když nastavíte `True`, klíče se ukládají v adresáři profilu uživatele a jsou chráněné pomocí DPAPI s klíčem specifickým pro uživatelský účet.</span><span class="sxs-lookup"><span data-stu-id="57fe9-440">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="57fe9-441">Klíče jsou uchovány ve složce *% localappdata%/ASP.NET/DataProtection-Keys* .</span><span class="sxs-lookup"><span data-stu-id="57fe9-441">Keys are persisted to the *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* folder.</span></span>

  <span data-ttu-id="57fe9-442">Musí být povolený i [atribut setProfileEnvironment](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="57fe9-442">The app pool's [setProfileEnvironment attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="57fe9-443">Výchozí hodnota `setProfileEnvironment` je `true`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-443">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="57fe9-444">V některých scénářích (například operační systém Windows) je `setProfileEnvironment` nastaveno na `false`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-444">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="57fe9-445">Pokud se klíče neukládají v adresáři profilu uživatele podle očekávání:</span><span class="sxs-lookup"><span data-stu-id="57fe9-445">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="57fe9-446">Přejděte do složky *% windir%/system32/Inetsrv/config* .</span><span class="sxs-lookup"><span data-stu-id="57fe9-446">Navigate to the *%windir%/system32/inetsrv/config* folder.</span></span>
  1. <span data-ttu-id="57fe9-447">Otevřete soubor *ApplicationHost. config* .</span><span class="sxs-lookup"><span data-stu-id="57fe9-447">Open the *applicationHost.config* file.</span></span>
  1. <span data-ttu-id="57fe9-448">Vyhledejte element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-448">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="57fe9-449">Potvrďte, že atribut `setProfileEnvironment` není přítomen, což nastaví hodnotu `true`, nebo explicitně nastavte hodnotu atributu na `true`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-449">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="57fe9-450">**Použití systému souborů jako úložiště pro Key Ring**</span><span class="sxs-lookup"><span data-stu-id="57fe9-450">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="57fe9-451">Upravte kód aplikace tak, aby [používal systém souborů jako úložiště ve službě Key Ring](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="57fe9-451">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="57fe9-452">Použijte X509 certifikátu k ochraně klíče kanál a zajistit certifikát není důvěryhodný certifikát.</span><span class="sxs-lookup"><span data-stu-id="57fe9-452">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="57fe9-453">Pokud certifikát podepsaný svým držitelem, můžete certifikát umístěte do úložiště důvěryhodných kořenových certifikátů.</span><span class="sxs-lookup"><span data-stu-id="57fe9-453">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="57fe9-454">Pokud používáte IIS ve webové farmě:</span><span class="sxs-lookup"><span data-stu-id="57fe9-454">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="57fe9-455">Použití sdílené složky, ke kterému přístup všechny počítače.</span><span class="sxs-lookup"><span data-stu-id="57fe9-455">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="57fe9-456">Nasazení x X509 certifikát pro každý počítač.</span><span class="sxs-lookup"><span data-stu-id="57fe9-456">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="57fe9-457">Konfigurace [ochrany dat v kódu](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="57fe9-457">Configure [data protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="57fe9-458">**Nastavení zásad pro ochranu dat na úrovni počítače**</span><span class="sxs-lookup"><span data-stu-id="57fe9-458">**Set a machine-wide policy for data protection**</span></span>

  <span data-ttu-id="57fe9-459">Systém ochrany dat má omezená podpora pro nastavení výchozích [zásad](xref:security/data-protection/configuration/machine-wide-policy) pro všechny aplikace, které využívají rozhraní API pro ochranu dat.</span><span class="sxs-lookup"><span data-stu-id="57fe9-459">The data protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="57fe9-460">Další informace naleznete v tématu <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-460">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="virtual-directories"></a><span data-ttu-id="57fe9-461">Virtuální adresáře</span><span class="sxs-lookup"><span data-stu-id="57fe9-461">Virtual Directories</span></span>

<span data-ttu-id="57fe9-462">Aplikace ASP.NET Core nepodporují [virtuální adresáře služby IIS](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) .</span><span class="sxs-lookup"><span data-stu-id="57fe9-462">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="57fe9-463">Aplikace může být hostována jako [dílčí aplikace](#sub-applications).</span><span class="sxs-lookup"><span data-stu-id="57fe9-463">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="57fe9-464">Dílčí aplikace</span><span class="sxs-lookup"><span data-stu-id="57fe9-464">Sub-applications</span></span>

<span data-ttu-id="57fe9-465">Aplikace ASP.NET Core může být hostovaná jako [podaplikace služby IIS (dílčí aplikace)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span><span class="sxs-lookup"><span data-stu-id="57fe9-465">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="57fe9-466">Sub – aplikace cesta stane součástí adresy URL kořenového aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-466">The sub-app's path becomes part of the root app's URL.</span></span>

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="57fe9-467">Odběr aplikace by neměl obsahovat modul ASP.NET Core jako obslužná rutina.</span><span class="sxs-lookup"><span data-stu-id="57fe9-467">A sub-app shouldn't include the ASP.NET Core Module as a handler.</span></span> <span data-ttu-id="57fe9-468">Pokud se modul přidá jako obslužná rutina v souboru *Web. config* dílčí aplikace 500,19, při pokusu o procházení dílčí aplikace se obdrží *interní chyba serveru* odkazující na chybný konfigurační soubor.</span><span class="sxs-lookup"><span data-stu-id="57fe9-468">If the module is added as a handler in a sub-app's *web.config* file, a *500.19 Internal Server Error* referencing the faulty config file is received when attempting to browse the sub-app.</span></span>

<span data-ttu-id="57fe9-469">Následující příklad ukazuje publikovaný soubor *Web. config* pro ASP.NET Core dílčí aplikaci:</span><span class="sxs-lookup"><span data-stu-id="57fe9-469">The following example shows a published *web.config* file for an ASP.NET Core sub-app:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="57fe9-470">Když se v aplikaci ASP.NET Core hostuje podaplikace Core non-ASP.NET, explicitně se odebere zděděná obslužná rutina v souboru *Web. config* dílčí aplikace:</span><span class="sxs-lookup"><span data-stu-id="57fe9-470">When hosting a non-ASP.NET Core sub-app underneath an ASP.NET Core app, explicitly remove the inherited handler in the sub-app's *web.config* file:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

<span data-ttu-id="57fe9-471">Propojení statických prostředků v rámci dílčí aplikace by měla používat notaci vlnovku (`~/`).</span><span class="sxs-lookup"><span data-stu-id="57fe9-471">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="57fe9-472">Znak tildy vlnovkou spustí [pomocnou nápovědu](xref:mvc/views/tag-helpers/intro) k předřazení pathbase dílčí aplikace na vykreslený relativní odkaz.</span><span class="sxs-lookup"><span data-stu-id="57fe9-472">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="57fe9-473">V případě dílčí aplikace na `/subapp_path`se obrázek propojený s `src="~/image.png"` vykresluje jako `src="/subapp_path/image.png"`.</span><span class="sxs-lookup"><span data-stu-id="57fe9-473">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="57fe9-474">Middleware kořenové aplikace statické soubory nelze zpracovat požadavek statický soubor.</span><span class="sxs-lookup"><span data-stu-id="57fe9-474">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="57fe9-475">Žádost zpracovává Middleware sub aplikace statické soubory.</span><span class="sxs-lookup"><span data-stu-id="57fe9-475">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="57fe9-476">Pokud je atribut `src` statického prostředku nastavený na absolutní cestu (například `src="/image.png"`), odkaz se vykreslí bez pathbase dílčí aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-476">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="57fe9-477">Middleware statických souborů v kořenové aplikaci se pokusí o poskytování assetu z [kořenového adresáře webu](xref:fundamentals/index#web-root)kořenové aplikace, což má za následek *404 – nenalezené* odpovědi, pokud není k dispozici statický prostředek z kořenové aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-477">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="57fe9-478">K hostování aplikace v ASP.NET Core jako podřízeným aplikacím v rámci jiné aplikace ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="57fe9-478">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="57fe9-479">Vytvořte fond aplikací pro aplikaci sub.</span><span class="sxs-lookup"><span data-stu-id="57fe9-479">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="57fe9-480">Nastavte **verzi .NET CLR** na **žádný spravovaný kód** , protože základní modul CLR (Common Language Runtime) pro .NET Core se spouští k hostování aplikace v pracovním procesu, ne CLR Desktop (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="57fe9-480">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="57fe9-481">Přidání kořenového webu s podřízeným aplikacím v rámci kořenového webu ve Správci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-481">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="57fe9-482">Klikněte pravým tlačítkem na složku dílčí aplikace ve Správci služby IIS a vyberte **převést na aplikaci**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-482">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="57fe9-483">V dialogovém okně **Přidat aplikaci** použijte pro **fond aplikací** tlačítko **Vybrat** , abyste přiřadili fond aplikací, který jste vytvořili pro dílčí aplikaci.</span><span class="sxs-lookup"><span data-stu-id="57fe9-483">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="57fe9-484">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-484">Select **OK**.</span></span>

<span data-ttu-id="57fe9-485">Přiřazení fondu samostatné aplikace k podřízeným aplikacím je požadavek, pokud používáte model hostingu v procesu.</span><span class="sxs-lookup"><span data-stu-id="57fe9-485">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="57fe9-486">Další informace o modelu hostování v rámci procesu a konfiguraci modulu ASP.NET Core naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-486">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="configuration-of-iis-with-webconfig"></a><span data-ttu-id="57fe9-487">Konfigurace služby IIS pomocí souboru web.config</span><span class="sxs-lookup"><span data-stu-id="57fe9-487">Configuration of IIS with web.config</span></span>

<span data-ttu-id="57fe9-488">Konfigurace služby IIS má vliv na `<system.webServer>` části *Web. config* pro scénáře služby IIS, které jsou funkční pro ASP.NET Core aplikace s modulem ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="57fe9-488">IIS configuration is influenced by the `<system.webServer>` section of *web.config* for IIS scenarios that are functional for ASP.NET Core apps with the ASP.NET Core Module.</span></span> <span data-ttu-id="57fe9-489">Konfigurace služby IIS je třeba funkční dynamické komprese.</span><span class="sxs-lookup"><span data-stu-id="57fe9-489">For example, IIS configuration is functional for dynamic compression.</span></span> <span data-ttu-id="57fe9-490">Pokud je služba IIS nakonfigurovaná na úrovni serveru, aby používala dynamickou kompresi, `<urlCompression>` element v souboru *Web. config* aplikace ho může zakázat pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-490">If IIS is configured at the server level to use dynamic compression, the `<urlCompression>` element in the app's *web.config* file can disable it for an ASP.NET Core app.</span></span>

<span data-ttu-id="57fe9-491">Další informace najdete v následujících tématech:</span><span class="sxs-lookup"><span data-stu-id="57fe9-491">For more information, see the following topics:</span></span>

* [<span data-ttu-id="57fe9-492">Odkaz na konfiguraci pro \<System. webServer ></span><span class="sxs-lookup"><span data-stu-id="57fe9-492">Configuration reference for \<system.webServer></span></span>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

<span data-ttu-id="57fe9-493">Chcete-li nastavit proměnné prostředí pro jednotlivé aplikace spuštěné v izolovaných fondech aplikací (podporované pro IIS 10,0 nebo novější), přečtěte si část *příkazového řádku Appcmd. exe* v tématu [proměnné prostředí \<environmentVariables >](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) v referenční dokumentaci služby IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-493">To set environment variables for individual apps running in isolated app pools (supported for IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic in the IIS reference documentation.</span></span>

## <a name="configuration-sections-of-webconfig"></a><span data-ttu-id="57fe9-494">Konfigurační oddíly souboru Web.config</span><span class="sxs-lookup"><span data-stu-id="57fe9-494">Configuration sections of web.config</span></span>

<span data-ttu-id="57fe9-495">Konfigurační oddíly aplikací ASP.NET 4. x v *souboru Web. config* nepoužívá aplikace ASP.NET Core pro konfiguraci:</span><span class="sxs-lookup"><span data-stu-id="57fe9-495">Configuration sections of ASP.NET 4.x apps in *web.config* aren't used by ASP.NET Core apps for configuration:</span></span>

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

<span data-ttu-id="57fe9-496">Aplikace ASP.NET Core jsou nakonfigurované pomocí jiných poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-496">ASP.NET Core apps are configured using other configuration providers.</span></span> <span data-ttu-id="57fe9-497">Další informace najdete v tématu [Konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="57fe9-497">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

## <a name="application-pools"></a><span data-ttu-id="57fe9-498">Fondy aplikací</span><span class="sxs-lookup"><span data-stu-id="57fe9-498">Application Pools</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="57fe9-499">Izolace fond aplikací je určeno model hostingu:</span><span class="sxs-lookup"><span data-stu-id="57fe9-499">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="57fe9-500">Hostování v procesu &ndash; aplikace se vyžadují ke spuštění v samostatných fondech aplikací.</span><span class="sxs-lookup"><span data-stu-id="57fe9-500">In-process hosting &ndash; Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="57fe9-501">Mimo procesy hostování &ndash; doporučujeme vzájemně izolovat aplikace tím, že spustíte každou aplikaci ve svém vlastním fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="57fe9-501">Out-of-process hosting &ndash; We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="57fe9-502">Dialogové okno **Přidat web** do služby IIS je ve výchozím nastavení nastaveno na jeden fond aplikací na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="57fe9-502">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="57fe9-503">Když je zadaný **název lokality** , text se automaticky přenese do textového pole **fondu aplikací** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-503">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="57fe9-504">Nový fond aplikací je vytvořený pomocí názvu serveru po přidání serveru.</span><span class="sxs-lookup"><span data-stu-id="57fe9-504">A new app pool is created using the site name when the site is added.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="57fe9-505">Při hostování více webů na serveru, doporučujeme izoluje aplikace od sebe navzájem spuštěním každou aplikaci ve vlastním fondu aplikací.</span><span class="sxs-lookup"><span data-stu-id="57fe9-505">When hosting multiple websites on a server, we recommend isolating the apps from each other by running each app in its own app pool.</span></span> <span data-ttu-id="57fe9-506">Do této konfigurace se nastaví dialogové okno **Přidat web** do služby IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-506">The IIS **Add Website** dialog defaults to this configuration.</span></span> <span data-ttu-id="57fe9-507">Když je zadaný **název lokality** , text se automaticky přenese do textového pole **fondu aplikací** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-507">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="57fe9-508">Nový fond aplikací je vytvořený pomocí názvu serveru po přidání serveru.</span><span class="sxs-lookup"><span data-stu-id="57fe9-508">A new app pool is created using the site name when the site is added.</span></span>

::: moniker-end

## <a name="application-pool-identity"></a><span data-ttu-id="57fe9-509">Identita fondu aplikací</span><span class="sxs-lookup"><span data-stu-id="57fe9-509">Application Pool Identity</span></span>

<span data-ttu-id="57fe9-510">Účet identita fondu aplikací umožňuje aplikaci běžet pod účtem jedinečný bez nutnosti vytvářet a spravovat domény nebo místní účty.</span><span class="sxs-lookup"><span data-stu-id="57fe9-510">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="57fe9-511">IIS 8.0 nebo novější procesů pro pracovníka Správce služby IIS (WAS) vytvoří virtuální účet s názvem nového fondu aplikací a aplikace spouští fondu pracovních procesů pod tímto účtem ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="57fe9-511">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="57fe9-512">V konzole pro správu služby IIS v části **Upřesnit nastavení** fondu aplikací zajistěte, aby byla **Identita** nastavená na použití **ApplicationPoolIdentity**:</span><span class="sxs-lookup"><span data-stu-id="57fe9-512">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use **ApplicationPoolIdentity**:</span></span>

![Dialogové okno pokročilé nastavení fondu aplikací](index/_static/apppool-identity.png)

<span data-ttu-id="57fe9-514">Proces správy služby IIS vytvoří zabezpečeného identifikátoru se název fondu aplikací v zabezpečení systému Windows.</span><span class="sxs-lookup"><span data-stu-id="57fe9-514">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="57fe9-515">Pomocí této identity, je možné svázat prostředky.</span><span class="sxs-lookup"><span data-stu-id="57fe9-515">Resources can be secured using this identity.</span></span> <span data-ttu-id="57fe9-516">Ale tato identita není skutečný účet a nezobrazí se v konzole pro správu uživatelů Windows.</span><span class="sxs-lookup"><span data-stu-id="57fe9-516">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="57fe9-517">Pokud pracovní proces služby IIS vyžaduje přístup k aplikaci přes se zvýšenými oprávněními, upravte seznam řízení přístupu (ACL) pro adresář obsahující aplikaci:</span><span class="sxs-lookup"><span data-stu-id="57fe9-517">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="57fe9-518">Otevřete Průzkumníka Windows a přejděte do adresáře.</span><span class="sxs-lookup"><span data-stu-id="57fe9-518">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="57fe9-519">Klikněte pravým tlačítkem na adresář a vyberte **vlastnosti**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-519">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="57fe9-520">Na kartě **zabezpečení** vyberte tlačítko **Upravit** a pak klikněte na tlačítko **Přidat** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-520">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="57fe9-521">Vyberte tlačítko **umístění** a ujistěte se, že je vybraný systém.</span><span class="sxs-lookup"><span data-stu-id="57fe9-521">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="57fe9-522">Do pole **Zadejte názvy objektů pro výběr** oblasti zadejte **\\< služby IIS AppPool > APP_POOL_NAME** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-522">Enter **IIS AppPool\\<app_pool_name>** in **Enter the object names to select** area.</span></span> <span data-ttu-id="57fe9-523">Vyberte tlačítko pro **kontrolu názvů** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-523">Select the **Check Names** button.</span></span> <span data-ttu-id="57fe9-524">Pro službu *DefaultAppPool* ověřte názvy pomocí **služby IIS AppPool\DefaultAppPool**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-524">For the *DefaultAppPool* check the names using **IIS AppPool\DefaultAppPool**.</span></span> <span data-ttu-id="57fe9-525">Když je vybráno tlačítko pro **kontrolu názvů** , hodnota **DefaultAppPool** je uvedena v oblasti názvy objektů.</span><span class="sxs-lookup"><span data-stu-id="57fe9-525">When the **Check Names** button is selected, a value of **DefaultAppPool** is indicated in the object names area.</span></span> <span data-ttu-id="57fe9-526">Není možné zadat název fondu aplikací přímo do oblasti názvy objektu.</span><span class="sxs-lookup"><span data-stu-id="57fe9-526">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="57fe9-527">Při kontrole názvu objektu použijte **\\< app_pool_name > formátu IIS AppPool** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-527">Use the **IIS AppPool\\<app_pool_name>** format when checking for the object name.</span></span>

   ![Dialogová okna pro výběr uživatelů nebo skupin pro složku aplikace: název fondu aplikací "DefaultAppPool" se připojí k "rozhraní IIS AppPool\" v oblasti názvy objektů před výběrem" kontrolovat jména ".](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="57fe9-529">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-529">Select **OK**.</span></span>

   ![Vyberte uživatele nebo skupiny dialogové okno pro složky aplikace: Po výběru "Kontrola názvů", "DefaultAppPool" se zobrazí v objektu název objektu názvy oblasti.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="57fe9-531">Ve výchozím nastavení by měla být udělena oprávnění ke čtení &amp;mu spouštění.</span><span class="sxs-lookup"><span data-stu-id="57fe9-531">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="57fe9-532">Zadejte další oprávnění podle potřeby.</span><span class="sxs-lookup"><span data-stu-id="57fe9-532">Provide additional permissions as needed.</span></span>

<span data-ttu-id="57fe9-533">Přístup se dá taky udělit na příkazovém řádku pomocí nástroje **Icacls** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-533">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="57fe9-534">Pomocí příkazu *DefaultAppPool* jako příkladu se používá následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="57fe9-534">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="57fe9-535">Další informace najdete v tématu [Icacls](/windows-server/administration/windows-commands/icacls) .</span><span class="sxs-lookup"><span data-stu-id="57fe9-535">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="57fe9-536">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="57fe9-536">HTTP/2 support</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="57fe9-537">[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje s ASP.NET Core v následujících scénářích nasazení služby IIS:</span><span class="sxs-lookup"><span data-stu-id="57fe9-537">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="57fe9-538">V procesu</span><span class="sxs-lookup"><span data-stu-id="57fe9-538">In-process</span></span>
  * <span data-ttu-id="57fe9-539">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="57fe9-539">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="57fe9-540">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="57fe9-540">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="57fe9-541">Mimo proces</span><span class="sxs-lookup"><span data-stu-id="57fe9-541">Out-of-process</span></span>
  * <span data-ttu-id="57fe9-542">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="57fe9-542">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="57fe9-543">Veřejná připojení hraničních serverů používají protokol HTTP/2, ale připojení reverzního proxy [serveru Kestrel](xref:fundamentals/servers/kestrel) používá protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="57fe9-543">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="57fe9-544">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="57fe9-544">TLS 1.2 or later connection</span></span>

<span data-ttu-id="57fe9-545">Pro nasazení v rámci procesu, když je navázáno připojení HTTP/2, `HTTP/2`sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) .</span><span class="sxs-lookup"><span data-stu-id="57fe9-545">For an in-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="57fe9-546">Pro nasazení mimo proces, když je navázáno připojení HTTP/2, `HTTP/1.1`sestavy [protokolu HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) .</span><span class="sxs-lookup"><span data-stu-id="57fe9-546">For an out-of-process deployment when an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="57fe9-547">Další informace o modelech hostování v procesu a mimoprocesové procesy naleznete v tématu <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="57fe9-547">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="57fe9-548">[Http/2](https://httpwg.org/specs/rfc7540.html) se podporuje pro nasazení mimo procesy, která splňují následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="57fe9-548">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported for out-of-process deployments that meet the following base requirements:</span></span>

* <span data-ttu-id="57fe9-549">Windows Server 2016 nebo Windows 10 nebo novější; IIS 10 nebo novější.</span><span class="sxs-lookup"><span data-stu-id="57fe9-549">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
* <span data-ttu-id="57fe9-550">Veřejná připojení hraničních serverů používají protokol HTTP/2, ale připojení reverzního proxy [serveru Kestrel](xref:fundamentals/servers/kestrel) používá protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="57fe9-550">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
* <span data-ttu-id="57fe9-551">Cílová architektura: není k dispozici pro nasazení mimo proces, protože připojení HTTP/2 je zpracována zcela službou IIS.</span><span class="sxs-lookup"><span data-stu-id="57fe9-551">Target framework: Not applicable to out-of-process deployments, since the HTTP/2 connection is handled entirely by IIS.</span></span>
* <span data-ttu-id="57fe9-552">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="57fe9-552">TLS 1.2 or later connection</span></span>

<span data-ttu-id="57fe9-553">Pokud je navázáno připojení HTTP/2, `HTTP/1.1`sestavy [protokolu HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) .</span><span class="sxs-lookup"><span data-stu-id="57fe9-553">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

::: moniker-end

<span data-ttu-id="57fe9-554">HTTP/2 je standardně povolená.</span><span class="sxs-lookup"><span data-stu-id="57fe9-554">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="57fe9-555">Připojení vrátit zpět k protokolu HTTP/1.1, pokud nedojde k připojení k protokolu HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="57fe9-555">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="57fe9-556">Další informace o konfiguraci HTTP/2 v nasazeních služby IIS najdete v tématu [http/2 ve službě IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="57fe9-556">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="57fe9-557">Požadavky na kontrolu před výstupem CORS</span><span class="sxs-lookup"><span data-stu-id="57fe9-557">CORS preflight requests</span></span>

<span data-ttu-id="57fe9-558">*Tato část platí jenom pro ASP.NET Core aplikace, které cílí na .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="57fe9-558">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="57fe9-559">Pro ASP.NET Core aplikaci, která cílí na .NET Framework, požadavky na možnosti nejsou ve výchozím nastavení ve službě IIS předány do aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-559">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="57fe9-560">Informace o tom, jak nakonfigurovat obslužné rutiny IIS aplikace v *souboru Web. config* , aby předávala požadavky na možnosti, najdete v tématu [Povolení žádostí o více zdrojů v ASP.NET webovém rozhraní API 2: jak CORS funguje](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="57fe9-560">To learn how to configure the app's IIS handlers in *web.config* to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="57fe9-561">Modul inicializace aplikace a časový limit nečinnosti</span><span class="sxs-lookup"><span data-stu-id="57fe9-561">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="57fe9-562">Při hostování ve službě IIS pomocí modulu ASP.NET Core verze 2:</span><span class="sxs-lookup"><span data-stu-id="57fe9-562">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="57fe9-563">[Modul inicializace aplikace](#application-initialization-module) &ndash; hostovaný [v procesu](#in-process-hosting-model) nebo [mimo proces](#out-of-process-hosting-model) aplikace se dá nakonfigurovat tak, aby se automaticky spouštěl v případě restartování nebo restartování serveru.</span><span class="sxs-lookup"><span data-stu-id="57fe9-563">[Application Initialization Module](#application-initialization-module) &ndash; App's hosted [in-process](#in-process-hosting-model) or [out-of-process](#out-of-process-hosting-model) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="57fe9-564">[Časový limit nečinnosti](#idle-timeout) &ndash; hostovaný [v procesu](#in-process-hosting-model) aplikace se dá nakonfigurovat tak, aby nevypršel časový limit v období nečinnosti.</span><span class="sxs-lookup"><span data-stu-id="57fe9-564">[Idle Timeout](#idle-timeout) &ndash; App's hosted [in-process](#in-process-hosting-model) can be configured not to timeout during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="57fe9-565">Modul inicializace aplikace</span><span class="sxs-lookup"><span data-stu-id="57fe9-565">Application Initialization Module</span></span>

<span data-ttu-id="57fe9-566">*Platí pro aplikace hostované v procesu a mimo proces.*</span><span class="sxs-lookup"><span data-stu-id="57fe9-566">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="57fe9-567">[Inicializace aplikace IIS](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) je funkce služby IIS, která do aplikace pošle požadavek HTTP, když se spustí nebo recykluje fond aplikací.</span><span class="sxs-lookup"><span data-stu-id="57fe9-567">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="57fe9-568">Požadavek spustí spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="57fe9-568">The request triggers the app to start.</span></span> <span data-ttu-id="57fe9-569">Ve výchozím nastavení služba IIS vydá požadavek na kořenovou adresu URL aplikace (`/`) k inicializaci aplikace (Další informace o konfiguraci najdete v [dalších zdrojích](#application-initialization-module-and-idle-timeout-additional-resources) ).</span><span class="sxs-lookup"><span data-stu-id="57fe9-569">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="57fe9-570">Potvrďte, že je povolená funkce role inicializace aplikace služby IIS:</span><span class="sxs-lookup"><span data-stu-id="57fe9-570">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="57fe9-571">V systémech Windows 7 nebo novějších stolních počítačích při používání služby IIS v místním prostředí:</span><span class="sxs-lookup"><span data-stu-id="57fe9-571">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="57fe9-572">Přejděte na **Ovládací panely** > **programy** > **programy a funkce** > **zapnout nebo vypnout funkce systému Windows** (levá strana obrazovky).</span><span class="sxs-lookup"><span data-stu-id="57fe9-572">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="57fe9-573">Otevřete **Internetová informační služba** > **webové služby** > funkce pro **vývoj aplikací**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-573">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="57fe9-574">Zaškrtněte políčko pro **inicializaci aplikace**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-574">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="57fe9-575">V systému Windows Server 2008 R2 nebo novějším:</span><span class="sxs-lookup"><span data-stu-id="57fe9-575">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="57fe9-576">Otevřete **Průvodce přidáním rolí a funkcí**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-576">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="57fe9-577">Na panelu **Vybrat služby rolí** otevřete uzel **vývoj aplikací** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-577">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="57fe9-578">Zaškrtněte políčko pro **inicializaci aplikace**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-578">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="57fe9-579">Pomocí některého z následujících přístupů povolte modul inicializace aplikace pro danou lokalitu:</span><span class="sxs-lookup"><span data-stu-id="57fe9-579">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="57fe9-580">Pomocí Správce služby IIS:</span><span class="sxs-lookup"><span data-stu-id="57fe9-580">Using IIS Manager:</span></span>

  1. <span data-ttu-id="57fe9-581">Na panelu **připojení** vyberte **fondy aplikací** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-581">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="57fe9-582">V seznamu klikněte pravým tlačítkem na fond aplikací aplikace a vyberte **Upřesnit nastavení**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-582">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="57fe9-583">Výchozí **režim spuštění** je **OnDemand**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-583">The default **Start Mode** is **OnDemand**.</span></span> <span data-ttu-id="57fe9-584">Nastavte **režim Start** na **AlwaysRunning**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-584">Set the **Start Mode** to **AlwaysRunning**.</span></span> <span data-ttu-id="57fe9-585">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-585">Select **OK**.</span></span>
  1. <span data-ttu-id="57fe9-586">Otevřete uzel **lokality** na panelu **připojení** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-586">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="57fe9-587">Klikněte pravým tlačítkem na aplikaci a vyberte **Spravovat web** > **Upřesnit nastavení**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-587">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="57fe9-588">Výchozí nastavení pro **Povolení přednačtení** je **false**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-588">The default **Preload Enabled** setting is **False**.</span></span> <span data-ttu-id="57fe9-589">Nastavte **předběžné načtení povoleno** na **hodnotu true**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-589">Set **Preload Enabled** to **True**.</span></span> <span data-ttu-id="57fe9-590">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-590">Select **OK**.</span></span>

* <span data-ttu-id="57fe9-591">Pomocí souboru *Web. config*přidejte prvek `<applicationInitialization>` s `doAppInitAfterRestart` nastavenou na `true` do `<system.webServer>` prvků v souboru *Web. config* aplikace:</span><span class="sxs-lookup"><span data-stu-id="57fe9-591">Using *web.config*, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's *web.config* file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="57fe9-592">Časový limit nečinnosti</span><span class="sxs-lookup"><span data-stu-id="57fe9-592">Idle Timeout</span></span>

<span data-ttu-id="57fe9-593">*Platí jenom pro aplikace hostované v procesu.*</span><span class="sxs-lookup"><span data-stu-id="57fe9-593">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="57fe9-594">Pokud chcete zabránit volnoběhu aplikace, nastavte časový limit nečinnosti fondu aplikací pomocí Správce služby IIS:</span><span class="sxs-lookup"><span data-stu-id="57fe9-594">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="57fe9-595">Na panelu **připojení** vyberte **fondy aplikací** .</span><span class="sxs-lookup"><span data-stu-id="57fe9-595">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="57fe9-596">V seznamu klikněte pravým tlačítkem na fond aplikací aplikace a vyberte **Upřesnit nastavení**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-596">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="57fe9-597">Výchozí **časový limit nečinnosti (minuty)** je **20** minut.</span><span class="sxs-lookup"><span data-stu-id="57fe9-597">The default **Idle Time-out (minutes)** is **20** minutes.</span></span> <span data-ttu-id="57fe9-598">Nastavte **časový limit nečinnosti (minuty)** na **0** (nula).</span><span class="sxs-lookup"><span data-stu-id="57fe9-598">Set the **Idle Time-out (minutes)** to **0** (zero).</span></span> <span data-ttu-id="57fe9-599">Vyberte **OK**.</span><span class="sxs-lookup"><span data-stu-id="57fe9-599">Select **OK**.</span></span>
1. <span data-ttu-id="57fe9-600">Recyklujte pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="57fe9-600">Recycle the worker process.</span></span>

<span data-ttu-id="57fe9-601">Chcete-li zabránit aplikacím hostovaným v [procesu](#out-of-process-hosting-model) vypršení časového limitu, použijte některý z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="57fe9-601">To prevent apps hosted [out-of-process](#out-of-process-hosting-model) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="57fe9-602">Pomocí příkazu otestujete aplikaci z externí služby, aby byla spuštěná.</span><span class="sxs-lookup"><span data-stu-id="57fe9-602">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="57fe9-603">Pokud aplikace hostuje jenom služby na pozadí, vyhněte se hostování služby IIS a použití [služby Windows k hostování aplikace ASP.NET Core](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="57fe9-603">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="57fe9-604">Modul inicializace aplikace a další prostředky časového limitu nečinnosti</span><span class="sxs-lookup"><span data-stu-id="57fe9-604">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="57fe9-605">Inicializace aplikace IIS 8,0</span><span class="sxs-lookup"><span data-stu-id="57fe9-605">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="57fe9-606">[Inicializace aplikace \<applicationInitialization >](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="57fe9-606">[Application Initialization \<applicationInitialization>](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="57fe9-607">[Nastavení modelu procesu pro fond aplikací \<processModel >](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="57fe9-607">[Process Model Settings for an Application Pool \<processModel>](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

::: moniker-end

## <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="57fe9-608">Materiály pro nasazení pro správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="57fe9-608">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="57fe9-609">Dokumentace ke službě IIS</span><span class="sxs-lookup"><span data-stu-id="57fe9-609">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="57fe9-610">Začínáme ve službě IIS pomocí Správce služby IIS</span><span class="sxs-lookup"><span data-stu-id="57fe9-610">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="57fe9-611">Nasazení aplikace .NET Core</span><span class="sxs-lookup"><span data-stu-id="57fe9-611">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

## <a name="additional-resources"></a><span data-ttu-id="57fe9-612">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="57fe9-612">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:index>
* [<span data-ttu-id="57fe9-613">Oficiální web Microsoft IIS</span><span class="sxs-lookup"><span data-stu-id="57fe9-613">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="57fe9-614">Knihovna technických obsahu pro Windows Server</span><span class="sxs-lookup"><span data-stu-id="57fe9-614">Windows Server technical content library</span></span>](/windows-server/windows-server)
* [<span data-ttu-id="57fe9-615">HTTP/2 ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="57fe9-615">HTTP/2 on IIS</span></span>](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>
