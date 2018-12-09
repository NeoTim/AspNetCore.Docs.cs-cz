---
title: Řešení potíží s ASP.NET Core ve službě IIS
author: guardrex
description: Zjistěte, jak diagnostikovat problémy s nasazením aplikací ASP.NET Core Internetové informační služby (IIS).
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 6d43057639ea88bb21ac66f2799062e06fffc530
ms.sourcegitcommit: 49faca2644590fc081d86db46ea5e29edfc28b7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2018
ms.locfileid: "53121684"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="467a8-103">Řešení potíží s ASP.NET Core ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="467a8-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="467a8-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="467a8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="467a8-105">Tento článek obsahuje pokyny o tom, jak Diagnostika ASP.NET Core problém při spuštění aplikace při hostování za nástrojem s [Internetové informační služby (IIS)](/iis).</span><span class="sxs-lookup"><span data-stu-id="467a8-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="467a8-106">Informace v tomto článku se vztahují k hostování ve službě IIS na serveru systému Windows a Windows Desktop.</span><span class="sxs-lookup"><span data-stu-id="467a8-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="467a8-107">V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="467a8-107">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="467a8-108">A *502.5 – selhání procesu* nebo *500.30 - Start selhání* , která nastane, pokud ladění místně může být troubleshooted pomocí doporučení v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="467a8-108">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="467a8-109">V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="467a8-109">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="467a8-110">A *502.5 selhání procesu* , která nastane, pokud ladění místně může být troubleshooted pomocí doporučení v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="467a8-110">A *502.5 Process Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

<span data-ttu-id="467a8-111">Další témata pro řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="467a8-111">Additional troubleshooting topics:</span></span>

<xref:host-and-deploy/azure-apps/troubleshoot>  
<span data-ttu-id="467a8-112">I když služba App Service používá [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) a služby IIS pro hostování aplikací, v tématu vyhrazené pro pokyny, které jsou specifické pro App Service.</span><span class="sxs-lookup"><span data-stu-id="467a8-112">Although App Service uses the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) and IIS to host apps, see the dedicated topic for instructions specific to App Service.</span></span>

<xref:fundamentals/error-handling>  
<span data-ttu-id="467a8-113">Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core během vývoje v místním systému.</span><span class="sxs-lookup"><span data-stu-id="467a8-113">Discover how to handle errors in ASP.NET Core apps during development on a local system.</span></span>

[<span data-ttu-id="467a8-114">Další informace k ladění pomocí sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="467a8-114">Learn to debug using Visual Studio</span></span>](/visualstudio/debugger/getting-started-with-the-debugger)  
<span data-ttu-id="467a8-115">Toto téma popisuje funkce ladicího programu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="467a8-115">This topic introduces the features of the Visual Studio debugger.</span></span>

[<span data-ttu-id="467a8-116">Ladění ve Visual Studiu Code</span><span class="sxs-lookup"><span data-stu-id="467a8-116">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/editor/debugging)  
<span data-ttu-id="467a8-117">Další informace o podporu ladění, které jsou součástí Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="467a8-117">Learn about the debugging support built into Visual Studio Code.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="467a8-118">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="467a8-118">App startup errors</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="467a8-119">502.5 zpracovat selhání</span><span class="sxs-lookup"><span data-stu-id="467a8-119">502.5 Process Failure</span></span>

<span data-ttu-id="467a8-120">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="467a8-120">The worker process fails.</span></span> <span data-ttu-id="467a8-121">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="467a8-121">The app doesn't start.</span></span>

<span data-ttu-id="467a8-122">Modul ASP.NET Core se pokusí spustit proces dotnet back-endu, ale že ji nebude možné spustit.</span><span class="sxs-lookup"><span data-stu-id="467a8-122">The ASP.NET Core Module attempts to start the backend dotnet process but it fails to start.</span></span> <span data-ttu-id="467a8-123">Příčinu selhání spuštění procesu lze určit obvykle položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).</span><span class="sxs-lookup"><span data-stu-id="467a8-123">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span> 

<span data-ttu-id="467a8-124">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="467a8-124">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="467a8-125">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="467a8-125">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

<span data-ttu-id="467a8-126">*502.5 selhání procesu* při hostování nebo aplikace chybná konfigurace způsobí, že se pracovní proces selže, vrátí se chybová stránka:</span><span class="sxs-lookup"><span data-stu-id="467a8-126">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

![Okno prohlížeče zobrazující stránku 502.5 selhání procesu](troubleshoot/_static/process-failure-page.png)

::: moniker range=">= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="467a8-128">500.30 v procesu selhání spuštění</span><span class="sxs-lookup"><span data-stu-id="467a8-128">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="467a8-129">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="467a8-129">The worker process fails.</span></span> <span data-ttu-id="467a8-130">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="467a8-130">The app doesn't start.</span></span>

<span data-ttu-id="467a8-131">Modul ASP.NET Core se pokusí spustit .NET Core CLR v procesu, ale že ji nebude možné spustit.</span><span class="sxs-lookup"><span data-stu-id="467a8-131">The ASP.NET Core Module attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="467a8-132">Příčinu selhání spuštění procesu lze určit obvykle položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).</span><span class="sxs-lookup"><span data-stu-id="467a8-132">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span> 

<span data-ttu-id="467a8-133">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="467a8-133">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="467a8-134">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="467a8-134">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="467a8-135">500.0 v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="467a8-135">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="467a8-136">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="467a8-136">The worker process fails.</span></span> <span data-ttu-id="467a8-137">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="467a8-137">The app doesn't start.</span></span>

<span data-ttu-id="467a8-138">Modul ASP.NET Core se nepodařilo najít .NET Core CLR a najít obslužnou rutinu požadavků v procesu (*aspnetcorev2_inprocess.dll*).</span><span class="sxs-lookup"><span data-stu-id="467a8-138">The ASP.NET Core Module fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="467a8-139">Zkontrolujte, jestli:</span><span class="sxs-lookup"><span data-stu-id="467a8-139">Check that:</span></span>

* <span data-ttu-id="467a8-140">Aplikace cílí na buď [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) balíček NuGet nebo [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="467a8-140">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="467a8-141">Verze rozhraní framework ASP.NET Core sdílené cíle, které aplikace nainstalované v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="467a8-141">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="467a8-142">500.0 Chyba načtení out-Of-Process obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="467a8-142">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="467a8-143">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="467a8-143">The worker process fails.</span></span> <span data-ttu-id="467a8-144">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="467a8-144">The app doesn't start.</span></span>

<span data-ttu-id="467a8-145">Modul ASP.NET Core nenajde žádné obslužné rutiny hostování požadavku na více instancí procesu.</span><span class="sxs-lookup"><span data-stu-id="467a8-145">The ASP.NET Core Module fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="467a8-146">Ujistěte se, *aspnetcorev2_outofprocess.dll* je k dispozici v podsložce vedle *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="467a8-146">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span> 

::: moniker-end

### <a name="500-internal-server-error"></a><span data-ttu-id="467a8-147">Chyba 500 interní Server</span><span class="sxs-lookup"><span data-stu-id="467a8-147">500 Internal Server Error</span></span>

<span data-ttu-id="467a8-148">Spuštění aplikace, ale chybu brání splnění žádosti. na serveru.</span><span class="sxs-lookup"><span data-stu-id="467a8-148">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="467a8-149">Při spuštění nebo při vytváření odpovědi, k této chybě dochází v kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="467a8-149">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="467a8-150">Odpověď může obsahovat žádný obsah nebo se může zobrazit odpovědi *500 – Interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="467a8-150">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="467a8-151">V protokolu událostí aplikace obvykle hlásí, že aplikace se normálně spustit.</span><span class="sxs-lookup"><span data-stu-id="467a8-151">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="467a8-152">Z pohledu serveru, který je správný.</span><span class="sxs-lookup"><span data-stu-id="467a8-152">From the server's perspective, that's correct.</span></span> <span data-ttu-id="467a8-153">Aplikace začal, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="467a8-153">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="467a8-154">[Spuštění aplikace příkazového řádku](#run-the-app-at-a-command-prompt) na serveru nebo [povolit protokol stdout modul ASP.NET Core](#aspnet-core-module-stdout-log) k vyřešení tohoto problému.</span><span class="sxs-lookup"><span data-stu-id="467a8-154">[Run the app at a command prompt](#run-the-app-at-a-command-prompt) on the server or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="467a8-155">Nepovedlo se spustit aplikaci (kód chyby "0x800700c1")</span><span class="sxs-lookup"><span data-stu-id="467a8-155">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="467a8-156">Aplikaci se nepodařilo spustit, protože sestavení aplikace (*.dll*) nelze načíst.</span><span class="sxs-lookup"><span data-stu-id="467a8-156">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="467a8-157">Tato chyba nastane, pokud došlo k neshodě bitové verze mezi publikované aplikace a proces w3wp/iisexpress.</span><span class="sxs-lookup"><span data-stu-id="467a8-157">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="467a8-158">Ověřte správnost nastavení 32-bit fondu aplikací:</span><span class="sxs-lookup"><span data-stu-id="467a8-158">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="467a8-159">Vyberte fond aplikací ve Správci služby IIS na **fondy aplikací**.</span><span class="sxs-lookup"><span data-stu-id="467a8-159">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="467a8-160">Vyberte **Upřesnit nastavení** pod **upravit fond aplikací** v **akce** panelu.</span><span class="sxs-lookup"><span data-stu-id="467a8-160">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="467a8-161">Nastavte **povolit 32bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="467a8-161">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="467a8-162">Pokud nasazení (x86) 32bitové aplikace, nastavte hodnotu na `True`.</span><span class="sxs-lookup"><span data-stu-id="467a8-162">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="467a8-163">Pokud nasazení (x64) 64bitové aplikace, nastavte hodnotu na `False`.</span><span class="sxs-lookup"><span data-stu-id="467a8-163">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="467a8-164">Obnovení připojení</span><span class="sxs-lookup"><span data-stu-id="467a8-164">Connection reset</span></span>

<span data-ttu-id="467a8-165">Pokud dojde k chybě po odeslání hlavičky, bude příliš pozdě pro server k odeslání **500 – Interní chyba serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="467a8-165">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="467a8-166">Často se to stane, když dojde k chybě při serializaci složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="467a8-166">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="467a8-167">Tento typ chyby se zobrazí jako *obnovení připojení* chyba na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="467a8-167">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="467a8-168">[Protokolování aplikací](xref:fundamentals/logging/index) mohou pomoci při řešení těchto typů chyb.</span><span class="sxs-lookup"><span data-stu-id="467a8-168">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

## <a name="default-startup-limits"></a><span data-ttu-id="467a8-169">Výchozí omezení při spuštění</span><span class="sxs-lookup"><span data-stu-id="467a8-169">Default startup limits</span></span>

<span data-ttu-id="467a8-170">Modul ASP.NET Core je nakonfigurovaná s výchozí *startupTimeLimit* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="467a8-170">The ASP.NET Core Module is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="467a8-171">Když necháte na výchozí hodnotu, aplikace může trvat až dvě minuty, spusťte před modulu protokoly selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="467a8-171">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="467a8-172">Informace o konfiguraci modulu najdete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="467a8-172">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="467a8-173">Řešení chyb při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="467a8-173">Troubleshoot app startup errors</span></span>

### <a name="enable-the-aspnet-core-module-debug-log"></a><span data-ttu-id="467a8-174">Povolit protokol ladění modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="467a8-174">Enable the ASP.NET Core Module debug log</span></span>

<span data-ttu-id="467a8-175">Přidáním následujícího nastavení obslužné rutiny na aplikaci *web.config* souboru povolení protokolů ladění modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="467a8-175">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug logs:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="467a8-176">Potvrďte, že cesta zadaná pro protokol existuje a že identita fondu aplikací má oprávnění k zápisu do umístění.</span><span class="sxs-lookup"><span data-stu-id="467a8-176">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

### <a name="application-event-log"></a><span data-ttu-id="467a8-177">Protokol událostí aplikace</span><span class="sxs-lookup"><span data-stu-id="467a8-177">Application Event Log</span></span>

<span data-ttu-id="467a8-178">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="467a8-178">Access the Application Event Log:</span></span>

1. <span data-ttu-id="467a8-179">Otevření nabídky Start, vyhledejte **Prohlížeč událostí**a pak vyberte **Prohlížeč událostí** aplikace.</span><span class="sxs-lookup"><span data-stu-id="467a8-179">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="467a8-180">V **Prohlížeč událostí**, otevřete **protokoly Windows** uzlu.</span><span class="sxs-lookup"><span data-stu-id="467a8-180">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="467a8-181">Vyberte **aplikace** otevřít protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="467a8-181">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="467a8-182">Vyhledejte chyby související s selhání aplikace.</span><span class="sxs-lookup"><span data-stu-id="467a8-182">Search for errors associated with the failing app.</span></span> <span data-ttu-id="467a8-183">Chyby mají hodnotu *modulu IIS AspNetCore* nebo *služby IIS Express AspNetCore modulu* v *zdroj* sloupce.</span><span class="sxs-lookup"><span data-stu-id="467a8-183">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="467a8-184">Spuštění aplikace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="467a8-184">Run the app at a command prompt</span></span>

<span data-ttu-id="467a8-185">Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="467a8-185">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="467a8-186">Příčin některých chyb můžete najít spuštěním aplikace v příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="467a8-186">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="467a8-187">Nasazení závisí na architektuře</span><span class="sxs-lookup"><span data-stu-id="467a8-187">Framework-dependent deployment</span></span>

<span data-ttu-id="467a8-188">Pokud je aplikace [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="467a8-188">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="467a8-189">Na příkazovém řádku přejděte do složky pro nasazení a spuštění aplikace spuštěním sestavení aplikace s *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="467a8-189">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="467a8-190">V následujícím příkazu nahraďte název sestavení aplikace pro \<název_sestavení >: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="467a8-190">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="467a8-191">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="467a8-191">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="467a8-192">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="467a8-192">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="467a8-193">Pomocí výchozího hostitele a post, vytvořit žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="467a8-193">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="467a8-194">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="467a8-194">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="467a8-195">Samostatná nasazení</span><span class="sxs-lookup"><span data-stu-id="467a8-195">Self-contained deployment</span></span>

<span data-ttu-id="467a8-196">Pokud je aplikace [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="467a8-196">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="467a8-197">Na příkazovém řádku přejděte do složky pro nasazení a spuštění spustitelného souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="467a8-197">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="467a8-198">V následujícím příkazu nahraďte název sestavení aplikace pro \<název_sestavení >: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="467a8-198">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="467a8-199">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="467a8-199">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="467a8-200">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="467a8-200">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="467a8-201">Pomocí výchozího hostitele a post, vytvořit žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="467a8-201">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="467a8-202">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="467a8-202">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="467a8-203">ASP.NET Core modulu stdout protokolu</span><span class="sxs-lookup"><span data-stu-id="467a8-203">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="467a8-204">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="467a8-204">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="467a8-205">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="467a8-205">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="467a8-206">Pokud *protokoly* složka není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="467a8-206">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="467a8-207">Pokyny o tom, jak povolit MSBuild k vytvoření *protokoly* složky v nasazení automaticky, zobrazí [adresářovou strukturu](xref:host-and-deploy/directory-structure) tématu.</span><span class="sxs-lookup"><span data-stu-id="467a8-207">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="467a8-208">Upravit *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="467a8-208">Edit the *web.config* file.</span></span> <span data-ttu-id="467a8-209">Nastavte **stdoutLogEnabled** k `true` a změnit **stdoutLogFile** tak, aby odkazoval na cestu *protokoly* složky (například `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="467a8-209">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="467a8-210">`stdout` v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="467a8-210">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="467a8-211">Časové razítko, id procesu a příponu souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="467a8-211">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="467a8-212">Pomocí `stdout` jako předpona názvu souboru, má název souboru typické protokolu *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="467a8-212">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span> 
1. <span data-ttu-id="467a8-213">Ověřte identitu fondu aplikací má oprávnění k zápisu *protokoly* složky.</span><span class="sxs-lookup"><span data-stu-id="467a8-213">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="467a8-214">Uložte aktualizovaný *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="467a8-214">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="467a8-215">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="467a8-215">Make a request to the app.</span></span>
1. <span data-ttu-id="467a8-216">Přejděte *protokoly* složky.</span><span class="sxs-lookup"><span data-stu-id="467a8-216">Navigate to the *logs* folder.</span></span> <span data-ttu-id="467a8-217">Vyhledání a otevření protokolu nejnovější stdout.</span><span class="sxs-lookup"><span data-stu-id="467a8-217">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="467a8-218">Studie v protokolu chyb.</span><span class="sxs-lookup"><span data-stu-id="467a8-218">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="467a8-219">Zakážete protokolování stdout po dokončení odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="467a8-219">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="467a8-220">Upravit *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="467a8-220">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="467a8-221">Nastavte **stdoutLogEnabled** k `false`.</span><span class="sxs-lookup"><span data-stu-id="467a8-221">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="467a8-222">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="467a8-222">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="467a8-223">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="467a8-223">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="467a8-224">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="467a8-224">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="467a8-225">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="467a8-225">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="467a8-226">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="467a8-226">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="467a8-227">Povolit na stránce výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="467a8-227">Enable the Developer Exception Page</span></span>

<span data-ttu-id="467a8-228">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do souboru web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) ke spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="467a8-228">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="467a8-229">Tak dlouho, dokud není prostředí přepsána ve spuštění aplikace podle `UseEnvironment` na tvůrce hostitele nastavení proměnné prostředí umožňuje [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) se zobrazí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="467a8-229">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
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

<span data-ttu-id="467a8-230">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití v přípravy a testování serverů, které nejsou vystaveny v Internetu.</span><span class="sxs-lookup"><span data-stu-id="467a8-230">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="467a8-231">Odebrat z proměnné prostředí *web.config* soubor po vyřešení potíží.</span><span class="sxs-lookup"><span data-stu-id="467a8-231">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="467a8-232">Informace o nastavení proměnných prostředí *web.config*, naleznete v tématu [environmentVariables podřízený prvek aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="467a8-232">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="467a8-233">Běžné chyby spuštění</span><span class="sxs-lookup"><span data-stu-id="467a8-233">Common startup errors</span></span>

<span data-ttu-id="467a8-234">Viz <xref:host-and-deploy/azure-iis-errors-reference>.</span><span class="sxs-lookup"><span data-stu-id="467a8-234">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="467a8-235">Většina běžných problémů, které brání spuštění aplikace jsou zahrnuté v referenčním tématu.</span><span class="sxs-lookup"><span data-stu-id="467a8-235">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="467a8-236">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="467a8-236">Obtain data from an app</span></span>

<span data-ttu-id="467a8-237">Pokud aplikace je schopná reagovat na požadavky, získáte žádost o připojení a další data z aplikace pomocí terminálu vložené middlewaru.</span><span class="sxs-lookup"><span data-stu-id="467a8-237">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="467a8-238">Další informace a ukázky kódu najdete v tématu <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="467a8-238">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="slow-or-hanging-app"></a><span data-ttu-id="467a8-239">Pomalá nebo Změ aplikace</span><span class="sxs-lookup"><span data-stu-id="467a8-239">Slow or hanging app</span></span>

<span data-ttu-id="467a8-240">Když aplikace reaguje pomalu nebo přestane reagovat na vyžádání, získání a analyzovat [soubor s výpisem paměti](/visualstudio/debugger/using-dump-files).</span><span class="sxs-lookup"><span data-stu-id="467a8-240">When an app responds slowly or hangs on a request, obtain and analyze a [dump file](/visualstudio/debugger/using-dump-files).</span></span> <span data-ttu-id="467a8-241">Soubory s výpisem paměti se dají získat pomocí kteréhokoli z následujících nástrojů:</span><span class="sxs-lookup"><span data-stu-id="467a8-241">Dump files can be obtained using any of the following tools:</span></span>

* [<span data-ttu-id="467a8-242">ProcDump</span><span class="sxs-lookup"><span data-stu-id="467a8-242">ProcDump</span></span>](/sysinternals/downloads/procdump)
* [<span data-ttu-id="467a8-243">DebugDiag</span><span class="sxs-lookup"><span data-stu-id="467a8-243">DebugDiag</span></span>](https://www.microsoft.com/download/details.aspx?id=49924)
* <span data-ttu-id="467a8-244">WinDbg: [stáhnout Debugging tools for Windows](https://developer.microsoft.com/windows/hardware/download-windbg), [ladění pomocí WinDbg](/windows-hardware/drivers/debugger/debugging-using-windbg)</span><span class="sxs-lookup"><span data-stu-id="467a8-244">WinDbg: [Download Debugging tools for Windows](https://developer.microsoft.com/windows/hardware/download-windbg), [Debugging Using WinDbg](/windows-hardware/drivers/debugger/debugging-using-windbg)</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="467a8-245">Vzdálené ladění</span><span class="sxs-lookup"><span data-stu-id="467a8-245">Remote debugging</span></span>

<span data-ttu-id="467a8-246">Zobrazit [vzdálené ladění ASP.NET Core ve vzdáleném počítači služby IIS v sadě Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) v dokumentaci k sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="467a8-246">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="467a8-247">Application Insights</span><span class="sxs-lookup"><span data-stu-id="467a8-247">Application Insights</span></span>

<span data-ttu-id="467a8-248">[Application Insights](/azure/application-insights/) poskytuje telemetrická data z aplikací hostovaných službou IIS, včetně protokolování a generování sestav funkce chyb.</span><span class="sxs-lookup"><span data-stu-id="467a8-248">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="467a8-249">Application Insights může jenom nahlásit to o chybách, ke kterým dochází po spuštění aplikace, když funkce protokolování aplikace budou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="467a8-249">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="467a8-250">Další informace najdete v tématu [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="467a8-250">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="467a8-251">Další Rady</span><span class="sxs-lookup"><span data-stu-id="467a8-251">Additional advice</span></span>

<span data-ttu-id="467a8-252">Někdy funkční aplikace selže okamžitě po provedení upgradu buď .NET Core SDK na vývojové počítače nebo balíček verze v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="467a8-252">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="467a8-253">V některých případech osamocené balíčky mohou narušit funkce aplikace při provádění hlavní upgrady.</span><span class="sxs-lookup"><span data-stu-id="467a8-253">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="467a8-254">Většina těchto problémů můžete opravit podle těchto pokynů:</span><span class="sxs-lookup"><span data-stu-id="467a8-254">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="467a8-255">Odstranit *bin* a *obj* složek.</span><span class="sxs-lookup"><span data-stu-id="467a8-255">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="467a8-256">Vymazat balíček ukládá do mezipaměti na *% UserProfile %\\.nuget\\balíčky* a *% LocalAppData %\\Nuget\\v3-cache*.</span><span class="sxs-lookup"><span data-stu-id="467a8-256">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="467a8-257">Obnovit a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="467a8-257">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="467a8-258">Potvrďte, že předchozího nasazení na serveru byl zcela odstraněn před opětovným nasazením aplikace.</span><span class="sxs-lookup"><span data-stu-id="467a8-258">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="467a8-259">Pohodlný způsob, jak Vymazat mezipaměti balíčku je ke spuštění `dotnet nuget locals all --clear` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="467a8-259">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="467a8-260">Vymazání mezipaměti balíčku provést taky pomocí [nuget.exe](https://www.nuget.org/downloads) nástroj a provádění příkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="467a8-260">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="467a8-261">*nuget.exe* není připojené instalace s operačním systémem klasické pracovní plochy Windows a je potřeba pořídit samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="467a8-261">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="467a8-262">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="467a8-262">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
