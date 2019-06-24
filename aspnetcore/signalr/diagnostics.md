---
title: Protokolování a Diagnostika v knihovně SignalR technologie ASP.NET Core
author: anurse
description: Zjistěte, jak shromažďovat diagnostické z vaší aplikace SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 06/19/2019
uid: signalr/diagnostics
ms.openlocfilehash: 69dbd057b3dcadeb3ca5d94ede1234530fb447db
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313700"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="b7064-103">Protokolování a Diagnostika v knihovně SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7064-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="b7064-104">Podle [Andrew Stanton sestry](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="b7064-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="b7064-105">Tento článek obsahuje pokyny pro shromažďování diagnostiky z vaší aplikace ASP.NET Core SignalR k řešení potíží.</span><span class="sxs-lookup"><span data-stu-id="b7064-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="b7064-106">Protokolování na straně serveru</span><span class="sxs-lookup"><span data-stu-id="b7064-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="b7064-107">Protokoly na straně serveru může obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7064-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="b7064-108">**Nikdy** odeslání nezpracovaných protokolů z produkčních aplikací na veřejných fórech jako GitHub.</span><span class="sxs-lookup"><span data-stu-id="b7064-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b7064-109">Protože SignalR je součástí ASP.NET Core, používá ASP.NET Core protokolování systému.</span><span class="sxs-lookup"><span data-stu-id="b7064-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="b7064-110">Ve výchozí konfiguraci SignalR protokoly velmi málo informací, ale to může nakonfigurovat.</span><span class="sxs-lookup"><span data-stu-id="b7064-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="b7064-111">Naleznete v dokumentaci [ASP.NET Core protokolování](xref:fundamentals/logging/index#configuration) podrobné informace o konfiguraci protokolování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7064-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="b7064-112">Funkce SignalR používá dvě kategorie protokolovacího nástroje:</span><span class="sxs-lookup"><span data-stu-id="b7064-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="b7064-113">`Microsoft.AspNetCore.SignalR` &ndash; související s protokoly centra protokolů aktivace rozbočovače, volání metod a dalších aktivit související s centrem.</span><span class="sxs-lookup"><span data-stu-id="b7064-113">`Microsoft.AspNetCore.SignalR` &ndash; for logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="b7064-114">`Microsoft.AspNetCore.Http.Connections` &ndash; pro protokoly související s přenosy, jako jsou protokoly Websocket, dlouhé dotazování a Server-Sent události a nízké úrovně infrastruktuře SignalR.</span><span class="sxs-lookup"><span data-stu-id="b7064-114">`Microsoft.AspNetCore.Http.Connections` &ndash; for logs related to transports such as WebSockets, Long Polling and Server-Sent Events and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="b7064-115">Pokud chcete povolit podrobné protokoly ze systému SignalR, nakonfigurujte oba předchozí předpon `Debug` úrovni v vaše *appsettings.json* souboru tak, že přidáte následující položky, které chcete `LogLevel` v dílčím oddílu `Logging`:</span><span class="sxs-lookup"><span data-stu-id="b7064-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="b7064-116">Můžete to taky nakonfigurovat v kódu ve vaší `CreateWebHostBuilder` metody:</span><span class="sxs-lookup"><span data-stu-id="b7064-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="b7064-117">Pokud nepoužíváte konfiguraci na základě JSON, nastavte následující hodnoty konfigurace v konfiguraci systému:</span><span class="sxs-lookup"><span data-stu-id="b7064-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="b7064-118">Dokumentaci k systému konfigurace a určit, jak zadat vnořené konfigurační hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b7064-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="b7064-119">Při použití proměnných prostředí, například dvě `_` znaky se používají místo `:` (například `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span><span class="sxs-lookup"><span data-stu-id="b7064-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="b7064-120">Doporučujeme použít `Debug` úroveň při shromažďování podrobnější diagnostiku pro aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b7064-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="b7064-121">`Trace` Úroveň vytváří velmi nízké úrovně diagnostiky a je málokdy potřeba diagnostikovat problémy ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b7064-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="b7064-122">Zobrazení protokolů na straně serveru</span><span class="sxs-lookup"><span data-stu-id="b7064-122">Access server-side logs</span></span>

<span data-ttu-id="b7064-123">Jak získat přístup k serverové protokoly, závisí na prostředí, ve kterém spouštíte.</span><span class="sxs-lookup"><span data-stu-id="b7064-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="b7064-124">Jako konzolové aplikace mimo službu IIS</span><span class="sxs-lookup"><span data-stu-id="b7064-124">As a console app outside IIS</span></span>

<span data-ttu-id="b7064-125">Pokud máte spuštěný v konzolové aplikaci, [protokolovací nástroj konzoly](xref:fundamentals/logging/index#console-provider) by měl být ve výchozím nastavení povolené.</span><span class="sxs-lookup"><span data-stu-id="b7064-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="b7064-126">Funkce SignalR protokoly se zobrazí v konzole.</span><span class="sxs-lookup"><span data-stu-id="b7064-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="b7064-127">V rámci služby IIS Express ze sady Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7064-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="b7064-128">Visual Studio zobrazí ve výstupu protokolu v **výstup** okna.</span><span class="sxs-lookup"><span data-stu-id="b7064-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="b7064-129">Vyberte **Webový Server ASP.NET Core** rozevírací seznam možností.</span><span class="sxs-lookup"><span data-stu-id="b7064-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="b7064-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b7064-130">Azure App Service</span></span>

<span data-ttu-id="b7064-131">Povolit **protokolování aplikace (systém souborů)** možnost **diagnostické protokoly** části portálu služby Azure App Service a nakonfigurovat **úroveň** k `Verbose`.</span><span class="sxs-lookup"><span data-stu-id="b7064-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="b7064-132">Protokoly by měly mít k dispozici na **streamování protokolů** služby a v protokolech systému souborů služby App Service.</span><span class="sxs-lookup"><span data-stu-id="b7064-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="b7064-133">Další informace najdete v tématu [streamování protokolů Azure](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="b7064-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="b7064-134">Další prostředí</span><span class="sxs-lookup"><span data-stu-id="b7064-134">Other environments</span></span>

<span data-ttu-id="b7064-135">Pokud je aplikace nasazena do jiného prostředí (například Docker, Kubernetes nebo služby Windows), přečtěte si téma <xref:fundamentals/logging/index> Další informace o tom, jak konfigurovat protokolování zprostředkovatele vhodná pro prostředí.</span><span class="sxs-lookup"><span data-stu-id="b7064-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="b7064-136">Protokolování javascriptový klient</span><span class="sxs-lookup"><span data-stu-id="b7064-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="b7064-137">Protokoly na straně klienta mohou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7064-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="b7064-138">**Nikdy** odeslání nezpracovaných protokolů z produkčních aplikací na veřejných fórech jako GitHub.</span><span class="sxs-lookup"><span data-stu-id="b7064-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b7064-139">Při použití Javascriptového klienta, můžete nakonfigurovat pomocí možnosti protokolování `configureLogging` metodu na `HubConnectionBuilder`:</span><span class="sxs-lookup"><span data-stu-id="b7064-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="b7064-140">Chcete-li zakázat protokolování úplně, zadejte `signalR.LogLevel.None` v `configureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="b7064-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b7064-141">Následující tabulka uvádí dostupné úrovně protokolu klientovi JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b7064-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="b7064-142">Nastavení úrovně protokolu na jednu z těchto hodnot povolí protokolování na této úrovni ve všech úrovních nad ním v tabulce.</span><span class="sxs-lookup"><span data-stu-id="b7064-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="b7064-143">Level</span><span class="sxs-lookup"><span data-stu-id="b7064-143">Level</span></span> | <span data-ttu-id="b7064-144">Popis</span><span class="sxs-lookup"><span data-stu-id="b7064-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="b7064-145">Jsou zaznamenány žádné zprávy.</span><span class="sxs-lookup"><span data-stu-id="b7064-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="b7064-146">Zprávy, které indikují chybu celé aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7064-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="b7064-147">Zprávy, které indikují chybu aktuální operaci.</span><span class="sxs-lookup"><span data-stu-id="b7064-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="b7064-148">Zprávy, které označují méně závažné potíže.</span><span class="sxs-lookup"><span data-stu-id="b7064-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="b7064-149">Informační zprávy.</span><span class="sxs-lookup"><span data-stu-id="b7064-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="b7064-150">Diagnostické zprávy je užitečné pro ladění.</span><span class="sxs-lookup"><span data-stu-id="b7064-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="b7064-151">Velmi podrobné diagnostické zprávy, které jsou navržené pro diagnostiku specifické problémy.</span><span class="sxs-lookup"><span data-stu-id="b7064-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="b7064-152">Jakmile nakonfigurujete úroveň podrobností, protokoly se zapíšou do konzoly prohlížeče (nebo standardní výstup v aplikaci NodeJS).</span><span class="sxs-lookup"><span data-stu-id="b7064-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="b7064-153">Pokud chcete odeslat protokoly do vlastního protokolování systému, můžete poskytnout implementaci objektu jazyka JavaScript `ILogger` rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b7064-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="b7064-154">Je jedinou metodou, kterou je potřeba implementovat `log`, který by úroveň události a zprávy přidružené k události.</span><span class="sxs-lookup"><span data-stu-id="b7064-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="b7064-155">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b7064-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="b7064-156">.NET client protokolování</span><span class="sxs-lookup"><span data-stu-id="b7064-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="b7064-157">Protokoly na straně klienta mohou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7064-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="b7064-158">**Nikdy** odeslání nezpracovaných protokolů z produkčních aplikací na veřejných fórech jako GitHub.</span><span class="sxs-lookup"><span data-stu-id="b7064-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b7064-159">Pokud chcete získat protokoly z klienta .NET, můžete použít `ConfigureLogging` metodu na `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b7064-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="b7064-160">Tento postup funguje stejným způsobem jako `ConfigureLogging` metoda `WebHostBuilder` a `HostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b7064-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="b7064-161">Můžete nakonfigurovat stejní poskytovatelé protokolování, které můžete použít v ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7064-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="b7064-162">Ale musíte ručně nainstalovat a povolit protokolování jednotlivých poskytovatelů balíčky NuGet.</span><span class="sxs-lookup"><span data-stu-id="b7064-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="b7064-163">Protokolování konzoly</span><span class="sxs-lookup"><span data-stu-id="b7064-163">Console logging</span></span>

<span data-ttu-id="b7064-164">Chcete-li povolit protokolování konzoly, přidejte [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) balíčku.</span><span class="sxs-lookup"><span data-stu-id="b7064-164">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="b7064-165">Potom použijte `AddConsole` metoda konfigurace protokolovací nástroj konzoly:</span><span class="sxs-lookup"><span data-stu-id="b7064-165">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="b7064-166">Protokolování okna výstup ladění</span><span class="sxs-lookup"><span data-stu-id="b7064-166">Debug output window logging</span></span>

<span data-ttu-id="b7064-167">Můžete taky nakonfigurovat protokoly a přejděte **výstup** okna v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b7064-167">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="b7064-168">Instalace [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) balíček a použít `AddDebug` metody:</span><span class="sxs-lookup"><span data-stu-id="b7064-168">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="b7064-169">Ostatní zprostředkovatelé protokolování</span><span class="sxs-lookup"><span data-stu-id="b7064-169">Other logging providers</span></span>

<span data-ttu-id="b7064-170">Funkce SignalR podporuje jiných poskytovatelů protokolování, jako je například Serilog, Seq, NLog nebo jakémkoli jiném systému protokolování, která se integruje s `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="b7064-170">SignalR supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="b7064-171">Pokud váš systém protokolování poskytuje `ILoggerProvider`, můžete ho zaregistrovat `AddProvider`:</span><span class="sxs-lookup"><span data-stu-id="b7064-171">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="b7064-172">Podrobnosti ovládacího prvku</span><span class="sxs-lookup"><span data-stu-id="b7064-172">Control verbosity</span></span>

<span data-ttu-id="b7064-173">Pokud se přihlašujete z jiných míst ve své aplikaci, změna výchozí úrovně do `Debug` může být příliš podrobné.</span><span class="sxs-lookup"><span data-stu-id="b7064-173">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="b7064-174">Filtr můžete nakonfigurovat úroveň protokolování pro protokoly SignalR.</span><span class="sxs-lookup"><span data-stu-id="b7064-174">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="b7064-175">To můžete udělat v kódu, téměř stejným způsobem jako na serveru:</span><span class="sxs-lookup"><span data-stu-id="b7064-175">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="b7064-176">Trasování sítě</span><span class="sxs-lookup"><span data-stu-id="b7064-176">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="b7064-177">Trasování sítě obsahuje úplný obsah všechny zprávy odeslané aplikací.</span><span class="sxs-lookup"><span data-stu-id="b7064-177">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="b7064-178">**Nikdy** odeslání nezpracovaná síťových trasování z produkčních aplikací na veřejných fórech jako GitHub.</span><span class="sxs-lookup"><span data-stu-id="b7064-178">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b7064-179">Pokud narazíte na problém, trasování sítě někdy poskytnout velké množství užitečných informací.</span><span class="sxs-lookup"><span data-stu-id="b7064-179">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="b7064-180">To je zvlášť užitečné, pokud se chystáte založit problém na našem sledování problémů.</span><span class="sxs-lookup"><span data-stu-id="b7064-180">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="b7064-181">Shromažďovat trasování pomocí fiddleru provedete (upřednostňovanou možnost ověřování) v síti</span><span class="sxs-lookup"><span data-stu-id="b7064-181">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="b7064-182">Tato metoda se dá použít pro všechny aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7064-182">This method works for all apps.</span></span>

<span data-ttu-id="b7064-183">Fiddler je velmi výkonný nástroj pro shromažďování trasování protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7064-183">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="b7064-184">Nainstalujte ji z [telerik.com/fiddler](https://www.telerik.com/fiddler), spusťte jej a pak spusťte aplikaci a reprodukujte problém.</span><span class="sxs-lookup"><span data-stu-id="b7064-184">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="b7064-185">Fiddler je k dispozici pro Windows a jsou beta verze pro macOS a Linux.</span><span class="sxs-lookup"><span data-stu-id="b7064-185">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="b7064-186">Pokud se připojíte pomocí protokolu HTTPS, existuje několik kroků navíc zajistěte, aby že Fiddler můžete dešifrování provozu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b7064-186">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="b7064-187">Další podrobnosti najdete v tématu [dokumentace k aplikaci Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="b7064-187">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="b7064-188">Jakmile jste shromážděné trasování, můžete to taky trasování výběrem **souboru** > **Uložit** > **všechny relace** z řádku nabídek.</span><span class="sxs-lookup"><span data-stu-id="b7064-188">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Export všech relacích z Fiddleru](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="b7064-190">Shromažďovat trasování v síti s tcpdump (macOS a Linux jenom)</span><span class="sxs-lookup"><span data-stu-id="b7064-190">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="b7064-191">Tato metoda se dá použít pro všechny aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7064-191">This method works for all apps.</span></span>

<span data-ttu-id="b7064-192">Můžete shromažďovat nezpracovaná TCP trasování pomocí tcpdump spuštěním následujícího příkazu z příkazového prostředí.</span><span class="sxs-lookup"><span data-stu-id="b7064-192">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="b7064-193">Musíte být `root` nebo příkazu u předpony `sudo` Pokud dojde k chybě oprávnění:</span><span class="sxs-lookup"><span data-stu-id="b7064-193">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="b7064-194">Nahraďte `[interface]` se síťovým rozhraním, kterou chcete zachytit na.</span><span class="sxs-lookup"><span data-stu-id="b7064-194">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="b7064-195">To je obvykle něco jako `/dev/eth0` (pro standardní rozhraní sítě Ethernet) nebo `/dev/lo0` (pro přenos localhost).</span><span class="sxs-lookup"><span data-stu-id="b7064-195">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="b7064-196">Další informace najdete v tématu `tcpdump` man stránku v systému hostitele.</span><span class="sxs-lookup"><span data-stu-id="b7064-196">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="b7064-197">Shromažďovat trasování sítě v prohlížeči</span><span class="sxs-lookup"><span data-stu-id="b7064-197">Collect a network trace in the browser</span></span>

<span data-ttu-id="b7064-198">Tato metoda funguje jenom pro aplikace založené na prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="b7064-198">This method only works for browser-based apps.</span></span>

<span data-ttu-id="b7064-199">Většina nástrojů pro vývojáře prohlížeče mít kartu "Sítě", která umožňuje zaznamenat síťové aktivity mezi prohlížečem a serveru.</span><span class="sxs-lookup"><span data-stu-id="b7064-199">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="b7064-200">Toto trasování však nezahrnují zprávy protokolu WebSocket a Server-Sent událostí.</span><span class="sxs-lookup"><span data-stu-id="b7064-200">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="b7064-201">Pokud používáte tyto přenosy, pomocí nástroje, jako jsou nástroje Fiddler nebo TcpDump (popsaných níže) není lepším řešením.</span><span class="sxs-lookup"><span data-stu-id="b7064-201">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="b7064-202">Microsoft Edge a Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="b7064-202">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="b7064-203">(Pokyny jsou stejné pro Edge a Internet Explorer)</span><span class="sxs-lookup"><span data-stu-id="b7064-203">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="b7064-204">Stisknutím klávesy F12 otevřete Nástroje pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="b7064-204">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="b7064-205">Klikněte na kartu síť</span><span class="sxs-lookup"><span data-stu-id="b7064-205">Click the Network Tab</span></span>
3. <span data-ttu-id="b7064-206">Aktualizujte stránku (v případě potřeby) a reprodukujte problém</span><span class="sxs-lookup"><span data-stu-id="b7064-206">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="b7064-207">Klikněte na ikonu Uložit na panelu nástrojů pro export trasování jako soubor "HAR":</span><span class="sxs-lookup"><span data-stu-id="b7064-207">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Uložení ikonu na vývoj pro Microsoft Edge nástroje síťové karty](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="b7064-209">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="b7064-209">Google Chrome</span></span>

1. <span data-ttu-id="b7064-210">Stisknutím klávesy F12 otevřete Nástroje pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="b7064-210">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="b7064-211">Klikněte na kartu síť</span><span class="sxs-lookup"><span data-stu-id="b7064-211">Click the Network Tab</span></span>
3. <span data-ttu-id="b7064-212">Aktualizujte stránku (v případě potřeby) a reprodukujte problém</span><span class="sxs-lookup"><span data-stu-id="b7064-212">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="b7064-213">Klikněte pravým tlačítkem myši klikněte na libovolné místo v seznamu požadavků a zvolte možnost "Uložit jako HAR s obsahem":</span><span class="sxs-lookup"><span data-stu-id="b7064-213">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Možnost "Uložit jako HAR s obsahem" Google Chrome Dev Tools síťové kartě](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="b7064-215">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="b7064-215">Mozilla Firefox</span></span>

1. <span data-ttu-id="b7064-216">Stisknutím klávesy F12 otevřete Nástroje pro vývojáře</span><span class="sxs-lookup"><span data-stu-id="b7064-216">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="b7064-217">Klikněte na kartu síť</span><span class="sxs-lookup"><span data-stu-id="b7064-217">Click the Network Tab</span></span>
3. <span data-ttu-id="b7064-218">Aktualizujte stránku (v případě potřeby) a reprodukujte problém</span><span class="sxs-lookup"><span data-stu-id="b7064-218">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="b7064-219">Klikněte pravým tlačítkem myši klikněte na libovolné místo v seznamu požadavků a zvolte možnost "Uložit všechny jako HAR"</span><span class="sxs-lookup"><span data-stu-id="b7064-219">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Možnost "Uložit vše jako HAR" Mozilla Firefox Dev Tools síťové kartě](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="b7064-221">Připojit soubory diagnostiky problémů Githubu</span><span class="sxs-lookup"><span data-stu-id="b7064-221">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="b7064-222">Soubory diagnostiky můžete připojit na problémy Githubu je přejmenováním mají `.txt` rozšíření a pak přetažení je k problému.</span><span class="sxs-lookup"><span data-stu-id="b7064-222">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="b7064-223">Prosím není vložte obsah souborů protokolu nebo trasování sítě do problém na Githubu.</span><span class="sxs-lookup"><span data-stu-id="b7064-223">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="b7064-224">Tyto protokoly a trasování může mít poměrně značnou a Githubu je obvykle zkrátí.</span><span class="sxs-lookup"><span data-stu-id="b7064-224">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Přetahování souborů protokolů do problém na Githubu](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="b7064-226">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b7064-226">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
