## <a name="app-startup-errors"></a><span data-ttu-id="7c087-101">Chyby při spuštění aplikace</span><span class="sxs-lookup"><span data-stu-id="7c087-101">App startup errors</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7c087-102">V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="7c087-102">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="7c087-103">A *502.5 – selhání procesu* nebo *500.30 - Start selhání* , která nastane, pokud ladění místně může být troubleshooted pomocí doporučení v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="7c087-103">A *502.5 - Process Failure* or a *500.30 - Start Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="7c087-104">V sadě Visual Studio projekt ASP.NET Core výchozí hodnota je [služby IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hostování během ladění.</span><span class="sxs-lookup"><span data-stu-id="7c087-104">In Visual Studio, an ASP.NET Core project defaults to [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) hosting during debugging.</span></span> <span data-ttu-id="7c087-105">A *502.5 selhání procesu* , která nastane, pokud ladění místně může být troubleshooted pomocí doporučení v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="7c087-105">A *502.5 Process Failure* that occurs when debugging locally can be troubleshooted using the advice in this topic.</span></span>

::: moniker-end

### <a name="500-internal-server-error"></a><span data-ttu-id="7c087-106">Chyba 500 interní Server</span><span class="sxs-lookup"><span data-stu-id="7c087-106">500 Internal Server Error</span></span>

<span data-ttu-id="7c087-107">Spuštění aplikace, ale chybu brání splnění žádosti. na serveru.</span><span class="sxs-lookup"><span data-stu-id="7c087-107">The app starts, but an error prevents the server from fulfilling the request.</span></span>

<span data-ttu-id="7c087-108">Při spuštění nebo při vytváření odpovědi, k této chybě dochází v kódu aplikace.</span><span class="sxs-lookup"><span data-stu-id="7c087-108">This error occurs within the app's code during startup or while creating a response.</span></span> <span data-ttu-id="7c087-109">Odpověď může obsahovat žádný obsah nebo se může zobrazit odpovědi *500 – Interní chyba serveru* v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="7c087-109">The response may contain no content, or the response may appear as a *500 Internal Server Error* in the browser.</span></span> <span data-ttu-id="7c087-110">V protokolu událostí aplikace obvykle hlásí, že aplikace se normálně spustit.</span><span class="sxs-lookup"><span data-stu-id="7c087-110">The Application Event Log usually states that the app started normally.</span></span> <span data-ttu-id="7c087-111">Z pohledu serveru, který je správný.</span><span class="sxs-lookup"><span data-stu-id="7c087-111">From the server's perspective, that's correct.</span></span> <span data-ttu-id="7c087-112">Aplikace začal, ale nemůže generovat platnou odpověď.</span><span class="sxs-lookup"><span data-stu-id="7c087-112">The app did start, but it can't generate a valid response.</span></span> <span data-ttu-id="7c087-113">Spuštění aplikace příkazového řádku na serveru nebo [povolit protokol stdout modul ASP.NET Core](#aspnet-core-module-stdout-log) k vyřešení tohoto problému.</span><span class="sxs-lookup"><span data-stu-id="7c087-113">Run the app at a command prompt on the server or [enable the ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log) to troubleshoot the problem.</span></span>

::: moniker range="= aspnetcore-2.2"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="7c087-114">500.0 v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="7c087-114">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="7c087-115">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="7c087-115">The worker process fails.</span></span> <span data-ttu-id="7c087-116">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7c087-116">The app doesn't start.</span></span>

<span data-ttu-id="7c087-117">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) nepodaří najít .NET Core CLR a najít obslužnou rutinu požadavků v procesu (*aspnetcorev2_inprocess.dll*).</span><span class="sxs-lookup"><span data-stu-id="7c087-117">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the .NET Core CLR and find the in-process request handler (*aspnetcorev2_inprocess.dll*).</span></span> <span data-ttu-id="7c087-118">Zkontrolujte, jestli:</span><span class="sxs-lookup"><span data-stu-id="7c087-118">Check that:</span></span>

* <span data-ttu-id="7c087-119">Aplikace cílí na buď [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) balíček NuGet nebo [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="7c087-119">The app targets either the [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet package or the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
* <span data-ttu-id="7c087-120">Verze rozhraní framework ASP.NET Core sdílené cíle, které aplikace nainstalované v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="7c087-120">The version of the ASP.NET Core shared framework that the app targets is installed on the target machine.</span></span>

### <a name="5000-out-of-process-handler-load-failure"></a><span data-ttu-id="7c087-121">500.0 Chyba načtení out-Of-Process obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="7c087-121">500.0 Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="7c087-122">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="7c087-122">The worker process fails.</span></span> <span data-ttu-id="7c087-123">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7c087-123">The app doesn't start.</span></span>

<span data-ttu-id="7c087-124">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) nenajde žádné obslužné rutiny hostování požadavku na více instancí procesu.</span><span class="sxs-lookup"><span data-stu-id="7c087-124">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) fails to find the out-of-process hosting request handler.</span></span> <span data-ttu-id="7c087-125">Ujistěte se, *aspnetcorev2_outofprocess.dll* je k dispozici v podsložce vedle *aspnetcorev2.dll*.</span><span class="sxs-lookup"><span data-stu-id="7c087-125">Make sure the *aspnetcorev2_outofprocess.dll* is present in a subfolder next to *aspnetcorev2.dll*.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="5000-in-process-handler-load-failure"></a><span data-ttu-id="7c087-126">500.0 v procesu selhání načtení obslužné rutiny</span><span class="sxs-lookup"><span data-stu-id="7c087-126">500.0 In-Process Handler Load Failure</span></span>

<span data-ttu-id="7c087-127">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="7c087-127">The worker process fails.</span></span> <span data-ttu-id="7c087-128">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7c087-128">The app doesn't start.</span></span>

<span data-ttu-id="7c087-129">Došlo k neznámé chybě při načítání [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) komponenty.</span><span class="sxs-lookup"><span data-stu-id="7c087-129">An unknown error occurred loading [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) components.</span></span> <span data-ttu-id="7c087-130">Proveďte jednu z následujících akcí:</span><span class="sxs-lookup"><span data-stu-id="7c087-130">Take one of the following actions:</span></span>

* <span data-ttu-id="7c087-131">Kontakt [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (vyberte **vývojářské nástroje** pak **ASP.NET Core**).</span><span class="sxs-lookup"><span data-stu-id="7c087-131">Contact [Microsoft Support](https://support.microsoft.com/oas/default.aspx?prid=15832) (select **Developer Tools** then **ASP.NET Core**).</span></span>
* <span data-ttu-id="7c087-132">Položte dotaz na Stack Overflow.</span><span class="sxs-lookup"><span data-stu-id="7c087-132">Ask a question on Stack Overflow.</span></span>
* <span data-ttu-id="7c087-133">Založte problém na našem [úložiště GitHub](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="7c087-133">File an issue on our [GitHub repository](https://github.com/aspnet/AspNetCore).</span></span>

### <a name="50030-in-process-startup-failure"></a><span data-ttu-id="7c087-134">500.30 v procesu selhání spuštění</span><span class="sxs-lookup"><span data-stu-id="7c087-134">500.30 In-Process Startup Failure</span></span>

<span data-ttu-id="7c087-135">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="7c087-135">The worker process fails.</span></span> <span data-ttu-id="7c087-136">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7c087-136">The app doesn't start.</span></span>

<span data-ttu-id="7c087-137">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pokusy o spuštění .NET Core CLR v procesu, ale nepovede spustit.</span><span class="sxs-lookup"><span data-stu-id="7c087-137">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core CLR in-process, but it fails to start.</span></span> <span data-ttu-id="7c087-138">Příčinu selhání spuštění procesu lze určit obvykle položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).</span><span class="sxs-lookup"><span data-stu-id="7c087-138">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

<span data-ttu-id="7c087-139">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="7c087-139">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="7c087-140">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="7c087-140">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span>

### <a name="50031-ancm-failed-to-find-native-dependencies"></a><span data-ttu-id="7c087-141">500.31 nepovedlo se najít nativní závislosti ANCM</span><span class="sxs-lookup"><span data-stu-id="7c087-141">500.31 ANCM Failed to Find Native Dependencies</span></span>

<span data-ttu-id="7c087-142">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="7c087-142">The worker process fails.</span></span> <span data-ttu-id="7c087-143">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7c087-143">The app doesn't start.</span></span>

<span data-ttu-id="7c087-144">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pokusy o spuštění .NET Core runtime v procesu, ale nepovede spustit.</span><span class="sxs-lookup"><span data-stu-id="7c087-144">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the .NET Core runtime in-process, but it fails to start.</span></span> <span data-ttu-id="7c087-145">Nejčastější příčinou této chyby při spuštění je, když `Microsoft.NETCore.App` nebo `Microsoft.AspNetCore.App` není nainstalován modul CLR.</span><span class="sxs-lookup"><span data-stu-id="7c087-145">The most common cause of this startup failure is when the `Microsoft.NETCore.App` or `Microsoft.AspNetCore.App` runtime isn't installed.</span></span> <span data-ttu-id="7c087-146">Pokud nasazení aplikace do cíle ASP.NET Core 3.0 a tuto verzi neexistuje na počítači, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="7c087-146">If the app is deployed to target ASP.NET Core 3.0 and that version doesn't exist on the machine, this error occurs.</span></span> <span data-ttu-id="7c087-147">Následuje příklad chybová zpráva:</span><span class="sxs-lookup"><span data-stu-id="7c087-147">An example error message follows:</span></span>

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

<span data-ttu-id="7c087-148">Chybová zpráva obsahuje seznam všech nainstalovaných verzí rozhraní .NET Core a verze požadované aplikace.</span><span class="sxs-lookup"><span data-stu-id="7c087-148">The error message lists all the installed .NET Core versions and the version requested by the app.</span></span> <span data-ttu-id="7c087-149">Chcete-li tuto chybu opravit, buď:</span><span class="sxs-lookup"><span data-stu-id="7c087-149">To fix this error, either:</span></span>

* <span data-ttu-id="7c087-150">Nainstalujte odpovídající verzi .NET Core na počítači.</span><span class="sxs-lookup"><span data-stu-id="7c087-150">Install the appropriate version of .NET Core on the machine.</span></span>
* <span data-ttu-id="7c087-151">Změna aplikace pro cílení na verzi .NET Core, který je k dispozici na počítači.</span><span class="sxs-lookup"><span data-stu-id="7c087-151">Change the app to target a version of .NET Core that's present on the machine.</span></span>
* <span data-ttu-id="7c087-152">Publikování aplikací jako [samostatná nasazení](/dotnet/core/deploying/#self-contained-deployments-scd).</span><span class="sxs-lookup"><span data-stu-id="7c087-152">Publish the app as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

<span data-ttu-id="7c087-153">Při spuštění ve vývoji ( `ASPNETCORE_ENVIRONMENT` proměnná prostředí je nastavená na `Development`), konkrétní chyba zapsána do odpovědi HTTP.</span><span class="sxs-lookup"><span data-stu-id="7c087-153">When running in development (the `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development`), the specific error is written to the HTTP response.</span></span> <span data-ttu-id="7c087-154">Příčinu selhání spuštění procesu je také součástí [protokolu událostí aplikace](#application-event-log).</span><span class="sxs-lookup"><span data-stu-id="7c087-154">The cause of a process startup failure is also found in the [Application Event Log](#application-event-log).</span></span>

### <a name="50032-ancm-failed-to-load-dll"></a><span data-ttu-id="7c087-155">500.32 ANCM nepovedlo se načíst knihovnu dll</span><span class="sxs-lookup"><span data-stu-id="7c087-155">500.32 ANCM Failed to Load dll</span></span>

<span data-ttu-id="7c087-156">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="7c087-156">The worker process fails.</span></span> <span data-ttu-id="7c087-157">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7c087-157">The app doesn't start.</span></span>

<span data-ttu-id="7c087-158">Nejčastější příčinou této chyby je, že aplikace je publikována pro architekturu nekompatibilní procesor.</span><span class="sxs-lookup"><span data-stu-id="7c087-158">The most common cause for this error is that the app is published for an incompatible processor architecture.</span></span> <span data-ttu-id="7c087-159">Pokud pracovní proces běží jako 32bitová aplikace a aplikace byla publikována na cíl 64-bit, dojde k této chybě.</span><span class="sxs-lookup"><span data-stu-id="7c087-159">If the worker process is running as a 32-bit app and the app was published to target 64-bit, this error occurs.</span></span>

<span data-ttu-id="7c087-160">Chcete-li tuto chybu opravit, buď:</span><span class="sxs-lookup"><span data-stu-id="7c087-160">To fix this error, either:</span></span>

* <span data-ttu-id="7c087-161">Znovu publikujte aplikaci pro stejnou architekturu procesorů jako pracovní proces.</span><span class="sxs-lookup"><span data-stu-id="7c087-161">Republish the app for the same processor architecture as the worker process.</span></span>
* <span data-ttu-id="7c087-162">Publikování aplikací jako [nasazení závisí na architektuře](/dotnet/core/deploying/#framework-dependent-executables-fde).</span><span class="sxs-lookup"><span data-stu-id="7c087-162">Publish the app as a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-executables-fde).</span></span>

### <a name="50033-ancm-request-handler-load-failure"></a><span data-ttu-id="7c087-163">500.33 Chyba načtení obslužná rutina požadavku ANCM</span><span class="sxs-lookup"><span data-stu-id="7c087-163">500.33 ANCM Request Handler Load Failure</span></span>

<span data-ttu-id="7c087-164">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="7c087-164">The worker process fails.</span></span> <span data-ttu-id="7c087-165">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7c087-165">The app doesn't start.</span></span>

<span data-ttu-id="7c087-166">Aplikace nebyla odkazovat `Microsoft.AspNetCore.App` rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="7c087-166">The app didn't reference the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="7c087-167">Jenom aplikace, jejichž cílem `Microsoft.AspNetCore.App` framework mohou být hostovány systémem [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="7c087-167">Only apps targeting the `Microsoft.AspNetCore.App` framework can be hosted by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="7c087-168">Chcete-li tuto chybu opravit, zkontrolujte, že aplikace je cílen na verzi `Microsoft.AspNetCore.App` rozhraní framework.</span><span class="sxs-lookup"><span data-stu-id="7c087-168">To fix this error, confirm that the app is targeting the `Microsoft.AspNetCore.App` framework.</span></span> <span data-ttu-id="7c087-169">Zkontrolujte `.runtimeconfig.json` ověření rozhraní framework aplikace cílí.</span><span class="sxs-lookup"><span data-stu-id="7c087-169">Check the `.runtimeconfig.json` to verify the framework targeted by the app.</span></span>

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a><span data-ttu-id="7c087-170">500.34 ANCM smíšené modelech hostování není podporován</span><span class="sxs-lookup"><span data-stu-id="7c087-170">500.34 ANCM Mixed Hosting Models Not Supported</span></span>

<span data-ttu-id="7c087-171">Pracovní proces nelze spustit aplikaci v rámci procesu a aplikace na více instancí procesu ve stejném procesu.</span><span class="sxs-lookup"><span data-stu-id="7c087-171">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="7c087-172">Chcete-li vyřešit tuto chybu, spouštění aplikací v samostatné fondy aplikací IIS.</span><span class="sxs-lookup"><span data-stu-id="7c087-172">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a><span data-ttu-id="7c087-173">500.35 ANCM několik aplikací v rámci procesu ve stejném procesu</span><span class="sxs-lookup"><span data-stu-id="7c087-173">500.35 ANCM Multiple In-Process Applications in same Process</span></span>

<span data-ttu-id="7c087-174">Pracovní proces nelze spustit aplikaci v rámci procesu a aplikace na více instancí procesu ve stejném procesu.</span><span class="sxs-lookup"><span data-stu-id="7c087-174">The worker process can't run both an in-process app and an out-of-process app in the same process.</span></span>

<span data-ttu-id="7c087-175">Chcete-li vyřešit tuto chybu, spouštění aplikací v samostatné fondy aplikací IIS.</span><span class="sxs-lookup"><span data-stu-id="7c087-175">To fix this error, run apps in separate IIS application pools.</span></span>

### <a name="50036-ancm-out-of-process-handler-load-failure"></a><span data-ttu-id="7c087-176">500.36 Chyba načtení ANCM obslužné rutiny na více instancí procesu</span><span class="sxs-lookup"><span data-stu-id="7c087-176">500.36 ANCM Out-Of-Process Handler Load Failure</span></span>

<span data-ttu-id="7c087-177">Obslužné rutiny požadavku na více instancí procesu *aspnetcorev2_outofprocess.dll*, vedle položky není *aspnetcorev2.dll* souboru.</span><span class="sxs-lookup"><span data-stu-id="7c087-177">The out-of-process request handler, *aspnetcorev2_outofprocess.dll*, isn't next to the *aspnetcorev2.dll* file.</span></span> <span data-ttu-id="7c087-178">To znamená poškozená instalace [modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="7c087-178">This indicates a corrupted installation of the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="7c087-179">Chcete-li vyřešit tuto chybu, opravte instalaci [sady hostování rozhraní .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (pro IIS) nebo Visual Studio (pro službu IIS Express).</span><span class="sxs-lookup"><span data-stu-id="7c087-179">To fix this error, repair the installation of the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (for IIS) or Visual Studio (for IIS Express).</span></span>

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a><span data-ttu-id="7c087-180">500.37 nepovedlo se spustit v rámci časový Limit spuštění ANCM</span><span class="sxs-lookup"><span data-stu-id="7c087-180">500.37 ANCM Failed to Start Within Startup Time Limit</span></span>

<span data-ttu-id="7c087-181">Spuštění v rámci časový limit spuštění poskytují ANCM se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="7c087-181">ANCM failed to start within the provied startup time limit.</span></span> <span data-ttu-id="7c087-182">Výchozí časový limit je 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="7c087-182">By default, the timeout is 120 seconds.</span></span>

<span data-ttu-id="7c087-183">Této chybě může dojít při spuštění velkého počtu aplikací ve stejném počítači.</span><span class="sxs-lookup"><span data-stu-id="7c087-183">This error can occur when starting a large number of apps on the same machine.</span></span> <span data-ttu-id="7c087-184">Zkontrolujte provozní špičky využití procesoru nebo paměti na serveru při spuštění.</span><span class="sxs-lookup"><span data-stu-id="7c087-184">Check for CPU/Memory usage spikes on the server during startup.</span></span> <span data-ttu-id="7c087-185">Budete muset rozvrhnout procesu spuštění více aplikací.</span><span class="sxs-lookup"><span data-stu-id="7c087-185">You may need to stagger the startup process of multiple apps.</span></span>

::: moniker-end

### <a name="5025-process-failure"></a><span data-ttu-id="7c087-186">502.5 zpracovat selhání</span><span class="sxs-lookup"><span data-stu-id="7c087-186">502.5 Process Failure</span></span>

<span data-ttu-id="7c087-187">Pracovní proces se nezdaří.</span><span class="sxs-lookup"><span data-stu-id="7c087-187">The worker process fails.</span></span> <span data-ttu-id="7c087-188">Aplikace se nespustí.</span><span class="sxs-lookup"><span data-stu-id="7c087-188">The app doesn't start.</span></span>

<span data-ttu-id="7c087-189">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) pokusy o spuštění pracovního procesu, ale nepovede spustit.</span><span class="sxs-lookup"><span data-stu-id="7c087-189">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) attempts to start the worker process but it fails to start.</span></span> <span data-ttu-id="7c087-190">Příčinu selhání spuštění procesu lze určit obvykle položky [protokolu událostí aplikace](#application-event-log) a [protokolů stdout modul ASP.NET Core](#aspnet-core-module-stdout-log).</span><span class="sxs-lookup"><span data-stu-id="7c087-190">The cause of a process startup failure can usually be determined from entries in the [Application Event Log](#application-event-log) and the [ASP.NET Core Module stdout log](#aspnet-core-module-stdout-log).</span></span>

<span data-ttu-id="7c087-191">Běžné chyby je, že aplikace je špatně nakonfigurovaný. kvůli cílení na určitou verzi rozhraní framework sdílené ASP.NET Core, který není k dispozici.</span><span class="sxs-lookup"><span data-stu-id="7c087-191">A common failure condition is the app is misconfigured due to targeting a version of the ASP.NET Core shared framework that isn't present.</span></span> <span data-ttu-id="7c087-192">Zkontrolujte, jaké verze rozhraní framework ASP.NET Core sdílené jsou nainstalovány v cílovém počítači.</span><span class="sxs-lookup"><span data-stu-id="7c087-192">Check which versions of the ASP.NET Core shared framework are installed on the target machine.</span></span> <span data-ttu-id="7c087-193">*Sdílené architektuře* je sada sestavení ( *.dll* soubory), které jsou na počítači nainstalovaný a odkazuje Microsoft.aspnetcore.all například `Microsoft.AspNetCore.App`.</span><span class="sxs-lookup"><span data-stu-id="7c087-193">The *shared framework* is the set of assemblies (*.dll* files) that are installed on the machine and referenced by a metapackage such as `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="7c087-194">Odkaz na Microsoft.aspnetcore.all můžete určit minimální požadovaná verze.</span><span class="sxs-lookup"><span data-stu-id="7c087-194">The metapackage reference can specify a minimum required version.</span></span> <span data-ttu-id="7c087-195">Další informace najdete v tématu [sdílené architektuře](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span><span class="sxs-lookup"><span data-stu-id="7c087-195">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="7c087-196">*502.5 selhání procesu* při hostování nebo aplikace chybná konfigurace způsobí, že se pracovní proces selže, vrátí se chybová stránka:</span><span class="sxs-lookup"><span data-stu-id="7c087-196">The *502.5 Process Failure* error page is returned when a hosting or app misconfiguration causes the worker process to fail:</span></span>

### <a name="failed-to-start-application-errorcode-0x800700c1"></a><span data-ttu-id="7c087-197">Nepovedlo se spustit aplikaci (kód chyby "0x800700c1")</span><span class="sxs-lookup"><span data-stu-id="7c087-197">Failed to start application (ErrorCode '0x800700c1')</span></span>

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

<span data-ttu-id="7c087-198">Aplikaci se nepodařilo spustit, protože sestavení aplikace ( *.dll*) nelze načíst.</span><span class="sxs-lookup"><span data-stu-id="7c087-198">The app failed to start because the app's assembly (*.dll*) couldn't be loaded.</span></span>

<span data-ttu-id="7c087-199">Tato chyba nastane, pokud došlo k neshodě bitové verze mezi publikované aplikace a proces w3wp/iisexpress.</span><span class="sxs-lookup"><span data-stu-id="7c087-199">This error occurs when there's a bitness mismatch between the published app and the w3wp/iisexpress process.</span></span>

<span data-ttu-id="7c087-200">Ověřte správnost nastavení 32-bit fondu aplikací:</span><span class="sxs-lookup"><span data-stu-id="7c087-200">Confirm that the app pool's 32-bit setting is correct:</span></span>

1. <span data-ttu-id="7c087-201">Vyberte fond aplikací ve Správci služby IIS na **fondy aplikací**.</span><span class="sxs-lookup"><span data-stu-id="7c087-201">Select the app pool in IIS Manager's **Application Pools**.</span></span>
1. <span data-ttu-id="7c087-202">Vyberte **Upřesnit nastavení** pod **upravit fond aplikací** v **akce** panelu.</span><span class="sxs-lookup"><span data-stu-id="7c087-202">Select **Advanced Settings** under **Edit Application Pool** in the **Actions** panel.</span></span>
1. <span data-ttu-id="7c087-203">Nastavte **povolit 32bitové aplikace**:</span><span class="sxs-lookup"><span data-stu-id="7c087-203">Set **Enable 32-Bit Applications**:</span></span>
   * <span data-ttu-id="7c087-204">Pokud nasazení (x86) 32bitové aplikace, nastavte hodnotu na `True`.</span><span class="sxs-lookup"><span data-stu-id="7c087-204">If deploying a 32-bit (x86) app, set the value to `True`.</span></span>
   * <span data-ttu-id="7c087-205">Pokud nasazení (x64) 64bitové aplikace, nastavte hodnotu na `False`.</span><span class="sxs-lookup"><span data-stu-id="7c087-205">If deploying a 64-bit (x64) app, set the value to `False`.</span></span>

### <a name="connection-reset"></a><span data-ttu-id="7c087-206">Obnovení připojení</span><span class="sxs-lookup"><span data-stu-id="7c087-206">Connection reset</span></span>

<span data-ttu-id="7c087-207">Pokud dojde k chybě po odeslání hlavičky, bude příliš pozdě pro server k odeslání **500 – Interní chyba serveru** , když dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="7c087-207">If an error occurs after the headers are sent, it's too late for the server to send a **500 Internal Server Error** when an error occurs.</span></span> <span data-ttu-id="7c087-208">Často se to stane, když dojde k chybě při serializaci složitých objektů pro odpověď.</span><span class="sxs-lookup"><span data-stu-id="7c087-208">This often happens when an error occurs during the serialization of complex objects for a response.</span></span> <span data-ttu-id="7c087-209">Tento typ chyby se zobrazí jako *obnovení připojení* chyba na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="7c087-209">This type of error appears as a *connection reset* error on the client.</span></span> <span data-ttu-id="7c087-210">[Protokolování aplikací](xref:fundamentals/logging/index) mohou pomoci při řešení těchto typů chyb.</span><span class="sxs-lookup"><span data-stu-id="7c087-210">[Application logging](xref:fundamentals/logging/index) can help troubleshoot these types of errors.</span></span>

## <a name="default-startup-limits"></a><span data-ttu-id="7c087-211">Výchozí omezení při spuštění</span><span class="sxs-lookup"><span data-stu-id="7c087-211">Default startup limits</span></span>

<span data-ttu-id="7c087-212">[Modul ASP.NET Core](xref:host-and-deploy/aspnet-core-module) má nakonfigurovanou výchozí *startupTimeLimit* 120 sekund.</span><span class="sxs-lookup"><span data-stu-id="7c087-212">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is configured with a default *startupTimeLimit* of 120 seconds.</span></span> <span data-ttu-id="7c087-213">Když necháte na výchozí hodnotu, aplikace může trvat až dvě minuty, spusťte před modulu protokoly selhání procesu.</span><span class="sxs-lookup"><span data-stu-id="7c087-213">When left at the default value, an app may take up to two minutes to start before the module logs a process failure.</span></span> <span data-ttu-id="7c087-214">Informace o konfiguraci modulu najdete v tématu [atributy elementu aspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="7c087-214">For information on configuring the module, see [Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>
