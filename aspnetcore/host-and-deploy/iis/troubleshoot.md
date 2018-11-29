---
title: Řešení potíží s ASP.NET Core ve službě IIS
author: guardrex
description: Zjistěte, jak diagnostikovat problémy s nasazením aplikací ASP.NET Core Internetové informační služby (IIS).
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2018
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 2ff870623de43676be38c5de8f338a7913e885a8
ms.sourcegitcommit: e9b99854b0a8021dafabee0db5e1338067f250a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52450707"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="9f69c-103">Řešení potíží s ASP.NET Core ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="9f69c-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="9f69c-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9f69c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9f69c-105">Tento článek obsahuje pokyny o tom, jak Diagnostika ASP.NET Core problém při spuštění aplikace při hostování za nástrojem s [Internetové informační služby (IIS)](/iis).</span><span class="sxs-lookup"><span data-stu-id="9f69c-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="9f69c-106">Informace v tomto článku se vztahují k hostování ve službě IIS na serveru systému Windows a Windows Desktop.</span><span class="sxs-lookup"><span data-stu-id="9f69c-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9f69c-107">V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="9f69c-107">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="9f69c-108">A *502.5 – selhání procesu* nebo *500.30 - Start selhání* , která nastane, pokud ladění místně může být troubleshooted pomocí doporučení v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="9f69c-108">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9f69c-109">V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="9f69c-109">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="9f69c-110">A *502.5 selhání procesu* , která nastane, pokud ladění místně může být troubleshooted pomocí doporučení v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="9f69c-110">A *502.5 Process Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

<span data-ttu-id="9f69c-111">Další témata pro řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="9f69c-111">Additional troubleshooting topics:</span></span>

<xref:host-and-deploy/azure-apps/troubleshoot>  
<span data-ttu-id="9f69c-112">I když služba App Service používá [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) a služby IIS pro hostování aplikací, v tématu vyhrazené pro pokyny, které jsou specifické pro App Service.</span><span class="sxs-lookup"><span data-stu-id="9f69c-112">Although App Service uses the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) and IIS to host apps, see the dedicated topic for instructions specific to App Service.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="9f69c-113">Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core během vývoje v místním systému.</span><span class="sxs-lookup"><span data-stu-id="9f69c-113">Discover how to handle errors in ASP.NET Core apps during development on a local system.</span></span>

[<span data-ttu-id="9f69c-114">Další informace k ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9f69c-114">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)  
<span data-ttu-id="9f69c-115">Toto téma popisuje funkce ladicího programu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9f69c-115">This topic introduces the features of the Visual Studio debugger.</span></span>

[<span data-ttu-id="9f69c-116">Ladění ve Visual Studiu Code</span><span class="sxs-lookup"><span data-stu-id="9f69c-116">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)  
<span data-ttu-id="9f69c-117">Další informace o podporu ladění, které jsou součástí Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="9f69c-117">Learn about the debugging support built into Visual Studio Code.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="9f69c-118">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="9f69c-118">App startup errors</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="9f69c-119">502.5 zpracovat selhání</span><span class="sxs-lookup"><span data-stu-id="9f69c-119">502.5 Process Failure</span></span>

<span data-ttu-id="9f69c-120">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="9f69c-120">The worker process fails.</span></span> <span data-ttu-id="9f69c-121">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="9f69c-121">The app doesn't start.</span></span>

<span data-ttu-id="9f69c-122">Modul ASP.NET Core se pokusí spustit proces dotnet back-endu, ale že ji nebude možné spustit.</span><span class="sxs-lookup"><span data-stu-id="9f69c-122">The ASP.NET Core Module attempts to start the backend dotnet process but it fails to start.</span></span> <span data-ttu-id="9f69c-123">Příčinu selhání spuštění procesu lze určit obvykle položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).</span><span class="sxs-lookup"><span data-stu-id="9f69c-123">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span> 

<span data-ttu-id="9f69c-124">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="9f69c-124">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="9f69c-125">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="9f69c-125">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

<span data-ttu-id="9f69c-126">*502.5 selhání procesu* při hostování nebo aplikace chybná konfigurace způsobí, že se pracovní proces selže, vrátí se chybová stránka:</span><span class="sxs-lookup"><span data-stu-id="9f69c-126">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

![Okno prohlížeče zobrazující stránku 502.5 selhání procesu](troubleshoot/_static/process-failure-page.png)

::: moniker range=">= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="9f69c-128">500.30 v procesu selhání spuštění</span><span class="sxs-lookup"><span data-stu-id="9f69c-128">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="9f69c-129">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="9f69c-129">The worker process fails.</span></span> <span data-ttu-id="9f69c-130">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="9f69c-130">The app doesn't start.</span></span>

<span data-ttu-id="9f69c-131">Modul ASP.NET Core se pokusí spustit .NET Core CLR v procesu, ale že ji nebude možné spustit.</span><span class="sxs-lookup"><span data-stu-id="9f69c-131">The ASP.NET Core Module attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="9f69c-132">Příčinu selhání spuštění procesu lze určit obvykle položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).</span><span class="sxs-lookup"><span data-stu-id="9f69c-132">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span> 

<span data-ttu-id="9f69c-133">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="9f69c-133">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="9f69c-134">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="9f69c-134">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="9f69c-135">500.0 v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="9f69c-135">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="9f69c-136">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="9f69c-136">The worker process fails.</span></span> <span data-ttu-id="9f69c-137">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="9f69c-137">The app doesn't start.</span></span>

<span data-ttu-id="9f69c-138">Modul ASP.NET Core se nepodařilo najít .NET Core CLR a najít obslužnou rutinu požadavků v procesu (*aspnetcorev2_inprocess.dll*).</span><span class="sxs-lookup"><span data-stu-id="9f69c-138">The ASP.NET Core Module fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="9f69c-139">Zkontrolujte, jestli:</span><span class="sxs-lookup"><span data-stu-id="9f69c-139">Check that:</span></span>

* <span data-ttu-id="9f69c-140">Aplikace cílí na buď [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) balíček NuGet nebo [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="9f69c-140">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="9f69c-141">Verze rozhraní framework ASP.NET Core sdílené cíle, které aplikace nainstalované v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="9f69c-141">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="9f69c-142">500.0 Chyba načtení out-Of-Process obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="9f69c-142">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="9f69c-143">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="9f69c-143">The worker process fails.</span></span> <span data-ttu-id="9f69c-144">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="9f69c-144">The app doesn't start.</span></span>

<span data-ttu-id="9f69c-145">Modul ASP.NET Core nenajde žádné obslužné rutiny hostování požadavku na více instancí procesu.</span><span class="sxs-lookup"><span data-stu-id="9f69c-145">The ASP.NET Core Module fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="9f69c-146">Ujistěte se, *aspnetcorev2_outofprocess.dll* je k dispozici v podsložce vedle *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="9f69c-146">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span> 

::: moniker-end

### <a name="500-internal-server-error"></a><span data-ttu-id="9f69c-147">Chyba 500 interní Server</span><span class="sxs-lookup"><span data-stu-id="9f69c-147">500 Internal Server Error</span></span>

<span data-ttu-id="9f69c-148">Spuštění aplikace, ale chybu brání splnění žádosti. na serveru.</span><span class="sxs-lookup"><span data-stu-id="9f69c-148">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="9f69c-149">Při spuštění nebo při vytváření odpovědi, k této chybě dochází v kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f69c-149">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="9f69c-150">Odpověď může obsahovat žádný obsah nebo se může zobrazit odpovědi *500 – Interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="9f69c-150">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="9f69c-151">V protokolu událostí aplikace obvykle hlásí, že aplikace se normálně spustit.</span><span class="sxs-lookup"><span data-stu-id="9f69c-151">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="9f69c-152">Z pohledu serveru, který je správný.</span><span class="sxs-lookup"><span data-stu-id="9f69c-152">From the server's perspective, that's correct.</span></span> <span data-ttu-id="9f69c-153">Aplikace začal, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="9f69c-153">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="9f69c-154">[Spuštění aplikace příkazového řádku](#run-the-app-at-a-command-prompt) na serveru nebo [povolit protokol stdout modul ASP.NET Core](#aspnet-core-module-stdout-log) k vyřešení tohoto problému.</span><span class="sxs-lookup"><span data-stu-id="9f69c-154">[Run the app at a command prompt](#run-the-app-at-a-command-prompt) on the server or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="9f69c-155">Obnovení připojení</span><span class="sxs-lookup"><span data-stu-id="9f69c-155">Connection reset</span></span>

<span data-ttu-id="9f69c-156">Pokud dojde k chybě po odeslání hlavičky, bude příliš pozdě pro server k odeslání **500 – Interní chyba serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="9f69c-156">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="9f69c-157">Často se to stane, když dojde k chybě při serializaci složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="9f69c-157">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="9f69c-158">Tento typ chyby se zobrazí jako *obnovení připojení* chyba na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="9f69c-158">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="9f69c-159">[Protokolování aplikací](xref:fundamentals/logging/index) mohou pomoci při řešení těchto typů chyb.</span><span class="sxs-lookup"><span data-stu-id="9f69c-159">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

## <a name="default-startup-limits"></a><span data-ttu-id="9f69c-160">Výchozí omezení při spuštění</span><span class="sxs-lookup"><span data-stu-id="9f69c-160">Default startup limits</span></span>

<span data-ttu-id="9f69c-161">Modul ASP.NET Core je nakonfigurovaná s výchozí *startupTimeLimit* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="9f69c-161">The ASP.NET Core Module is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="9f69c-162">Když necháte na výchozí hodnotu, aplikace může trvat až dvě minuty, spusťte před modulu protokoly selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="9f69c-162">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="9f69c-163">Informace o konfiguraci modulu najdete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="9f69c-163">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="9f69c-164">Řešení chyb při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="9f69c-164">Troubleshoot app startup errors</span></span>

### <a name="application-event-log"></a><span data-ttu-id="9f69c-165">Protokol událostí aplikace</span><span class="sxs-lookup"><span data-stu-id="9f69c-165">Application Event Log</span></span>

<span data-ttu-id="9f69c-166">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="9f69c-166">Access the Application Event Log:</span></span>

1. <span data-ttu-id="9f69c-167">Otevření nabídky Start, vyhledejte **Prohlížeč událostí**a pak vyberte **Prohlížeč událostí** aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f69c-167">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="9f69c-168">V **Prohlížeč událostí**, otevřete **protokoly Windows** uzlu.</span><span class="sxs-lookup"><span data-stu-id="9f69c-168">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="9f69c-169">Vyberte **aplikace** otevřít protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f69c-169">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="9f69c-170">Vyhledejte chyby související s selhání aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f69c-170">Search for errors associated with the failing app.</span></span> <span data-ttu-id="9f69c-171">Chyby mají hodnotu *modulu IIS AspNetCore* nebo *služby IIS Express AspNetCore modulu* v *zdroj* sloupce.</span><span class="sxs-lookup"><span data-stu-id="9f69c-171">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="9f69c-172">Spuštění aplikace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="9f69c-172">Run the app at a command prompt</span></span>

<span data-ttu-id="9f69c-173">Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f69c-173">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="9f69c-174">Příčin některých chyb můžete najít spuštěním aplikace v příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="9f69c-174">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="9f69c-175">Nasazení závisí na architektuře</span><span class="sxs-lookup"><span data-stu-id="9f69c-175">Framework-dependent deployment</span></span>

<span data-ttu-id="9f69c-176">Pokud je aplikace [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="9f69c-176">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="9f69c-177">Na příkazovém řádku přejděte do složky pro nasazení a spuštění aplikace spuštěním sestavení aplikace s *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="9f69c-177">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="9f69c-178">V následujícím příkazu nahraďte název sestavení aplikace pro \<název_sestavení >: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="9f69c-178">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="9f69c-179">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="9f69c-179">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="9f69c-180">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="9f69c-180">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="9f69c-181">Pomocí výchozího hostitele a post, vytvořit žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="9f69c-181">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="9f69c-182">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace reverzního proxy serveru a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f69c-182">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the reverse proxy configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="9f69c-183">Samostatná nasazení</span><span class="sxs-lookup"><span data-stu-id="9f69c-183">Self-contained deployment</span></span>

<span data-ttu-id="9f69c-184">Pokud je aplikace [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="9f69c-184">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="9f69c-185">Na příkazovém řádku přejděte do složky pro nasazení a spuštění spustitelného souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f69c-185">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="9f69c-186">V následujícím příkazu nahraďte název sestavení aplikace pro \<název_sestavení >: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="9f69c-186">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="9f69c-187">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="9f69c-187">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="9f69c-188">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="9f69c-188">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="9f69c-189">Pomocí výchozího hostitele a post, vytvořit žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="9f69c-189">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="9f69c-190">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace reverzního proxy serveru a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f69c-190">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the reverse proxy configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="9f69c-191">ASP.NET Core modulu stdout protokolu</span><span class="sxs-lookup"><span data-stu-id="9f69c-191">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="9f69c-192">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="9f69c-192">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="9f69c-193">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="9f69c-193">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="9f69c-194">Pokud *protokoly* složka není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="9f69c-194">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="9f69c-195">Pokyny o tom, jak povolit MSBuild k vytvoření *protokoly* složky v nasazení automaticky, zobrazí [adresářovou strukturu](xref:host-and-deploy/directory-structure) tématu.</span><span class="sxs-lookup"><span data-stu-id="9f69c-195">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="9f69c-196">Upravit *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="9f69c-196">Edit the *web.config* file.</span></span> <span data-ttu-id="9f69c-197">Nastavte **stdoutLogEnabled** k `true` a změnit **stdoutLogFile** tak, aby odkazoval na cestu *protokoly* složky (například `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="9f69c-197">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="9f69c-198">`stdout` v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="9f69c-198">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="9f69c-199">Časové razítko, id procesu a příponu souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="9f69c-199">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="9f69c-200">Pomocí `stdout` jako předpona názvu souboru, má název souboru typické protokolu *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="9f69c-200">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span> 
1. <span data-ttu-id="9f69c-201">Ověřte identitu fondu aplikací má oprávnění k zápisu *protokoly* složky.</span><span class="sxs-lookup"><span data-stu-id="9f69c-201">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="9f69c-202">Uložte aktualizovaný *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="9f69c-202">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="9f69c-203">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f69c-203">Make a request to the app.</span></span>
1. <span data-ttu-id="9f69c-204">Přejděte *protokoly* složky.</span><span class="sxs-lookup"><span data-stu-id="9f69c-204">Navigate to the *logs* folder.</span></span> <span data-ttu-id="9f69c-205">Vyhledání a otevření protokolu nejnovější stdout.</span><span class="sxs-lookup"><span data-stu-id="9f69c-205">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="9f69c-206">Studie v protokolu chyb.</span><span class="sxs-lookup"><span data-stu-id="9f69c-206">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="9f69c-207">Zakážete protokolování stdout po dokončení odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="9f69c-207">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="9f69c-208">Upravit *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="9f69c-208">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="9f69c-209">Nastavte **stdoutLogEnabled** k `false`.</span><span class="sxs-lookup"><span data-stu-id="9f69c-209">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="9f69c-210">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="9f69c-210">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="9f69c-211">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="9f69c-211">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="9f69c-212">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="9f69c-212">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="9f69c-213">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="9f69c-213">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="9f69c-214">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="9f69c-214">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="9f69c-215">Povolit na stránce výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="9f69c-215">Enable the Developer Exception Page</span></span>

<span data-ttu-id="9f69c-216">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do souboru web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) ke spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="9f69c-216">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="9f69c-217">Tak dlouho, dokud není prostředí přepsána ve spuštění aplikace podle `UseEnvironment` na tvůrce hostitele nastavení proměnné prostředí umožňuje [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) se zobrazí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f69c-217">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

<span data-ttu-id="9f69c-218">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití v přípravy a testování serverů, které nejsou vystaveny v Internetu.</span><span class="sxs-lookup"><span data-stu-id="9f69c-218">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="9f69c-219">Odebrat z proměnné prostředí *web.config* soubor po vyřešení potíží.</span><span class="sxs-lookup"><span data-stu-id="9f69c-219">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="9f69c-220">Informace o nastavení proměnných prostředí *web.config*, naleznete v tématu [environmentVariables podřízený prvek aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="9f69c-220">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="9f69c-221">Běžné chyby spuštění</span><span class="sxs-lookup"><span data-stu-id="9f69c-221">Common startup errors</span></span>

<span data-ttu-id="9f69c-222">Viz <xref:host-and-deploy/azure-iis-errors-reference>.</span><span class="sxs-lookup"><span data-stu-id="9f69c-222">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="9f69c-223">Většina běžných problémů, které brání spuštění aplikace jsou zahrnuté v referenčním tématu.</span><span class="sxs-lookup"><span data-stu-id="9f69c-223">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="9f69c-224">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="9f69c-224">Obtain data from an app</span></span>

<span data-ttu-id="9f69c-225">Pokud aplikace je schopná reagovat na požadavky, získáte žádost o připojení a další data z aplikace pomocí terminálu vložené middlewaru.</span><span class="sxs-lookup"><span data-stu-id="9f69c-225">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="9f69c-226">Další informace a ukázky kódu najdete v tématu <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="9f69c-226">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="slow-or-hanging-app"></a><span data-ttu-id="9f69c-227">Pomalá nebo Změ aplikace</span><span class="sxs-lookup"><span data-stu-id="9f69c-227">Slow or hanging app</span></span>

<span data-ttu-id="9f69c-228">Když aplikace reaguje pomalu nebo přestane reagovat na vyžádání, získání a analyzovat [soubor s výpisem paměti](/visualstudio/debugger/using-dump-files).</span><span class="sxs-lookup"><span data-stu-id="9f69c-228">When an app responds slowly or hangs on a request, obtain and analyze a [dump file](/visualstudio/debugger/using-dump-files).</span></span> <span data-ttu-id="9f69c-229">Soubory s výpisem paměti se dají získat pomocí kteréhokoli z následujících nástrojů:</span><span class="sxs-lookup"><span data-stu-id="9f69c-229">Dump files can be obtained using any of the following tools:</span></span>

* [<span data-ttu-id="9f69c-230">ProcDump</span><span class="sxs-lookup"><span data-stu-id="9f69c-230">ProcDump</span></span>](/sysinternals/downloads/procdump)
* [<span data-ttu-id="9f69c-231">DebugDiag</span><span class="sxs-lookup"><span data-stu-id="9f69c-231">DebugDiag</span></span>](https://www.microsoft.com/download/details.aspx?id=49924)
* <span data-ttu-id="9f69c-232">WinDbg: [stáhnout Debugging tools for Windows](https://developer.microsoft.com/windows/hardware/download-windbg), [ladění pomocí WinDbg](/windows-hardware/drivers/debugger/debugging-using-windbg)</span><span class="sxs-lookup"><span data-stu-id="9f69c-232">WinDbg: [Download Debugging tools for Windows](https://developer.microsoft.com/windows/hardware/download-windbg), [Debugging Using WinDbg](/windows-hardware/drivers/debugger/debugging-using-windbg)</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="9f69c-233">Vzdálené ladění</span><span class="sxs-lookup"><span data-stu-id="9f69c-233">Remote debugging</span></span>

<span data-ttu-id="9f69c-234">Zobrazit [vzdálené ladění ASP.NET Core ve vzdáleném počítači služby IIS v sadě Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) v dokumentaci k sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="9f69c-234">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="9f69c-235">Application Insights</span><span class="sxs-lookup"><span data-stu-id="9f69c-235">Application Insights</span></span>

<span data-ttu-id="9f69c-236">[Application Insights](/azure/application-insights/) poskytuje telemetrická data z aplikací hostovaných službou IIS, včetně protokolování a generování sestav funkce chyb.</span><span class="sxs-lookup"><span data-stu-id="9f69c-236">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="9f69c-237">Application Insights může jenom nahlásit to o chybách, ke kterým dochází po spuštění aplikace, když funkce protokolování aplikace budou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="9f69c-237">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="9f69c-238">Další informace najdete v tématu [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="9f69c-238">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="9f69c-239">Další Rady</span><span class="sxs-lookup"><span data-stu-id="9f69c-239">Additional advice</span></span>

<span data-ttu-id="9f69c-240">Někdy funkční aplikace selže okamžitě po provedení upgradu buď .NET Core SDK na vývojové počítače nebo balíček verze v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f69c-240">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="9f69c-241">V některých případech osamocené balíčky mohou narušit funkce aplikace při provádění hlavní upgrady.</span><span class="sxs-lookup"><span data-stu-id="9f69c-241">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="9f69c-242">Většina těchto problémů můžete opravit podle těchto pokynů:</span><span class="sxs-lookup"><span data-stu-id="9f69c-242">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="9f69c-243">Odstranit *bin* a *obj* složek.</span><span class="sxs-lookup"><span data-stu-id="9f69c-243">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="9f69c-244">Vymazat balíček ukládá do mezipaměti na *% UserProfile %\\.nuget\\balíčky* a *% LocalAppData %\\Nuget\\v3-cache*.</span><span class="sxs-lookup"><span data-stu-id="9f69c-244">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="9f69c-245">Obnovit a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="9f69c-245">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="9f69c-246">Potvrďte, že předchozího nasazení na serveru byl zcela odstraněn před opětovným nasazením aplikace.</span><span class="sxs-lookup"><span data-stu-id="9f69c-246">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="9f69c-247">Pohodlný způsob, jak Vymazat mezipaměti balíčku je ke spuštění `dotnet nuget locals all --clear` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="9f69c-247">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="9f69c-248">Vymazání mezipaměti balíčku provést taky pomocí [nuget.exe](https://www.nuget.org/downloads) nástroj a provádění příkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="9f69c-248">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="9f69c-249">*nuget.exe* není připojené instalace s operačním systémem klasické pracovní plochy Windows a je potřeba pořídit samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="9f69c-249">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9f69c-250">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="9f69c-250">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
