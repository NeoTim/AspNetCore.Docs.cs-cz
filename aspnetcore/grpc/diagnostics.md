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
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Protokolování a diagnostika v gRPC na .NET

Podle [James Newton-King](https://twitter.com/jamesnk)

Tento článek obsahuje pokyny pro shromažďování diagnostiky z aplikace gRPC, která vám pomůže vyřešit problémy. Probíraná témata zahrnují:

* **Protokolování** - strukturované protokoly zapsané do [protokolování jádra .NET](xref:fundamentals/logging/index). <xref:Microsoft.Extensions.Logging.ILogger>používá rozhraní aplikace pro zápis protokolů a uživatelé pro jejich vlastní protokolování v aplikaci.
* **Trasování** - Události související s `DiaganosticSource` `Activity`operací napsanou pomocí a . Trasování ze zdroje diagnostiky se běžně používají ke shromažďování telemetrie aplikací knihovnami, jako jsou [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) a [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).
* **Metriky** – reprezentace dat měří v časových intervalech, například požadavky za sekundu. Metriky jsou vydávány pomocí `EventCounter` a lze je pozorovat pomocí nástroje příkazového řádku [čítače dotnet](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) nebo pomocí application [insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).

## <a name="logging"></a>Protokolování

gRPC a protokoly zápisu klienta gRPC pomocí [protokolování .NET Core](xref:fundamentals/logging/index). Protokoly jsou vhodné místo pro spuštění, když potřebujete ladit neočekávané chování ve vašich aplikacích.

### <a name="grpc-services-logging"></a>protokolování služeb gRPC

> [!WARNING]
> Protokoly na straně serveru mohou obsahovat citlivé informace z vaší aplikace. **Nikdy nezveřejňujte** nezpracované protokoly z produkčních aplikací na veřejná fóra, jako je GitHub.

Vzhledem k tomu, že služby gRPC jsou hostovány na ASP.NET Core, používá systém protokolování ASP.NET Core. Ve výchozí konfiguraci gRPC zaznamenává velmi málo informací, ale to může být nakonfigurováno. Podrobnosti o konfiguraci ASP.NET protokolování jádra naleznete v dokumentaci [k protokolování jádra ASP.NET.](xref:fundamentals/logging/index#configuration)

gRPC přidává protokoly `Grpc` v rámci kategorie. Chcete-li povolit podrobné protokoly `Grpc` z gRPC, nakonfigurujte předpony na `Debug` úroveň v `LogLevel` souboru `Logging` *appsettings.json* přidáním následujících položek do pododdílu v aplikaci :

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

Můžete také nakonfigurovat *Startup.cs* v `ConfigureLogging`Startup.cs pomocí :

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Pokud nepoužíváte konfiguraci založenou na službě JSON, nastavte v konfiguračním systému následující hodnotu konfigurace:

* `Logging:LogLevel:Grpc` = `Debug`

V dokumentaci konfiguračního systému zjistěte, jak zadat vnořené hodnoty konfigurace. Například při použití proměnných `_` prostředí se místo `:` `Logging__LogLevel__Grpc`(například) používají dva znaky.

Doporučujeme použít `Debug` úroveň při shromažďování podrobnější diagnostiky pro vaši aplikaci. Úroveň `Trace` vytváří velmi nízkou úroveň diagnostiky a je zřídka potřeba diagnostikovat problémy ve vaší aplikaci.

#### <a name="sample-logging-output"></a>Ukázkový výstup protokolování

Zde je příklad výstupu konzoly na `Debug` úrovni služby gRPC:

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

Způsob přístupu k protokolům na straně serveru závisí na prostředí, ve kterém používáte.

#### <a name="as-a-console-app"></a>Jako konzolová aplikace

Pokud používáte v konzolové aplikaci, měl by být [protokolování konzoly](xref:fundamentals/logging/index#console-provider) ve výchozím nastavení povoleno. v konzoli se zobrazí protokoly gRPC.

#### <a name="other-environments"></a>Ostatní prostředí

Pokud je aplikace nasazená do jiného prostředí (například Docker, Kubernetes nebo Windows Service), přečtěte si <xref:fundamentals/logging/index> další informace o tom, jak nakonfigurovat poskytovatele protokolování vhodné pro prostředí.

### <a name="grpc-client-logging"></a>gRPC protokolování klienta

> [!WARNING]
> Protokoly na straně klienta mohou obsahovat citlivé informace z vaší aplikace. **Nikdy nezveřejňujte** nezpracované protokoly z produkčních aplikací na veřejná fóra, jako je GitHub.

Chcete-li získat protokoly z klienta `GrpcChannelOptions.LoggerFactory` .NET, můžete nastavit vlastnost při vytvoření kanálu klienta. Pokud voláte službu gRPC z aplikace ASP.NET Core, pak lze továrnu protokolování vyřešit z vkládání závislostí (DI):

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Alternativní způsob, jak povolit protokolování klienta je použít [gRPC client factory](xref:grpc/clientfactory) k vytvoření klienta. GRPC klient registrovaný v továrně klienta a vyřešený z DI bude automaticky používat nakonfigurované protokolování aplikace.

Pokud vaše aplikace nepoužívá DI, můžete vytvořit `ILoggerFactory` novou instanci pomocí [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*). Chcete-li získat přístup k této metodě, přidejte do aplikace balíček [Microsoft.Extensions.Logging.](https://www.nuget.org/packages/microsoft.extensions.logging/)

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>gRPC log obory klienta

Klient gRPC přidá do protokolů během volání gRPC [rozsah protokolů.](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) Obor má metadata související s voláním gRPC:

* **GrpcMethodType** - Typ metody gRPC. Možné hodnoty jsou `Grpc.Core.MethodType` názvy z výčtu, např.
* **GrpcUri** - Relativní URI metody gRPC, např. Greeter/SayHellos

#### <a name="sample-logging-output"></a>Ukázkový výstup protokolování

Zde je příklad výstupu konzoly na `Debug` úrovni klienta gRPC:

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

gRPC služby a klient gRPC poskytují informace o volání gRPC pomocí [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) a [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).

* .NET gRPC používá aktivitu k reprezentaci volání gRPC.
* Trasovací události jsou zapsány do diagnostického zdroje na začátku a zastavení aktivity volání gRPC.
* Trasování nezachycuje informace o tom, kdy jsou zprávy odesílány po celou dobu životnosti volání streamování gRPC.

### <a name="grpc-service-tracing"></a>gRPC trasování služby

gRPC služby jsou hostovány na ASP.NET Core, který hlásí události o příchozích HTTP požadavcích. gRPC specifická metadata jsou přidána do existující diagnostiky požadavků HTTP, kterou poskytuje ASP.NET Core.

* Název diagnostického `Microsoft.AspNetCore`zdroje je .
* Název aktivity je `Microsoft.AspNetCore.Hosting.HttpRequestIn`.
  * Název metody gRPC vyvolané voláním gRPC je přidán jako `grpc.method`značka s názvem .
  * Stavový kód volání gRPC po jeho dokončení je přidán `grpc.status_code`jako značka s názvem .

### <a name="grpc-client-tracing"></a>gRPC trasování klienta

Klient gRPC rozhraní `HttpClient` používá k volání gRPC. Přestože `HttpClient` zapisuje diagnostické události, klient .NET gRPC poskytuje vlastní diagnostický zdroj, aktivitu a události, takže lze shromažďovat úplné informace o volání gRPC.

* Název diagnostického `Grpc.Net.Client`zdroje je .
* Název aktivity je `Grpc.Net.Client.GrpcOut`.
  * Název metody gRPC vyvolané voláním gRPC je přidán jako `grpc.method`značka s názvem .
  * Stavový kód volání gRPC po jeho dokončení je přidán `grpc.status_code`jako značka s názvem .

### <a name="collecting-tracing"></a>Sběr trasování

Nejjednodušší způsob použití `DiagnosticSource` je konfigurace telemetrické knihovny, jako jsou Application [Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) nebo [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) ve vaší aplikaci. Knihovna bude zpracovávat informace o volání gRPC spolu s další telemetrií aplikace.

Trasování lze zobrazit ve spravované službě, jako je Application Insights, nebo můžete spustit vlastní distribuovaný systém trasování. OpenTelemetry podporuje export dat trasování do [Jaegera](https://www.jaegertracing.io/) a [Zipkina](https://zipkin.io/).

`DiagnosticSource`může využívat trasovací události `DiagnosticListener`v kódu pomocí . Informace o naslouchání diagnostickému zdroji s kódem naleznete v [uživatelské příručce DiagnosticSource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).

> [!NOTE]
> Telemetrické knihovny aktuálně nezachycují `Grpc.Net.Client.GrpcOut` telemetrii specifickou gRPC. Práce na zlepšení telemetrie knihovny zachycující toto trasování probíhá.

## <a name="metrics"></a>Metriky

Metriky je reprezentace dat měří v časových intervalech, například požadavky za sekundu. Data metrik umožňuje sledovat stav aplikace na vysoké úrovni. .NET gRPC metriky jsou `EventCounter`vydávány pomocí .

### <a name="grpc-service-metrics"></a>gRPC metriky služeb

gRPC server metriky `Grpc.AspNetCore.Server` jsou hlášeny na zdroj událostí.

| Name (Název)                      | Popis                   |
| --------------------------|-------------------------------|
| `total-calls`             | Celkový počet volání                   |
| `current-calls`           | Aktuální hovory                 |
| `calls-failed`            | Celkový počet neúspěšných volání            |
| `calls-deadline-exceeded` | Byl překročen celkový termín volání |
| `messages-sent`           | Celkový počet odeslaných zpráv           |
| `messages-received`       | Celkový počet přijatých zpráv       |
| `calls-unimplemented`     | Celkový počet neimplementovaných volání     |

ASP.NET Core také poskytuje své `Microsoft.AspNetCore.Hosting` vlastní metriky na zdroj událostí.

### <a name="grpc-client-metrics"></a>gRPC klientské metriky

gRPC klientské metriky `Grpc.Net.Client` jsou hlášeny na zdroj událostí.

| Name (Název)                      | Popis                   |
| --------------------------|-------------------------------|
| `total-calls`             | Celkový počet volání                   |
| `current-calls`           | Aktuální hovory                 |
| `calls-failed`            | Celkový počet neúspěšných volání            |
| `calls-deadline-exceeded` | Byl překročen celkový termín volání |
| `messages-sent`           | Celkový počet odeslaných zpráv           |
| `messages-received`       | Celkový počet přijatých zpráv       |

### <a name="observe-metrics"></a>Sledování metrik

[čítače dotnet](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) je nástroj pro sledování stavu ad hoc a šetření výkonu první úrovně. Sledujte aplikaci .NET s názvem `Grpc.AspNetCore.Server` zprostředkovatele nebo `Grpc.Net.Client` jako název zprostředkovatele.

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

Dalším způsobem, jak sledovat metriky gRPC, je zachytit data čítačů pomocí [balíčku Microsoft.ApplicationInsights.EventCounterCollector](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)společnosti Application Insights . Po instalaci application insights shromažďuje běžné čítače .NET za běhu. čítače gRPC nejsou ve výchozím nastavení shromažďovány, ale přehledy aplikací lze [přizpůsobit tak, aby zahrnovaly další čítače](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).

Zadejte čítače gRPC pro insight aplikací, které mají být shromážděna v *Startup.cs*:

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

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
