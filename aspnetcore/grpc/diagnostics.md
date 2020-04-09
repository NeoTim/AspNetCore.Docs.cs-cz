---
title: Protokolování a diagnostika v gRPC na .NET
author: jamesnk
description: Přečtěte si, jak shromažďovat diagnostiku z aplikace gRPC na rozhraní .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 131144bf7a2c637eb2c1a1d5c54990dd4d429502
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417521"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="b30f0-103">Protokolování a diagnostika v gRPC na .NET</span><span class="sxs-lookup"><span data-stu-id="b30f0-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="b30f0-104">Podle [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="b30f0-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="b30f0-105">Tento článek obsahuje pokyny pro shromažďování diagnostiky z aplikace gRPC, která vám pomůže vyřešit problémy.</span><span class="sxs-lookup"><span data-stu-id="b30f0-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="b30f0-106">Probíraná témata zahrnují:</span><span class="sxs-lookup"><span data-stu-id="b30f0-106">Topics covered include:</span></span>

* <span data-ttu-id="b30f0-107">**Protokolování** - strukturované protokoly zapsané do [protokolování jádra .NET](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="b30f0-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b30f0-108"><xref:Microsoft.Extensions.Logging.ILogger>používá rozhraní aplikace pro zápis protokolů a uživatelé pro jejich vlastní protokolování v aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b30f0-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="b30f0-109">**Trasování** - Události související s `DiaganosticSource` `Activity`operací napsanou pomocí a .</span><span class="sxs-lookup"><span data-stu-id="b30f0-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="b30f0-110">Trasování ze zdroje diagnostiky se běžně používají ke shromažďování telemetrie aplikací knihovnami, jako jsou [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) a [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span><span class="sxs-lookup"><span data-stu-id="b30f0-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="b30f0-111">**Metriky** – reprezentace dat měří v časových intervalech, například požadavky za sekundu.</span><span class="sxs-lookup"><span data-stu-id="b30f0-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="b30f0-112">Metriky jsou vydávány pomocí `EventCounter` a lze je pozorovat pomocí nástroje příkazového řádku [čítače dotnet](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) nebo pomocí application [insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span><span class="sxs-lookup"><span data-stu-id="b30f0-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="b30f0-113">Protokolování</span><span class="sxs-lookup"><span data-stu-id="b30f0-113">Logging</span></span>

<span data-ttu-id="b30f0-114">gRPC a protokoly zápisu klienta gRPC pomocí [protokolování .NET Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="b30f0-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="b30f0-115">Protokoly jsou vhodné místo pro spuštění, když potřebujete ladit neočekávané chování ve vašich aplikacích.</span><span class="sxs-lookup"><span data-stu-id="b30f0-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="b30f0-116">protokolování služeb gRPC</span><span class="sxs-lookup"><span data-stu-id="b30f0-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="b30f0-117">Protokoly na straně serveru mohou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="b30f0-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="b30f0-118">**Nikdy nezveřejňujte** nezpracované protokoly z produkčních aplikací na veřejná fóra, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="b30f0-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b30f0-119">Vzhledem k tomu, že služby gRPC jsou hostovány na ASP.NET Core, používá systém protokolování ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b30f0-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="b30f0-120">Ve výchozí konfiguraci gRPC zaznamenává velmi málo informací, ale to může být nakonfigurováno.</span><span class="sxs-lookup"><span data-stu-id="b30f0-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="b30f0-121">Podrobnosti o konfiguraci ASP.NET protokolování jádra naleznete v dokumentaci [k protokolování jádra ASP.NET.](xref:fundamentals/logging/index#configuration)</span><span class="sxs-lookup"><span data-stu-id="b30f0-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="b30f0-122">gRPC přidává protokoly `Grpc` v rámci kategorie.</span><span class="sxs-lookup"><span data-stu-id="b30f0-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="b30f0-123">Chcete-li povolit podrobné protokoly `Grpc` z gRPC, nakonfigurujte předpony na `Debug` úroveň v `LogLevel` souboru `Logging` *appsettings.json* přidáním následujících položek do pododdílu v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="b30f0-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="b30f0-124">Můžete také nakonfigurovat *Startup.cs* v `ConfigureLogging`Startup.cs pomocí :</span><span class="sxs-lookup"><span data-stu-id="b30f0-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="b30f0-125">Pokud nepoužíváte konfiguraci založenou na službě JSON, nastavte v konfiguračním systému následující hodnotu konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b30f0-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="b30f0-126">V dokumentaci konfiguračního systému zjistěte, jak zadat vnořené hodnoty konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b30f0-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="b30f0-127">Například při použití proměnných `_` prostředí se místo `:` `Logging__LogLevel__Grpc`(například) používají dva znaky.</span><span class="sxs-lookup"><span data-stu-id="b30f0-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="b30f0-128">Doporučujeme použít `Debug` úroveň při shromažďování podrobnější diagnostiky pro vaši aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b30f0-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="b30f0-129">Úroveň `Trace` vytváří velmi nízkou úroveň diagnostiky a je zřídka potřeba diagnostikovat problémy ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b30f0-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="b30f0-130">Ukázkový výstup protokolování</span><span class="sxs-lookup"><span data-stu-id="b30f0-130">Sample logging output</span></span>

<span data-ttu-id="b30f0-131">Zde je příklad výstupu konzoly na `Debug` úrovni služby gRPC:</span><span class="sxs-lookup"><span data-stu-id="b30f0-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

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

### <a name="access-server-side-logs"></a><span data-ttu-id="b30f0-132">Přístup k protokolům na straně serveru</span><span class="sxs-lookup"><span data-stu-id="b30f0-132">Access server-side logs</span></span>

<span data-ttu-id="b30f0-133">Způsob přístupu k protokolům na straně serveru závisí na prostředí, ve kterém používáte.</span><span class="sxs-lookup"><span data-stu-id="b30f0-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="b30f0-134">Jako konzolová aplikace</span><span class="sxs-lookup"><span data-stu-id="b30f0-134">As a console app</span></span>

<span data-ttu-id="b30f0-135">Pokud používáte v konzolové aplikaci, měl by být [protokolování konzoly](xref:fundamentals/logging/index#console-provider) ve výchozím nastavení povoleno.</span><span class="sxs-lookup"><span data-stu-id="b30f0-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="b30f0-136">v konzoli se zobrazí protokoly gRPC.</span><span class="sxs-lookup"><span data-stu-id="b30f0-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="b30f0-137">Ostatní prostředí</span><span class="sxs-lookup"><span data-stu-id="b30f0-137">Other environments</span></span>

<span data-ttu-id="b30f0-138">Pokud je aplikace nasazená do jiného prostředí (například Docker, Kubernetes nebo Windows Service), přečtěte si <xref:fundamentals/logging/index> další informace o tom, jak nakonfigurovat poskytovatele protokolování vhodné pro prostředí.</span><span class="sxs-lookup"><span data-stu-id="b30f0-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="b30f0-139">gRPC protokolování klienta</span><span class="sxs-lookup"><span data-stu-id="b30f0-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="b30f0-140">Protokoly na straně klienta mohou obsahovat citlivé informace z vaší aplikace.</span><span class="sxs-lookup"><span data-stu-id="b30f0-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="b30f0-141">**Nikdy nezveřejňujte** nezpracované protokoly z produkčních aplikací na veřejná fóra, jako je GitHub.</span><span class="sxs-lookup"><span data-stu-id="b30f0-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b30f0-142">Chcete-li získat protokoly z klienta `GrpcChannelOptions.LoggerFactory` .NET, můžete nastavit vlastnost při vytvoření kanálu klienta.</span><span class="sxs-lookup"><span data-stu-id="b30f0-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="b30f0-143">Pokud voláte službu gRPC z aplikace ASP.NET Core, pak lze továrnu protokolování vyřešit z vkládání závislostí (DI):</span><span class="sxs-lookup"><span data-stu-id="b30f0-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="b30f0-144">Alternativní způsob, jak povolit protokolování klienta je použít [gRPC client factory](xref:grpc/clientfactory) k vytvoření klienta.</span><span class="sxs-lookup"><span data-stu-id="b30f0-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="b30f0-145">GRPC klient registrovaný v továrně klienta a vyřešený z DI bude automaticky používat nakonfigurované protokolování aplikace.</span><span class="sxs-lookup"><span data-stu-id="b30f0-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="b30f0-146">Pokud vaše aplikace nepoužívá DI, můžete vytvořit `ILoggerFactory` novou instanci pomocí [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span><span class="sxs-lookup"><span data-stu-id="b30f0-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="b30f0-147">Chcete-li získat přístup k této metodě, přidejte do aplikace balíček [Microsoft.Extensions.Logging.](https://www.nuget.org/packages/microsoft.extensions.logging/)</span><span class="sxs-lookup"><span data-stu-id="b30f0-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="b30f0-148">gRPC log obory klienta</span><span class="sxs-lookup"><span data-stu-id="b30f0-148">gRPC client log scopes</span></span>

<span data-ttu-id="b30f0-149">Klient gRPC přidá do protokolů během volání gRPC [rozsah protokolů.](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes)</span><span class="sxs-lookup"><span data-stu-id="b30f0-149">The gRPC client adds a [logging scope](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="b30f0-150">Obor má metadata související s voláním gRPC:</span><span class="sxs-lookup"><span data-stu-id="b30f0-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="b30f0-151">**GrpcMethodType** - Typ metody gRPC.</span><span class="sxs-lookup"><span data-stu-id="b30f0-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="b30f0-152">Možné hodnoty jsou `Grpc.Core.MethodType` názvy z výčtu, např.</span><span class="sxs-lookup"><span data-stu-id="b30f0-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="b30f0-153">**GrpcUri** - Relativní URI metody gRPC, např. Greeter/SayHellos</span><span class="sxs-lookup"><span data-stu-id="b30f0-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="b30f0-154">Ukázkový výstup protokolování</span><span class="sxs-lookup"><span data-stu-id="b30f0-154">Sample logging output</span></span>

<span data-ttu-id="b30f0-155">Zde je příklad výstupu konzoly na `Debug` úrovni klienta gRPC:</span><span class="sxs-lookup"><span data-stu-id="b30f0-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

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

## <a name="tracing"></a><span data-ttu-id="b30f0-156">Trasování</span><span class="sxs-lookup"><span data-stu-id="b30f0-156">Tracing</span></span>

<span data-ttu-id="b30f0-157">gRPC služby a klient gRPC poskytují informace o volání gRPC pomocí [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) a [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span><span class="sxs-lookup"><span data-stu-id="b30f0-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="b30f0-158">.NET gRPC používá aktivitu k reprezentaci volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="b30f0-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="b30f0-159">Trasovací události jsou zapsány do diagnostického zdroje na začátku a zastavení aktivity volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="b30f0-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="b30f0-160">Trasování nezachycuje informace o tom, kdy jsou zprávy odesílány po celou dobu životnosti volání streamování gRPC.</span><span class="sxs-lookup"><span data-stu-id="b30f0-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="b30f0-161">gRPC trasování služby</span><span class="sxs-lookup"><span data-stu-id="b30f0-161">gRPC service tracing</span></span>

<span data-ttu-id="b30f0-162">gRPC služby jsou hostovány na ASP.NET Core, který hlásí události o příchozích HTTP požadavcích.</span><span class="sxs-lookup"><span data-stu-id="b30f0-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="b30f0-163">gRPC specifická metadata jsou přidána do existující diagnostiky požadavků HTTP, kterou poskytuje ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b30f0-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="b30f0-164">Název diagnostického `Microsoft.AspNetCore`zdroje je .</span><span class="sxs-lookup"><span data-stu-id="b30f0-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="b30f0-165">Název aktivity je `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span><span class="sxs-lookup"><span data-stu-id="b30f0-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="b30f0-166">Název metody gRPC vyvolané voláním gRPC je přidán jako `grpc.method`značka s názvem .</span><span class="sxs-lookup"><span data-stu-id="b30f0-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="b30f0-167">Stavový kód volání gRPC po jeho dokončení je přidán `grpc.status_code`jako značka s názvem .</span><span class="sxs-lookup"><span data-stu-id="b30f0-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="b30f0-168">gRPC trasování klienta</span><span class="sxs-lookup"><span data-stu-id="b30f0-168">gRPC client tracing</span></span>

<span data-ttu-id="b30f0-169">Klient gRPC rozhraní `HttpClient` používá k volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="b30f0-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="b30f0-170">Přestože `HttpClient` zapisuje diagnostické události, klient .NET gRPC poskytuje vlastní diagnostický zdroj, aktivitu a události, takže lze shromažďovat úplné informace o volání gRPC.</span><span class="sxs-lookup"><span data-stu-id="b30f0-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="b30f0-171">Název diagnostického `Grpc.Net.Client`zdroje je .</span><span class="sxs-lookup"><span data-stu-id="b30f0-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="b30f0-172">Název aktivity je `Grpc.Net.Client.GrpcOut`.</span><span class="sxs-lookup"><span data-stu-id="b30f0-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="b30f0-173">Název metody gRPC vyvolané voláním gRPC je přidán jako `grpc.method`značka s názvem .</span><span class="sxs-lookup"><span data-stu-id="b30f0-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="b30f0-174">Stavový kód volání gRPC po jeho dokončení je přidán `grpc.status_code`jako značka s názvem .</span><span class="sxs-lookup"><span data-stu-id="b30f0-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="b30f0-175">Sběr trasování</span><span class="sxs-lookup"><span data-stu-id="b30f0-175">Collecting tracing</span></span>

<span data-ttu-id="b30f0-176">Nejjednodušší způsob použití `DiagnosticSource` je konfigurace telemetrické knihovny, jako jsou Application [Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) nebo [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b30f0-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="b30f0-177">Knihovna bude zpracovávat informace o volání gRPC spolu s další telemetrií aplikace.</span><span class="sxs-lookup"><span data-stu-id="b30f0-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="b30f0-178">Trasování lze zobrazit ve spravované službě, jako je Application Insights, nebo můžete spustit vlastní distribuovaný systém trasování.</span><span class="sxs-lookup"><span data-stu-id="b30f0-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="b30f0-179">OpenTelemetry podporuje export dat trasování do [Jaegera](https://www.jaegertracing.io/) a [Zipkina](https://zipkin.io/).</span><span class="sxs-lookup"><span data-stu-id="b30f0-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="b30f0-180">`DiagnosticSource`může využívat trasovací události `DiagnosticListener`v kódu pomocí .</span><span class="sxs-lookup"><span data-stu-id="b30f0-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="b30f0-181">Informace o naslouchání diagnostickému zdroji s kódem naleznete v [uživatelské příručce DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span><span class="sxs-lookup"><span data-stu-id="b30f0-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="b30f0-182">Telemetrické knihovny aktuálně nezachycují `Grpc.Net.Client.GrpcOut` telemetrii specifickou gRPC.</span><span class="sxs-lookup"><span data-stu-id="b30f0-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="b30f0-183">Práce na zlepšení telemetrie knihovny zachycující toto trasování probíhá.</span><span class="sxs-lookup"><span data-stu-id="b30f0-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="b30f0-184">Metriky</span><span class="sxs-lookup"><span data-stu-id="b30f0-184">Metrics</span></span>

<span data-ttu-id="b30f0-185">Metriky je reprezentace dat měří v časových intervalech, například požadavky za sekundu.</span><span class="sxs-lookup"><span data-stu-id="b30f0-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="b30f0-186">Data metrik umožňuje sledovat stav aplikace na vysoké úrovni.</span><span class="sxs-lookup"><span data-stu-id="b30f0-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="b30f0-187">.NET gRPC metriky jsou `EventCounter`vydávány pomocí .</span><span class="sxs-lookup"><span data-stu-id="b30f0-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="b30f0-188">gRPC metriky služeb</span><span class="sxs-lookup"><span data-stu-id="b30f0-188">gRPC service metrics</span></span>

<span data-ttu-id="b30f0-189">gRPC server metriky `Grpc.AspNetCore.Server` jsou hlášeny na zdroj událostí.</span><span class="sxs-lookup"><span data-stu-id="b30f0-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="b30f0-190">Name (Název)</span><span class="sxs-lookup"><span data-stu-id="b30f0-190">Name</span></span>                      | <span data-ttu-id="b30f0-191">Popis</span><span class="sxs-lookup"><span data-stu-id="b30f0-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="b30f0-192">Celkový počet volání</span><span class="sxs-lookup"><span data-stu-id="b30f0-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="b30f0-193">Aktuální hovory</span><span class="sxs-lookup"><span data-stu-id="b30f0-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="b30f0-194">Celkový počet neúspěšných volání</span><span class="sxs-lookup"><span data-stu-id="b30f0-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="b30f0-195">Byl překročen celkový termín volání</span><span class="sxs-lookup"><span data-stu-id="b30f0-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="b30f0-196">Celkový počet odeslaných zpráv</span><span class="sxs-lookup"><span data-stu-id="b30f0-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="b30f0-197">Celkový počet přijatých zpráv</span><span class="sxs-lookup"><span data-stu-id="b30f0-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="b30f0-198">Celkový počet neimplementovaných volání</span><span class="sxs-lookup"><span data-stu-id="b30f0-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="b30f0-199">ASP.NET Core také poskytuje své `Microsoft.AspNetCore.Hosting` vlastní metriky na zdroj událostí.</span><span class="sxs-lookup"><span data-stu-id="b30f0-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="b30f0-200">gRPC klientské metriky</span><span class="sxs-lookup"><span data-stu-id="b30f0-200">gRPC client metrics</span></span>

<span data-ttu-id="b30f0-201">gRPC klientské metriky `Grpc.Net.Client` jsou hlášeny na zdroj událostí.</span><span class="sxs-lookup"><span data-stu-id="b30f0-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="b30f0-202">Name (Název)</span><span class="sxs-lookup"><span data-stu-id="b30f0-202">Name</span></span>                      | <span data-ttu-id="b30f0-203">Popis</span><span class="sxs-lookup"><span data-stu-id="b30f0-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="b30f0-204">Celkový počet volání</span><span class="sxs-lookup"><span data-stu-id="b30f0-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="b30f0-205">Aktuální hovory</span><span class="sxs-lookup"><span data-stu-id="b30f0-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="b30f0-206">Celkový počet neúspěšných volání</span><span class="sxs-lookup"><span data-stu-id="b30f0-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="b30f0-207">Byl překročen celkový termín volání</span><span class="sxs-lookup"><span data-stu-id="b30f0-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="b30f0-208">Celkový počet odeslaných zpráv</span><span class="sxs-lookup"><span data-stu-id="b30f0-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="b30f0-209">Celkový počet přijatých zpráv</span><span class="sxs-lookup"><span data-stu-id="b30f0-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="b30f0-210">Sledování metrik</span><span class="sxs-lookup"><span data-stu-id="b30f0-210">Observe metrics</span></span>

<span data-ttu-id="b30f0-211">[čítače dotnet](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) je nástroj pro sledování stavu ad hoc a šetření výkonu první úrovně.</span><span class="sxs-lookup"><span data-stu-id="b30f0-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="b30f0-212">Sledujte aplikaci .NET s názvem `Grpc.AspNetCore.Server` zprostředkovatele nebo `Grpc.Net.Client` jako název zprostředkovatele.</span><span class="sxs-lookup"><span data-stu-id="b30f0-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

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

<span data-ttu-id="b30f0-213">Dalším způsobem, jak sledovat metriky gRPC, je zachytit data čítačů pomocí [balíčku Microsoft.ApplicationInsights.EventCounterCollector](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)společnosti Application Insights .</span><span class="sxs-lookup"><span data-stu-id="b30f0-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="b30f0-214">Po instalaci application insights shromažďuje běžné čítače .NET za běhu.</span><span class="sxs-lookup"><span data-stu-id="b30f0-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="b30f0-215">čítače gRPC nejsou ve výchozím nastavení shromažďovány, ale přehledy aplikací lze [přizpůsobit tak, aby zahrnovaly další čítače](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span><span class="sxs-lookup"><span data-stu-id="b30f0-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="b30f0-216">Zadejte čítače gRPC pro insight aplikací, které mají být shromážděna v *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b30f0-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="b30f0-217">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b30f0-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
