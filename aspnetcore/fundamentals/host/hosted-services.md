---
title: Úlohy na pozadí s hostovanými službami v ASP.NET Core
author: guardrex
description: Zjistěte, jak implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/03/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 1db3ee1a9bcc0d41edf24df55bcd8d54fb0e9724
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081784"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Úlohy na pozadí s hostovanými službami v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

V ASP.NET Core se úlohy na pozadí dají implementovat jako *hostované služby*. Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>. V tomto tématu najdete tři příklady hostovaných služeb:

* Úloha na pozadí, která běží na časovači.
* Hostovaná služba, která aktivuje [vymezenou službu](xref:fundamentals/dependency-injection#service-lifetimes). Vymezená služba může používat vkládání závislostí.
* Úlohy na pozadí, které běží sekvenčně.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([stažení](xref:index#how-to-download-a-sample))

Ukázková aplikace je k dispozici ve dvou verzích:

* Webový hostitel &ndash; webového hostitele je vhodný pro hostování webových aplikací. Vzorový kód uvedený v tomto tématu je z verze ukázky webového hostitele. Další informace najdete v tématu věnovaném [webovému hostiteli](xref:fundamentals/host/web-host) .
* Obecný hostitel &ndash; obecného hostitele je v ASP.NET Core 2,1 nový. Další informace najdete v tématu [Obecné hostitele](xref:fundamentals/host/generic-host) .

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a>Šablona služby pracovní proces

Šablona služby ASP.NET Core Worker poskytuje výchozí bod pro psaní dlouhotrvajících aplikací služeb. Použití šablony jako základu pro aplikaci hostované služby:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Vytvořte nový projekt.
1. Vyberte **webová aplikace ASP.NET Core**. Vyberte **Další**.
1. Do pole **název projektu** zadejte název projektu nebo přijměte výchozí název projektu. Vyberte **Vytvořit**.
1. V dialogovém okně **vytvořit novou webovou aplikaci ASP.NET Core** potvrďte, že je vybrána možnost **.net Core** a **ASP.NET Core 3,0** .
1. Vyberte šablonu **služby pracovního procesu** . Vyberte **Vytvořit**.

# <a name="net-core-clitabnetcore-cli"></a>[Rozhraní příkazového řádku .NET Core](#tab/netcore-cli)

Použijte šablonu Worker Service`worker`() s příkazem [dotnet New](/dotnet/core/tools/dotnet-new) z příkazového prostředí. V následujícím příkladu se vytvoří aplikace pracovní služby s názvem `ContosoWorkerService`. Složka pro `ContosoWorkerService` aplikaci se vytvoří automaticky při spuštění příkazu.

```dotnetcli
dotnet new worker -o ContosoWorkerService
```

---

::: moniker-end

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

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

Služba je zaregistrovaná `Startup.ConfigureServices` v `AddHostedService` s metodou rozšíření:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Využívání vymezené služby v úloze na pozadí

Chcete-li použít [vymezené služby](xref:fundamentals/dependency-injection#service-lifetimes) v rámci `IHostedService`, vytvořte obor. Ve výchozím nastavení není pro hostovanou službu vytvořen žádný obor.

Služba úlohy vymezeného na pozadí obsahuje logiku úlohy na pozadí. V následujícím příkladu <xref:Microsoft.Extensions.Logging.ILogger> je vložena do služby:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ScopedProcessingService.cs?name=snippet1)]

Hostovaná služba vytvoří obor pro řešení služby úlohy na pozadí, aby volal jeho `DoWork` metodu:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

Služby jsou zaregistrované `Startup.ConfigureServices`v. Implementace je registrována `AddHostedService` s metodou rozšíření: `IHostedService`

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Úlohy na pozadí zařazené do fronty

Fronta úloh na pozadí je založena na rozhraní .NET 4. x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánováno jako integrované pro ASP.NET Core 3,0](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/BackgroundTaskQueue.cs?name=snippet1)]

V `QueueHostedService`nástroji se úlohy na pozadí ve frontě odřadí a spustí <xref:Microsoft.Extensions.Hosting.BackgroundService>jako, což je základní třída pro implementaci dlouhého běhu `IHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Služby jsou zaregistrované `Startup.ConfigureServices`v. Implementace je registrována `AddHostedService` s metodou rozšíření: `IHostedService`

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet3)]

Ve třídě modelu stránky indexu:

* Je vložen do konstruktoru a přiřazen k `Queue`. `IBackgroundTaskQueue`
* Je vložený a přiřazený k `_serviceScopeFactory`. <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> Objekt pro vytváření se používá k vytvoření instancí <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, které se používají k vytváření služeb v rámci oboru. Je vytvořen obor, aby bylo možné použít `AppDbContext` ( [vymezenou](xref:fundamentals/dependency-injection#service-lifetimes)) aplikaci k zápisu záznamů `IBackgroundTaskQueue` databáze do služby (singleton).

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet1)]

Je-li na stránce indexu vybráno tlačítko **Přidat úkol** , `OnPostAddTask` je metoda spuštěna. `QueueBackgroundWorkItem`je volána k zařazení pracovní položky do fronty:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="additional-resources"></a>Další zdroje

* [Implementace úloh na pozadí v mikroslužbách pomocí IHostedService a třídy BackgroundService](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
