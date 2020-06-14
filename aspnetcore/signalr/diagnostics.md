---
title: Protokolování a diagnostika v ASP.NET CoreSignalR
author: anurse
description: Naučte se shromažďovat diagnostiku z vaší SignalR aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 06/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/diagnostics
ms.openlocfilehash: d26bb71a8ae06764b58a094b28d5e6f9eb581ecd
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2020
ms.locfileid: "84755960"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="065ef-103">Protokolování a diagnostika v ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="065ef-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="065ef-104">Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="065ef-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="065ef-105">Tento článek poskytuje pokyny pro shromažďování diagnostických informací z vaší SignalR aplikace ASP.NET Core, které vám pomůžou při řešení problémů.</span><span class="sxs-lookup"><span data-stu-id="065ef-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="065ef-106">Protokolování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="065ef-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="065ef-107">Protokoly na straně serveru můžou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="065ef-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="065ef-108">**Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="065ef-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="065ef-109">Vzhledem k SignalR tomu, že je součást ASP.NET Core, používá systém protokolování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="065ef-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="065ef-110">Ve výchozí konfiguraci SignalR protokoluje velmi málo informací, ale je možné ho nakonfigurovat.</span><span class="sxs-lookup"><span data-stu-id="065ef-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="065ef-111">Podrobnosti o konfiguraci ASP.NET Core protokolování najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index#configuration) .</span><span class="sxs-lookup"><span data-stu-id="065ef-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

SignalR<span data-ttu-id="065ef-112">používá dvě kategorie protokolovacího nástroje:</span><span class="sxs-lookup"><span data-stu-id="065ef-112"> uses two logger categories:</span></span>

* <span data-ttu-id="065ef-113">`Microsoft.AspNetCore.SignalR`: Protokoly týkající se protokolů centrálního centra, aktivace rozbočovačů, volání metod a dalších aktivit souvisejících s centrem.</span><span class="sxs-lookup"><span data-stu-id="065ef-113">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="065ef-114">`Microsoft.AspNetCore.Http.Connections`: Pro protokoly týkající se přenosů, jako jsou WebSockets, dlouhé cyklické dotazování, události odeslané serverem a infrastruktura nízké úrovně SignalR .</span><span class="sxs-lookup"><span data-stu-id="065ef-114">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="065ef-115">Chcete-li povolit podrobné protokoly SignalR , nakonfigurujte obě předchozí předpony na `Debug` úroveň v *appsettings.js* souboru přidáním následujících položek do `LogLevel` dílčí části v `Logging` :</span><span class="sxs-lookup"><span data-stu-id="065ef-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="065ef-116">Tuto možnost lze také nakonfigurovat v kódu v `CreateWebHostBuilder` metodě:</span><span class="sxs-lookup"><span data-stu-id="065ef-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="065ef-117">Pokud nepoužíváte konfiguraci založenou na formátu JSON, nastavte v konfiguračním systému následující konfigurační hodnoty:</span><span class="sxs-lookup"><span data-stu-id="065ef-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="065ef-118">Informace o tom, jak zadat hodnoty vnořených konfigurací, najdete v dokumentaci ke konfiguračnímu systému.</span><span class="sxs-lookup"><span data-stu-id="065ef-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="065ef-119">Například při použití proměnných prostředí `_` jsou místo `:` (například) použity dva znaky (například `Logging__LogLevel__Microsoft.AspNetCore.SignalR` ).</span><span class="sxs-lookup"><span data-stu-id="065ef-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="065ef-120">Tuto úroveň doporučujeme používat `Debug` při shromažďování podrobnějších diagnostických nástrojů pro vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="065ef-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="065ef-121">`Trace`Úroveň přináší vysoce nízkou diagnostiku a je zřídka nutná pro diagnostiku problémů ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="065ef-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="065ef-122">Přístup k protokolům na straně serveru</span><span class="sxs-lookup"><span data-stu-id="065ef-122">Access server-side logs</span></span>

<span data-ttu-id="065ef-123">Způsob přístupu ke protokolům na straně serveru závisí na prostředí, ve kterém používáte.</span><span class="sxs-lookup"><span data-stu-id="065ef-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="065ef-124">Jako Konzolová aplikace mimo IIS</span><span class="sxs-lookup"><span data-stu-id="065ef-124">As a console app outside IIS</span></span>

<span data-ttu-id="065ef-125">Pokud používáte konzolovou aplikaci, měl by být ve výchozím nastavení povolený [protokolovací nástroj konzoly](xref:fundamentals/logging/index#console) .</span><span class="sxs-lookup"><span data-stu-id="065ef-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> SignalR<span data-ttu-id="065ef-126">protokoly se zobrazí v konzole nástroje.</span><span class="sxs-lookup"><span data-stu-id="065ef-126"> logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="065ef-127">V rámci IIS Express ze sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="065ef-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="065ef-128">Visual Studio zobrazí výstup protokolu v okně **výstup** .</span><span class="sxs-lookup"><span data-stu-id="065ef-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="065ef-129">Vyberte možnost rozevíracího seznamu **ASP.NET Core webového serveru** .</span><span class="sxs-lookup"><span data-stu-id="065ef-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="065ef-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="065ef-130">Azure App Service</span></span>

<span data-ttu-id="065ef-131">V části **diagnostické protokoly** na portálu Azure App Service povolte možnost **protokolování aplikace (systém souborů)** a nakonfigurujte **úroveň** na `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="065ef-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="065ef-132">Protokoly by měly být dostupné ze služby **streamování protokolů** a v protokolech v systému souborů App Service.</span><span class="sxs-lookup"><span data-stu-id="065ef-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="065ef-133">Další informace najdete v tématu [streamování protokolů Azure](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="065ef-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="065ef-134">Další prostředí</span><span class="sxs-lookup"><span data-stu-id="065ef-134">Other environments</span></span>

<span data-ttu-id="065ef-135">Pokud je aplikace nasazená do jiného prostředí (například Docker, Kubernetes nebo Windows), najdete <xref:fundamentals/logging/index> Další informace o tom, jak nakonfigurovat zprostředkovatele protokolování vhodné pro prostředí.</span><span class="sxs-lookup"><span data-stu-id="065ef-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="065ef-136">Protokolování klienta JavaScript</span><span class="sxs-lookup"><span data-stu-id="065ef-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="065ef-137">Protokoly na straně klienta můžou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="065ef-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="065ef-138">**Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="065ef-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="065ef-139">Při použití klienta JavaScriptu můžete nakonfigurovat možnosti protokolování pomocí `configureLogging` metody na `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="065ef-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="065ef-140">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="065ef-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="065ef-141">V následující tabulce jsou uvedeny úrovně protokolu dostupné pro klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="065ef-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="065ef-142">Nastavením úrovně protokolu na jednu z těchto hodnot povolíte protokolování na této úrovni a všechny úrovně nad ním v tabulce.</span><span class="sxs-lookup"><span data-stu-id="065ef-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="065ef-143">Úroveň</span><span class="sxs-lookup"><span data-stu-id="065ef-143">Level</span></span> | <span data-ttu-id="065ef-144">Description</span><span class="sxs-lookup"><span data-stu-id="065ef-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="065ef-145">Nejsou protokolovány žádné zprávy.</span><span class="sxs-lookup"><span data-stu-id="065ef-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="065ef-146">Zprávy indikující selhání v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="065ef-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="065ef-147">Zprávy indikující selhání aktuální operace.</span><span class="sxs-lookup"><span data-stu-id="065ef-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="065ef-148">Zprávy, které indikují méně závažnou chybu.</span><span class="sxs-lookup"><span data-stu-id="065ef-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="065ef-149">Informační zprávy.</span><span class="sxs-lookup"><span data-stu-id="065ef-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="065ef-150">Diagnostické zprávy užitečné pro ladění.</span><span class="sxs-lookup"><span data-stu-id="065ef-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="065ef-151">Velmi podrobné diagnostické zprávy navržené pro diagnostiku konkrétních problémů.</span><span class="sxs-lookup"><span data-stu-id="065ef-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="065ef-152">Po nakonfigurování podrobností se protokoly zapíší do konzoly prohlížeče (nebo standardního výstupu v aplikaci NodeJS).</span><span class="sxs-lookup"><span data-stu-id="065ef-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="065ef-153">Pokud chcete odesílat protokoly do vlastního systému protokolování, můžete poskytnout JavaScriptový objekt implementující `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="065ef-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="065ef-154">Jedinou metodou, kterou je třeba implementovat, je `log` , která přebírá úroveň události a zprávu spojenou s událostí.</span><span class="sxs-lookup"><span data-stu-id="065ef-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="065ef-155">Například:</span><span class="sxs-lookup"><span data-stu-id="065ef-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="065ef-156">Protokolování klienta .NET</span><span class="sxs-lookup"><span data-stu-id="065ef-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="065ef-157">Protokoly na straně klienta můžou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="065ef-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="065ef-158">**Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="065ef-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="065ef-159">Chcete-li získat protokoly z klienta rozhraní .NET, můžete použít `ConfigureLogging` metodu na `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="065ef-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="065ef-160">To funguje stejným způsobem jako `ConfigureLogging` metoda na `WebHostBuilder` a `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="065ef-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="065ef-161">Můžete nakonfigurovat stejné poskytovatele protokolování, které používáte v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="065ef-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="065ef-162">Je však nutné ručně nainstalovat a povolit balíčky NuGet pro jednotlivé zprostředkovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="065ef-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

<span data-ttu-id="065ef-163">Chcete-li přidat protokolování klienta rozhraní .NET do Blazor aplikace WebAssembly, přečtěte si téma <xref:fundamentals/logging/index#blazor-webassembly-signalr-net-client-logging> .</span><span class="sxs-lookup"><span data-stu-id="065ef-163">To add .NET client logging to a Blazor WebAssembly app, see <xref:fundamentals/logging/index#blazor-webassembly-signalr-net-client-logging>.</span></span>

### <a name="console-logging"></a><span data-ttu-id="065ef-164">Protokolování konzoly</span><span class="sxs-lookup"><span data-stu-id="065ef-164">Console logging</span></span>

<span data-ttu-id="065ef-165">Aby bylo možné povolit protokolování konzoly, přidejte balíček [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) .</span><span class="sxs-lookup"><span data-stu-id="065ef-165">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="065ef-166">Pak použijte `AddConsole` metodu ke konfiguraci protokolovacího nástroje konzoly:</span><span class="sxs-lookup"><span data-stu-id="065ef-166">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="065ef-167">Protokolování výstupního okna ladění</span><span class="sxs-lookup"><span data-stu-id="065ef-167">Debug output window logging</span></span>

<span data-ttu-id="065ef-168">Můžete také nakonfigurovat protokoly pro přechod do okna **výstup** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="065ef-168">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="065ef-169">Nainstalujte balíček [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) a použijte `AddDebug` metodu:</span><span class="sxs-lookup"><span data-stu-id="065ef-169">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="065ef-170">Další zprostředkovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="065ef-170">Other logging providers</span></span>

SignalR<span data-ttu-id="065ef-171">podporuje jiné poskytovatele protokolování, jako je Serilog, SEQ, NLog nebo jakýkoli jiný systém protokolování, který se integruje s `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="065ef-171"> supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="065ef-172">Pokud váš systém protokolování poskytuje `ILoggerProvider` , můžete ho zaregistrovat pomocí `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="065ef-172">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="065ef-173">Podrobnost ovládacího prvku</span><span class="sxs-lookup"><span data-stu-id="065ef-173">Control verbosity</span></span>

<span data-ttu-id="065ef-174">Pokud se přihlašujete z jiných míst v aplikaci, Změna výchozí úrovně na `Debug` může být příliš podrobná.</span><span class="sxs-lookup"><span data-stu-id="065ef-174">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="065ef-175">Pomocí filtru můžete nakonfigurovat úroveň protokolování pro SignalR protokoly.</span><span class="sxs-lookup"><span data-stu-id="065ef-175">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="065ef-176">To lze provést v kódu, podobně jako na serveru:</span><span class="sxs-lookup"><span data-stu-id="065ef-176">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="065ef-177">Trasování sítě</span><span class="sxs-lookup"><span data-stu-id="065ef-177">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="065ef-178">Trasování sítě obsahuje úplný obsah každé zprávy odesílané vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="065ef-178">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="065ef-179">**Nikdy** nezveřejňujte nezpracované síťové trasování z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="065ef-179">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="065ef-180">Pokud narazíte na problém, trasování sítě může někdy poskytnout spoustu užitečných informací.</span><span class="sxs-lookup"><span data-stu-id="065ef-180">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="065ef-181">To je užitečné hlavně v případě, že se chystáte zaslat problém do souboru sledování problémů.</span><span class="sxs-lookup"><span data-stu-id="065ef-181">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="065ef-182">Shromáždění trasování sítě pomocí Fiddler (upřednostňovaná možnost)</span><span class="sxs-lookup"><span data-stu-id="065ef-182">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="065ef-183">Tato metoda se používá pro všechny aplikace.</span><span class="sxs-lookup"><span data-stu-id="065ef-183">This method works for all apps.</span></span>

<span data-ttu-id="065ef-184">Fiddler je vysoce výkonný nástroj pro shromažďování trasování HTTP.</span><span class="sxs-lookup"><span data-stu-id="065ef-184">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="065ef-185">Nainstalujte si ho z [Telerik.com/Fiddler](https://www.telerik.com/fiddler), spusťte ho a pak spusťte aplikaci a pokuste se problém reprodukování.</span><span class="sxs-lookup"><span data-stu-id="065ef-185">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="065ef-186">Fiddler je k dispozici pro Windows a pro macOS a Linux jsou k dispozici beta verze.</span><span class="sxs-lookup"><span data-stu-id="065ef-186">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="065ef-187">Pokud se připojujete pomocí protokolu HTTPS, je potřeba provést několik kroků navíc, abyste zajistili, že Fiddler dokáže dešifrovat přenosy HTTPS.</span><span class="sxs-lookup"><span data-stu-id="065ef-187">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="065ef-188">Další podrobnosti najdete v [dokumentaci k Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="065ef-188">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="065ef-189">Po shromáždění trasování můžete trasování exportovat výběrem možnosti **soubor**  >  **Uložit**  >  **všechny relace** z řádku nabídek.</span><span class="sxs-lookup"><span data-stu-id="065ef-189">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Exportují se všechny relace z Fiddler.](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="065ef-191">Shromažďovat trasování sítě pomocí tcpdump (jenom macOS a Linux)</span><span class="sxs-lookup"><span data-stu-id="065ef-191">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="065ef-192">Tato metoda se používá pro všechny aplikace.</span><span class="sxs-lookup"><span data-stu-id="065ef-192">This method works for all apps.</span></span>

<span data-ttu-id="065ef-193">Nezpracované trasování TCP můžete shromažďovat pomocí tcpdump spuštěním následujícího příkazu z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="065ef-193">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="065ef-194">Pokud se zobrazí chyba oprávnění, může být nutné, abyste museli být `root` nebo začínat příkazem `sudo` .</span><span class="sxs-lookup"><span data-stu-id="065ef-194">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="065ef-195">Nahraďte `[interface]` síťovým rozhraním, na kterém chcete zachytit.</span><span class="sxs-lookup"><span data-stu-id="065ef-195">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="065ef-196">Obvykle se jedná o něco podobného `/dev/eth0` (pro standardní rozhraní sítě Ethernet) nebo `/dev/lo0` (pro přenosy v localhost).</span><span class="sxs-lookup"><span data-stu-id="065ef-196">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="065ef-197">Další informace najdete na `tcpdump` stránce muž v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="065ef-197">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="065ef-198">Shromáždění trasování sítě v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="065ef-198">Collect a network trace in the browser</span></span>

<span data-ttu-id="065ef-199">Tato metoda funguje pouze pro aplikace založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="065ef-199">This method only works for browser-based apps.</span></span>

<span data-ttu-id="065ef-200">Většina prohlížečů Vývojářské nástroje mít kartu síť, která umožňuje zachytit síťovou aktivitu mezi prohlížečem a serverem.</span><span class="sxs-lookup"><span data-stu-id="065ef-200">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="065ef-201">Tato trasování ale neobsahují zprávy protokolu WebSocket a události odeslané serverem.</span><span class="sxs-lookup"><span data-stu-id="065ef-201">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="065ef-202">Pokud používáte tyto přenosy, je lepší přístup pomocí nástroje, jako je například Fiddler nebo TcpDump (popsané níže).</span><span class="sxs-lookup"><span data-stu-id="065ef-202">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="065ef-203">Microsoft Edge a Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="065ef-203">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="065ef-204">(Pokyny jsou pro aplikaci Edge i Internet Explorer stejné.)</span><span class="sxs-lookup"><span data-stu-id="065ef-204">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="065ef-205">Stisknutím klávesy F12 otevřete nástroje pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="065ef-205">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="065ef-206">Klikněte na kartu síť.</span><span class="sxs-lookup"><span data-stu-id="065ef-206">Click the Network Tab</span></span>
3. <span data-ttu-id="065ef-207">Aktualizujte stránku (Pokud je to potřeba) a reprodukování problému.</span><span class="sxs-lookup"><span data-stu-id="065ef-207">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="065ef-208">Kliknutím na ikonu Uložit na panelu nástrojů exportujte trasování jako soubor "HAR":</span><span class="sxs-lookup"><span data-stu-id="065ef-208">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Ikona uložit na kartě síť nástrojů Microsoft Edge pro vývoj](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="065ef-210">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="065ef-210">Google Chrome</span></span>

1. <span data-ttu-id="065ef-211">Stisknutím klávesy F12 otevřete nástroje pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="065ef-211">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="065ef-212">Klikněte na kartu síť.</span><span class="sxs-lookup"><span data-stu-id="065ef-212">Click the Network Tab</span></span>
3. <span data-ttu-id="065ef-213">Aktualizujte stránku (Pokud je to potřeba) a reprodukování problému.</span><span class="sxs-lookup"><span data-stu-id="065ef-213">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="065ef-214">Klikněte pravým tlačítkem na libovolné místo v seznamu požadavků a vyberte Uložit jako HAR s obsahem:</span><span class="sxs-lookup"><span data-stu-id="065ef-214">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Možnost Uložit jako HAR s obsahem na kartě síť Google Chrome dev Tools](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="065ef-216">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="065ef-216">Mozilla Firefox</span></span>

1. <span data-ttu-id="065ef-217">Stisknutím klávesy F12 otevřete nástroje pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="065ef-217">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="065ef-218">Klikněte na kartu síť.</span><span class="sxs-lookup"><span data-stu-id="065ef-218">Click the Network Tab</span></span>
3. <span data-ttu-id="065ef-219">Aktualizujte stránku (Pokud je to potřeba) a reprodukování problému.</span><span class="sxs-lookup"><span data-stu-id="065ef-219">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="065ef-220">Klikněte pravým tlačítkem na libovolné místo v seznamu požadavků a vyberte Uložit vše jako HAR.</span><span class="sxs-lookup"><span data-stu-id="065ef-220">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Možnost Uložit vše jako HAR na kartě síť Mozilla Firefox nástroje pro vývojáře](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="065ef-222">Připojení diagnostických souborů k problémům GitHubu</span><span class="sxs-lookup"><span data-stu-id="065ef-222">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="065ef-223">Diagnostické soubory můžete k problémům s GitHubem připojit tak, že je přejmenujete, aby měly `.txt` rozšíření a pak je přetáhnete na problém.</span><span class="sxs-lookup"><span data-stu-id="065ef-223">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="065ef-224">Do problému GitHubu prosím nevložíme obsah souborů protokolu ani trasování sítě.</span><span class="sxs-lookup"><span data-stu-id="065ef-224">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="065ef-225">Tyto protokoly a trasování můžou být poměrně velké a GitHub je obvykle ořízne.</span><span class="sxs-lookup"><span data-stu-id="065ef-225">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Přetahování souborů protokolů na problém GitHubu](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a><span data-ttu-id="065ef-227">Metriky</span><span class="sxs-lookup"><span data-stu-id="065ef-227">Metrics</span></span>

<span data-ttu-id="065ef-228">Metrika je reprezentace datových měr v časových intervalech.</span><span class="sxs-lookup"><span data-stu-id="065ef-228">Metrics is a representation of data measures over intervals of time.</span></span> <span data-ttu-id="065ef-229">Například požadavky za sekundu.</span><span class="sxs-lookup"><span data-stu-id="065ef-229">For example, requests per second.</span></span> <span data-ttu-id="065ef-230">Data metrik umožňují sledovat stav aplikace na vysoké úrovni.</span><span class="sxs-lookup"><span data-stu-id="065ef-230">Metrics data allows observation of the state of an app at a high level.</span></span> <span data-ttu-id="065ef-231">Metriky .NET gRPC jsou vydávány pomocí <xref:System.Diagnostics.Tracing.EventCounter> .</span><span class="sxs-lookup"><span data-stu-id="065ef-231">.NET gRPC metrics are emitted using <xref:System.Diagnostics.Tracing.EventCounter>.</span></span>

### <a name="signalr-server-metrics"></a>SignalR<span data-ttu-id="065ef-232">metriky serveru</span><span class="sxs-lookup"><span data-stu-id="065ef-232"> server metrics</span></span>

SignalR<span data-ttu-id="065ef-233">metriky serveru jsou hlášeny ve <xref:Microsoft.AspNetCore.Http.Connections> zdroji událostí.</span><span class="sxs-lookup"><span data-stu-id="065ef-233"> server metrics are reported on the <xref:Microsoft.AspNetCore.Http.Connections> event source.</span></span>

| <span data-ttu-id="065ef-234">Name</span><span class="sxs-lookup"><span data-stu-id="065ef-234">Name</span></span>                    | <span data-ttu-id="065ef-235">Description</span><span class="sxs-lookup"><span data-stu-id="065ef-235">Description</span></span>                 |
|-------------------------|-----------------------------|
| `connections-started`   | <span data-ttu-id="065ef-236">Celkový počet spuštěných připojení</span><span class="sxs-lookup"><span data-stu-id="065ef-236">Total connections started</span></span>   |
| `connections-stopped`   | <span data-ttu-id="065ef-237">Celkový počet zastavených připojení</span><span class="sxs-lookup"><span data-stu-id="065ef-237">Total connections stopped</span></span>   |
| `connections-timed-out` | <span data-ttu-id="065ef-238">Celkový počet připojení vypršel.</span><span class="sxs-lookup"><span data-stu-id="065ef-238">Total connections timed out</span></span> |
| `current-connections`   | <span data-ttu-id="065ef-239">Aktuální připojení</span><span class="sxs-lookup"><span data-stu-id="065ef-239">Current connections</span></span>         |
| `connections-duration`  | <span data-ttu-id="065ef-240">Průměrná doba trvání připojení</span><span class="sxs-lookup"><span data-stu-id="065ef-240">Average connection duration</span></span> |

### <a name="observe-metrics"></a><span data-ttu-id="065ef-241">Sledovat metriky</span><span class="sxs-lookup"><span data-stu-id="065ef-241">Observe metrics</span></span>

<span data-ttu-id="065ef-242">[dotnet – čítače](/dotnet/core/diagnostics/dotnet-counters) jsou nástrojem pro monitorování výkonu, který slouží ke sledování stavu ad-hoc a prvotnímu šetření výkonu na nejvyšší úrovni.</span><span class="sxs-lookup"><span data-stu-id="065ef-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="065ef-243">Monitorujte aplikaci .NET s `Microsoft.AspNetCore.Http.Connections` názvem poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="065ef-243">Monitor a .NET app with `Microsoft.AspNetCore.Http.Connections` as the provider name.</span></span> <span data-ttu-id="065ef-244">Například:</span><span class="sxs-lookup"><span data-stu-id="065ef-244">For example:</span></span>

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a><span data-ttu-id="065ef-245">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="065ef-245">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
