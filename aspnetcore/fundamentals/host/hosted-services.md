---
title: Úlohy na pozadí s hostovanými službami v ASP.NET Core
author: rick-anderson
description: Naučte se implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/hosted-services
ms.openlocfilehash: ebc39e7a6869911d464a340caea8eadc93ea72e0
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407198"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Úlohy na pozadí s hostovanými službami v ASP.NET Core

[Jeow li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*. Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní. V tomto tématu najdete tři příklady hostovaných služeb:

* Úloha na pozadí, která běží na časovači.
* Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes). Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection).
* Úlohy na pozadí, které běží sekvenčně.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="worker-service-template"></a>Šablona služby pracovní proces

Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb. Aplikace vytvořená ze šablony pracovní služby určuje sadu SDK pracovního procesu v jeho souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

Použití šablony jako základu pro aplikaci hostované služby:

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a>Balíček

Aplikace založená na šabloně pracovní služby používá `Microsoft.NET.Sdk.Worker` sadu SDK a má explicitní odkaz na balíček na balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) . Podívejte se například na soubor projektu ukázkové aplikace (*BackgroundTasksSample. csproj*).

U webových aplikací, které používají `Microsoft.NET.Sdk.Web` sadu SDK, se na balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) odkazuje implicitně ze sdíleného rozhraní. V souboru projektu aplikace není vyžadován explicitní odkaz na balíček.

## <a name="ihostedservice-interface"></a>Rozhraní IHostedService

<xref:Microsoft.Extensions.Hosting.IHostedService>Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:

* [StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` obsahuje logiku pro spuštění úlohy na pozadí. `StartAsync`se volá *před*:

  * Je nakonfigurovaný kanál zpracování požadavků aplikace ( `Startup.Configure` ).
  * Server se spustí a spustí se [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .

  Výchozí chování se dá změnit tak, aby se hostovaná služba `StartAsync` spouštěla po nakonfigurování kanálu aplikace a `ApplicationStarted` zavolala se. Chcete-li změnit výchozí chování, přidejte hostovanou službu ( `VideosWatcher` v následujícím příkladu) po volání `ConfigureWebHostDefaults` :

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): aktivovaná v případě, že hostitel provádí bezproblémové vypnutí. `StopAsync`obsahuje logiku pro ukončení úlohy na pozadí. Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pro uvolnění jakýchkoli nespravovaných prostředků.

  Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný. Když se na tokenu požaduje zrušení:

  * Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.
  * Všechny metody, které jsou volány v nástroji, `StopAsync` by měly vracet výzvu.

  Úkoly se ale po zrušení žádosti zruší, protože &mdash; volající čeká na dokončení všech úkolů.

  Pokud se aplikace neočekávaně ukončí (například proces aplikace se nezdařil), `StopAsync` nemusí být volána. Proto žádné metody, které jsou volány nebo operací, které jsou prováděny v nástroji, nemusí `StopAsync` nastat.

  Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Při použití obecného hostitele. Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.

Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace. Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` měla by být volána i v případě, že `StopAsync` není volána.

## <a name="backgroundservice-base-class"></a>Základní třída BackgroundService

<xref:Microsoft.Extensions.Hosting.BackgroundService>je základní třídou pro implementaci dlouhého běhu <xref:Microsoft.Extensions.Hosting.IHostedService> .

[Metody ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) se volá za účelem spuštění služby na pozadí. Implementace vrátí <xref:System.Threading.Tasks.Task> , která představuje celou dobu života služby na pozadí. Žádné další služby nejsou spuštěny, dokud [metody ExecuteAsync nebude asynchronní](https://github.com/dotnet/extensions/issues/2149), například voláním `await` . Vyhněte se provádění dlouhého a blokujícího inicializačního fungování v `ExecuteAsync` . Hostitelské bloky v [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) čekají na `ExecuteAsync` dokončení.

Token zrušení se aktivuje, když se zavolá [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) . Vaše implementace `ExecuteAsync` by měla být dokončena okamžitě při vyvolání tokenu zrušení, aby bylo možné službu řádně vypnout. V opačném případě se služba nekorektně ukončí v časovém limitu vypnutí. Další informace najdete v části [rozhraní IHostedService](#ihostedservice-interface) .

## <a name="timed-background-tasks"></a>Časované úlohy na pozadí

Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) . Časovač aktivuje `DoWork` metodu úkolu. Časovač je zakázán `StopAsync` a vyřazen při vyřazení kontejneru služby `Dispose` :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

Nečeká na <xref:System.Threading.Timer> Dokončení předchozích spuštění `DoWork` , takže zobrazený přístup nemusí být vhodný pro každý scénář. [Prolínání. přírůstek](xref:System.Threading.Interlocked.Increment*) se používá ke zvýšení čítače spouštění jako atomické operace, což zajistí, že se více vláken současně neaktualizuje `executionCount` .

Služba je registrována v `IHostBuilder.ConfigureServices` (*program.cs*) s `AddHostedService` metodou rozšíření:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Využívání vymezené služby v úloze na pozadí

Pokud chcete používat [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci [BackgroundService](#backgroundservice-base-class), vytvořte obor. Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.

Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí. V následujícím příkladu:

* Služba je asynchronní. `DoWork`Metoda vrátí `Task` . Pro demonstrační účely je v metodě očekáváno zpoždění deseti sekund `DoWork` .
* <xref:Microsoft.Extensions.Logging.ILogger>Do služby se vloží.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Hostovaná služba vytvoří obor pro rozpoznání služby úlohy na pozadí pro volání `DoWork` metody. `DoWork`Vrátí `Task` , který je očekáván v `ExecuteAsync` :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

Služby jsou zaregistrované v `IHostBuilder.ConfigureServices` (*program.cs*). Hostovaná služba je zaregistrovaná v rámci `AddHostedService` metody rozšíření:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Úlohy na pozadí zařazené do fronty

Fronta úloh na pozadí je založena na rozhraní .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

V následujícím `QueueHostedService` příkladu:

* `BackgroundProcessing`Metoda vrátí hodnotu `Task` , která je očekávána v `ExecuteAsync` .
* Úlohy na pozadí ve frontě se odřadí a spustí v `BackgroundProcessing` .
* Pracovní položky jsou očekávány před zastavením služby `StopAsync` .

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

`MonitorLoop`Služba zpracovává enqueuing úlohy pro hostovanou službu vždy, když `w` je vybraný klíč na vstupním zařízení:

* `IBackgroundTaskQueue`Je vložen do `MonitorLoop` služby.
* `IBackgroundTaskQueue.QueueBackgroundWorkItem`je volána pro zařazení pracovní položky do fronty.
* Pracovní položka simuluje dlouho běžící úlohu na pozadí:
  * Spustí se tři prodlevy 5 sekund ( `Task.Delay` ).
  * `try-catch`Příkaz zachytávání, <xref:System.OperationCanceledException> Pokud je úkol zrušen.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

Služby jsou zaregistrované v `IHostBuilder.ConfigureServices` (*program.cs*). Hostovaná služba je zaregistrovaná v rámci `AddHostedService` metody rozšíření:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

`MonitorLoop`je spuštěn v `Program.Main` :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*. Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní. V tomto tématu najdete tři příklady hostovaných služeb:

* Úloha na pozadí, která běží na časovači.
* Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes). Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .
* Úlohy na pozadí, které běží sekvenčně.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="package"></a>Balíček

Odkaz na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .

## <a name="ihostedservice-interface"></a>Rozhraní IHostedService

Hostované služby implementují <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní. Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:

* [StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` obsahuje logiku pro spuštění úlohy na pozadí. Při použití [webového hostitele](xref:fundamentals/host/web-host) `StartAsync` se volá po spuštění serveru a aktivaci [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) . Při použití [obecného hostitele](xref:fundamentals/host/generic-host) `StartAsync` se před `ApplicationStarted` aktivací volá.

* [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): aktivovaná v případě, že hostitel provádí bezproblémové vypnutí. `StopAsync`obsahuje logiku pro ukončení úlohy na pozadí. Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pro uvolnění jakýchkoli nespravovaných prostředků.

  Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný. Když se na tokenu požaduje zrušení:

  * Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.
  * Všechny metody, které jsou volány v nástroji, `StopAsync` by měly vracet výzvu.

  Úkoly se ale po zrušení žádosti zruší, protože &mdash; volající čeká na dokončení všech úkolů.

  Pokud se aplikace neočekávaně ukončí (například proces aplikace se nezdařil), `StopAsync` nemusí být volána. Proto žádné metody, které jsou volány nebo operací, které jsou prováděny v nástroji, nemusí `StopAsync` nastat.

  Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Při použití obecného hostitele. Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.

Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace. Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` měla by být volána i v případě, že `StopAsync` není volána.

## <a name="timed-background-tasks"></a>Časované úlohy na pozadí

Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) . Časovač aktivuje `DoWork` metodu úkolu. Časovač je zakázán `StopAsync` a vyřazen při vyřazení kontejneru služby `Dispose` :

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

Nečeká na <xref:System.Threading.Timer> Dokončení předchozích spuštění `DoWork` , takže zobrazený přístup nemusí být vhodný pro každý scénář.

Služba je zaregistrovaná v `Startup.ConfigureServices` s `AddHostedService` metodou rozšíření:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Využívání vymezené služby v úloze na pozadí

Chcete-li použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci `IHostedService` , vytvořte obor. Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.

Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí. V následujícím příkladu je vložena <xref:Microsoft.Extensions.Logging.ILogger> do služby:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Hostovaná služba vytvoří obor pro řešení služby úlohy na pozadí, aby volal jeho `DoWork` metodu:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

Služby jsou zaregistrované v `Startup.ConfigureServices` . `IHostedService`Implementace je registrována s `AddHostedService` metodou rozšíření:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Úlohy na pozadí zařazené do fronty

Fronta úloh na pozadí je založena na .NET Framework 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánovaná jako integrovaná pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

V nástroji se `QueueHostedService` úlohy na pozadí ve frontě odřadí a spustí jako [BackgroundService](#backgroundservice-base-class), což je základní třída pro implementaci dlouhého běhu `IHostedService` :

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Služby jsou zaregistrované v `Startup.ConfigureServices` . `IHostedService`Implementace je registrována s `AddHostedService` metodou rozšíření:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

Ve třídě modelu stránky indexu:

* `IBackgroundTaskQueue`Je vložen do konstruktoru a přiřazen k `Queue` .
* <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory>Je vložený a přiřazený k `_serviceScopeFactory` . Objekt pro vytváření se používá k vytvoření instancí <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> , které se používají k vytváření služeb v rámci oboru. Je vytvořen obor, aby bylo možné použít `AppDbContext` ( [vymezenou](xref:fundamentals/dependency-injection#service-lifetimes)) aplikaci k zápisu záznamů databáze do `IBackgroundTaskQueue` služby (singleton).

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

Je-li na stránce indexu vybráno tlačítko **Přidat úkol** , `OnPostAddTask` je metoda spuštěna. `QueueBackgroundWorkItem`je volána pro zařazení pracovní položky do fronty:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Implementace úloh na pozadí v mikroslužbách pomocí IHostedService a třídy BackgroundService](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [Spouštění úloh na pozadí pomocí WebJobs v Azure App Service](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
