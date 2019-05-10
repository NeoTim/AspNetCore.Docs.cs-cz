---
title: Řešení potíží s ASP.NET Core ve službě IIS
author: guardrex
description: Zjistěte, jak diagnostikovat problémy s nasazením aplikací ASP.NET Core Internetové informační služby (IIS).
ms.author: riande
ms.custom: mvc
ms.date: 03/14/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: f89eac3ae6fc704bc8bf38a9707fc3c6c3568e91
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2019
ms.locfileid: "64899409"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="1ba9f-103">Řešení potíží s ASP.NET Core ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="1ba9f-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="1ba9f-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1ba9f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1ba9f-105">Tento článek obsahuje pokyny o tom, jak Diagnostika ASP.NET Core problém při spuštění aplikace při hostování za nástrojem s [Internetové informační služby (IIS)](/iis).</span><span class="sxs-lookup"><span data-stu-id="1ba9f-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="1ba9f-106">Informace v tomto článku se vztahují k hostování ve službě IIS na serveru systému Windows a Windows Desktop.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="1ba9f-107">V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-107">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="1ba9f-108">A *502.5 – selhání procesu* nebo *500.30 - Start selhání* , která nastane, pokud ladění místně může být troubleshooted pomocí doporučení v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-108">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="1ba9f-109">V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-109">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="1ba9f-110">A *502.5 selhání procesu* , která nastane, pokud ladění místně může být troubleshooted pomocí doporučení v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-110">A *502.5 Process Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

<span data-ttu-id="1ba9f-111">Další témata pro řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="1ba9f-111">Additional troubleshooting topics:</span></span>

<span data-ttu-id="1ba9f-112"><xref:host-and-deploy/azure-apps/troubleshoot> I když služba App Service používá [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a služby IIS pro hostování aplikací, v tématu vyhrazené pro pokyny, které jsou specifické pro App Service.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-112"><xref:host-and-deploy/azure-apps/troubleshoot> Although App Service uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and IIS to host apps, see the dedicated topic for instructions specific to App Service.</span></span>

<span data-ttu-id="1ba9f-113"><xref:fundamentals/error-handling> Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core během vývoje v místním systému.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-113"><xref:fundamentals/error-handling> Discover how to handle errors in ASP.NET Core apps during development on a local system.</span></span>

<span data-ttu-id="1ba9f-114">[Zjistěte, jak ladit pomocí sady Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) Toto téma popisuje funkce ladicího programu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-114">[Learn to debug using Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) This topic introduces the features of the Visual Studio debugger.</span></span>

<span data-ttu-id="1ba9f-115">[Ladění pomocí Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) přečtěte si víc o podporu ladění, které jsou součástí Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-115">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) Learn about the debugging support built into Visual Studio Code.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="1ba9f-116">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="1ba9f-116">App startup errors</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="1ba9f-117">502.5 zpracovat selhání</span><span class="sxs-lookup"><span data-stu-id="1ba9f-117">502.5 Process Failure</span></span>

<span data-ttu-id="1ba9f-118">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-118">The worker process fails.</span></span> <span data-ttu-id="1ba9f-119">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-119">The app doesn't start.</span></span>

<span data-ttu-id="1ba9f-120">Modul ASP.NET Core se pokusí spustit proces dotnet back-endu, ale že ji nebude možné spustit.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-120">The ASP.NET Core Module attempts to start the backend dotnet process but it fails to start.</span></span> <span data-ttu-id="1ba9f-121">Příčinu selhání spuštění procesu lze určit obvykle položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).</span><span class="sxs-lookup"><span data-stu-id="1ba9f-121">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

<span data-ttu-id="1ba9f-122">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-122">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="1ba9f-123">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-123">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="1ba9f-124">*Sdílené architektuře* je sada sestavení (*.dll* soubory), které jsou na počítači nainstalovaný a odkazuje Microsoft.aspnetcore.all například `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-124">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="1ba9f-125">Odkaz na Microsoft.aspnetcore.all můžete určit minimální požadovaná verze.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-125">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="1ba9f-126">Další informace najdete v tématu [sdílené architektuře](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="1ba9f-126">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="1ba9f-127">*502.5 selhání procesu* při hostování nebo aplikace chybná konfigurace způsobí, že se pracovní proces selže, vrátí se chybová stránka:</span><span class="sxs-lookup"><span data-stu-id="1ba9f-127">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

![Okno prohlížeče zobrazující stránku 502.5 selhání procesu](troubleshoot/_static/process-failure-page.png)

::: moniker range=">= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="1ba9f-129">500.30 v procesu selhání spuštění</span><span class="sxs-lookup"><span data-stu-id="1ba9f-129">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="1ba9f-130">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-130">The worker process fails.</span></span> <span data-ttu-id="1ba9f-131">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-131">The app doesn't start.</span></span>

<span data-ttu-id="1ba9f-132">Modul ASP.NET Core se pokusí spustit .NET Core CLR v procesu, ale že ji nebude možné spustit.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-132">The ASP.NET Core Module attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="1ba9f-133">Příčinu selhání spuštění procesu lze určit obvykle položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).</span><span class="sxs-lookup"><span data-stu-id="1ba9f-133">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

<span data-ttu-id="1ba9f-134">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-134">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="1ba9f-135">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-135">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="1ba9f-136">500.0 v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="1ba9f-136">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="1ba9f-137">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-137">The worker process fails.</span></span> <span data-ttu-id="1ba9f-138">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-138">The app doesn't start.</span></span>

<span data-ttu-id="1ba9f-139">Modul ASP.NET Core se nepodařilo najít .NET Core CLR a najít obslužnou rutinu požadavků v procesu (*aspnetcorev2_inprocess.dll*).</span><span class="sxs-lookup"><span data-stu-id="1ba9f-139">The ASP.NET Core Module fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="1ba9f-140">Zkontrolujte, jestli:</span><span class="sxs-lookup"><span data-stu-id="1ba9f-140">Check that:</span></span>

* <span data-ttu-id="1ba9f-141">Aplikace cílí na buď [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) balíček NuGet nebo [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="1ba9f-141">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="1ba9f-142">Verze rozhraní framework ASP.NET Core sdílené cíle, které aplikace nainstalované v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-142">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="1ba9f-143">500.0 Chyba načtení out-Of-Process obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="1ba9f-143">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="1ba9f-144">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-144">The worker process fails.</span></span> <span data-ttu-id="1ba9f-145">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-145">The app doesn't start.</span></span>

<span data-ttu-id="1ba9f-146">Modul ASP.NET Core nenajde žádné obslužné rutiny hostování požadavku na více instancí procesu.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-146">The ASP.NET Core Module fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="1ba9f-147">Ujistěte se, *aspnetcorev2_outofprocess.dll* je k dispozici v podsložce vedle *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-147">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

### <a name="500-internal-server-error"></a><span data-ttu-id="1ba9f-148">Chyba 500 interní Server</span><span class="sxs-lookup"><span data-stu-id="1ba9f-148">500 Internal Server Error</span></span>

<span data-ttu-id="1ba9f-149">Spuštění aplikace, ale chybu brání splnění žádosti. na serveru.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-149">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="1ba9f-150">Při spuštění nebo při vytváření odpovědi, k této chybě dochází v kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-150">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="1ba9f-151">Odpověď může obsahovat žádný obsah nebo se může zobrazit odpovědi *500 – Interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-151">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="1ba9f-152">V protokolu událostí aplikace obvykle hlásí, že aplikace se normálně spustit.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-152">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="1ba9f-153">Z pohledu serveru, který je správný.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-153">From the server's perspective, that's correct.</span></span> <span data-ttu-id="1ba9f-154">Aplikace začal, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-154">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="1ba9f-155">[Spuštění aplikace příkazového řádku](#run-the-app-at-a-command-prompt) na serveru nebo [povolit protokol stdout modul ASP.NET Core](#aspnet-core-module-stdout-log) k vyřešení tohoto problému.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-155">[Run the app at a command prompt](#run-the-app-at-a-command-prompt) on the server or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="1ba9f-156">Nepovedlo se spustit aplikaci (kód chyby "0x800700c1")</span><span class="sxs-lookup"><span data-stu-id="1ba9f-156">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="1ba9f-157">Aplikaci se nepodařilo spustit, protože sestavení aplikace (*.dll*) nelze načíst.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-157">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="1ba9f-158">Tato chyba nastane, pokud došlo k neshodě bitové verze mezi publikované aplikace a proces w3wp/iisexpress.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-158">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="1ba9f-159">Ověřte správnost nastavení 32-bit fondu aplikací:</span><span class="sxs-lookup"><span data-stu-id="1ba9f-159">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="1ba9f-160">Vyberte fond aplikací ve Správci služby IIS na **fondy aplikací**.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-160">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="1ba9f-161">Vyberte **Upřesnit nastavení** pod **upravit fond aplikací** v **akce** panelu.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-161">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="1ba9f-162">Nastavte **povolit 32bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="1ba9f-162">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="1ba9f-163">Pokud nasazení (x86) 32bitové aplikace, nastavte hodnotu na `True`.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-163">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="1ba9f-164">Pokud nasazení (x64) 64bitové aplikace, nastavte hodnotu na `False`.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-164">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="1ba9f-165">Obnovení připojení</span><span class="sxs-lookup"><span data-stu-id="1ba9f-165">Connection reset</span></span>

<span data-ttu-id="1ba9f-166">Pokud dojde k chybě po odeslání hlavičky, bude příliš pozdě pro server k odeslání **500 – Interní chyba serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-166">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="1ba9f-167">Často se to stane, když dojde k chybě při serializaci složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-167">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="1ba9f-168">Tento typ chyby se zobrazí jako *obnovení připojení* chyba na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-168">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="1ba9f-169">[Protokolování aplikací](xref:fundamentals/logging/index) mohou pomoci při řešení těchto typů chyb.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-169">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

## <a name="default-startup-limits"></a><span data-ttu-id="1ba9f-170">Výchozí omezení při spuštění</span><span class="sxs-lookup"><span data-stu-id="1ba9f-170">Default startup limits</span></span>

<span data-ttu-id="1ba9f-171">Modul ASP.NET Core je nakonfigurovaná s výchozí *startupTimeLimit* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-171">The ASP.NET Core Module is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="1ba9f-172">Když necháte na výchozí hodnotu, aplikace může trvat až dvě minuty, spusťte před modulu protokoly selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-172">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="1ba9f-173">Informace o konfiguraci modulu najdete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="1ba9f-173">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="1ba9f-174">Řešení chyb při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="1ba9f-174">Troubleshoot app startup errors</span></span>

### <a name="enable-the-aspnet-core-module-debug-log"></a><span data-ttu-id="1ba9f-175">Povolit protokol ladění modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1ba9f-175">Enable the ASP.NET Core Module debug log</span></span>

<span data-ttu-id="1ba9f-176">Přidáním následujícího nastavení obslužné rutiny na aplikaci *web.config* souboru povolení protokolů ladění modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="1ba9f-176">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug logs:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="1ba9f-177">Potvrďte, že cesta zadaná pro protokol existuje a že identita fondu aplikací má oprávnění k zápisu do umístění.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-177">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

### <a name="application-event-log"></a><span data-ttu-id="1ba9f-178">Protokol událostí aplikace</span><span class="sxs-lookup"><span data-stu-id="1ba9f-178">Application Event Log</span></span>

<span data-ttu-id="1ba9f-179">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="1ba9f-179">Access the Application Event Log:</span></span>

1. <span data-ttu-id="1ba9f-180">Otevření nabídky Start, vyhledejte **Prohlížeč událostí**a pak vyberte **Prohlížeč událostí** aplikace.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-180">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="1ba9f-181">V **Prohlížeč událostí**, otevřete **protokoly Windows** uzlu.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-181">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="1ba9f-182">Vyberte **aplikace** otevřít protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-182">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="1ba9f-183">Vyhledejte chyby související s selhání aplikace.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-183">Search for errors associated with the failing app.</span></span> <span data-ttu-id="1ba9f-184">Chyby mají hodnotu *modulu IIS AspNetCore* nebo *služby IIS Express AspNetCore modulu* v *zdroj* sloupce.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-184">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="1ba9f-185">Spuštění aplikace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="1ba9f-185">Run the app at a command prompt</span></span>

<span data-ttu-id="1ba9f-186">Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-186">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="1ba9f-187">Příčin některých chyb můžete najít spuštěním aplikace v příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-187">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="1ba9f-188">Nasazení závisí na architektuře</span><span class="sxs-lookup"><span data-stu-id="1ba9f-188">Framework-dependent deployment</span></span>

<span data-ttu-id="1ba9f-189">Pokud je aplikace [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="1ba9f-189">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="1ba9f-190">Na příkazovém řádku přejděte do složky pro nasazení a spuštění aplikace spuštěním sestavení aplikace s *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-190">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="1ba9f-191">V následujícím příkazu nahraďte název sestavení aplikace pro \<název_sestavení >: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-191">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="1ba9f-192">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-192">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="1ba9f-193">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-193">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="1ba9f-194">Pomocí výchozího hostitele a post, vytvořit žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-194">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="1ba9f-195">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-195">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="1ba9f-196">Samostatná nasazení</span><span class="sxs-lookup"><span data-stu-id="1ba9f-196">Self-contained deployment</span></span>

<span data-ttu-id="1ba9f-197">Pokud je aplikace [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="1ba9f-197">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="1ba9f-198">Na příkazovém řádku přejděte do složky pro nasazení a spuštění spustitelného souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-198">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="1ba9f-199">V následujícím příkazu nahraďte název sestavení aplikace pro \<název_sestavení >: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-199">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="1ba9f-200">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-200">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="1ba9f-201">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-201">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="1ba9f-202">Pomocí výchozího hostitele a post, vytvořit žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-202">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="1ba9f-203">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-203">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="1ba9f-204">ASP.NET Core modulu stdout protokolu</span><span class="sxs-lookup"><span data-stu-id="1ba9f-204">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="1ba9f-205">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="1ba9f-205">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="1ba9f-206">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-206">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="1ba9f-207">Pokud *protokoly* složka není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-207">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="1ba9f-208">Pokyny o tom, jak povolit MSBuild k vytvoření *protokoly* složky v nasazení automaticky, zobrazí [adresářovou strukturu](xref:host-and-deploy/directory-structure) tématu.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-208">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="1ba9f-209">Upravit *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-209">Edit the *web.config* file.</span></span> <span data-ttu-id="1ba9f-210">Nastavte **stdoutLogEnabled** k `true` a změnit **stdoutLogFile** tak, aby odkazoval na cestu *protokoly* složky (například `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="1ba9f-210">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="1ba9f-211">`stdout` v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-211">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="1ba9f-212">Časové razítko, id procesu a příponu souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-212">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="1ba9f-213">Pomocí `stdout` jako předpona názvu souboru, má název souboru typické protokolu *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-213">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="1ba9f-214">Ověřte identitu fondu aplikací má oprávnění k zápisu *protokoly* složky.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-214">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="1ba9f-215">Uložte aktualizovaný *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-215">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="1ba9f-216">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-216">Make a request to the app.</span></span>
1. <span data-ttu-id="1ba9f-217">Přejděte *protokoly* složky.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-217">Navigate to the *logs* folder.</span></span> <span data-ttu-id="1ba9f-218">Vyhledání a otevření protokolu nejnovější stdout.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-218">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="1ba9f-219">Studie v protokolu chyb.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-219">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1ba9f-220">Zakážete protokolování stdout po dokončení odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-220">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="1ba9f-221">Upravit *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-221">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="1ba9f-222">Nastavte **stdoutLogEnabled** k `false`.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-222">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="1ba9f-223">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-223">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="1ba9f-224">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-224">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="1ba9f-225">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-225">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="1ba9f-226">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-226">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="1ba9f-227">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="1ba9f-227">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="1ba9f-228">Povolit na stránce výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="1ba9f-228">Enable the Developer Exception Page</span></span>

<span data-ttu-id="1ba9f-229">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do souboru web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) ke spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-229">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="1ba9f-230">Tak dlouho, dokud není prostředí přepsána ve spuštění aplikace podle `UseEnvironment` na tvůrce hostitele nastavení proměnné prostředí umožňuje [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) se zobrazí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-230">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="1ba9f-231">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití v přípravy a testování serverů, které nejsou vystaveny v Internetu.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-231">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="1ba9f-232">Odebrat z proměnné prostředí *web.config* soubor po vyřešení potíží.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-232">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="1ba9f-233">Informace o nastavení proměnných prostředí *web.config*, naleznete v tématu [environmentVariables podřízený prvek aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="1ba9f-233">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="1ba9f-234">Běžné chyby spuštění</span><span class="sxs-lookup"><span data-stu-id="1ba9f-234">Common startup errors</span></span>

<span data-ttu-id="1ba9f-235">Viz <xref:host-and-deploy/azure-iis-errors-reference>.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-235">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="1ba9f-236">Většina běžných problémů, které brání spuštění aplikace jsou zahrnuté v referenčním tématu.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-236">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="1ba9f-237">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="1ba9f-237">Obtain data from an app</span></span>

<span data-ttu-id="1ba9f-238">Pokud aplikace je schopná reagovat na požadavky, získáte žádost o připojení a další data z aplikace pomocí terminálu vložené middlewaru.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-238">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="1ba9f-239">Další informace a ukázky kódu najdete v tématu <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-239">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="create-a-dump"></a><span data-ttu-id="1ba9f-240">Vytvoření výpisu</span><span class="sxs-lookup"><span data-stu-id="1ba9f-240">Create a dump</span></span>

<span data-ttu-id="1ba9f-241">A *s výpisem paměti* snímek paměti v systému a vám může pomoct zjistit příčinu selhání aplikace, spuštění selhání nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-241">A *dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="1ba9f-242">Aplikace dojde k chybě nebo dojde k výjimce</span><span class="sxs-lookup"><span data-stu-id="1ba9f-242">App crashes or encounters an exception</span></span>

<span data-ttu-id="1ba9f-243">Získání a analýza výpisu paměti z [hlášení chyb Windows (zasílání)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="1ba9f-243">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="1ba9f-244">Vytvořte složku pro uložení souborů se stavem systému na `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-244">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="1ba9f-245">Fond aplikací musí mít oprávnění k zápisu do složky.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-245">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="1ba9f-246">Spustit [EnableDumps Powershellový skript](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="1ba9f-246">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="1ba9f-247">Pokud aplikace využívá [model hostingu v procesu](xref:fundamentals/servers/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="1ba9f-247">If the app uses the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="1ba9f-248">Pokud aplikace využívá [model hostingu mimo proces](xref:fundamentals/servers/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="1ba9f-248">If the app uses the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="1ba9f-249">Spusťte aplikaci v rámci podmínky, které způsobily selhání dojde k.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-249">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="1ba9f-250">Po došlo k selhání, spusťte [DisableDumps Powershellový skript](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="1ba9f-250">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="1ba9f-251">Pokud aplikace využívá [model hostingu v procesu](xref:fundamentals/servers/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="1ba9f-251">If the app uses the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="1ba9f-252">Pokud aplikace využívá [model hostingu mimo proces](xref:fundamentals/servers/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="1ba9f-252">If the app uses the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="1ba9f-253">Po dokončení shromažďování výpisu stavu systému dojde k chybě aplikace a aplikace je povoleno jej měla ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-253">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="1ba9f-254">Skript Powershellu nakonfiguruje zasílání shromažďovat až o pěti výpisů paměti na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-254">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="1ba9f-255">Výpisy stavu systému může trvat až velké množství místa na disku (až několik gigabajtů každý).</span><span class="sxs-lookup"><span data-stu-id="1ba9f-255">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="1ba9f-256">Aplikace přestane reagovat, při spuštění selže nebo běží normálně</span><span class="sxs-lookup"><span data-stu-id="1ba9f-256">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="1ba9f-257">Když aplikaci *přestane reagovat* (přestane reagovat, ale nedojde k chybě), selže při spuštění nebo spuštění za normálních okolností viz [soubory výpisu paměti uživatelského režimu: Výběr vždycky ten nejlepší nástroj](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) vybrat vhodného nástroje k vytvoření výpisu paměti.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-257">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

### <a name="analyze-the-dump"></a><span data-ttu-id="1ba9f-258">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="1ba9f-258">Analyze the dump</span></span>

<span data-ttu-id="1ba9f-259">Výpis paměti mohou být analyzovány pomocí několik přístupů.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-259">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="1ba9f-260">Další informace najdete v tématu [analýzy souboru výpisu paměti uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="1ba9f-260">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="1ba9f-261">Vzdálené ladění</span><span class="sxs-lookup"><span data-stu-id="1ba9f-261">Remote debugging</span></span>

<span data-ttu-id="1ba9f-262">Zobrazit [vzdálené ladění ASP.NET Core ve vzdáleném počítači služby IIS v sadě Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) v dokumentaci k sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-262">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="1ba9f-263">Application Insights</span><span class="sxs-lookup"><span data-stu-id="1ba9f-263">Application Insights</span></span>

<span data-ttu-id="1ba9f-264">[Application Insights](/azure/application-insights/) poskytuje telemetrická data z aplikací hostovaných službou IIS, včetně protokolování a generování sestav funkce chyb.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-264">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="1ba9f-265">Application Insights může jenom nahlásit to o chybách, ke kterým dochází po spuštění aplikace, když funkce protokolování aplikace budou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-265">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="1ba9f-266">Další informace najdete v tématu [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="1ba9f-266">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="1ba9f-267">Další Rady</span><span class="sxs-lookup"><span data-stu-id="1ba9f-267">Additional advice</span></span>

<span data-ttu-id="1ba9f-268">Někdy funkční aplikace selže okamžitě po provedení upgradu buď .NET Core SDK na vývojové počítače nebo balíček verze v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-268">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="1ba9f-269">V některých případech osamocené balíčky mohou narušit funkce aplikace při provádění hlavní upgrady.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-269">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="1ba9f-270">Většina těchto problémů můžete opravit podle těchto pokynů:</span><span class="sxs-lookup"><span data-stu-id="1ba9f-270">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="1ba9f-271">Odstranit *bin* a *obj* složek.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-271">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="1ba9f-272">Vymazat balíček ukládá do mezipaměti na *% UserProfile %\\.nuget\\balíčky* a *% LocalAppData %\\Nuget\\v3-cache*.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-272">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="1ba9f-273">Obnovit a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-273">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="1ba9f-274">Potvrďte, že předchozího nasazení na serveru byl zcela odstraněn před opětovným nasazením aplikace.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-274">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="1ba9f-275">Pohodlný způsob, jak Vymazat mezipaměti balíčku je ke spuštění `dotnet nuget locals all --clear` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-275">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="1ba9f-276">Vymazání mezipaměti balíčku provést taky pomocí [nuget.exe](https://www.nuget.org/downloads) nástroj a provádění příkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="1ba9f-276">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="1ba9f-277">*nuget.exe* není připojené instalace s operačním systémem klasické pracovní plochy Windows a je potřeba pořídit samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="1ba9f-277">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1ba9f-278">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="1ba9f-278">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
