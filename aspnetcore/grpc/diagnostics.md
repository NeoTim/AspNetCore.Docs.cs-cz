---
title: Protokolování a diagnostika v gRPC v .NET
author: jamesnk
description: Naučte se shromažďovat diagnostiku z vaší aplikace gRPC v .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/diagnostics
ms.openlocfilehash: 7d2da20d04b93ebcd16fb58a4b74b5b67d37bd72
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722920"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Protokolování a diagnostika v gRPC v .NET

Od [James Newton – král](https://twitter.com/jamesnk)

Tento článek poskytuje pokyny pro shromažďování diagnostických informací z aplikace gRPC, které vám pomůžou při řešení problémů. Probíraná témata zahrnují:

* **Protokolování** strukturovaných protokolů zapsaných do [protokolování .NET Core](xref:fundamentals/logging/index). <xref:Microsoft.Extensions.Logging.ILogger> používá v aplikačních architekturách k zápisu protokolů a uživatelů pro vlastní protokolování v aplikaci.
* **Trasování** – události související s operací napsanou pomocí `DiaganosticSource` a `Activity` . Trasování ze zdroje diagnostiky se běžně používají ke shromažďování telemetrie aplikací pomocí knihoven, jako jsou [Application Insights](/azure/azure-monitor/app/asp-net-core) a [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).
* **Metriky** – reprezentace míry dat v časových intervalech, například požadavků za sekundu. Metriky jsou vydávány pomocí `EventCounter` nástroje a mohou být pozorovány pomocí nástroje příkazového řádku [dotnet-Counters](/dotnet/core/diagnostics/dotnet-counters) nebo s [Application Insights](/azure/azure-monitor/app/eventcounters).

## <a name="logging"></a>Protokolování

gRPC Services a klient gRPC zapisují protokoly pomocí [protokolování .NET Core](xref:fundamentals/logging/index). Protokoly jsou vhodné ke spuštění, když potřebujete ladit neočekávané chování ve vašich aplikacích.

### <a name="grpc-services-logging"></a>protokolování služeb gRPC Services

> [!WARNING]
> Protokoly na straně serveru můžou obsahovat citlivé informace z vaší aplikace. **Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.

Vzhledem k tomu, že jsou služby gRPC hostované na ASP.NET Core, používá systém protokolování ASP.NET Core. Ve výchozí konfiguraci gRPC protokoluje velmi málo informací, ale může se nakonfigurovat. Podrobnosti o konfiguraci ASP.NET Core protokolování najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index#configuration) .

gRPC přidá do kategorie protokoly `Grpc` . Pokud chcete povolit podrobné protokoly z gRPC, nakonfigurujte `Grpc` předpony na `Debug` úroveň v *appsettings.jsv* souboru přidáním následujících položek do `LogLevel` dílčí části v `Logging` :

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

Můžete ho také nakonfigurovat v *Startup.cs* pomocí `ConfigureLogging` :

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Pokud nepoužíváte konfiguraci založenou na formátu JSON, nastavte v konfiguračním systému následující hodnotu konfigurace:

* `Logging:LogLevel:Grpc` = `Debug`

Informace o tom, jak zadat hodnoty vnořených konfigurací, najdete v dokumentaci ke konfiguračnímu systému. Například při použití proměnných prostředí `_` jsou místo `:` (například) použity dva znaky (například `Logging__LogLevel__Grpc` ).

Tuto úroveň doporučujeme používat `Debug` při shromažďování podrobnějších diagnostických nástrojů pro vaši aplikaci. `Trace`Úroveň přináší vysoce nízkou diagnostiku a je zřídka nutná pro diagnostiku problémů ve vaší aplikaci.

#### <a name="sample-logging-output"></a>Ukázka výstupu protokolování

Tady je příklad výstupu konzoly na `Debug` úrovni služby gRPC:

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

### <a name="access-server-side-logs"></a>Přístup k protokolům na straně serveru

Způsob přístupu ke protokolům na straně serveru závisí na prostředí, ve kterém používáte.

#### <a name="as-a-console-app"></a>Jako Konzolová aplikace

Pokud používáte konzolovou aplikaci, měl by být ve výchozím nastavení povolený [protokolovací nástroj konzoly](xref:fundamentals/logging/index#console) . protokoly gRPC se zobrazí v konzole nástroje.

#### <a name="other-environments"></a>Další prostředí

Pokud je aplikace nasazená do jiného prostředí (například Docker, Kubernetes nebo Windows), najdete <xref:fundamentals/logging/index> Další informace o tom, jak nakonfigurovat zprostředkovatele protokolování vhodné pro prostředí.

### <a name="grpc-client-logging"></a>protokolování klienta gRPC

> [!WARNING]
> Protokoly na straně klienta můžou obsahovat citlivé informace z vaší aplikace. **Nikdy** nezveřejňujte nezpracované protokoly z produkčních aplikací do veřejných fór, jako je GitHub.

Chcete-li získat protokoly z klienta rozhraní .NET, můžete nastavit `GrpcChannelOptions.LoggerFactory` vlastnost při vytvoření kanálu klienta. Pokud voláte službu gRPC z aplikace ASP.NET Core, bude objekt pro vytváření protokolovacího nástroje možné přeložit z injektáže závislosti (DI):

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Alternativním způsobem, jak povolit protokolování klienta, je použít objekt pro vytváření klientů [gRPC](xref:grpc/clientfactory) k vytvoření klienta. Klient gRPC zaregistrovaný ve službě klient Factory a vyřešený z typu DI bude automaticky používat nakonfigurované protokolování aplikace.

Pokud vaše aplikace nepoužívá DI, můžete vytvořit novou `ILoggerFactory` instanci pomocí [LoggerFactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*). Pokud chcete získat přístup k této metodě, přidejte do své aplikace balíček [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) .

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>obory protokolu klienta gRPC

Klient gRPC přidá [Rozsah protokolování](../fundamentals/logging/index.md#log-scopes) do protokolů provedených během volání gRPC. Obor obsahuje metadata související s voláním gRPC:

* **GrpcMethodType** – typ metody gRPC. Možné hodnoty jsou názvy ze `Grpc.Core.MethodType` výčtu, např. unární.
* **GrpcUri** – relativní identifikátor URI metody gRPC, např./Greet. Pozdrav/SayHellos

#### <a name="sample-logging-output"></a>Ukázka výstupu protokolování

Tady je příklad výstupu konzoly na `Debug` úrovni klienta gRPC:

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

## <a name="tracing"></a>Trasování

gRPC Services a klient gRPC poskytují informace o voláních gRPC pomocí [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) a [aktivity](/dotnet/api/system.diagnostics.activity).

* .NET gRPC používá aktivitu k reprezentaci volání gRPC.
* Události trasování se zapisují do zdroje diagnostiky na začátku a zastavení aktivity volání gRPC.
* Trasování nezachycuje informace o tom, kdy se zprávy odesílají po dobu platnosti volání streamování gRPC.

### <a name="grpc-service-tracing"></a>trasování služby gRPC

služby gRPC se hostují na ASP.NET Core, které hlásí události týkající se příchozích požadavků HTTP. do existující diagnostiky požadavků HTTP, kterou ASP.NET Core poskytuje, se přidají metadata specifická pro gRPC.

* Název zdroje diagnostiky je `Microsoft.AspNetCore` .
* Název aktivity je `Microsoft.AspNetCore.Hosting.HttpRequestIn` .
  * Název metody gRPC vyvolané voláním gRPC se přidá jako značka s názvem `grpc.method` .
  * Stavový kód volání gRPC, když se dokončí, se přidá jako značka s názvem `grpc.status_code` .

### <a name="grpc-client-tracing"></a>trasování klienta gRPC

Klient .NET gRPC používá `HttpClient` k zajištění volání gRPC. I když `HttpClient` zapisuje diagnostické události, klient .NET gRPC poskytuje vlastní diagnostický zdroj, aktivitu a události, aby bylo možné shromáždit informace o volání gRPC.

* Název zdroje diagnostiky je `Grpc.Net.Client` .
* Název aktivity je `Grpc.Net.Client.GrpcOut` .
  * Název metody gRPC vyvolané voláním gRPC se přidá jako značka s názvem `grpc.method` .
  * Stavový kód volání gRPC, když se dokončí, se přidá jako značka s názvem `grpc.status_code` .

### <a name="collecting-tracing"></a>Shromažďování trasování

Nejjednodušší způsob, jak použít, `DiagnosticSource` je nakonfigurovat v aplikaci knihovnu telemetrie, jako je například [Application Insights](/azure/azure-monitor/app/asp-net-core) nebo [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) . Knihovna zpracuje informace o gRPC volání souběžně jiné telemetrie aplikací.

Trasování lze zobrazit ve spravované službě, jako je například Application Insights, nebo můžete zvolit spuštění vlastního systému distribuované vektorizace. OpenTelemetry podporuje export dat trasování do [Jaeger](https://www.jaegertracing.io/) a [Zipkin](https://zipkin.io/).

`DiagnosticSource` může spotřebovávat události trasování v kódu pomocí `DiagnosticListener` . Informace o naslouchání diagnostickému zdroji pomocí kódu naleznete v [uživatelské příručce DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).

> [!NOTE]
> Knihovny telemetrie momentálně nezachycují gRPC specifickou `Grpc.Net.Client.GrpcOut` telemetrii. Práci pro zlepšení knihoven telemetrie, které zachytí toto trasování, pokračuje.

## <a name="metrics"></a>Metriky

Metrika je reprezentace datových měr v časových intervalech, například požadavků za sekundu. Data metrik umožňují sledovat stav aplikace na vysoké úrovni. Metriky .NET gRPC jsou vydávány pomocí `EventCounter` .

### <a name="grpc-service-metrics"></a>metriky služby gRPC

metriky serveru gRPC jsou hlášeny ve `Grpc.AspNetCore.Server` zdroji události.

| Název                      | Popis                   |
| --------------------------|-------------------------------|
| `total-calls`             | Celkový počet volání                   |
| `current-calls`           | Aktuální volání                 |
| `calls-failed`            | Neúspěšná volání celkem            |
| `calls-deadline-exceeded` | Byl překročen konečný termín počtu volání. |
| `messages-sent`           | Celkový počet odeslaných zpráv           |
| `messages-received`       | Celkový počet přijatých zpráv       |
| `calls-unimplemented`     | Celkový počet volání neimplementovaných     |

ASP.NET Core také poskytuje vlastní metriky pro `Microsoft.AspNetCore.Hosting` zdroj události.

### <a name="grpc-client-metrics"></a>gRPC klientské metriky

metriky klienta gRPC jsou hlášeny ve `Grpc.Net.Client` zdroji události.

| Název                      | Popis                   |
| --------------------------|-------------------------------|
| `total-calls`             | Celkový počet volání                   |
| `current-calls`           | Aktuální volání                 |
| `calls-failed`            | Neúspěšná volání celkem            |
| `calls-deadline-exceeded` | Byl překročen konečný termín počtu volání. |
| `messages-sent`           | Celkový počet odeslaných zpráv           |
| `messages-received`       | Celkový počet přijatých zpráv       |

### <a name="observe-metrics"></a>Sledovat metriky

[dotnet – čítače](/dotnet/core/diagnostics/dotnet-counters) jsou nástrojem pro monitorování výkonu, který slouží ke sledování stavu ad-hoc a prvotnímu šetření výkonu na nejvyšší úrovni. Monitorujte aplikaci .NET pomocí `Grpc.AspNetCore.Server` nebo `Grpc.Net.Client` jako názvu poskytovatele.

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

Dalším způsobem, jak sledovat metriky gRPC, je zachytit data čítače pomocí [balíčku Microsoft. ApplicationInsights. EventCounterCollector](/azure/azure-monitor/app/eventcounters)Application Insights. Po nastavení Application Insights shromažďuje běžné čítače .NET za běhu. čítače gRPC se ve výchozím nastavení neshromažďují, ale App Insights se dá [přizpůsobit tak, aby zahrnoval další čítače](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).

Zadejte gRPC čítače pro Application Insights, který chcete shromažďovat v *Startup.cs*:

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

## <a name="additional-resources"></a>Další materiály

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>