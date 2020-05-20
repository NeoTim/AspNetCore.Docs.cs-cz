---
title: Protokolování a diagnostika v gRPC v .NET
author: jamesnk
description: Naučte se shromažďovat diagnostiku z vaší aplikace gRPC v .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/diagnostics
ms.openlocfilehash: 33b2ee29830cd3012ff791c949c3a7c23a2e98c7
ms.sourcegitcommit: 16b3abec1ed70f9a206f0cfa7cf6404eebaf693d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2020
ms.locfileid: "83444344"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="3b80b-103">Protokolování a diagnostika v gRPC v .NET</span><span class="sxs-lookup"><span data-stu-id="3b80b-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="3b80b-104">Od [James Newton – král](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="3b80b-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="3b80b-105">Tento článek poskytuje pokyny pro shromažďování diagnostických informací z aplikace gRPC, které vám pomůžou při řešení problémů.</span><span class="sxs-lookup"><span data-stu-id="3b80b-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="3b80b-106">Probíraná témata zahrnují:</span><span class="sxs-lookup"><span data-stu-id="3b80b-106">Topics covered include:</span></span>

* <span data-ttu-id="3b80b-107">**Protokolování** strukturovaných protokolů zapsaných do [protokolování .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="3b80b-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="3b80b-108"><xref:Microsoft.Extensions.Logging.ILogger>používá v aplikačních architekturách k zápisu protokolů a uživatelů pro vlastní protokolování v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b80b-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="3b80b-109">**Trasování** – události související s operací napsanou pomocí `DiaganosticSource` a `Activity` .</span><span class="sxs-lookup"><span data-stu-id="3b80b-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="3b80b-110">Trasování ze zdroje diagnostiky se běžně používají ke shromažďování telemetrie aplikací pomocí knihoven, jako jsou [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) a [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span><span class="sxs-lookup"><span data-stu-id="3b80b-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="3b80b-111">**Metriky** – reprezentace míry dat v časových intervalech, například požadavků za sekundu.</span><span class="sxs-lookup"><span data-stu-id="3b80b-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="3b80b-112">Metriky jsou vydávány pomocí `EventCounter` nástroje a mohou být pozorovány pomocí nástroje příkazového řádku [dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) nebo s [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span><span class="sxs-lookup"><span data-stu-id="3b80b-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="3b80b-113">Protokolování</span><span class="sxs-lookup"><span data-stu-id="3b80b-113">Logging</span></span>

<span data-ttu-id="3b80b-114">gRPC Services a klient gRPC zapisují protokoly pomocí [protokolování .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="3b80b-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="3b80b-115">Protokoly jsou vhodné ke spuštění, když potřebujete ladit neočekávané chování ve vašich aplikacích.</span><span class="sxs-lookup"><span data-stu-id="3b80b-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="3b80b-116">protokolování služeb gRPC Services</span><span class="sxs-lookup"><span data-stu-id="3b80b-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="3b80b-117">Protokoly na straně serveru můžou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b80b-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="3b80b-118">**Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="3b80b-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="3b80b-119">Vzhledem k tomu, že jsou služby gRPC hostované na ASP.NET Core, používá systém protokolování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3b80b-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="3b80b-120">Ve výchozí konfiguraci gRPC protokoluje velmi málo informací, ale může se nakonfigurovat.</span><span class="sxs-lookup"><span data-stu-id="3b80b-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="3b80b-121">Podrobnosti o konfiguraci ASP.NET Core protokolování najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index#configuration) .</span><span class="sxs-lookup"><span data-stu-id="3b80b-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="3b80b-122">gRPC přidá do kategorie protokoly `Grpc` .</span><span class="sxs-lookup"><span data-stu-id="3b80b-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="3b80b-123">Pokud chcete povolit podrobné protokoly z gRPC, nakonfigurujte `Grpc` předpony na `Debug` úroveň v souboru *appSettings. JSON* přidáním následujících položek do `LogLevel` dílčí části v `Logging` :</span><span class="sxs-lookup"><span data-stu-id="3b80b-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="3b80b-124">Můžete ho také nakonfigurovat v *Startup.cs* pomocí `ConfigureLogging` :</span><span class="sxs-lookup"><span data-stu-id="3b80b-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="3b80b-125">Pokud nepoužíváte konfiguraci založenou na formátu JSON, nastavte v konfiguračním systému následující hodnotu konfigurace:</span><span class="sxs-lookup"><span data-stu-id="3b80b-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="3b80b-126">Informace o tom, jak zadat hodnoty vnořených konfigurací, najdete v dokumentaci ke konfiguračnímu systému.</span><span class="sxs-lookup"><span data-stu-id="3b80b-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="3b80b-127">Například při použití proměnných prostředí `_` jsou místo `:` (například) použity dva znaky (například `Logging__LogLevel__Grpc` ).</span><span class="sxs-lookup"><span data-stu-id="3b80b-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="3b80b-128">Tuto úroveň doporučujeme používat `Debug` při shromažďování podrobnějších diagnostických nástrojů pro vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b80b-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="3b80b-129">`Trace`Úroveň přináší vysoce nízkou diagnostiku a je zřídka nutná pro diagnostiku problémů ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="3b80b-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="3b80b-130">Ukázka výstupu protokolování</span><span class="sxs-lookup"><span data-stu-id="3b80b-130">Sample logging output</span></span>

<span data-ttu-id="3b80b-131">Tady je příklad výstupu konzoly na `Debug` úrovni služby gRPC:</span><span class="sxs-lookup"><span data-stu-id="3b80b-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a><span data-ttu-id="3b80b-132">Přístup k protokolům na straně serveru</span><span class="sxs-lookup"><span data-stu-id="3b80b-132">Access server-side logs</span></span>

<span data-ttu-id="3b80b-133">Způsob přístupu ke protokolům na straně serveru závisí na prostředí, ve kterém používáte.</span><span class="sxs-lookup"><span data-stu-id="3b80b-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="3b80b-134">Jako Konzolová aplikace</span><span class="sxs-lookup"><span data-stu-id="3b80b-134">As a console app</span></span>

<span data-ttu-id="3b80b-135">Pokud používáte konzolovou aplikaci, měl by být ve výchozím nastavení povolený [protokolovací nástroj konzoly](xref:fundamentals/logging/index#console) .</span><span class="sxs-lookup"><span data-stu-id="3b80b-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="3b80b-136">protokoly gRPC se zobrazí v konzole nástroje.</span><span class="sxs-lookup"><span data-stu-id="3b80b-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="3b80b-137">Další prostředí</span><span class="sxs-lookup"><span data-stu-id="3b80b-137">Other environments</span></span>

<span data-ttu-id="3b80b-138">Pokud je aplikace nasazená do jiného prostředí (například Docker, Kubernetes nebo Windows), najdete <xref:fundamentals/logging/index> Další informace o tom, jak nakonfigurovat zprostředkovatele protokolování vhodné pro prostředí.</span><span class="sxs-lookup"><span data-stu-id="3b80b-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="3b80b-139">protokolování klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="3b80b-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="3b80b-140">Protokoly na straně klienta můžou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b80b-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="3b80b-141">**Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="3b80b-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="3b80b-142">Chcete-li získat protokoly z klienta rozhraní .NET, můžete nastavit `GrpcChannelOptions.LoggerFactory` vlastnost při vytvoření kanálu klienta.</span><span class="sxs-lookup"><span data-stu-id="3b80b-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="3b80b-143">Pokud voláte službu gRPC z aplikace ASP.NET Core, bude objekt pro vytváření protokolovacího nástroje možné přeložit z injektáže závislosti (DI):</span><span class="sxs-lookup"><span data-stu-id="3b80b-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="3b80b-144">Alternativním způsobem, jak povolit protokolování klienta, je použít objekt pro vytváření klientů [gRPC](xref:grpc/clientfactory) k vytvoření klienta.</span><span class="sxs-lookup"><span data-stu-id="3b80b-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="3b80b-145">Klient gRPC zaregistrovaný ve službě klient Factory a vyřešený z typu DI bude automaticky používat nakonfigurované protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="3b80b-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="3b80b-146">Pokud vaše aplikace nepoužívá DI, můžete vytvořit novou `ILoggerFactory` instanci pomocí [LoggerFactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span><span class="sxs-lookup"><span data-stu-id="3b80b-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="3b80b-147">Pokud chcete získat přístup k této metodě, přidejte do své aplikace balíček [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) .</span><span class="sxs-lookup"><span data-stu-id="3b80b-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="3b80b-148">obory protokolu klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="3b80b-148">gRPC client log scopes</span></span>

<span data-ttu-id="3b80b-149">Klient gRPC přidá [Rozsah protokolování](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) do protokolů provedených během volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="3b80b-149">The gRPC client adds a [logging scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="3b80b-150">Obor obsahuje metadata související s voláním gRPC:</span><span class="sxs-lookup"><span data-stu-id="3b80b-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="3b80b-151">**GrpcMethodType** – typ metody gRPC.</span><span class="sxs-lookup"><span data-stu-id="3b80b-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="3b80b-152">Možné hodnoty jsou názvy ze `Grpc.Core.MethodType` výčtu, např. unární.</span><span class="sxs-lookup"><span data-stu-id="3b80b-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="3b80b-153">**GrpcUri** – relativní identifikátor URI metody gRPC, např./Greet. Pozdrav/SayHellos</span><span class="sxs-lookup"><span data-stu-id="3b80b-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="3b80b-154">Ukázka výstupu protokolování</span><span class="sxs-lookup"><span data-stu-id="3b80b-154">Sample logging output</span></span>

<span data-ttu-id="3b80b-155">Tady je příklad výstupu konzoly na `Debug` úrovni klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="3b80b-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a><span data-ttu-id="3b80b-156">Trasování</span><span class="sxs-lookup"><span data-stu-id="3b80b-156">Tracing</span></span>

<span data-ttu-id="3b80b-157">gRPC Services a klient gRPC poskytují informace o voláních gRPC pomocí [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) a [aktivity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span><span class="sxs-lookup"><span data-stu-id="3b80b-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="3b80b-158">.NET gRPC používá aktivitu k reprezentaci volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="3b80b-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="3b80b-159">Události trasování se zapisují do zdroje diagnostiky na začátku a zastavení aktivity volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="3b80b-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="3b80b-160">Trasování nezachycuje informace o tom, kdy se zprávy odesílají po dobu platnosti volání streamování gRPC.</span><span class="sxs-lookup"><span data-stu-id="3b80b-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="3b80b-161">trasování služby gRPC</span><span class="sxs-lookup"><span data-stu-id="3b80b-161">gRPC service tracing</span></span>

<span data-ttu-id="3b80b-162">služby gRPC se hostují na ASP.NET Core, které hlásí události týkající se příchozích požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="3b80b-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="3b80b-163">do existující diagnostiky požadavků HTTP, kterou ASP.NET Core poskytuje, se přidají metadata specifická pro gRPC.</span><span class="sxs-lookup"><span data-stu-id="3b80b-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="3b80b-164">Název zdroje diagnostiky je `Microsoft.AspNetCore` .</span><span class="sxs-lookup"><span data-stu-id="3b80b-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="3b80b-165">Název aktivity je `Microsoft.AspNetCore.Hosting.HttpRequestIn` .</span><span class="sxs-lookup"><span data-stu-id="3b80b-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="3b80b-166">Název metody gRPC vyvolané voláním gRPC se přidá jako značka s názvem `grpc.method` .</span><span class="sxs-lookup"><span data-stu-id="3b80b-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="3b80b-167">Stavový kód volání gRPC, když se dokončí, se přidá jako značka s názvem `grpc.status_code` .</span><span class="sxs-lookup"><span data-stu-id="3b80b-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="3b80b-168">trasování klienta gRPC</span><span class="sxs-lookup"><span data-stu-id="3b80b-168">gRPC client tracing</span></span>

<span data-ttu-id="3b80b-169">Klient .NET gRPC používá `HttpClient` k zajištění volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="3b80b-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="3b80b-170">I když `HttpClient` zapisuje diagnostické události, klient .NET gRPC poskytuje vlastní diagnostický zdroj, aktivitu a události, aby bylo možné shromáždit informace o volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="3b80b-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="3b80b-171">Název zdroje diagnostiky je `Grpc.Net.Client` .</span><span class="sxs-lookup"><span data-stu-id="3b80b-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="3b80b-172">Název aktivity je `Grpc.Net.Client.GrpcOut` .</span><span class="sxs-lookup"><span data-stu-id="3b80b-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="3b80b-173">Název metody gRPC vyvolané voláním gRPC se přidá jako značka s názvem `grpc.method` .</span><span class="sxs-lookup"><span data-stu-id="3b80b-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="3b80b-174">Stavový kód volání gRPC, když se dokončí, se přidá jako značka s názvem `grpc.status_code` .</span><span class="sxs-lookup"><span data-stu-id="3b80b-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="3b80b-175">Shromažďování trasování</span><span class="sxs-lookup"><span data-stu-id="3b80b-175">Collecting tracing</span></span>

<span data-ttu-id="3b80b-176">Nejjednodušší způsob, jak použít, `DiagnosticSource` je nakonfigurovat v aplikaci knihovnu telemetrie, jako je například [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) nebo [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) .</span><span class="sxs-lookup"><span data-stu-id="3b80b-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="3b80b-177">Knihovna zpracuje informace o gRPC volání souběžně jiné telemetrie aplikací.</span><span class="sxs-lookup"><span data-stu-id="3b80b-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="3b80b-178">Trasování lze zobrazit ve spravované službě, jako je například Application Insights, nebo můžete zvolit spuštění vlastního systému distribuované vektorizace.</span><span class="sxs-lookup"><span data-stu-id="3b80b-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="3b80b-179">OpenTelemetry podporuje export dat trasování do [Jaeger](https://www.jaegertracing.io/) a [Zipkin](https://zipkin.io/).</span><span class="sxs-lookup"><span data-stu-id="3b80b-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="3b80b-180">`DiagnosticSource`může spotřebovávat události trasování v kódu pomocí `DiagnosticListener` .</span><span class="sxs-lookup"><span data-stu-id="3b80b-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="3b80b-181">Informace o naslouchání diagnostickému zdroji pomocí kódu naleznete v [uživatelské příručce DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span><span class="sxs-lookup"><span data-stu-id="3b80b-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="3b80b-182">Knihovny telemetrie momentálně nezachycují gRPC specifickou `Grpc.Net.Client.GrpcOut` telemetrii.</span><span class="sxs-lookup"><span data-stu-id="3b80b-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="3b80b-183">Práci pro zlepšení knihoven telemetrie, které zachytí toto trasování, pokračuje.</span><span class="sxs-lookup"><span data-stu-id="3b80b-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="3b80b-184">Metriky</span><span class="sxs-lookup"><span data-stu-id="3b80b-184">Metrics</span></span>

<span data-ttu-id="3b80b-185">Metrika je reprezentace datových měr v časových intervalech, například požadavků za sekundu.</span><span class="sxs-lookup"><span data-stu-id="3b80b-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="3b80b-186">Data metrik umožňují sledovat stav aplikace na vysoké úrovni.</span><span class="sxs-lookup"><span data-stu-id="3b80b-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="3b80b-187">Metriky .NET gRPC jsou vydávány pomocí `EventCounter` .</span><span class="sxs-lookup"><span data-stu-id="3b80b-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="3b80b-188">metriky služby gRPC</span><span class="sxs-lookup"><span data-stu-id="3b80b-188">gRPC service metrics</span></span>

<span data-ttu-id="3b80b-189">metriky serveru gRPC jsou hlášeny ve `Grpc.AspNetCore.Server` zdroji události.</span><span class="sxs-lookup"><span data-stu-id="3b80b-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="3b80b-190">Name</span><span class="sxs-lookup"><span data-stu-id="3b80b-190">Name</span></span>                      | <span data-ttu-id="3b80b-191">Popis</span><span class="sxs-lookup"><span data-stu-id="3b80b-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="3b80b-192">Celkový počet volání</span><span class="sxs-lookup"><span data-stu-id="3b80b-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="3b80b-193">Aktuální volání</span><span class="sxs-lookup"><span data-stu-id="3b80b-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="3b80b-194">Neúspěšná volání celkem</span><span class="sxs-lookup"><span data-stu-id="3b80b-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="3b80b-195">Byl překročen konečný termín počtu volání.</span><span class="sxs-lookup"><span data-stu-id="3b80b-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="3b80b-196">Celkový počet odeslaných zpráv</span><span class="sxs-lookup"><span data-stu-id="3b80b-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="3b80b-197">Celkový počet přijatých zpráv</span><span class="sxs-lookup"><span data-stu-id="3b80b-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="3b80b-198">Celkový počet volání neimplementovaných</span><span class="sxs-lookup"><span data-stu-id="3b80b-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="3b80b-199">ASP.NET Core také poskytuje vlastní metriky pro `Microsoft.AspNetCore.Hosting` zdroj události.</span><span class="sxs-lookup"><span data-stu-id="3b80b-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="3b80b-200">gRPC klientské metriky</span><span class="sxs-lookup"><span data-stu-id="3b80b-200">gRPC client metrics</span></span>

<span data-ttu-id="3b80b-201">metriky klienta gRPC jsou hlášeny ve `Grpc.Net.Client` zdroji události.</span><span class="sxs-lookup"><span data-stu-id="3b80b-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="3b80b-202">Name</span><span class="sxs-lookup"><span data-stu-id="3b80b-202">Name</span></span>                      | <span data-ttu-id="3b80b-203">Popis</span><span class="sxs-lookup"><span data-stu-id="3b80b-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="3b80b-204">Celkový počet volání</span><span class="sxs-lookup"><span data-stu-id="3b80b-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="3b80b-205">Aktuální volání</span><span class="sxs-lookup"><span data-stu-id="3b80b-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="3b80b-206">Neúspěšná volání celkem</span><span class="sxs-lookup"><span data-stu-id="3b80b-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="3b80b-207">Byl překročen konečný termín počtu volání.</span><span class="sxs-lookup"><span data-stu-id="3b80b-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="3b80b-208">Celkový počet odeslaných zpráv</span><span class="sxs-lookup"><span data-stu-id="3b80b-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="3b80b-209">Celkový počet přijatých zpráv</span><span class="sxs-lookup"><span data-stu-id="3b80b-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="3b80b-210">Sledovat metriky</span><span class="sxs-lookup"><span data-stu-id="3b80b-210">Observe metrics</span></span>

<span data-ttu-id="3b80b-211">[dotnet – čítače](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) jsou nástrojem pro monitorování výkonu, který slouží ke sledování stavu ad-hoc a prvotnímu šetření výkonu na nejvyšší úrovni.</span><span class="sxs-lookup"><span data-stu-id="3b80b-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="3b80b-212">Monitorujte aplikaci .NET pomocí `Grpc.AspNetCore.Server` nebo `Grpc.Net.Client` jako názvu poskytovatele.</span><span class="sxs-lookup"><span data-stu-id="3b80b-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

<span data-ttu-id="3b80b-213">Dalším způsobem, jak sledovat metriky gRPC, je zachytit data čítače pomocí [balíčku Microsoft. ApplicationInsights. EventCounterCollector](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)Application Insights.</span><span class="sxs-lookup"><span data-stu-id="3b80b-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="3b80b-214">Po nastavení Application Insights shromažďuje běžné čítače .NET za běhu.</span><span class="sxs-lookup"><span data-stu-id="3b80b-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="3b80b-215">čítače gRPC se ve výchozím nastavení neshromažďují, ale App Insights se dá [přizpůsobit tak, aby zahrnoval další čítače](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span><span class="sxs-lookup"><span data-stu-id="3b80b-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="3b80b-216">Zadejte gRPC čítače pro Application Insights, který chcete shromažďovat v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="3b80b-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a><span data-ttu-id="3b80b-217">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3b80b-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
