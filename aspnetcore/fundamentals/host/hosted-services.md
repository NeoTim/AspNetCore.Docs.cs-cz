---
title: Úlohy na pozadí s hostovanými službami v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/host/hosted-services
ms.openlocfilehash: d3f409170eedd281fd7608c4b9835bf9443c49b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666199"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Úlohy na pozadí s hostovanými službami v ASP.NET Core

Podle [Jeow Li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

V ASP.NET Core, úlohy na pozadí lze implementovat jako *hostované služby*. Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní. Toto téma obsahuje tři příklady hostovaných služeb:

* Úloha na pozadí, která běží na časovači.
* Hostovaná služba, která aktivuje [službu s vymezeným oborem](xref:fundamentals/dependency-injection#service-lifetimes). Služba s vymezenou oborem můžete použít [vkládání závislostí (DI)](xref:fundamentals/dependency-injection).
* Úlohy na pozadí ve frontě, které běží postupně.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="worker-service-template"></a>Šablona služby pracovního procesu

Šablona ASP.NET core worker service poskytuje výchozí bod pro zápis dlouho běžících aplikací služby. Aplikace vytvořená ze šablony Pracovní služba určuje pracovní sdk v souboru projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

Použití šablony jako základu pro aplikaci hostovaných služeb:

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a>Balíček

Aplikace založená na šabloně `Microsoft.NET.Sdk.Worker` pracovní služby používá sadu SDK a obsahuje explicitní odkaz na balíček [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) Podívejte se například na soubor projektu ukázkové aplikace (*BackgroundTasksSample.csproj*).

Pro webové aplikace, `Microsoft.NET.Sdk.Web` které používají sadu SDK, [microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) balíček odkazuje implicitně ze sdíleného rozhraní. Explicitní odkaz na balíček v souboru projektu aplikace není vyžadován.

## <a name="ihostedservice-interface"></a>Rozhraní IHostedService

Rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService> definuje dvě metody pro objekty, které jsou spravovány hostitelem:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` obsahuje logiku pro spuštění úlohy na pozadí. `StartAsync`je volána *před*:

  * Kanál zpracování požadavků aplikace je`Startup.Configure`nakonfigurován ( ).
  * Server je spuštěn a [iApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) je aktivována.

  Výchozí chování lze změnit tak, aby hostované služby `StartAsync` běží po kanálu `ApplicationStarted` aplikace byla nakonfigurována a je volána. Chcete-li změnit výchozí chování, přidejte hostovku`VideosWatcher` `ConfigureWebHostDefaults`(v následujícím příkladu) po volání :

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

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Aktivováno, když hostitel provádí řádné vypnutí. `StopAsync`obsahuje logiku pro ukončení úlohy na pozadí. Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) k vyřazení všech nespravovaných prostředků.

  Token zrušení má výchozí časový limit pět sekund označující, že proces vypnutí by již neměl být elegantní. Při zrušení je požadováno na tokenu:

  * Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.
  * Všechny metody `StopAsync` volané v by měl vrátit okamžitě.

  Úkoly však nejsou opuštěny po&mdash;zrušení je požadováno volající čeká všechny úkoly k dokončení.

  Pokud se aplikace neočekávaně vypne (například proces `StopAsync` aplikace se nezdaří), nemusí být volána. Proto žádné metody volané nebo `StopAsync` operace prováděné v nemusí dojít.

  Chcete-li prodloužit výchozí časový limit pětisekundového vypnutí, nastavte:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>při použití obecného hostitele. Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Nastavení konfigurace hostitele časového času vypnutí při použití webového hostitele. Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.

Hostovaná služba se aktivuje jednou při spuštění aplikace a řádně se vypne při vypnutí aplikace. Pokud je vyvolána chyba během `Dispose` provádění úlohy `StopAsync` na pozadí, by měla být volána i v případě, že není volána.

## <a name="backgroundservice-base-class"></a>Základní třída BackgroundService

<xref:Microsoft.Extensions.Hosting.BackgroundService>je základní třída pro implementaci <xref:Microsoft.Extensions.Hosting.IHostedService>dlouhotrvajícího běhu .

[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) se nazývá ke spuštění služby na pozadí. Implementace <xref:System.Threading.Tasks.Task> vrátí, který představuje celou životnost služby na pozadí. Žádné další služby jsou [spuštěny, dokud ExecuteAsync se stane asynchronní](https://github.com/dotnet/extensions/issues/2149), například voláním `await`. Vyhněte se provádění dlouhých `ExecuteAsync`a blokujících inicializačních prací v . Bloky hostitele v [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) čeká `ExecuteAsync` na dokončení.

Token zrušení se aktivuje při volání [iHostedService.StopAsync.](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) Implementace `ExecuteAsync` by měla být dokončena okamžitě při zrušení token uvolní, aby bylo řádně vypnout službu. V opačném případě služba ungracefully vypne při časovém uzávěru vypnutí. Další informace naleznete v části [rozhraní IHostedService.](#ihostedservice-interface)

## <a name="timed-background-tasks"></a>Časované úlohy na pozadí

Časovaná úloha na pozadí využívá třídu [System.Threading.Timer.](xref:System.Threading.Timer) Časovač spustí metodu úlohy. `DoWork` Časovač je `StopAsync` vypnuta a uvolněna, když je `Dispose`uvolněn servisní kontejner na :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

Nečeká <xref:System.Threading.Timer> na předchozí spuštění `DoWork` až do konce, takže zobrazený přístup nemusí být vhodné pro každý scénář. [Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) se používá k zvýšení čítače spuštění jako atomické operace, `executionCount` která zajišťuje, že více vláken neaktualizují souběžně.

Služba je registrována v `IHostBuilder.ConfigureServices` (*Program.cs*) pomocí metody `AddHostedService` rozšíření:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Využívání služby s vymezeným oborem v úloze na pozadí

Chcete-li používat [služby s vymezeným oborem](xref:fundamentals/dependency-injection#service-lifetimes) v rámci [služby BackgroundService](#backgroundservice-base-class), vytvořte obor. Pro hostovizovanou službu není ve výchozím nastavení vytvořen žádný obor.

Služba úlohy na pozadí s vymezenou rozsahem obsahuje logiku úlohy na pozadí. V následujícím příkladu:

* Služba je asynchronní. Metoda `DoWork` vrátí `Task`. Pro demonstrační účely se v metodě `DoWork` očekává zpoždění deseti sekund.
* Je <xref:Microsoft.Extensions.Logging.ILogger> vstřikován do služby.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Hostovaná služba vytvoří obor pro překlad služby úlohy `DoWork` na pozadí s vymezeným oborem a volání její metody. `DoWork`vrátí `Task`a , který `ExecuteAsync`je očekáván v :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

Služby jsou `IHostBuilder.ConfigureServices` registrovány v (*Program.cs*). Hostovaná služba je registrována `AddHostedService` metodou rozšíření:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Úlohy na pozadí ve frontě

Fronta úloh na pozadí je založena <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> na rozhraní .NET 4.x[(nezávazně naplánované k předstouzení pro ASP.NET jádra](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

V následujícím `QueueHostedService` příkladu:

* Metoda `BackgroundProcessing` vrátí `Task`, který je `ExecuteAsync`očekáván v .
* Úlohy na pozadí ve frontě jsou `BackgroundProcessing`dequeued a provedeny v aplikaci .
* Pracovní položky jsou očekávány `StopAsync`před zastavením služby v .

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

Služba `MonitorLoop` zpracovává enqueuing úkoly pro hostované `w` služby vždy, když je klíč vybrán na vstupním zařízení:

* Je `IBackgroundTaskQueue` vložen do `MonitorLoop` služby.
* `IBackgroundTaskQueue.QueueBackgroundWorkItem`je volána k zařazení pracovní položky do fronty.
* Pracovní položka simuluje dlouhotrvající úlohu na pozadí:
  * Jsou provedena tři pětisekundová`Task.Delay`zpoždění ( ).
  * Příkaz `try-catch` soutisk, <xref:System.OperationCanceledException> pokud je úkol zrušen.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

Služby jsou `IHostBuilder.ConfigureServices` registrovány v (*Program.cs*). Hostovaná služba je registrována `AddHostedService` metodou rozšíření:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

`MontiorLoop`se spustí `Program.Main`v :

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

V ASP.NET Core, úlohy na pozadí lze implementovat jako *hostované služby*. Hostovaná služba je třída s logikou úlohy na pozadí, která implementuje <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní. Toto téma obsahuje tři příklady hostovaných služeb:

* Úloha na pozadí, která běží na časovači.
* Hostovaná služba, která aktivuje [službu s vymezeným oborem](xref:fundamentals/dependency-injection#service-lifetimes). Služba s vymezenou oborem může používat [vkládání závislostí (DI)](xref:fundamentals/dependency-injection)
* Úlohy na pozadí ve frontě, které běží postupně.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="package"></a>Balíček

Odkazna [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček [Microsoft.Extensions.Hosting.](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)

## <a name="ihostedservice-interface"></a>Rozhraní IHostedService

Hostované služby <xref:Microsoft.Extensions.Hosting.IHostedService> implementují rozhraní. Rozhraní definuje dvě metody pro objekty, které jsou spravovány hostitelem:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` obsahuje logiku pro spuštění úlohy na pozadí. Při použití [webového hostitele](xref:fundamentals/host/web-host) `StartAsync` je volána po spuštění serveru a je [spuštěna hodnota IApplicationLifetime.ApplicationStarted.](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) Při použití [obecného hostitele](xref:fundamentals/host/generic-host), `StartAsync` je volána před `ApplicationStarted` je aktivována.

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Aktivováno, když hostitel provádí řádné vypnutí. `StopAsync`obsahuje logiku pro ukončení úlohy na pozadí. Implementujte <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) k vyřazení všech nespravovaných prostředků.

  Token zrušení má výchozí časový limit pět sekund označující, že proces vypnutí by již neměl být elegantní. Při zrušení je požadováno na tokenu:

  * Všechny zbývající operace na pozadí, které aplikace provádí, by měly být přerušeny.
  * Všechny metody `StopAsync` volané v by měl vrátit okamžitě.

  Úkoly však nejsou opuštěny po&mdash;zrušení je požadováno volající čeká všechny úkoly k dokončení.

  Pokud se aplikace neočekávaně vypne (například proces `StopAsync` aplikace se nezdaří), nemusí být volána. Proto žádné metody volané nebo `StopAsync` operace prováděné v nemusí dojít.

  Chcete-li prodloužit výchozí časový limit pětisekundového vypnutí, nastavte:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>při použití obecného hostitele. Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Nastavení konfigurace hostitele časového času vypnutí při použití webového hostitele. Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.

Hostovaná služba se aktivuje jednou při spuštění aplikace a řádně se vypne při vypnutí aplikace. Pokud je vyvolána chyba během `Dispose` provádění úlohy `StopAsync` na pozadí, by měla být volána i v případě, že není volána.

## <a name="timed-background-tasks"></a>Časované úlohy na pozadí

Časovaná úloha na pozadí využívá třídu [System.Threading.Timer.](xref:System.Threading.Timer) Časovač spustí metodu úlohy. `DoWork` Časovač je `StopAsync` vypnuta a uvolněna, když je `Dispose`uvolněn servisní kontejner na :

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

Nečeká <xref:System.Threading.Timer> na předchozí spuštění `DoWork` až do konce, takže zobrazený přístup nemusí být vhodné pro každý scénář.

Služba je registrována `Startup.ConfigureServices` `AddHostedService` pomocí metody rozšíření:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Využívání služby s vymezeným oborem v úloze na pozadí

Chcete-li použít [služby s vymezeným oborem](xref:fundamentals/dependency-injection#service-lifetimes) `IHostedService`v rámci , vytvořte obor. Pro hostovizovanou službu není ve výchozím nastavení vytvořen žádný obor.

Služba úlohy na pozadí s vymezenou rozsahem obsahuje logiku úlohy na pozadí. V následujícím příkladu <xref:Microsoft.Extensions.Logging.ILogger> je do služby vložen a:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Hostovaná služba vytvoří obor pro překlad služby úlohy `DoWork` na pozadí s rozsahem a zavolá její metodu:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

Služby jsou `Startup.ConfigureServices`registrovány v . Implementace `IHostedService` je registrována `AddHostedService` metodou rozšíření:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Úlohy na pozadí ve frontě

Fronta úloh na pozadí je založena na <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> rozhraní .NET Framework 4.x[(nezávazně naplánované k předstouzené pro ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

V `QueueHostedService`aplikaci jsou úlohy na pozadí ve frontě dequeued a provedeny jako služba `IHostedService` [BackgroundService](#backgroundservice-base-class), což je základní třída pro implementaci dlouhotrvajícího :

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Služby jsou `Startup.ConfigureServices`registrovány v . Implementace `IHostedService` je registrována `AddHostedService` metodou rozšíření:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

Ve třídě modelu stránky Index:

* Je `IBackgroundTaskQueue` vstřikován do konstruktoru a přiřazen . `Queue`
* A <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> je injekčně a přiřazena . `_serviceScopeFactory` Factory se používá k vytvoření <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>instance , který se používá k vytvoření služeb v rámci oboru. Obor je vytvořen za účelem použití `AppDbContext` aplikace [(služba s vymezeným oborem](xref:fundamentals/dependency-injection#service-lifetimes)) k zápisu `IBackgroundTaskQueue` databázových záznamů v (singleton service).

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

Když je na stránce Index vybráno `OnPostAddTask` tlačítko **Přidat úkol,** metoda se spustí. `QueueBackgroundWorkItem`je volána k zařazení pracovní položky do fronty:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Implementace úloh na pozadí v mikroslužbách pomocí služby IHostedService a třídy BackgroundService](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [Spouštění úloh na pozadí pomocí webových úloh ve službě Azure App Service](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
