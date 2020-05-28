---
<span data-ttu-id="6fd82-101">title: protokolování a diagnostika ve ASP.NET Core SignalR Autor: Description: ' Naučte se shromažďovat diagnostiku z vaší ASP.NET Core SignalR aplikace. '</span><span class="sxs-lookup"><span data-stu-id="6fd82-101">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="6fd82-102">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fd82-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fd82-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fd82-103">'Blazor'</span></span>
- <span data-ttu-id="6fd82-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fd82-104">'Identity'</span></span>
- <span data-ttu-id="6fd82-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fd82-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fd82-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fd82-106">'Razor'</span></span>
- <span data-ttu-id="6fd82-107">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="6fd82-107">'SignalR' uid:</span></span> 

---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="6fd82-108">Protokolování a diagnostika v ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="6fd82-108">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="6fd82-109">Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="6fd82-109">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="6fd82-110">Tento článek poskytuje pokyny pro shromažďování diagnostických informací z vaší SignalR aplikace ASP.NET Core, které vám pomůžou při řešení problémů.</span><span class="sxs-lookup"><span data-stu-id="6fd82-110">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="6fd82-111">Protokolování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="6fd82-111">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="6fd82-112">Protokoly na straně serveru můžou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="6fd82-112">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="6fd82-113">**Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="6fd82-113">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="6fd82-114">Vzhledem k SignalR tomu, že je součást ASP.NET Core, používá systém protokolování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6fd82-114">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="6fd82-115">Ve výchozí konfiguraci SignalR protokoluje velmi málo informací, ale je možné ho nakonfigurovat.</span><span class="sxs-lookup"><span data-stu-id="6fd82-115">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="6fd82-116">Podrobnosti o konfiguraci ASP.NET Core protokolování najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index#configuration) .</span><span class="sxs-lookup"><span data-stu-id="6fd82-116">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

SignalR<span data-ttu-id="6fd82-117">používá dvě kategorie protokolovacího nástroje:</span><span class="sxs-lookup"><span data-stu-id="6fd82-117"> uses two logger categories:</span></span>

* <span data-ttu-id="6fd82-118">`Microsoft.AspNetCore.SignalR`: Protokoly týkající se protokolů centrálního centra, aktivace rozbočovačů, volání metod a dalších aktivit souvisejících s centrem.</span><span class="sxs-lookup"><span data-stu-id="6fd82-118">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="6fd82-119">`Microsoft.AspNetCore.Http.Connections`: Pro protokoly týkající se přenosů, jako jsou WebSockets, dlouhé cyklické dotazování, události odeslané serverem a infrastruktura nízké úrovně SignalR .</span><span class="sxs-lookup"><span data-stu-id="6fd82-119">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="6fd82-120">Pokud chcete povolit podrobné protokoly SignalR , nakonfigurujte obě předchozí předpony na `Debug` úroveň v souboru *appSettings. JSON* přidáním následujících položek do `LogLevel` dílčí části v `Logging` :</span><span class="sxs-lookup"><span data-stu-id="6fd82-120">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="6fd82-121">Tuto možnost lze také nakonfigurovat v kódu v `CreateWebHostBuilder` metodě:</span><span class="sxs-lookup"><span data-stu-id="6fd82-121">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="6fd82-122">Pokud nepoužíváte konfiguraci založenou na formátu JSON, nastavte v konfiguračním systému následující konfigurační hodnoty:</span><span class="sxs-lookup"><span data-stu-id="6fd82-122">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="6fd82-123">Informace o tom, jak zadat hodnoty vnořených konfigurací, najdete v dokumentaci ke konfiguračnímu systému.</span><span class="sxs-lookup"><span data-stu-id="6fd82-123">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="6fd82-124">Například při použití proměnných prostředí `_` jsou místo `:` (například) použity dva znaky (například `Logging__LogLevel__Microsoft.AspNetCore.SignalR` ).</span><span class="sxs-lookup"><span data-stu-id="6fd82-124">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="6fd82-125">Tuto úroveň doporučujeme používat `Debug` při shromažďování podrobnějších diagnostických nástrojů pro vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6fd82-125">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="6fd82-126">`Trace`Úroveň přináší vysoce nízkou diagnostiku a je zřídka nutná pro diagnostiku problémů ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6fd82-126">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="6fd82-127">Přístup k protokolům na straně serveru</span><span class="sxs-lookup"><span data-stu-id="6fd82-127">Access server-side logs</span></span>

<span data-ttu-id="6fd82-128">Způsob přístupu ke protokolům na straně serveru závisí na prostředí, ve kterém používáte.</span><span class="sxs-lookup"><span data-stu-id="6fd82-128">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="6fd82-129">Jako Konzolová aplikace mimo IIS</span><span class="sxs-lookup"><span data-stu-id="6fd82-129">As a console app outside IIS</span></span>

<span data-ttu-id="6fd82-130">Pokud používáte konzolovou aplikaci, měl by být ve výchozím nastavení povolený [protokolovací nástroj konzoly](xref:fundamentals/logging/index#console) .</span><span class="sxs-lookup"><span data-stu-id="6fd82-130">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> SignalR<span data-ttu-id="6fd82-131">protokoly se zobrazí v konzole nástroje.</span><span class="sxs-lookup"><span data-stu-id="6fd82-131"> logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="6fd82-132">V rámci IIS Express ze sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6fd82-132">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="6fd82-133">Visual Studio zobrazí výstup protokolu v okně **výstup** .</span><span class="sxs-lookup"><span data-stu-id="6fd82-133">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="6fd82-134">Vyberte možnost rozevíracího seznamu **ASP.NET Core webového serveru** .</span><span class="sxs-lookup"><span data-stu-id="6fd82-134">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="6fd82-135">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="6fd82-135">Azure App Service</span></span>

<span data-ttu-id="6fd82-136">V části **diagnostické protokoly** na portálu Azure App Service povolte možnost **protokolování aplikace (systém souborů)** a nakonfigurujte **úroveň** na `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="6fd82-136">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="6fd82-137">Protokoly by měly být dostupné ze služby **streamování protokolů** a v protokolech v systému souborů App Service.</span><span class="sxs-lookup"><span data-stu-id="6fd82-137">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="6fd82-138">Další informace najdete v tématu [streamování protokolů Azure](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="6fd82-138">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="6fd82-139">Další prostředí</span><span class="sxs-lookup"><span data-stu-id="6fd82-139">Other environments</span></span>

<span data-ttu-id="6fd82-140">Pokud je aplikace nasazená do jiného prostředí (například Docker, Kubernetes nebo Windows), najdete <xref:fundamentals/logging/index> Další informace o tom, jak nakonfigurovat zprostředkovatele protokolování vhodné pro prostředí.</span><span class="sxs-lookup"><span data-stu-id="6fd82-140">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="6fd82-141">Protokolování klienta JavaScript</span><span class="sxs-lookup"><span data-stu-id="6fd82-141">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="6fd82-142">Protokoly na straně klienta můžou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="6fd82-142">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="6fd82-143">**Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="6fd82-143">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="6fd82-144">Při použití klienta JavaScriptu můžete nakonfigurovat možnosti protokolování pomocí `configureLogging` metody na `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="6fd82-144">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="6fd82-145">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="6fd82-145">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6fd82-146">V následující tabulce jsou uvedeny úrovně protokolu dostupné pro klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6fd82-146">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="6fd82-147">Nastavením úrovně protokolu na jednu z těchto hodnot povolíte protokolování na této úrovni a všechny úrovně nad ním v tabulce.</span><span class="sxs-lookup"><span data-stu-id="6fd82-147">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="6fd82-148">Úroveň</span><span class="sxs-lookup"><span data-stu-id="6fd82-148">Level</span></span> | <span data-ttu-id="6fd82-149">Description</span><span class="sxs-lookup"><span data-stu-id="6fd82-149">Description</span></span> |
| ----- | ---
<span data-ttu-id="6fd82-150">title: protokolování a diagnostika ve ASP.NET Core SignalR Autor: Description: ' Naučte se shromažďovat diagnostiku z vaší ASP.NET Core SignalR aplikace. '</span><span class="sxs-lookup"><span data-stu-id="6fd82-150">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="6fd82-151">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fd82-151">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fd82-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fd82-152">'Blazor'</span></span>
- <span data-ttu-id="6fd82-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fd82-153">'Identity'</span></span>
- <span data-ttu-id="6fd82-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fd82-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fd82-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fd82-155">'Razor'</span></span>
- <span data-ttu-id="6fd82-156">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="6fd82-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fd82-157">title: protokolování a diagnostika ve ASP.NET Core SignalR Autor: Description: ' Naučte se shromažďovat diagnostiku z vaší ASP.NET Core SignalR aplikace. '</span><span class="sxs-lookup"><span data-stu-id="6fd82-157">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="6fd82-158">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fd82-158">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fd82-159">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fd82-159">'Blazor'</span></span>
- <span data-ttu-id="6fd82-160">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fd82-160">'Identity'</span></span>
- <span data-ttu-id="6fd82-161">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fd82-161">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fd82-162">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fd82-162">'Razor'</span></span>
- <span data-ttu-id="6fd82-163">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="6fd82-163">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6fd82-164">title: protokolování a diagnostika ve ASP.NET Core SignalR Autor: Description: ' Naučte se shromažďovat diagnostiku z vaší ASP.NET Core SignalR aplikace. '</span><span class="sxs-lookup"><span data-stu-id="6fd82-164">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="6fd82-165">monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="6fd82-165">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6fd82-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6fd82-166">'Blazor'</span></span>
- <span data-ttu-id="6fd82-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6fd82-167">'Identity'</span></span>
- <span data-ttu-id="6fd82-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6fd82-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="6fd82-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6fd82-169">'Razor'</span></span>
- <span data-ttu-id="6fd82-170">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="6fd82-170">'SignalR' uid:</span></span> 

<span data-ttu-id="6fd82-171">------ | | `None` | Nejsou protokolovány žádné zprávy.</span><span class="sxs-lookup"><span data-stu-id="6fd82-171">------ | | `None` | No messages are logged.</span></span> <span data-ttu-id="6fd82-172">| | `Critical` | Zprávy indikující selhání v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="6fd82-172">| | `Critical` | Messages that indicate a failure in the entire app.</span></span> <span data-ttu-id="6fd82-173">| | `Error` | Zprávy indikující selhání aktuální operace.</span><span class="sxs-lookup"><span data-stu-id="6fd82-173">| | `Error` | Messages that indicate a failure in the current operation.</span></span> <span data-ttu-id="6fd82-174">| | `Warning` | Zprávy, které indikují méně závažnou chybu.</span><span class="sxs-lookup"><span data-stu-id="6fd82-174">| | `Warning` | Messages that indicate a non-fatal problem.</span></span> <span data-ttu-id="6fd82-175">| | `Information` | Informační zprávy.</span><span class="sxs-lookup"><span data-stu-id="6fd82-175">| | `Information` | Informational messages.</span></span> <span data-ttu-id="6fd82-176">| | `Debug` | Diagnostické zprávy užitečné pro ladění.</span><span class="sxs-lookup"><span data-stu-id="6fd82-176">| | `Debug` | Diagnostic messages useful for debugging.</span></span> <span data-ttu-id="6fd82-177">| | `Trace` | Velmi podrobné diagnostické zprávy navržené pro diagnostiku konkrétních problémů.</span><span class="sxs-lookup"><span data-stu-id="6fd82-177">| | `Trace` | Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="6fd82-178">Po nakonfigurování podrobností se protokoly zapíší do konzoly prohlížeče (nebo standardního výstupu v aplikaci NodeJS).</span><span class="sxs-lookup"><span data-stu-id="6fd82-178">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="6fd82-179">Pokud chcete odesílat protokoly do vlastního systému protokolování, můžete poskytnout JavaScriptový objekt implementující `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="6fd82-179">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="6fd82-180">Jedinou metodou, kterou je třeba implementovat, je `log` , která přebírá úroveň události a zprávu spojenou s událostí.</span><span class="sxs-lookup"><span data-stu-id="6fd82-180">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="6fd82-181">Příklad:</span><span class="sxs-lookup"><span data-stu-id="6fd82-181">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="6fd82-182">Protokolování klienta .NET</span><span class="sxs-lookup"><span data-stu-id="6fd82-182">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="6fd82-183">Protokoly na straně klienta můžou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="6fd82-183">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="6fd82-184">**Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="6fd82-184">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="6fd82-185">Chcete-li získat protokoly z klienta rozhraní .NET, můžete použít `ConfigureLogging` metodu na `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6fd82-185">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="6fd82-186">To funguje stejným způsobem jako `ConfigureLogging` metoda na `WebHostBuilder` a `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="6fd82-186">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="6fd82-187">Můžete nakonfigurovat stejné poskytovatele protokolování, které používáte v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6fd82-187">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="6fd82-188">Je však nutné ručně nainstalovat a povolit balíčky NuGet pro jednotlivé zprostředkovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="6fd82-188">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="6fd82-189">Protokolování konzoly</span><span class="sxs-lookup"><span data-stu-id="6fd82-189">Console logging</span></span>

<span data-ttu-id="6fd82-190">Aby bylo možné povolit protokolování konzoly, přidejte balíček [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) .</span><span class="sxs-lookup"><span data-stu-id="6fd82-190">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="6fd82-191">Pak použijte `AddConsole` metodu ke konfiguraci protokolovacího nástroje konzoly:</span><span class="sxs-lookup"><span data-stu-id="6fd82-191">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="6fd82-192">Protokolování výstupního okna ladění</span><span class="sxs-lookup"><span data-stu-id="6fd82-192">Debug output window logging</span></span>

<span data-ttu-id="6fd82-193">Můžete také nakonfigurovat protokoly pro přechod do okna **výstup** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6fd82-193">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="6fd82-194">Nainstalujte balíček [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) a použijte `AddDebug` metodu:</span><span class="sxs-lookup"><span data-stu-id="6fd82-194">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="6fd82-195">Další zprostředkovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="6fd82-195">Other logging providers</span></span>

SignalR<span data-ttu-id="6fd82-196">podporuje jiné poskytovatele protokolování, jako je Serilog, SEQ, NLog nebo jakýkoli jiný systém protokolování, který se integruje s `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="6fd82-196"> supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="6fd82-197">Pokud váš systém protokolování poskytuje `ILoggerProvider` , můžete ho zaregistrovat pomocí `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="6fd82-197">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="6fd82-198">Podrobnost ovládacího prvku</span><span class="sxs-lookup"><span data-stu-id="6fd82-198">Control verbosity</span></span>

<span data-ttu-id="6fd82-199">Pokud se přihlašujete z jiných míst v aplikaci, Změna výchozí úrovně na `Debug` může být příliš podrobná.</span><span class="sxs-lookup"><span data-stu-id="6fd82-199">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="6fd82-200">Pomocí filtru můžete nakonfigurovat úroveň protokolování pro SignalR protokoly.</span><span class="sxs-lookup"><span data-stu-id="6fd82-200">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="6fd82-201">To lze provést v kódu, podobně jako na serveru:</span><span class="sxs-lookup"><span data-stu-id="6fd82-201">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="6fd82-202">Trasování sítě</span><span class="sxs-lookup"><span data-stu-id="6fd82-202">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="6fd82-203">Trasování sítě obsahuje úplný obsah každé zprávy odesílané vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="6fd82-203">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="6fd82-204">**Nikdy** nezveřejňujte nezpracované síťové trasování z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="6fd82-204">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="6fd82-205">Pokud narazíte na problém, trasování sítě může někdy poskytnout spoustu užitečných informací.</span><span class="sxs-lookup"><span data-stu-id="6fd82-205">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="6fd82-206">To je užitečné hlavně v případě, že se chystáte zaslat problém do souboru sledování problémů.</span><span class="sxs-lookup"><span data-stu-id="6fd82-206">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="6fd82-207">Shromáždění trasování sítě pomocí Fiddler (upřednostňovaná možnost)</span><span class="sxs-lookup"><span data-stu-id="6fd82-207">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="6fd82-208">Tato metoda se používá pro všechny aplikace.</span><span class="sxs-lookup"><span data-stu-id="6fd82-208">This method works for all apps.</span></span>

<span data-ttu-id="6fd82-209">Fiddler je vysoce výkonný nástroj pro shromažďování trasování HTTP.</span><span class="sxs-lookup"><span data-stu-id="6fd82-209">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="6fd82-210">Nainstalujte si ho z [Telerik.com/Fiddler](https://www.telerik.com/fiddler), spusťte ho a pak spusťte aplikaci a pokuste se problém reprodukování.</span><span class="sxs-lookup"><span data-stu-id="6fd82-210">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="6fd82-211">Fiddler je k dispozici pro Windows a pro macOS a Linux jsou k dispozici beta verze.</span><span class="sxs-lookup"><span data-stu-id="6fd82-211">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="6fd82-212">Pokud se připojujete pomocí protokolu HTTPS, je potřeba provést několik kroků navíc, abyste zajistili, že Fiddler dokáže dešifrovat přenosy HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6fd82-212">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="6fd82-213">Další podrobnosti najdete v [dokumentaci k Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="6fd82-213">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="6fd82-214">Po shromáždění trasování můžete trasování exportovat výběrem možnosti **soubor**  >  **Uložit**  >  **všechny relace** z řádku nabídek.</span><span class="sxs-lookup"><span data-stu-id="6fd82-214">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Exportují se všechny relace z Fiddler.](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="6fd82-216">Shromažďovat trasování sítě pomocí tcpdump (jenom macOS a Linux)</span><span class="sxs-lookup"><span data-stu-id="6fd82-216">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="6fd82-217">Tato metoda se používá pro všechny aplikace.</span><span class="sxs-lookup"><span data-stu-id="6fd82-217">This method works for all apps.</span></span>

<span data-ttu-id="6fd82-218">Nezpracované trasování TCP můžete shromažďovat pomocí tcpdump spuštěním následujícího příkazu z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="6fd82-218">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="6fd82-219">Pokud se zobrazí chyba oprávnění, může být nutné, abyste museli být `root` nebo začínat příkazem `sudo` .</span><span class="sxs-lookup"><span data-stu-id="6fd82-219">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="6fd82-220">Nahraďte `[interface]` síťovým rozhraním, na kterém chcete zachytit.</span><span class="sxs-lookup"><span data-stu-id="6fd82-220">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="6fd82-221">Obvykle se jedná o něco podobného `/dev/eth0` (pro standardní rozhraní sítě Ethernet) nebo `/dev/lo0` (pro přenosy v localhost).</span><span class="sxs-lookup"><span data-stu-id="6fd82-221">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="6fd82-222">Další informace najdete na `tcpdump` stránce muž v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="6fd82-222">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="6fd82-223">Shromáždění trasování sítě v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="6fd82-223">Collect a network trace in the browser</span></span>

<span data-ttu-id="6fd82-224">Tato metoda funguje pouze pro aplikace založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="6fd82-224">This method only works for browser-based apps.</span></span>

<span data-ttu-id="6fd82-225">Většina prohlížečů Vývojářské nástroje mít kartu síť, která umožňuje zachytit síťovou aktivitu mezi prohlížečem a serverem.</span><span class="sxs-lookup"><span data-stu-id="6fd82-225">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="6fd82-226">Tato trasování ale neobsahují zprávy protokolu WebSocket a události odeslané serverem.</span><span class="sxs-lookup"><span data-stu-id="6fd82-226">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="6fd82-227">Pokud používáte tyto přenosy, je lepší přístup pomocí nástroje, jako je například Fiddler nebo TcpDump (popsané níže).</span><span class="sxs-lookup"><span data-stu-id="6fd82-227">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="6fd82-228">Microsoft Edge a Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="6fd82-228">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="6fd82-229">(Pokyny jsou pro aplikaci Edge i Internet Explorer stejné.)</span><span class="sxs-lookup"><span data-stu-id="6fd82-229">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="6fd82-230">Stisknutím klávesy F12 otevřete nástroje pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="6fd82-230">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="6fd82-231">Klikněte na kartu síť.</span><span class="sxs-lookup"><span data-stu-id="6fd82-231">Click the Network Tab</span></span>
3. <span data-ttu-id="6fd82-232">Aktualizujte stránku (Pokud je to potřeba) a reprodukování problému.</span><span class="sxs-lookup"><span data-stu-id="6fd82-232">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="6fd82-233">Kliknutím na ikonu Uložit na panelu nástrojů exportujte trasování jako soubor "HAR":</span><span class="sxs-lookup"><span data-stu-id="6fd82-233">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Ikona uložit na kartě síť nástrojů Microsoft Edge pro vývoj](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="6fd82-235">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="6fd82-235">Google Chrome</span></span>

1. <span data-ttu-id="6fd82-236">Stisknutím klávesy F12 otevřete nástroje pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="6fd82-236">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="6fd82-237">Klikněte na kartu síť.</span><span class="sxs-lookup"><span data-stu-id="6fd82-237">Click the Network Tab</span></span>
3. <span data-ttu-id="6fd82-238">Aktualizujte stránku (Pokud je to potřeba) a reprodukování problému.</span><span class="sxs-lookup"><span data-stu-id="6fd82-238">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="6fd82-239">Klikněte pravým tlačítkem na libovolné místo v seznamu požadavků a vyberte Uložit jako HAR s obsahem:</span><span class="sxs-lookup"><span data-stu-id="6fd82-239">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Možnost Uložit jako HAR s obsahem na kartě síť Google Chrome dev Tools](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="6fd82-241">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="6fd82-241">Mozilla Firefox</span></span>

1. <span data-ttu-id="6fd82-242">Stisknutím klávesy F12 otevřete nástroje pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="6fd82-242">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="6fd82-243">Klikněte na kartu síť.</span><span class="sxs-lookup"><span data-stu-id="6fd82-243">Click the Network Tab</span></span>
3. <span data-ttu-id="6fd82-244">Aktualizujte stránku (Pokud je to potřeba) a reprodukování problému.</span><span class="sxs-lookup"><span data-stu-id="6fd82-244">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="6fd82-245">Klikněte pravým tlačítkem na libovolné místo v seznamu požadavků a vyberte Uložit vše jako HAR.</span><span class="sxs-lookup"><span data-stu-id="6fd82-245">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Možnost Uložit vše jako HAR na kartě síť Mozilla Firefox nástroje pro vývojáře](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="6fd82-247">Připojení diagnostických souborů k problémům GitHubu</span><span class="sxs-lookup"><span data-stu-id="6fd82-247">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="6fd82-248">Diagnostické soubory můžete k problémům s GitHubem připojit tak, že je přejmenujete, aby měly `.txt` rozšíření a pak je přetáhnete na problém.</span><span class="sxs-lookup"><span data-stu-id="6fd82-248">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="6fd82-249">Do problému GitHubu prosím nevložíme obsah souborů protokolu ani trasování sítě.</span><span class="sxs-lookup"><span data-stu-id="6fd82-249">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="6fd82-250">Tyto protokoly a trasování můžou být poměrně velké a GitHub je obvykle ořízne.</span><span class="sxs-lookup"><span data-stu-id="6fd82-250">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Přetahování souborů protokolů na problém GitHubu](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="6fd82-252">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6fd82-252">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
