---
title: Řešení potíží s ASP.NET Core ve službě IIS
author: guardrex
description: Zjistěte, jak diagnostikovat problémy s nasazením aplikací ASP.NET Core Internetové informační služby (IIS).
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: e4c93459f2030c7c0a55ea90e0cc8c8d30b76c51
ms.sourcegitcommit: a04eb20e81243930ec829a9db5dd5de49f669450
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2019
ms.locfileid: "66470457"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="a37d8-103">Řešení potíží s ASP.NET Core ve službě IIS</span><span class="sxs-lookup"><span data-stu-id="a37d8-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="a37d8-104">Podle [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a37d8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a37d8-105">Tento článek obsahuje pokyny o tom, jak Diagnostika ASP.NET Core problém při spuštění aplikace při hostování za nástrojem s [Internetové informační služby (IIS)](/iis).</span><span class="sxs-lookup"><span data-stu-id="a37d8-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="a37d8-106">Informace v tomto článku se vztahují k hostování ve službě IIS na serveru systému Windows a Windows Desktop.</span><span class="sxs-lookup"><span data-stu-id="a37d8-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="a37d8-107">V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="a37d8-107">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="a37d8-108">A *502.5 – selhání procesu* nebo *500.30 - Start selhání* , která nastane, pokud ladění místně může být troubleshooted pomocí doporučení v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a37d8-108">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a37d8-109">V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="a37d8-109">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="a37d8-110">A *502.5 selhání procesu* , která nastane, pokud ladění místně může být troubleshooted pomocí doporučení v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a37d8-110">A *502.5 Process Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

<span data-ttu-id="a37d8-111">Další témata pro řešení potíží:</span><span class="sxs-lookup"><span data-stu-id="a37d8-111">Additional troubleshooting topics:</span></span>

<span data-ttu-id="a37d8-112"><xref:host-and-deploy/azure-apps/troubleshoot> I když služba App Service používá [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) a služby IIS pro hostování aplikací, v tématu vyhrazené pro pokyny, které jsou specifické pro App Service.</span><span class="sxs-lookup"><span data-stu-id="a37d8-112"><xref:host-and-deploy/azure-apps/troubleshoot> Although App Service uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and IIS to host apps, see the dedicated topic for instructions specific to App Service.</span></span>

<span data-ttu-id="a37d8-113"><xref:fundamentals/error-handling> Objevte, jak zpracovávat chyby v aplikacích ASP.NET Core během vývoje v místním systému.</span><span class="sxs-lookup"><span data-stu-id="a37d8-113"><xref:fundamentals/error-handling> Discover how to handle errors in ASP.NET Core apps during development on a local system.</span></span>

<span data-ttu-id="a37d8-114">[Zjistěte, jak ladit pomocí sady Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) Toto téma popisuje funkce ladicího programu sady Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a37d8-114">[Learn to debug using Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) This topic introduces the features of the Visual Studio debugger.</span></span>

<span data-ttu-id="a37d8-115">[Ladění pomocí Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) přečtěte si víc o podporu ladění, které jsou součástí Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="a37d8-115">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) Learn about the debugging support built into Visual Studio Code.</span></span>

## <a name="app-startup-errors"></a><span data-ttu-id="a37d8-116">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="a37d8-116">App startup errors</span></span>

### <a name="5025-process-failure"></a><span data-ttu-id="a37d8-117">502.5 zpracovat selhání</span><span class="sxs-lookup"><span data-stu-id="a37d8-117">502.5 Process Failure</span></span>

<span data-ttu-id="a37d8-118">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="a37d8-118">The worker process fails.</span></span> <span data-ttu-id="a37d8-119">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a37d8-119">The app doesn't start.</span></span>

<span data-ttu-id="a37d8-120">Modul ASP.NET Core se pokusí spustit proces dotnet back-endu, ale že ji nebude možné spustit.</span><span class="sxs-lookup"><span data-stu-id="a37d8-120">The ASP.NET Core Module attempts to start the backend dotnet process but it fails to start.</span></span> <span data-ttu-id="a37d8-121">Příčinu selhání spuštění procesu lze určit obvykle položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).</span><span class="sxs-lookup"><span data-stu-id="a37d8-121">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

<span data-ttu-id="a37d8-122">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="a37d8-122">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="a37d8-123">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="a37d8-123">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="a37d8-124">*Sdílené architektuře* je sada sestavení ( *.dll* soubory), které jsou na počítači nainstalovaný a odkazuje Microsoft.aspnetcore.all například `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="a37d8-124">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="a37d8-125">Odkaz na Microsoft.aspnetcore.all můžete určit minimální požadovaná verze.</span><span class="sxs-lookup"><span data-stu-id="a37d8-125">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="a37d8-126">Další informace najdete v tématu [sdílené architektuře](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="a37d8-126">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="a37d8-127">*502.5 selhání procesu* při hostování nebo aplikace chybná konfigurace způsobí, že se pracovní proces selže, vrátí se chybová stránka:</span><span class="sxs-lookup"><span data-stu-id="a37d8-127">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

![Okno prohlížeče zobrazující stránku 502.5 selhání procesu](troubleshoot/_static/process-failure-page.png)

::: moniker range="= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="a37d8-129">500.30 v procesu selhání spuštění</span><span class="sxs-lookup"><span data-stu-id="a37d8-129">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="a37d8-130">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="a37d8-130">The worker process fails.</span></span> <span data-ttu-id="a37d8-131">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a37d8-131">The app doesn't start.</span></span>

<span data-ttu-id="a37d8-132">Modul ASP.NET Core se pokusí spustit .NET Core CLR v procesu, ale že ji nebude možné spustit.</span><span class="sxs-lookup"><span data-stu-id="a37d8-132">The ASP.NET Core Module attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="a37d8-133">Příčinu selhání spuštění procesu lze určit obvykle položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).</span><span class="sxs-lookup"><span data-stu-id="a37d8-133">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

<span data-ttu-id="a37d8-134">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="a37d8-134">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="a37d8-135">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="a37d8-135">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="a37d8-136">500.0 v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="a37d8-136">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="a37d8-137">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="a37d8-137">The worker process fails.</span></span> <span data-ttu-id="a37d8-138">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a37d8-138">The app doesn't start.</span></span>

<span data-ttu-id="a37d8-139">Modul ASP.NET Core se nepodařilo najít .NET Core CLR a najít obslužnou rutinu požadavků v procesu (*aspnetcorev2_inprocess.dll*).</span><span class="sxs-lookup"><span data-stu-id="a37d8-139">The ASP.NET Core Module fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="a37d8-140">Zkontrolujte, jestli:</span><span class="sxs-lookup"><span data-stu-id="a37d8-140">Check that:</span></span>

* <span data-ttu-id="a37d8-141">Aplikace cílí na buď [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) balíček NuGet nebo [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="a37d8-141">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="a37d8-142">Verze rozhraní framework ASP.NET Core sdílené cíle, které aplikace nainstalované v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="a37d8-142">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="a37d8-143">500.0 Chyba načtení out-Of-Process obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="a37d8-143">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="a37d8-144">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="a37d8-144">The worker process fails.</span></span> <span data-ttu-id="a37d8-145">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a37d8-145">The app doesn't start.</span></span>

<span data-ttu-id="a37d8-146">Modul ASP.NET Core nenajde žádné obslužné rutiny hostování požadavku na více instancí procesu.</span><span class="sxs-lookup"><span data-stu-id="a37d8-146">The ASP.NET Core Module fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="a37d8-147">Ujistěte se, *aspnetcorev2_outofprocess.dll* je k dispozici v podsložce vedle *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="a37d8-147">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="a37d8-148">500.31 nepovedlo se najít nativní závislosti ANCM</span><span class="sxs-lookup"><span data-stu-id="a37d8-148">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="a37d8-149">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="a37d8-149">The worker process fails.</span></span> <span data-ttu-id="a37d8-150">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a37d8-150">The app doesn't start.</span></span>

<span data-ttu-id="a37d8-151">Modul ASP.NET Core se pokusí spustit .NET Core runtime v procesu, ale že ji nebude možné spustit.</span><span class="sxs-lookup"><span data-stu-id="a37d8-151">The ASP.NET Core Module attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="a37d8-152">Nejčastější příčinou této chyby při spuštění je, když `Microsoft.NETCore.App` nebo `Microsoft.AspNetCore.App` není nainstalován modul CLR.</span><span class="sxs-lookup"><span data-stu-id="a37d8-152">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="a37d8-153">Pokud nasazení aplikace do cíle ASP.NET Core 3.0 a tuto verzi neexistuje na počítači, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="a37d8-153">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="a37d8-154">Následuje příklad chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="a37d8-154">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="a37d8-155">Chybová zpráva obsahuje seznam všech nainstalovaných verzí rozhraní .NET Core a verze požadované aplikace.</span><span class="sxs-lookup"><span data-stu-id="a37d8-155">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="a37d8-156">Chcete-li tuto chybu opravit, buď:</span><span class="sxs-lookup"><span data-stu-id="a37d8-156">To fix this error, either:</span></span>

* <span data-ttu-id="a37d8-157">Nainstalujte odpovídající verzi .NET Core na počítači.</span><span class="sxs-lookup"><span data-stu-id="a37d8-157">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="a37d8-158">Změna aplikace pro cílení na verzi .NET Core, který je k dispozici na počítači.</span><span class="sxs-lookup"><span data-stu-id="a37d8-158">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="a37d8-159">Publikování aplikací jako [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="a37d8-159">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="a37d8-160">Při spuštění ve vývoji ( `ASPNETCORE_ENVIRONMENT` proměnná prostředí je nastavená na `Development`), konkrétní chyba zapsána do odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="a37d8-160">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="a37d8-161">Příčinu selhání spuštění procesu je také součástí [protokolu událostí aplikace](#application-event-log).</span><span class="sxs-lookup"><span data-stu-id="a37d8-161">The cause of a process startup failure is also found in the [Application Event Log](#application-event-log).</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="a37d8-162">500.32 ANCM nepovedlo se načíst knihovnu dll</span><span class="sxs-lookup"><span data-stu-id="a37d8-162">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="a37d8-163">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="a37d8-163">The worker process fails.</span></span> <span data-ttu-id="a37d8-164">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a37d8-164">The app doesn't start.</span></span>

<span data-ttu-id="a37d8-165">Nejčastější příčinou této chyby je, že aplikace je publikována pro architekturu nekompatibilní procesor.</span><span class="sxs-lookup"><span data-stu-id="a37d8-165">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="a37d8-166">Pokud pracovní proces běží jako 32bitová aplikace a aplikace byla publikována na cíl 64-bit, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="a37d8-166">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="a37d8-167">Chcete-li tuto chybu opravit, buď:</span><span class="sxs-lookup"><span data-stu-id="a37d8-167">To fix this error, either:</span></span>

* <span data-ttu-id="a37d8-168">Znovu publikujte aplikaci pro stejnou architekturu procesorů jako pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="a37d8-168">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="a37d8-169">Publikování aplikací jako [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-executables-fde).</span><span class="sxs-lookup"><span data-stu-id="a37d8-169">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="a37d8-170">500.33 Chyba načtení obslužná rutina požadavku ANCM</span><span class="sxs-lookup"><span data-stu-id="a37d8-170">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="a37d8-171">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="a37d8-171">The worker process fails.</span></span> <span data-ttu-id="a37d8-172">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a37d8-172">The app doesn't start.</span></span>

<span data-ttu-id="a37d8-173">Aplikace nebyla odkazovat `Microsoft.AspNetCore.App` rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="a37d8-173">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="a37d8-174">Jenom aplikace, jejichž cílem `Microsoft.AspNetCore.App` framework mohou být hostovány systémem modul ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a37d8-174">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the ASP.NET Core Module.</span></span>

<span data-ttu-id="a37d8-175">Chcete-li tuto chybu opravit, zkontrolujte, že aplikace je cílen na verzi `Microsoft.AspNetCore.App` rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="a37d8-175">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="a37d8-176">Zkontrolujte `.runtimeconfig.json` ověření rozhraní framework aplikace cílí.</span><span class="sxs-lookup"><span data-stu-id="a37d8-176">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="a37d8-177">500.34 ANCM smíšené modelech hostování není podporován</span><span class="sxs-lookup"><span data-stu-id="a37d8-177">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="a37d8-178">Pracovní proces nelze spustit aplikaci v rámci procesu a aplikace na více instancí procesu ve stejném procesu.</span><span class="sxs-lookup"><span data-stu-id="a37d8-178">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="a37d8-179">Chcete-li vyřešit tuto chybu, spouštění aplikací v samostatné fondy aplikací IIS.</span><span class="sxs-lookup"><span data-stu-id="a37d8-179">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="a37d8-180">500.35 ANCM několik aplikací v rámci procesu ve stejném procesu</span><span class="sxs-lookup"><span data-stu-id="a37d8-180">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="a37d8-181">Pracovní proces nelze spustit aplikaci v rámci procesu a aplikace na více instancí procesu ve stejném procesu.</span><span class="sxs-lookup"><span data-stu-id="a37d8-181">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="a37d8-182">Chcete-li vyřešit tuto chybu, spouštění aplikací v samostatné fondy aplikací IIS.</span><span class="sxs-lookup"><span data-stu-id="a37d8-182">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="a37d8-183">500.36 Chyba načtení ANCM obslužné rutiny na více instancí procesu</span><span class="sxs-lookup"><span data-stu-id="a37d8-183">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="a37d8-184">Obslužné rutiny požadavku na více instancí procesu *aspnetcorev2_outofprocess.dll*, vedle položky není *aspnetcorev2.dll* souboru.</span><span class="sxs-lookup"><span data-stu-id="a37d8-184">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="a37d8-185">To znamená, že modul ASP.NET Core poškozená instalace.</span><span class="sxs-lookup"><span data-stu-id="a37d8-185">This indicates a corrupted installation of the ASP.NET Core Module.</span></span>

<span data-ttu-id="a37d8-186">Chcete-li vyřešit tuto chybu, opravte instalaci [sady hostování rozhraní .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (pro IIS) nebo Visual Studio (pro službu IIS Express).</span><span class="sxs-lookup"><span data-stu-id="a37d8-186">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="a37d8-187">500.37 nepovedlo se spustit v rámci časový Limit spuštění ANCM</span><span class="sxs-lookup"><span data-stu-id="a37d8-187">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="a37d8-188">Spuštění v rámci časový limit spuštění poskytují ANCM se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="a37d8-188">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="a37d8-189">Výchozí časový limit je 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="a37d8-189">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="a37d8-190">Této chybě může dojít při spuštění velkého počtu aplikací ve stejném počítači.</span><span class="sxs-lookup"><span data-stu-id="a37d8-190">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="a37d8-191">Zkontrolujte provozní špičky využití procesoru nebo paměti na serveru při spuštění.</span><span class="sxs-lookup"><span data-stu-id="a37d8-191">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="a37d8-192">Budete muset rozvrhnout procesu spuštění více aplikací.</span><span class="sxs-lookup"><span data-stu-id="a37d8-192">You may need to stagger the startup process of multiple apps.</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="a37d8-193">500.30 v procesu selhání spuštění</span><span class="sxs-lookup"><span data-stu-id="a37d8-193">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="a37d8-194">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="a37d8-194">The worker process fails.</span></span> <span data-ttu-id="a37d8-195">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a37d8-195">The app doesn't start.</span></span>

<span data-ttu-id="a37d8-196">Modul ASP.NET Core se pokusí spustit .NET Core runtime v procesu, ale že ji nebude možné spustit.</span><span class="sxs-lookup"><span data-stu-id="a37d8-196">The ASP.NET Core Module attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="a37d8-197">Příčinu selhání spuštění procesu se obvykle určí na základě položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).</span><span class="sxs-lookup"><span data-stu-id="a37d8-197">The cause of a process startup failure is usually determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="a37d8-198">500.0 v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="a37d8-198">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="a37d8-199">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="a37d8-199">The worker process fails.</span></span> <span data-ttu-id="a37d8-200">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="a37d8-200">The app doesn't start.</span></span>

<span data-ttu-id="a37d8-201">Načítání modul ASP.NET Core komponenty došlo k neznámé chybě.</span><span class="sxs-lookup"><span data-stu-id="a37d8-201">An unknown error occurred loading ASP.NET Core Module components.</span></span> <span data-ttu-id="a37d8-202">Proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="a37d8-202">Take one of the following actions:</span></span>

* <span data-ttu-id="a37d8-203">Kontakt [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (vyberte **vývojářské nástroje** pak **ASP.NET Core**).</span><span class="sxs-lookup"><span data-stu-id="a37d8-203">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="a37d8-204">Položte dotaz na Stack Overflow.</span><span class="sxs-lookup"><span data-stu-id="a37d8-204">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="a37d8-205">Založte problém na našem [úložiště GitHub](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="a37d8-205">File an issue on our [GitHub repository](https://github.com/aspnet/AspNetCore).</span></span>

::: moniker-end

### <a name="500-internal-server-error"></a><span data-ttu-id="a37d8-206">Chyba 500 interní Server</span><span class="sxs-lookup"><span data-stu-id="a37d8-206">500 Internal Server Error</span></span>

<span data-ttu-id="a37d8-207">Spuštění aplikace, ale chybu brání splnění žádosti. na serveru.</span><span class="sxs-lookup"><span data-stu-id="a37d8-207">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="a37d8-208">Při spuštění nebo při vytváření odpovědi, k této chybě dochází v kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a37d8-208">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="a37d8-209">Odpověď může obsahovat žádný obsah nebo se může zobrazit odpovědi *500 – Interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="a37d8-209">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="a37d8-210">V protokolu událostí aplikace obvykle hlásí, že aplikace se normálně spustit.</span><span class="sxs-lookup"><span data-stu-id="a37d8-210">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="a37d8-211">Z pohledu serveru, který je správný.</span><span class="sxs-lookup"><span data-stu-id="a37d8-211">From the server's perspective, that's correct.</span></span> <span data-ttu-id="a37d8-212">Aplikace začal, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="a37d8-212">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="a37d8-213">[Spuštění aplikace příkazového řádku](#run-the-app-at-a-command-prompt) na serveru nebo [povolit protokol stdout modul ASP.NET Core](#aspnet-core-module-stdout-log) k vyřešení tohoto problému.</span><span class="sxs-lookup"><span data-stu-id="a37d8-213">[Run the app at a command prompt](#run-the-app-at-a-command-prompt) on the server or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="a37d8-214">Nepovedlo se spustit aplikaci (kód chyby "0x800700c1")</span><span class="sxs-lookup"><span data-stu-id="a37d8-214">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="a37d8-215">Aplikaci se nepodařilo spustit, protože sestavení aplikace ( *.dll*) nelze načíst.</span><span class="sxs-lookup"><span data-stu-id="a37d8-215">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="a37d8-216">Tato chyba nastane, pokud došlo k neshodě bitové verze mezi publikované aplikace a proces w3wp/iisexpress.</span><span class="sxs-lookup"><span data-stu-id="a37d8-216">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="a37d8-217">Ověřte správnost nastavení 32-bit fondu aplikací:</span><span class="sxs-lookup"><span data-stu-id="a37d8-217">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="a37d8-218">Vyberte fond aplikací ve Správci služby IIS na **fondy aplikací**.</span><span class="sxs-lookup"><span data-stu-id="a37d8-218">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="a37d8-219">Vyberte **Upřesnit nastavení** pod **upravit fond aplikací** v **akce** panelu.</span><span class="sxs-lookup"><span data-stu-id="a37d8-219">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="a37d8-220">Nastavte **povolit 32bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="a37d8-220">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="a37d8-221">Pokud nasazení (x86) 32bitové aplikace, nastavte hodnotu na `True`.</span><span class="sxs-lookup"><span data-stu-id="a37d8-221">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="a37d8-222">Pokud nasazení (x64) 64bitové aplikace, nastavte hodnotu na `False`.</span><span class="sxs-lookup"><span data-stu-id="a37d8-222">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="a37d8-223">Obnovení připojení</span><span class="sxs-lookup"><span data-stu-id="a37d8-223">Connection reset</span></span>

<span data-ttu-id="a37d8-224">Pokud dojde k chybě po odeslání hlavičky, bude příliš pozdě pro server k odeslání **500 – Interní chyba serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="a37d8-224">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="a37d8-225">Často se to stane, když dojde k chybě při serializaci složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="a37d8-225">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="a37d8-226">Tento typ chyby se zobrazí jako *obnovení připojení* chyba na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="a37d8-226">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="a37d8-227">[Protokolování aplikací](xref:fundamentals/logging/index) mohou pomoci při řešení těchto typů chyb.</span><span class="sxs-lookup"><span data-stu-id="a37d8-227">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

## <a name="default-startup-limits"></a><span data-ttu-id="a37d8-228">Výchozí omezení při spuštění</span><span class="sxs-lookup"><span data-stu-id="a37d8-228">Default startup limits</span></span>

<span data-ttu-id="a37d8-229">Modul ASP.NET Core je nakonfigurovaná s výchozí *startupTimeLimit* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="a37d8-229">The ASP.NET Core Module is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="a37d8-230">Když necháte na výchozí hodnotu, aplikace může trvat až dvě minuty, spusťte před modulu protokoly selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="a37d8-230">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="a37d8-231">Informace o konfiguraci modulu najdete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="a37d8-231">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="a37d8-232">Řešení chyb při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="a37d8-232">Troubleshoot app startup errors</span></span>

### <a name="enable-the-aspnet-core-module-debug-log"></a><span data-ttu-id="a37d8-233">Povolit protokol ladění modul ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a37d8-233">Enable the ASP.NET Core Module debug log</span></span>

<span data-ttu-id="a37d8-234">Přidáním následujícího nastavení obslužné rutiny na aplikaci *web.config* souboru povolení protokolů ladění modul ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a37d8-234">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug logs:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="a37d8-235">Potvrďte, že cesta zadaná pro protokol existuje a že identita fondu aplikací má oprávnění k zápisu do umístění.</span><span class="sxs-lookup"><span data-stu-id="a37d8-235">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

### <a name="application-event-log"></a><span data-ttu-id="a37d8-236">Protokol událostí aplikace</span><span class="sxs-lookup"><span data-stu-id="a37d8-236">Application Event Log</span></span>

<span data-ttu-id="a37d8-237">Přístup k protokolu událostí aplikace:</span><span class="sxs-lookup"><span data-stu-id="a37d8-237">Access the Application Event Log:</span></span>

1. <span data-ttu-id="a37d8-238">Otevření nabídky Start, vyhledejte **Prohlížeč událostí**a pak vyberte **Prohlížeč událostí** aplikace.</span><span class="sxs-lookup"><span data-stu-id="a37d8-238">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="a37d8-239">V **Prohlížeč událostí**, otevřete **protokoly Windows** uzlu.</span><span class="sxs-lookup"><span data-stu-id="a37d8-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="a37d8-240">Vyberte **aplikace** otevřít protokol událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a37d8-240">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="a37d8-241">Vyhledejte chyby související s selhání aplikace.</span><span class="sxs-lookup"><span data-stu-id="a37d8-241">Search for errors associated with the failing app.</span></span> <span data-ttu-id="a37d8-242">Chyby mají hodnotu *modulu IIS AspNetCore* nebo *služby IIS Express AspNetCore modulu* v *zdroj* sloupce.</span><span class="sxs-lookup"><span data-stu-id="a37d8-242">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="a37d8-243">Spuštění aplikace příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="a37d8-243">Run the app at a command prompt</span></span>

<span data-ttu-id="a37d8-244">Mnoho chyb při spuštění nevytvářejí užitečné informace v protokolu událostí aplikace.</span><span class="sxs-lookup"><span data-stu-id="a37d8-244">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="a37d8-245">Příčin některých chyb můžete najít spuštěním aplikace v příkazovém řádku v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a37d8-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="a37d8-246">Nasazení závisí na architektuře</span><span class="sxs-lookup"><span data-stu-id="a37d8-246">Framework-dependent deployment</span></span>

<span data-ttu-id="a37d8-247">Pokud je aplikace [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="a37d8-247">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="a37d8-248">Na příkazovém řádku přejděte do složky pro nasazení a spuštění aplikace spuštěním sestavení aplikace s *dotnet.exe*.</span><span class="sxs-lookup"><span data-stu-id="a37d8-248">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="a37d8-249">V následujícím příkazu nahraďte název sestavení aplikace pro \<název_sestavení >: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="a37d8-249">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="a37d8-250">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="a37d8-250">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="a37d8-251">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="a37d8-251">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="a37d8-252">Pomocí výchozího hostitele a post, vytvořit žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="a37d8-252">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="a37d8-253">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a37d8-253">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="a37d8-254">Samostatná nasazení</span><span class="sxs-lookup"><span data-stu-id="a37d8-254">Self-contained deployment</span></span>

<span data-ttu-id="a37d8-255">Pokud je aplikace [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd):</span><span class="sxs-lookup"><span data-stu-id="a37d8-255">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="a37d8-256">Na příkazovém řádku přejděte do složky pro nasazení a spuštění spustitelného souboru aplikace.</span><span class="sxs-lookup"><span data-stu-id="a37d8-256">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="a37d8-257">V následujícím příkazu nahraďte název sestavení aplikace pro \<název_sestavení >: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="a37d8-257">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="a37d8-258">Výstup z aplikace zobrazuje všechny chyby konzoly je zapsán do okna konzoly.</span><span class="sxs-lookup"><span data-stu-id="a37d8-258">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="a37d8-259">Je-li této chybě dojde při požadavku na aplikaci, vytvořte žádost na hostitele a port, kde Kestrel naslouchá.</span><span class="sxs-lookup"><span data-stu-id="a37d8-259">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="a37d8-260">Pomocí výchozího hostitele a post, vytvořit žádost o `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="a37d8-260">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="a37d8-261">Aplikace reaguje, obvykle na adrese Kestrel koncový bod, tím je pravděpodobnější týkající se konfigurace hostování a méně pravděpodobné, že v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a37d8-261">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="a37d8-262">ASP.NET Core modulu stdout protokolu</span><span class="sxs-lookup"><span data-stu-id="a37d8-262">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="a37d8-263">Povolení a zobrazení protokolů stdout:</span><span class="sxs-lookup"><span data-stu-id="a37d8-263">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="a37d8-264">Přejděte do složky pro nasazení webu v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="a37d8-264">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="a37d8-265">Pokud *protokoly* složka není k dispozici, vytvořte složku.</span><span class="sxs-lookup"><span data-stu-id="a37d8-265">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="a37d8-266">Pokyny o tom, jak povolit MSBuild k vytvoření *protokoly* složky v nasazení automaticky, zobrazí [adresářovou strukturu](xref:host-and-deploy/directory-structure) tématu.</span><span class="sxs-lookup"><span data-stu-id="a37d8-266">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="a37d8-267">Upravit *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="a37d8-267">Edit the *web.config* file.</span></span> <span data-ttu-id="a37d8-268">Nastavte **stdoutLogEnabled** k `true` a změnit **stdoutLogFile** tak, aby odkazoval na cestu *protokoly* složky (například `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="a37d8-268">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="a37d8-269">`stdout` v cestě je předpona názvu souboru protokolu.</span><span class="sxs-lookup"><span data-stu-id="a37d8-269">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="a37d8-270">Časové razítko, id procesu a příponu souboru jsou přidány automaticky při vytvoření protokolu.</span><span class="sxs-lookup"><span data-stu-id="a37d8-270">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="a37d8-271">Pomocí `stdout` jako předpona názvu souboru, má název souboru typické protokolu *stdout_20180205184032_5412.log*.</span><span class="sxs-lookup"><span data-stu-id="a37d8-271">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="a37d8-272">Ověřte identitu fondu aplikací má oprávnění k zápisu *protokoly* složky.</span><span class="sxs-lookup"><span data-stu-id="a37d8-272">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="a37d8-273">Uložte aktualizovaný *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="a37d8-273">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="a37d8-274">Vytvořte žádost do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a37d8-274">Make a request to the app.</span></span>
1. <span data-ttu-id="a37d8-275">Přejděte *protokoly* složky.</span><span class="sxs-lookup"><span data-stu-id="a37d8-275">Navigate to the *logs* folder.</span></span> <span data-ttu-id="a37d8-276">Vyhledání a otevření protokolu nejnovější stdout.</span><span class="sxs-lookup"><span data-stu-id="a37d8-276">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="a37d8-277">Studie v protokolu chyb.</span><span class="sxs-lookup"><span data-stu-id="a37d8-277">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a37d8-278">Zakážete protokolování stdout po dokončení odstraňování potíží.</span><span class="sxs-lookup"><span data-stu-id="a37d8-278">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="a37d8-279">Upravit *web.config* souboru.</span><span class="sxs-lookup"><span data-stu-id="a37d8-279">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="a37d8-280">Nastavte **stdoutLogEnabled** k `false`.</span><span class="sxs-lookup"><span data-stu-id="a37d8-280">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="a37d8-281">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="a37d8-281">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="a37d8-282">Nepodařilo se zakázat protokol stdout může vést k selhání aplikace nebo serveru.</span><span class="sxs-lookup"><span data-stu-id="a37d8-282">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="a37d8-283">Neexistuje žádné omezení velikosti souboru protokolu nebo počet souborů protokolů, které jsou vytvořeny.</span><span class="sxs-lookup"><span data-stu-id="a37d8-283">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="a37d8-284">Pro rutiny protokolování v aplikaci ASP.NET Core, použijte protokolování knihovnu, která omezuje velikost souboru protokolu a otočí protokoly.</span><span class="sxs-lookup"><span data-stu-id="a37d8-284">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="a37d8-285">Další informace najdete v tématu [zprostředkovatele přihlášení třetí strany](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="a37d8-285">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="a37d8-286">Povolit na stránce výjimek pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="a37d8-286">Enable the Developer Exception Page</span></span>

<span data-ttu-id="a37d8-287">`ASPNETCORE_ENVIRONMENT` [Proměnnou prostředí lze přidat do souboru web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) ke spuštění aplikace ve vývojovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a37d8-287">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="a37d8-288">Tak dlouho, dokud není prostředí přepsána ve spuštění aplikace podle `UseEnvironment` na tvůrce hostitele nastavení proměnné prostředí umožňuje [stránku výjimek pro vývojáře](xref:fundamentals/error-handling) se zobrazí při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="a37d8-288">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

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

<span data-ttu-id="a37d8-289">Nastavení proměnné prostředí pro `ASPNETCORE_ENVIRONMENT` se doporučuje jenom pro použití v přípravy a testování serverů, které nejsou vystaveny v Internetu.</span><span class="sxs-lookup"><span data-stu-id="a37d8-289">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="a37d8-290">Odebrat z proměnné prostředí *web.config* soubor po vyřešení potíží.</span><span class="sxs-lookup"><span data-stu-id="a37d8-290">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="a37d8-291">Informace o nastavení proměnných prostředí *web.config*, naleznete v tématu [environmentVariables podřízený prvek aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="a37d8-291">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="a37d8-292">Běžné chyby spuštění</span><span class="sxs-lookup"><span data-stu-id="a37d8-292">Common startup errors</span></span>

<span data-ttu-id="a37d8-293">Viz <xref:host-and-deploy/azure-iis-errors-reference>.</span><span class="sxs-lookup"><span data-stu-id="a37d8-293">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="a37d8-294">Většina běžných problémů, které brání spuštění aplikace jsou zahrnuté v referenčním tématu.</span><span class="sxs-lookup"><span data-stu-id="a37d8-294">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="a37d8-295">Získání dat z aplikace</span><span class="sxs-lookup"><span data-stu-id="a37d8-295">Obtain data from an app</span></span>

<span data-ttu-id="a37d8-296">Pokud aplikace je schopná reagovat na požadavky, získáte žádost o připojení a další data z aplikace pomocí terminálu vložené middlewaru.</span><span class="sxs-lookup"><span data-stu-id="a37d8-296">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="a37d8-297">Další informace a ukázky kódu najdete v tématu <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="a37d8-297">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="create-a-dump"></a><span data-ttu-id="a37d8-298">Vytvoření výpisu</span><span class="sxs-lookup"><span data-stu-id="a37d8-298">Create a dump</span></span>

<span data-ttu-id="a37d8-299">A *s výpisem paměti* snímek paměti v systému a vám může pomoct zjistit příčinu selhání aplikace, spuštění selhání nebo pomalé aplikace.</span><span class="sxs-lookup"><span data-stu-id="a37d8-299">A *dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="a37d8-300">Aplikace dojde k chybě nebo dojde k výjimce</span><span class="sxs-lookup"><span data-stu-id="a37d8-300">App crashes or encounters an exception</span></span>

<span data-ttu-id="a37d8-301">Získání a analýza výpisu paměti z [hlášení chyb Windows (zasílání)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="a37d8-301">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="a37d8-302">Vytvořte složku pro uložení souborů se stavem systému na `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="a37d8-302">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="a37d8-303">Fond aplikací musí mít oprávnění k zápisu do složky.</span><span class="sxs-lookup"><span data-stu-id="a37d8-303">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="a37d8-304">Spustit [EnableDumps Powershellový skript](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="a37d8-304">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="a37d8-305">Pokud aplikace využívá [model hostingu v procesu](xref:fundamentals/servers/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="a37d8-305">If the app uses the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="a37d8-306">Pokud aplikace využívá [model hostingu mimo proces](xref:fundamentals/servers/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="a37d8-306">If the app uses the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="a37d8-307">Spusťte aplikaci v rámci podmínky, které způsobily selhání dojde k.</span><span class="sxs-lookup"><span data-stu-id="a37d8-307">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="a37d8-308">Po došlo k selhání, spusťte [DisableDumps Powershellový skript](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span><span class="sxs-lookup"><span data-stu-id="a37d8-308">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="a37d8-309">Pokud aplikace využívá [model hostingu v procesu](xref:fundamentals/servers/index#in-process-hosting-model), spusťte skript pro *w3wp.exe*:</span><span class="sxs-lookup"><span data-stu-id="a37d8-309">If the app uses the [in-process hosting model](xref:fundamentals/servers/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="a37d8-310">Pokud aplikace využívá [model hostingu mimo proces](xref:fundamentals/servers/index#out-of-process-hosting-model), spusťte skript pro *dotnet.exe*:</span><span class="sxs-lookup"><span data-stu-id="a37d8-310">If the app uses the [out-of-process hosting model](xref:fundamentals/servers/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="a37d8-311">Po dokončení shromažďování výpisu stavu systému dojde k chybě aplikace a aplikace je povoleno jej měla ukončit normálně.</span><span class="sxs-lookup"><span data-stu-id="a37d8-311">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="a37d8-312">Skript Powershellu nakonfiguruje zasílání shromažďovat až o pěti výpisů paměti na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="a37d8-312">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="a37d8-313">Výpisy stavu systému může trvat až velké množství místa na disku (až několik gigabajtů každý).</span><span class="sxs-lookup"><span data-stu-id="a37d8-313">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="a37d8-314">Aplikace přestane reagovat, při spuštění selže nebo běží normálně</span><span class="sxs-lookup"><span data-stu-id="a37d8-314">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="a37d8-315">Když aplikaci *přestane reagovat* (přestane reagovat, ale nedojde k chybě), selže při spuštění nebo spuštění za normálních okolností viz [soubory výpisu paměti uživatelského režimu: Výběr vždycky ten nejlepší nástroj](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) vybrat vhodného nástroje k vytvoření výpisu paměti.</span><span class="sxs-lookup"><span data-stu-id="a37d8-315">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

### <a name="analyze-the-dump"></a><span data-ttu-id="a37d8-316">Analýza výpisu paměti</span><span class="sxs-lookup"><span data-stu-id="a37d8-316">Analyze the dump</span></span>

<span data-ttu-id="a37d8-317">Výpis paměti mohou být analyzovány pomocí několik přístupů.</span><span class="sxs-lookup"><span data-stu-id="a37d8-317">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="a37d8-318">Další informace najdete v tématu [analýzy souboru výpisu paměti uživatelského režimu](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="a37d8-318">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="a37d8-319">Vzdálené ladění</span><span class="sxs-lookup"><span data-stu-id="a37d8-319">Remote debugging</span></span>

<span data-ttu-id="a37d8-320">Zobrazit [vzdálené ladění ASP.NET Core ve vzdáleném počítači služby IIS v sadě Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) v dokumentaci k sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="a37d8-320">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="a37d8-321">Application Insights</span><span class="sxs-lookup"><span data-stu-id="a37d8-321">Application Insights</span></span>

<span data-ttu-id="a37d8-322">[Application Insights](/azure/application-insights/) poskytuje telemetrická data z aplikací hostovaných službou IIS, včetně protokolování a generování sestav funkce chyb.</span><span class="sxs-lookup"><span data-stu-id="a37d8-322">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="a37d8-323">Application Insights může jenom nahlásit to o chybách, ke kterým dochází po spuštění aplikace, když funkce protokolování aplikace budou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="a37d8-323">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="a37d8-324">Další informace najdete v tématu [Application Insights pro ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="a37d8-324">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="a37d8-325">Další Rady</span><span class="sxs-lookup"><span data-stu-id="a37d8-325">Additional advice</span></span>

<span data-ttu-id="a37d8-326">Někdy funkční aplikace selže okamžitě po provedení upgradu buď .NET Core SDK na vývojové počítače nebo balíček verze v rámci aplikace.</span><span class="sxs-lookup"><span data-stu-id="a37d8-326">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="a37d8-327">V některých případech osamocené balíčky mohou narušit funkce aplikace při provádění hlavní upgrady.</span><span class="sxs-lookup"><span data-stu-id="a37d8-327">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="a37d8-328">Většina těchto problémů můžete opravit podle těchto pokynů:</span><span class="sxs-lookup"><span data-stu-id="a37d8-328">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="a37d8-329">Odstranit *bin* a *obj* složek.</span><span class="sxs-lookup"><span data-stu-id="a37d8-329">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="a37d8-330">Vymazat balíček ukládá do mezipaměti na *% UserProfile %\\.nuget\\balíčky* a *% LocalAppData %\\Nuget\\v3-cache*.</span><span class="sxs-lookup"><span data-stu-id="a37d8-330">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="a37d8-331">Obnovit a znovu sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="a37d8-331">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="a37d8-332">Potvrďte, že předchozího nasazení na serveru byl zcela odstraněn před opětovným nasazením aplikace.</span><span class="sxs-lookup"><span data-stu-id="a37d8-332">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="a37d8-333">Pohodlný způsob, jak Vymazat mezipaměti balíčku je ke spuštění `dotnet nuget locals all --clear` z příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="a37d8-333">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="a37d8-334">Vymazání mezipaměti balíčku provést taky pomocí [nuget.exe](https://www.nuget.org/downloads) nástroj a provádění příkazu `nuget locals all -clear`.</span><span class="sxs-lookup"><span data-stu-id="a37d8-334">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="a37d8-335">*nuget.exe* není připojené instalace s operačním systémem klasické pracovní plochy Windows a je potřeba pořídit samostatně z [webu NuGet](https://www.nuget.org/downloads).</span><span class="sxs-lookup"><span data-stu-id="a37d8-335">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a37d8-336">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="a37d8-336">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
