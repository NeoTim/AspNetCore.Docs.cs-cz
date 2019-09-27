---
title: Úlohy na pozadí s hostovanými službami v ASP.NET Core
author: guardrex
description: Zjistěte, jak implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 5a29952c4e50edb953fa03c6ea1a1ae27b728bb0
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317724"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Úlohy na pozadí s hostovanými službami v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*. Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>. V tomto tématu najdete tři příklady hostovaných služeb:

* Úloha na pozadí, která běží na časovači.
* Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes). Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection).
* Úlohy na pozadí, které běží sekvenčně.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([stažení](xref:index#how-to-download-a-sample))

Ukázková aplikace je k dispozici ve dvou verzích:

* Webový hostitel &ndash; webového hostitele je vhodný pro hostování webových aplikací. Vzorový kód uvedený v tomto tématu je z verze ukázky webového hostitele. Další informace najdete v tématu věnovaném [webovému hostiteli](xref:fundamentals/host/web-host) .
* Obecný hostitel &ndash; obecného hostitele je v ASP.NET Core 2,1 nový. Další informace najdete v tématu [Obecné hostitele](xref:fundamentals/host/generic-host) .

## <a name="worker-service-template"></a>Šablona služby pracovní proces

Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb. Použití šablony jako základu pro aplikaci hostované služby:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvořte nový projekt.
1. Vyberte **webová aplikace ASP.NET Core**. Vyberte **Další**.
1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. Vyberte **Vytvořit**.
1. V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** .
1. Vyberte šablonu **služby pracovního procesu** . Vyberte **Vytvořit**.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. Vytvořte nový projekt.
1. Na bočním panelu vyberte **aplikaci** v části **.NET Core** .
1. V části **ASP.NET Core**vyberte **Worker** . Vyberte **Další**.
1. Pro **cílovou architekturu**vyberte **.NET Core 3,0** . Vyberte **Další**.
1. Do pole **název projektu** zadejte název. Vyberte **Vytvořit**.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Použijte šablonu Worker Service`worker`() s příkazem [dotnet New](/dotnet/core/tools/dotnet-new) z příkazového prostředí. V následujícím příkladu se vytvoří aplikace pracovní služby s názvem `ContosoWorker`. Složka pro `ContosoWorker` aplikaci se vytvoří automaticky při spuštění příkazu.

```dotnetcli
dotnet new worker -o ContosoWorker
```

---

## <a name="package"></a>Balíček

Odkaz na balíček [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) se přidá implicitně pro ASP.NET Core aplikace.

## <a name="ihostedservice-interface"></a>Rozhraní IHostedService

<xref:Microsoft.Extensions.Hosting.IHostedService> Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:

* [StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; obsahujelogikupro`StartAsync` zahájení úlohy na pozadí. `StartAsync`se volá *před*:

  * Je nakonfigurovaný kanál zpracování požadavků aplikace (`Startup.Configure`).
  * Server se spustí a spustí se [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) .

  Výchozí chování se dá změnit tak, aby se hostovaná služba `StartAsync` spouštěla po nakonfigurování kanálu aplikace a `ApplicationStarted` zavolala se. Chcete-li změnit výchozí chování, přidejte hostovanou službu`VideosWatcher` (v následujícím příkladu) po volání `ConfigureWebHostDefaults`:

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

* [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Aktivováno, když hostitel provádí bezproblémové vypnutí. `StopAsync`obsahuje logiku pro ukončení úlohy na pozadí. Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pro uvolnění jakýchkoli nespravovaných prostředků.

  Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný. Když se na tokenu požaduje zrušení:

  * Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.
  * Všechny metody, které `StopAsync` jsou volány v nástroji, by měly vracet výzvu.

  Úkoly se ale po zrušení žádosti&mdash;zruší, protože volající čeká na dokončení všech úkolů.

  Pokud se aplikace neočekávaně ukončí (například proces aplikace se nezdařil), `StopAsync` nemusí být volána. Proto žádné metody, které jsou volány nebo `StopAsync` operací, které jsou prováděny v nástroji, nemusí nastat.

  Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Při použití obecného hostitele. Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.

Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace. Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` měla by být volána i `StopAsync` v případě, že není volána.

## <a name="backgroundservice"></a>BackgroundService

`BackgroundService`je základní třídou pro implementaci dlouhého běhu <xref:Microsoft.Extensions.Hosting.IHostedService>. `BackgroundService``ExecuteAsync(CancellationToken stoppingToken)` poskytuje abstraktní metodu, která bude obsahovat logiku služby. Je `stoppingToken` aktivována, když je volána metoda [IHostedService. StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) . Implementace této metody vrátí `Task` , která představuje celou dobu života služby na pozadí.

*Volitelně můžete* také přepsat metody definované v `IHostedService` pro spuštění spouštěcího a ukončovacího kódu pro vaši službu:

* `StopAsync(CancellationToken cancellationToken)`&ndash; sevolá,kdyžhostitel`StopAsync` aplikace provádí řádné vypnutí. Je `cancellationToken` správcem signalizována, když se hostitel rozhodne vynutit ukončení služby. Pokud je tato metoda přepsána, je **nutné** zavolat `await`(a) metodu základní třídy, aby se služba správně vypnula.
* `StartAsync(CancellationToken cancellationToken)`&ndash; sevoláke`StartAsync` spuštění služby na pozadí. `cancellationToken` Je správcem signalizována, pokud je proces spuštění přerušený. Implementace vrátí `Task` , který představuje proces spuštění služby. Žádné další služby nejsou spuštěny, `Task` dokud se nedokončí. Pokud je tato metoda přepsána, je **nutné** zavolat `await`(a) metodu základní třídy, aby bylo zajištěno, že služba bude spuštěna správně.

## <a name="timed-background-tasks"></a>Časované úlohy na pozadí

Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) . Časovač aktivuje `DoWork` metodu úkolu. Časovač je zakázán `StopAsync` a vyřazen při vyřazení `Dispose`kontejneru služby:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

Služba je registrována v `IHostBuilder.ConfigureServices` (*program.cs*) s `AddHostedService` metodou rozšíření:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Využívání vymezené služby v úloze na pozadí

Chcete-li použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci `BackgroundService`, vytvořte obor. Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.

Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí. V následujícím příkladu:

* Služba je asynchronní. `DoWork` Metoda`Task`vrátí. Pro demonstrační účely je v `DoWork` metodě očekáváno zpoždění deseti sekund.
* Do <xref:Microsoft.Extensions.Logging.ILogger> služby se vloží.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Hostovaná služba vytvoří obor pro rozpoznání služby úlohy na pozadí pro volání `DoWork` metody. `DoWork`Vrátí, který je očekáván v `ExecuteAsync`: `Task`

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

Služby jsou zaregistrované `IHostBuilder.ConfigureServices` v (*program.cs*). Hostovaná služba je zaregistrovaná `AddHostedService` v rámci metody rozšíření:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Úlohy na pozadí zařazené do fronty

Fronta úloh na pozadí je založena na rozhraní .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánováno jako integrované pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

V následujícím `QueueHostedService` příkladu:

* Metoda vrátí hodnotu ,která`ExecuteAsync`je očekávána v. `Task` `BackgroundProcessing`
* Úlohy na pozadí ve frontě se odřadí a spustí v `BackgroundProcessing`.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28,39-40,44)]

Služba zpracovává enqueuing úlohy pro hostovanou službu vždy, `w` když je vybraný klíč na vstupním zařízení: `MonitorLoop`

* `IBackgroundTaskQueue` Je vložen`MonitorLoop` do služby.
* `IBackgroundTaskQueue.QueueBackgroundWorkItem`je volána pro zařazení pracovní položky do fronty.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

Služby jsou zaregistrované `IHostBuilder.ConfigureServices` v (*program.cs*). Hostovaná služba je zaregistrovaná `AddHostedService` v rámci metody rozšíření:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*. Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>. V tomto tématu najdete tři příklady hostovaných služeb:

* Úloha na pozadí, která běží na časovači.
* Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes). Vymezená služba může používat [vkládání závislostí (di)](xref:fundamentals/dependency-injection) .
* Úlohy na pozadí, které běží sekvenčně.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([stažení](xref:index#how-to-download-a-sample))

Ukázková aplikace je k dispozici ve dvou verzích:

* Webový hostitel &ndash; webového hostitele je vhodný pro hostování webových aplikací. Vzorový kód uvedený v tomto tématu je z verze ukázky webového hostitele. Další informace najdete v tématu věnovaném [webovému hostiteli](xref:fundamentals/host/web-host) .
* Obecný hostitel &ndash; obecného hostitele je v ASP.NET Core 2,1 nový. Další informace najdete v tématu [Obecné hostitele](xref:fundamentals/host/generic-host) .

## <a name="package"></a>Balíček

Odkaz na [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. Extensions. hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) .

## <a name="ihostedservice-interface"></a>Rozhraní IHostedService

Hostované služby implementují <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní. Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:

* [StartAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; obsahujelogikupro`StartAsync` zahájení úlohy na pozadí. Při použití [webového hostitele](xref:fundamentals/host/web-host) `StartAsync` se volá po spuštění serveru a aktivaci [IApplicationLifetime. ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) . Při použití [obecného hostitele](xref:fundamentals/host/generic-host) `StartAsync` se před `ApplicationStarted` aktivací volá.

* [StopAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Aktivováno, když hostitel provádí bezproblémové vypnutí. `StopAsync`obsahuje logiku pro ukončení úlohy na pozadí. Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) pro uvolnění jakýchkoli nespravovaných prostředků.

  Token zrušení má výchozí 5 sekundový časový limit, který označuje, že proces vypnutí by již neměl být řádný. Když se na tokenu požaduje zrušení:

  * Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.
  * Všechny metody, které `StopAsync` jsou volány v nástroji, by měly vracet výzvu.

  Úkoly se ale po zrušení žádosti&mdash;zruší, protože volající čeká na dokončení všech úkolů.

  Pokud se aplikace neočekávaně ukončí (například proces aplikace se nezdařil), `StopAsync` nemusí být volána. Proto žádné metody, které jsou volány nebo `StopAsync` operací, které jsou prováděny v nástroji, nemusí nastat.

  Chcete-li zvětšit výchozí časový limit pěti sekund, nastavte:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>Při použití obecného hostitele. Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Nastavení konfigurace hostitele časového limitu vypnutí při použití webového hostitele Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.

Hostovaná služba se aktivuje při spuštění aplikace a řádně se vypíná při vypnutí aplikace. Pokud je vyvolána chyba během provádění úlohy na pozadí, `Dispose` měla by být volána i `StopAsync` v případě, že není volána.

## <a name="timed-background-tasks"></a>Časované úlohy na pozadí

Úloha s časovým limitem na pozadí využívá třídu [System. Threading. Timer](xref:System.Threading.Timer) . Časovač aktivuje `DoWork` metodu úkolu. Časovač je zakázán `StopAsync` a vyřazen při vyřazení `Dispose`kontejneru služby:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

Služba je zaregistrovaná `Startup.ConfigureServices` v `AddHostedService` s metodou rozšíření:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Využívání vymezené služby v úloze na pozadí

Chcete-li použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci `IHostedService`, vytvořte obor. Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.

Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí. V následujícím příkladu <xref:Microsoft.Extensions.Logging.ILogger> je vložena do služby:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Hostovaná služba vytvoří obor pro řešení služby úlohy na pozadí, aby volal jeho `DoWork` metodu:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

Služby jsou zaregistrované `Startup.ConfigureServices`v. Implementace je registrována `AddHostedService` s metodou rozšíření: `IHostedService`

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Úlohy na pozadí zařazené do fronty

Fronta úloh na pozadí je založena na rozhraní .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánováno jako integrované pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

V `QueueHostedService`nástroji se úlohy na pozadí ve frontě odřadí a spustí <xref:Microsoft.Extensions.Hosting.BackgroundService>jako, což je základní třída pro implementaci dlouhého běhu `IHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Služby jsou zaregistrované `Startup.ConfigureServices`v. Implementace je registrována `AddHostedService` s metodou rozšíření: `IHostedService`

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

Ve třídě modelu stránky indexu:

* Je vložen do konstruktoru a přiřazen k `Queue`. `IBackgroundTaskQueue`
* Je vložený a přiřazený k `_serviceScopeFactory`. <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> Objekt pro vytváření se používá k vytvoření instancí <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, které se používají k vytváření služeb v rámci oboru. Je vytvořen obor, aby bylo možné použít `AppDbContext` ( [vymezenou](xref:fundamentals/dependency-injection#service-lifetimes)) aplikaci k zápisu záznamů `IBackgroundTaskQueue` databáze do služby (singleton).

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

Je-li na stránce indexu vybráno tlačítko **Přidat úkol** , `OnPostAddTask` je metoda spuštěna. `QueueBackgroundWorkItem`je volána pro zařazení pracovní položky do fronty:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Implementace úloh na pozadí v mikroslužbách pomocí IHostedService a třídy BackgroundService](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
