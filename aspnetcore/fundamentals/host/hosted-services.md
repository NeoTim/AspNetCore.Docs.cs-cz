---
title: Úlohy na pozadí s hostovanými službami v ASP.NET Core
author: guardrex
description: Naučte se implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: c1fbb5ae8ffc4ee506f42df6a4cbbe845b2b903d
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333661"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Úlohy na pozadí s hostovanými službami v ASP.NET Core

Od [Luke Latham](https://github.com/guardrex) a [Jeow li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*. Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>. V tomto tématu najdete tři příklady hostovaných služeb:

* Úloha na pozadí, která běží na časovači.
* Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes). Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection).
* Úlohy na pozadí, které běží sekvenčně.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázková aplikace je k dispozici ve dvou verzích:

* Webový hostitel &ndash; webový hostitel je vhodný pro hostování webových aplikací. Vzorový kód uvedený v tomto tématu je z verze ukázky webového hostitele. Další informace najdete v tématu věnovaném [webovému hostiteli](xref:fundamentals/host/web-host) .
* Generic host &ndash; obecný hostitel je v ASP.NET Core 2,1 nový. Další informace najdete v tématu [Obecné hostitele](xref:fundamentals/host/generic-host) .

## <a name="worker-service-template"></a>Šablona služby pracovní proces

Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb. Použití šablony jako základu pro aplikaci hostované služby:

[!INCLUDE[](~/includes/worker-template-instructions.md)]

---

## <a name="package"></a>Balíček

Odkaz na balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) se přidá implicitně pro ASP.NET Core aplikace.

## <a name="ihostedservice-interface"></a>Rozhraní IHostedService

Rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService> definuje dvě metody pro objekty, které jsou spravovány hostitelem:

* [StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` obsahuje logiku ke spuštění úlohy na pozadí. `StartAsync` se volá *před*:

  * Je nakonfigurovaný kanál zpracování požadavků aplikace (`Startup.Configure`).
  * Server se spustí a spustí se [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .

  Výchozí chování se dá změnit tak, aby se po konfiguraci kanálu aplikace `StartAsync` hostované služby spouštěla a volala se `ApplicationStarted`. Chcete-li změnit výchozí chování, přidejte hostovanou službu (`VideosWatcher` v následujícím příkladu) po volání `ConfigureWebHostDefaults`:

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

* [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; spouštěna v případě, že hostitel provádí bezproblémové vypnutí. `StopAsync` obsahuje logiku pro ukončení úlohy na pozadí. Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pro uvolnění jakýchkoli nespravovaných prostředků.

  Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný. Když se na tokenu požaduje zrušení:

  * Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.
  * Všechny metody, které jsou volány v `StopAsync`, by měly vracet dotaz znovu.

  Po zrušení se ale úkoly neopustily, protože volající @ no__t-0the očekává, že se všechny úkoly dokončí.

  Pokud se aplikace neočekávaně ukončí (například proces aplikace nebude úspěšný), `StopAsync` se nemusí volat. Proto se nemusí vyskytovat žádné metody volané nebo operace prováděné v `StopAsync`.

  Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:

  * Při použití obecného hostitele <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>. Další informace najdete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele Další informace najdete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.

Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace. Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` by měla být volána i v případě, že není volána `StopAsync`.

## <a name="backgroundservice"></a>BackgroundService

`BackgroundService` je základní třídou pro implementaci dlouhého běhu <xref:Microsoft.Extensions.Hosting.IHostedService>. `BackgroundService` poskytuje abstraktní metodu `ExecuteAsync(CancellationToken stoppingToken)`, která bude obsahovat logiku služby. @No__t-0 se aktivuje, když se zavolá [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) . Implementace této metody vrací `Task`, který představuje celou dobu života služby na pozadí.

*Volitelně můžete* také přepsat metody definované v `IHostedService` pro spuštění kódu spuštění a vypnutí pro vaši službu:

* `StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` se volá, když hostitel aplikace provádí řádné vypnutí. @No__t-0 je správcem signalizována, když se hostitel rozhodne vynutit ukončení služby. Pokud je tato metoda přepsána, je **nutné** zavolat (a `await`) metody základní třídy, aby se zajistilo, že se služba správně ukončí.
* `StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` se volá ke spuštění služby na pozadí. @No__t-0 je správcem signalizována, pokud dojde k přerušení procesu spouštění. Implementace vrací `Task`, který představuje proces spuštění služby. Žádné další služby nejsou spuštěny, dokud se nedokončí tato `Task`. Pokud je tato metoda přepsána, je **nutné** zavolat (a `await`) metody základní třídy, aby se zajistilo, že se služba spustí správně.

## <a name="timed-background-tasks"></a>Časované úlohy na pozadí

Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) . Časovač aktivuje metodu @no__t 0 úlohy. Časovač je zakázán na `StopAsync` a odstraněn, když je kontejner služby vyřazen v `Dispose`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

Služba je zaregistrovaná v `IHostBuilder.ConfigureServices` (*program.cs*) s metodou rozšíření `AddHostedService`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Využívání vymezené služby v úloze na pozadí

Chcete-li použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci `BackgroundService`, vytvořte obor. Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.

Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí. V následujícím příkladu:

* Služba je asynchronní. Metoda `DoWork` vrátí `Task`. Pro demonstrační účely je v metodě `DoWork` očekáváno zpoždění deseti sekund.
* Do služby se vloží <xref:Microsoft.Extensions.Logging.ILogger>.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Hostovaná služba vytvoří obor pro rozpoznání služby úlohy na pozadí, aby volala metodu `DoWork`. `DoWork` vrátí `Task`, který je očekáván v `ExecuteAsync`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

Služby jsou zaregistrované v `IHostBuilder.ConfigureServices` (*program.cs*). Hostovaná služba je registrovaná s metodou rozšíření `AddHostedService`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Úlohy na pozadí zařazené do fronty

Fronta úloh na pozadí je založena na rozhraní .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánováno jako integrované pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

V následujícím příkladu `QueueHostedService`:

* Metoda `BackgroundProcessing` vrátí hodnotu `Task`, která je očekávána v `ExecuteAsync`.
* Úlohy na pozadí ve frontě se odřadí a spustí v `BackgroundProcessing`.
* Pracovní položky jsou očekávány před zastavením služby v `StopAsync`.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

Služba `MonitorLoop` zpracovává úlohy enqueuing pro hostovanou službu pokaždé, když se na vstupním zařízení vybere klíč `w`:

* @No__t-0 se vloží do služby `MonitorLoop`.
* pro zařazení pracovní položky do fronty je volána hodnota `IBackgroundTaskQueue.QueueBackgroundWorkItem`.
* Pracovní položka simuluje dlouho běžící úlohu na pozadí:
  * Spustí se tři prodlevy 5 sekund (`Task.Delay`).
  * Příkaz `try-catch` depeše <xref:System.OperationCanceledException>, pokud se úkol zruší.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

Služby jsou zaregistrované v `IHostBuilder.ConfigureServices` (*program.cs*). Hostovaná služba je registrovaná s metodou rozšíření `AddHostedService`:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*. Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>. V tomto tématu najdete tři příklady hostovaných služeb:

* Úloha na pozadí, která běží na časovači.
* Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes). Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .
* Úlohy na pozadí, které běží sekvenčně.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázková aplikace je k dispozici ve dvou verzích:

* Webový hostitel &ndash; webový hostitel je vhodný pro hostování webových aplikací. Vzorový kód uvedený v tomto tématu je z verze ukázky webového hostitele. Další informace najdete v tématu věnovaném [webovému hostiteli](xref:fundamentals/host/web-host) .
* Generic host &ndash; obecný hostitel je v ASP.NET Core 2,1 nový. Další informace najdete v tématu [Obecné hostitele](xref:fundamentals/host/generic-host) .

## <a name="package"></a>Balíček

Odkaz na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .

## <a name="ihostedservice-interface"></a>Rozhraní IHostedService

Hostované služby implementují rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>. Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:

* [StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` obsahuje logiku ke spuštění úlohy na pozadí. Při použití [webového hostitele](xref:fundamentals/host/web-host)je `StartAsync` volána po spuštění serveru a aktivaci [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) . Při použití [obecného hostitele](xref:fundamentals/host/generic-host)se zavolá `StartAsync` před aktivací `ApplicationStarted`.

* [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; spouštěna v případě, že hostitel provádí bezproblémové vypnutí. `StopAsync` obsahuje logiku pro ukončení úlohy na pozadí. Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pro uvolnění jakýchkoli nespravovaných prostředků.

  Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný. Když se na tokenu požaduje zrušení:

  * Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.
  * Všechny metody, které jsou volány v `StopAsync`, by měly vracet dotaz znovu.

  Po zrušení se ale úkoly neopustily, protože volající @ no__t-0the očekává, že se všechny úkoly dokončí.

  Pokud se aplikace neočekávaně ukončí (například proces aplikace nebude úspěšný), `StopAsync` se nemusí volat. Proto se nemusí vyskytovat žádné metody volané nebo operace prováděné v `StopAsync`.

  Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:

  * Při použití obecného hostitele <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>. Další informace najdete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele Další informace najdete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.

Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace. Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` by měla být volána i v případě, že není volána `StopAsync`.

## <a name="timed-background-tasks"></a>Časované úlohy na pozadí

Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) . Časovač aktivuje metodu @no__t 0 úlohy. Časovač je zakázán na `StopAsync` a odstraněn, když je kontejner služby vyřazen v `Dispose`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

Služba je zaregistrovaná v `Startup.ConfigureServices` s metodou rozšíření `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Využívání vymezené služby v úloze na pozadí

Chcete-li použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci `IHostedService`, vytvořte obor. Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.

Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí. V následujícím příkladu je <xref:Microsoft.Extensions.Logging.ILogger> vložen do služby:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Hostovaná služba vytvoří obor pro rozpoznání služby úlohy na pozadí, aby volala metodu `DoWork`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

Služby jsou zaregistrované v `Startup.ConfigureServices`. Implementace `IHostedService` je zaregistrována s metodou rozšíření `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Úlohy na pozadí zařazené do fronty

Fronta úloh na pozadí je založena na .NET Framework 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánovala být integrovaná pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

V `QueueHostedService` se úlohy na pozadí ve frontě odřadí a spustí se jako <xref:Microsoft.Extensions.Hosting.BackgroundService>, což je základní třída pro implementaci dlouhého běhu `IHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Služby jsou zaregistrované v `Startup.ConfigureServices`. Implementace `IHostedService` je zaregistrována s metodou rozšíření `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

Ve třídě modelu stránky indexu:

* @No__t-0 se vloží do konstruktoru a přiřadí se k `Queue`.
* @No__t-0 je vložen a přiřazeno `_serviceScopeFactory`. Objekt pro vytváření se používá k vytvoření instancí <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, která se používá k vytváření služeb v rámci oboru. Je vytvořen obor, aby bylo možné použít `AppDbContext` (s [vymezenou službou](xref:fundamentals/dependency-injection#service-lifetimes)) k zápisu záznamů databáze do `IBackgroundTaskQueue` (služba s jedním prvkem).

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

Když je na stránce index vybrané tlačítko **Přidat úlohu** , spustí se metoda `OnPostAddTask`. pro zařazení pracovní položky do fronty je volána hodnota `QueueBackgroundWorkItem`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Implementace úloh na pozadí v mikroslužbách pomocí IHostedService a třídy BackgroundService](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
