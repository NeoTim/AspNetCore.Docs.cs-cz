---
title: Protokolování a diagnostika v ASP.NET CoreSignalR
author: anurse
description: Naučte se shromažďovat diagnostiku z vaší SignalR aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/diagnostics
ms.openlocfilehash: 0dda4fb55b1e2275d9cdb2af0b55824b12121dee
ms.sourcegitcommit: 16b3abec1ed70f9a206f0cfa7cf6404eebaf693d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2020
ms.locfileid: "83444214"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="5d31e-103">Protokolování a diagnostika v nástroji ASP.NET Core Signal</span><span class="sxs-lookup"><span data-stu-id="5d31e-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="5d31e-104">Autor [: Andrew Stanton – zdravotní sestry](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="5d31e-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="5d31e-105">Tento článek poskytuje pokyny pro shromažďování diagnostických informací z aplikace ASP.NET Core Signal, které vám pomůžou při řešení problémů.</span><span class="sxs-lookup"><span data-stu-id="5d31e-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="5d31e-106">Protokolování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="5d31e-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="5d31e-107">Protokoly na straně serveru můžou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="5d31e-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="5d31e-108">**Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="5d31e-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="5d31e-109">Vzhledem k tomu, že je signál, který je součástí ASP.NET Core, používá systém protokolování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5d31e-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="5d31e-110">Ve výchozí konfiguraci Signaler zaznamená velmi málo informací, ale může se nakonfigurovat.</span><span class="sxs-lookup"><span data-stu-id="5d31e-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="5d31e-111">Podrobnosti o konfiguraci ASP.NET Core protokolování najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index#configuration) .</span><span class="sxs-lookup"><span data-stu-id="5d31e-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="5d31e-112">Návěstí používá dvě kategorie protokolovacího nástroje:</span><span class="sxs-lookup"><span data-stu-id="5d31e-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="5d31e-113">`Microsoft.AspNetCore.SignalR`&ndash;pro protokoly související s protokoly centra, aktivací Center, vyvoláním metod a dalšími aktivitami souvisejícími s centrem.</span><span class="sxs-lookup"><span data-stu-id="5d31e-113">`Microsoft.AspNetCore.SignalR` &ndash; for logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="5d31e-114">`Microsoft.AspNetCore.Http.Connections`&ndash;pro protokoly týkající se přenosů, jako jsou například WebSockets, dlouhé cyklické dotazování a události odeslané serverem a infrastruktura signalizace nízké úrovně.</span><span class="sxs-lookup"><span data-stu-id="5d31e-114">`Microsoft.AspNetCore.Http.Connections` &ndash; for logs related to transports such as WebSockets, Long Polling and Server-Sent Events and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="5d31e-115">Pokud chcete povolit podrobné protokoly ze služby Signal, nakonfigurujte obě předchozí předpony na `Debug` úroveň v souboru *appSettings. JSON* přidáním následujících položek do `LogLevel` dílčí části v `Logging` :</span><span class="sxs-lookup"><span data-stu-id="5d31e-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="5d31e-116">Tuto možnost lze také nakonfigurovat v kódu v `CreateWebHostBuilder` metodě:</span><span class="sxs-lookup"><span data-stu-id="5d31e-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="5d31e-117">Pokud nepoužíváte konfiguraci založenou na formátu JSON, nastavte v konfiguračním systému následující konfigurační hodnoty:</span><span class="sxs-lookup"><span data-stu-id="5d31e-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="5d31e-118">Informace o tom, jak zadat hodnoty vnořených konfigurací, najdete v dokumentaci ke konfiguračnímu systému.</span><span class="sxs-lookup"><span data-stu-id="5d31e-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="5d31e-119">Například při použití proměnných prostředí `_` jsou místo `:` (například) použity dva znaky (například `Logging__LogLevel__Microsoft.AspNetCore.SignalR` ).</span><span class="sxs-lookup"><span data-stu-id="5d31e-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="5d31e-120">Tuto úroveň doporučujeme používat `Debug` při shromažďování podrobnějších diagnostických nástrojů pro vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5d31e-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="5d31e-121">`Trace`Úroveň přináší vysoce nízkou diagnostiku a je zřídka nutná pro diagnostiku problémů ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5d31e-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="5d31e-122">Přístup k protokolům na straně serveru</span><span class="sxs-lookup"><span data-stu-id="5d31e-122">Access server-side logs</span></span>

<span data-ttu-id="5d31e-123">Způsob přístupu ke protokolům na straně serveru závisí na prostředí, ve kterém používáte.</span><span class="sxs-lookup"><span data-stu-id="5d31e-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="5d31e-124">Jako Konzolová aplikace mimo IIS</span><span class="sxs-lookup"><span data-stu-id="5d31e-124">As a console app outside IIS</span></span>

<span data-ttu-id="5d31e-125">Pokud používáte konzolovou aplikaci, měl by být ve výchozím nastavení povolený [protokolovací nástroj konzoly](xref:fundamentals/logging/index#console) .</span><span class="sxs-lookup"><span data-stu-id="5d31e-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="5d31e-126">Protokoly signálu se zobrazí v konzole nástroje.</span><span class="sxs-lookup"><span data-stu-id="5d31e-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="5d31e-127">V rámci IIS Express ze sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5d31e-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="5d31e-128">Visual Studio zobrazí výstup protokolu v okně **výstup** .</span><span class="sxs-lookup"><span data-stu-id="5d31e-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="5d31e-129">Vyberte možnost rozevíracího seznamu **ASP.NET Core webového serveru** .</span><span class="sxs-lookup"><span data-stu-id="5d31e-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="5d31e-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5d31e-130">Azure App Service</span></span>

<span data-ttu-id="5d31e-131">V části **diagnostické protokoly** na portálu Azure App Service povolte možnost **protokolování aplikace (systém souborů)** a nakonfigurujte **úroveň** na `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="5d31e-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="5d31e-132">Protokoly by měly být dostupné ze služby **streamování protokolů** a v protokolech v systému souborů App Service.</span><span class="sxs-lookup"><span data-stu-id="5d31e-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="5d31e-133">Další informace najdete v tématu [streamování protokolů Azure](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="5d31e-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="5d31e-134">Další prostředí</span><span class="sxs-lookup"><span data-stu-id="5d31e-134">Other environments</span></span>

<span data-ttu-id="5d31e-135">Pokud je aplikace nasazená do jiného prostředí (například Docker, Kubernetes nebo Windows), najdete <xref:fundamentals/logging/index> Další informace o tom, jak nakonfigurovat zprostředkovatele protokolování vhodné pro prostředí.</span><span class="sxs-lookup"><span data-stu-id="5d31e-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="5d31e-136">Protokolování klienta JavaScript</span><span class="sxs-lookup"><span data-stu-id="5d31e-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="5d31e-137">Protokoly na straně klienta můžou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="5d31e-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="5d31e-138">**Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="5d31e-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="5d31e-139">Při použití klienta JavaScriptu můžete nakonfigurovat možnosti protokolování pomocí `configureLogging` metody na `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="5d31e-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="5d31e-140">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="5d31e-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="5d31e-141">V následující tabulce jsou uvedeny úrovně protokolu dostupné pro klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="5d31e-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="5d31e-142">Nastavením úrovně protokolu na jednu z těchto hodnot povolíte protokolování na této úrovni a všechny úrovně nad ním v tabulce.</span><span class="sxs-lookup"><span data-stu-id="5d31e-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="5d31e-143">Úroveň</span><span class="sxs-lookup"><span data-stu-id="5d31e-143">Level</span></span> | <span data-ttu-id="5d31e-144">Popis</span><span class="sxs-lookup"><span data-stu-id="5d31e-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="5d31e-145">Nejsou protokolovány žádné zprávy.</span><span class="sxs-lookup"><span data-stu-id="5d31e-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="5d31e-146">Zprávy indikující selhání v celé aplikaci.</span><span class="sxs-lookup"><span data-stu-id="5d31e-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="5d31e-147">Zprávy indikující selhání aktuální operace.</span><span class="sxs-lookup"><span data-stu-id="5d31e-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="5d31e-148">Zprávy, které indikují méně závažnou chybu.</span><span class="sxs-lookup"><span data-stu-id="5d31e-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="5d31e-149">Informační zprávy.</span><span class="sxs-lookup"><span data-stu-id="5d31e-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="5d31e-150">Diagnostické zprávy užitečné pro ladění.</span><span class="sxs-lookup"><span data-stu-id="5d31e-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="5d31e-151">Velmi podrobné diagnostické zprávy navržené pro diagnostiku konkrétních problémů.</span><span class="sxs-lookup"><span data-stu-id="5d31e-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="5d31e-152">Po nakonfigurování podrobností se protokoly zapíší do konzoly prohlížeče (nebo standardního výstupu v aplikaci NodeJS).</span><span class="sxs-lookup"><span data-stu-id="5d31e-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="5d31e-153">Pokud chcete odesílat protokoly do vlastního systému protokolování, můžete poskytnout JavaScriptový objekt implementující `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="5d31e-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="5d31e-154">Jedinou metodou, kterou je třeba implementovat, je `log` , která přebírá úroveň události a zprávu spojenou s událostí.</span><span class="sxs-lookup"><span data-stu-id="5d31e-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="5d31e-155">Například:</span><span class="sxs-lookup"><span data-stu-id="5d31e-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="5d31e-156">Protokolování klienta .NET</span><span class="sxs-lookup"><span data-stu-id="5d31e-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="5d31e-157">Protokoly na straně klienta můžou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="5d31e-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="5d31e-158">**Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="5d31e-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="5d31e-159">Chcete-li získat protokoly z klienta rozhraní .NET, můžete použít `ConfigureLogging` metodu na `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5d31e-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="5d31e-160">To funguje stejným způsobem jako `ConfigureLogging` metoda na `WebHostBuilder` a `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="5d31e-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="5d31e-161">Můžete nakonfigurovat stejné poskytovatele protokolování, které používáte v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5d31e-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="5d31e-162">Je však nutné ručně nainstalovat a povolit balíčky NuGet pro jednotlivé zprostředkovatele protokolování.</span><span class="sxs-lookup"><span data-stu-id="5d31e-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="5d31e-163">Protokolování konzoly</span><span class="sxs-lookup"><span data-stu-id="5d31e-163">Console logging</span></span>

<span data-ttu-id="5d31e-164">Aby bylo možné povolit protokolování konzoly, přidejte balíček [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) .</span><span class="sxs-lookup"><span data-stu-id="5d31e-164">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="5d31e-165">Pak použijte `AddConsole` metodu ke konfiguraci protokolovacího nástroje konzoly:</span><span class="sxs-lookup"><span data-stu-id="5d31e-165">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="5d31e-166">Protokolování výstupního okna ladění</span><span class="sxs-lookup"><span data-stu-id="5d31e-166">Debug output window logging</span></span>

<span data-ttu-id="5d31e-167">Můžete také nakonfigurovat protokoly pro přechod do okna **výstup** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="5d31e-167">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="5d31e-168">Nainstalujte balíček [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) a použijte `AddDebug` metodu:</span><span class="sxs-lookup"><span data-stu-id="5d31e-168">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="5d31e-169">Další zprostředkovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="5d31e-169">Other logging providers</span></span>

SignalR<span data-ttu-id="5d31e-170">podporuje jiné poskytovatele protokolování, jako je Serilog, SEQ, NLog nebo jakýkoli jiný systém protokolování, který se integruje s `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="5d31e-170"> supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="5d31e-171">Pokud váš systém protokolování poskytuje `ILoggerProvider` , můžete ho zaregistrovat pomocí `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="5d31e-171">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="5d31e-172">Podrobnost ovládacího prvku</span><span class="sxs-lookup"><span data-stu-id="5d31e-172">Control verbosity</span></span>

<span data-ttu-id="5d31e-173">Pokud se přihlašujete z jiných míst v aplikaci, Změna výchozí úrovně na `Debug` může být příliš podrobná.</span><span class="sxs-lookup"><span data-stu-id="5d31e-173">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="5d31e-174">Pomocí filtru můžete nakonfigurovat úroveň protokolování pro SignalR protokoly.</span><span class="sxs-lookup"><span data-stu-id="5d31e-174">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="5d31e-175">To lze provést v kódu, podobně jako na serveru:</span><span class="sxs-lookup"><span data-stu-id="5d31e-175">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="5d31e-176">Trasování sítě</span><span class="sxs-lookup"><span data-stu-id="5d31e-176">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="5d31e-177">Trasování sítě obsahuje úplný obsah každé zprávy odesílané vaší aplikací.</span><span class="sxs-lookup"><span data-stu-id="5d31e-177">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="5d31e-178">**Nikdy** nezveřejňujte nezpracované síťové trasování z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="5d31e-178">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="5d31e-179">Pokud narazíte na problém, trasování sítě může někdy poskytnout spoustu užitečných informací.</span><span class="sxs-lookup"><span data-stu-id="5d31e-179">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="5d31e-180">To je užitečné hlavně v případě, že se chystáte zaslat problém do souboru sledování problémů.</span><span class="sxs-lookup"><span data-stu-id="5d31e-180">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="5d31e-181">Shromáždění trasování sítě pomocí Fiddler (upřednostňovaná možnost)</span><span class="sxs-lookup"><span data-stu-id="5d31e-181">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="5d31e-182">Tato metoda se používá pro všechny aplikace.</span><span class="sxs-lookup"><span data-stu-id="5d31e-182">This method works for all apps.</span></span>

<span data-ttu-id="5d31e-183">Fiddler je vysoce výkonný nástroj pro shromažďování trasování HTTP.</span><span class="sxs-lookup"><span data-stu-id="5d31e-183">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="5d31e-184">Nainstalujte si ho z [Telerik.com/Fiddler](https://www.telerik.com/fiddler), spusťte ho a pak spusťte aplikaci a pokuste se problém reprodukování.</span><span class="sxs-lookup"><span data-stu-id="5d31e-184">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="5d31e-185">Fiddler je k dispozici pro Windows a pro macOS a Linux jsou k dispozici beta verze.</span><span class="sxs-lookup"><span data-stu-id="5d31e-185">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="5d31e-186">Pokud se připojujete pomocí protokolu HTTPS, je potřeba provést několik kroků navíc, abyste zajistili, že Fiddler dokáže dešifrovat přenosy HTTPS.</span><span class="sxs-lookup"><span data-stu-id="5d31e-186">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="5d31e-187">Další podrobnosti najdete v [dokumentaci k Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="5d31e-187">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="5d31e-188">Po shromáždění trasování můžete trasování exportovat výběrem možnosti **soubor**  >  **Uložit**  >  **všechny relace** z řádku nabídek.</span><span class="sxs-lookup"><span data-stu-id="5d31e-188">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Exportují se všechny relace z Fiddler.](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="5d31e-190">Shromažďovat trasování sítě pomocí tcpdump (jenom macOS a Linux)</span><span class="sxs-lookup"><span data-stu-id="5d31e-190">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="5d31e-191">Tato metoda se používá pro všechny aplikace.</span><span class="sxs-lookup"><span data-stu-id="5d31e-191">This method works for all apps.</span></span>

<span data-ttu-id="5d31e-192">Nezpracované trasování TCP můžete shromažďovat pomocí tcpdump spuštěním následujícího příkazu z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="5d31e-192">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="5d31e-193">Pokud se zobrazí chyba oprávnění, může být nutné, abyste museli být `root` nebo začínat příkazem `sudo` .</span><span class="sxs-lookup"><span data-stu-id="5d31e-193">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="5d31e-194">Nahraďte `[interface]` síťovým rozhraním, na kterém chcete zachytit.</span><span class="sxs-lookup"><span data-stu-id="5d31e-194">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="5d31e-195">Obvykle se jedná o něco podobného `/dev/eth0` (pro standardní rozhraní sítě Ethernet) nebo `/dev/lo0` (pro přenosy v localhost).</span><span class="sxs-lookup"><span data-stu-id="5d31e-195">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="5d31e-196">Další informace najdete na `tcpdump` stránce muž v hostitelském systému.</span><span class="sxs-lookup"><span data-stu-id="5d31e-196">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="5d31e-197">Shromáždění trasování sítě v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="5d31e-197">Collect a network trace in the browser</span></span>

<span data-ttu-id="5d31e-198">Tato metoda funguje pouze pro aplikace založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="5d31e-198">This method only works for browser-based apps.</span></span>

<span data-ttu-id="5d31e-199">Většina prohlížečů Vývojářské nástroje mít kartu síť, která umožňuje zachytit síťovou aktivitu mezi prohlížečem a serverem.</span><span class="sxs-lookup"><span data-stu-id="5d31e-199">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="5d31e-200">Tato trasování ale neobsahují zprávy protokolu WebSocket a události odeslané serverem.</span><span class="sxs-lookup"><span data-stu-id="5d31e-200">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="5d31e-201">Pokud používáte tyto přenosy, je lepší přístup pomocí nástroje, jako je například Fiddler nebo TcpDump (popsané níže).</span><span class="sxs-lookup"><span data-stu-id="5d31e-201">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="5d31e-202">Microsoft Edge a Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="5d31e-202">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="5d31e-203">(Pokyny jsou pro aplikaci Edge i Internet Explorer stejné.)</span><span class="sxs-lookup"><span data-stu-id="5d31e-203">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="5d31e-204">Stisknutím klávesy F12 otevřete nástroje pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="5d31e-204">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="5d31e-205">Klikněte na kartu síť.</span><span class="sxs-lookup"><span data-stu-id="5d31e-205">Click the Network Tab</span></span>
3. <span data-ttu-id="5d31e-206">Aktualizujte stránku (Pokud je to potřeba) a reprodukování problému.</span><span class="sxs-lookup"><span data-stu-id="5d31e-206">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="5d31e-207">Kliknutím na ikonu Uložit na panelu nástrojů exportujte trasování jako soubor "HAR":</span><span class="sxs-lookup"><span data-stu-id="5d31e-207">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Ikona uložit na kartě síť nástrojů Microsoft Edge pro vývoj](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="5d31e-209">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="5d31e-209">Google Chrome</span></span>

1. <span data-ttu-id="5d31e-210">Stisknutím klávesy F12 otevřete nástroje pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="5d31e-210">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="5d31e-211">Klikněte na kartu síť.</span><span class="sxs-lookup"><span data-stu-id="5d31e-211">Click the Network Tab</span></span>
3. <span data-ttu-id="5d31e-212">Aktualizujte stránku (Pokud je to potřeba) a reprodukování problému.</span><span class="sxs-lookup"><span data-stu-id="5d31e-212">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="5d31e-213">Klikněte pravým tlačítkem na libovolné místo v seznamu požadavků a vyberte Uložit jako HAR s obsahem:</span><span class="sxs-lookup"><span data-stu-id="5d31e-213">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Možnost Uložit jako HAR s obsahem na kartě síť Google Chrome dev Tools](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="5d31e-215">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="5d31e-215">Mozilla Firefox</span></span>

1. <span data-ttu-id="5d31e-216">Stisknutím klávesy F12 otevřete nástroje pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="5d31e-216">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="5d31e-217">Klikněte na kartu síť.</span><span class="sxs-lookup"><span data-stu-id="5d31e-217">Click the Network Tab</span></span>
3. <span data-ttu-id="5d31e-218">Aktualizujte stránku (Pokud je to potřeba) a reprodukování problému.</span><span class="sxs-lookup"><span data-stu-id="5d31e-218">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="5d31e-219">Klikněte pravým tlačítkem na libovolné místo v seznamu požadavků a vyberte Uložit vše jako HAR.</span><span class="sxs-lookup"><span data-stu-id="5d31e-219">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Možnost Uložit vše jako HAR na kartě síť Mozilla Firefox nástroje pro vývojáře](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="5d31e-221">Připojení diagnostických souborů k problémům GitHubu</span><span class="sxs-lookup"><span data-stu-id="5d31e-221">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="5d31e-222">Diagnostické soubory můžete k problémům s GitHubem připojit tak, že je přejmenujete, aby měly `.txt` rozšíření a pak je přetáhnete na problém.</span><span class="sxs-lookup"><span data-stu-id="5d31e-222">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="5d31e-223">Do problému GitHubu prosím nevložíme obsah souborů protokolu ani trasování sítě.</span><span class="sxs-lookup"><span data-stu-id="5d31e-223">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="5d31e-224">Tyto protokoly a trasování můžou být poměrně velké a GitHub je obvykle ořízne.</span><span class="sxs-lookup"><span data-stu-id="5d31e-224">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Přetahování souborů protokolů na problém GitHubu](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="5d31e-226">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5d31e-226">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
