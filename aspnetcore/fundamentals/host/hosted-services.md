---
title: Úlohy na pozadí s hostovanými službami v ASP.NET Core
author: guardrex
description: Zjistěte, jak implementovat úlohy na pozadí s hostovanými službami v ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/28/2018
uid: fundamentals/host/hosted-services
ms.openlocfilehash: de419357d4d96a6e348a77055e67c0fcd190ae42
ms.sourcegitcommit: 0fc89b80bb1952852ecbcf3c5c156459b02a6ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2018
ms.locfileid: "52618139"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Úlohy na pozadí s hostovanými službami v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

V ASP.NET Core, je možné implementovat úlohy na pozadí jako *hostovaných služeb*. Hostovaná služba je třída s logikou úlohy na pozadí a implementuje rozhraní <xref:Microsoft.Extensions.Hosting.IHostedService>. Toto téma obsahuje tři příklady hostované služby:

* Úlohy na pozadí, který běží na časovač.
* Hostovanou službu, která aktivuje vymezené služby. Injektáž závislostí můžete použít službu s vymezeným oborem.
* Úkoly ve frontě na pozadí, které spouští sekvenčně.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([stažení](xref:index#how-to-download-a-sample))

Ukázková aplikace je k dispozici ve dvou verzích:

* Webového hostitele &ndash; The webového hostitele je užitečné pro hostování webových aplikací. Příklad kódu v tomto tématu je od verze webového hostitele vzorku. Další informace najdete v tématu [webového hostitele](xref:fundamentals/host/web-host) tématu.
* Obecný hostitele &ndash; obecný hostitelské Novinky v ASP.NET Core 2.1. Další informace najdete v tématu [obecný hostitele](xref:fundamentals/host/generic-host) tématu.

## <a name="package"></a>Balíček

Odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) nebo přidat odkaz na balíček [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) balíčku.

## <a name="ihostedservice-interface"></a>IHostedService rozhraní

Hostované služby, které implementují <xref:Microsoft.Extensions.Hosting.IHostedService> rozhraní. Rozhraní definuje dvě metody pro objekty, které se spravují přes hostitele:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` obsahuje logiku pro spuštění úlohy na pozadí. Při použití [webového hostitele](xref:fundamentals/host/web-host), `StartAsync` se volá, když server spuštěn a [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) se aktivuje. Při použití [obecný hostitele](xref:fundamentals/host/generic-host), `StartAsync` je volána před provedením `ApplicationStarted` se aktivuje.

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; aktivuje, pokud hostitel provádí řádné vypnutí. `StopAsync` obsahuje logiku pro ukončení úlohy na pozadí. Implementace <xref:System.IDisposable> a [finalizační metody (destruktory)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) k uvolnění nespravovaných prostředků.

  Token rušení, který má výchozí pět druhý časový limit k označení, že proces vypnutí by už nebude bezproblémové. Když bude podán požadavek na token:

  * Všechny zbývající operace na pozadí, které provádí aplikace by měla přerušena.
  * Všechny metody v `StopAsync` by měla vrátit okamžitě.

  Nicméně úlohy nejsou byly zanechány po zrušení požadováno&mdash;volající čeká na dokončení všech úloh.

  Pokud se aplikace ukončí neočekávaně (například aplikace proces selže), `StopAsync` nemusí být volána. Proto jakékoli metody volá nebo operací provedených v `StopAsync` nemusí být.

  Chcete-li rozšířit výchozí pět druhý časový limit ukončení, nastavte:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> jestli používáte obecný hostitele. Další informace naleznete v tématu <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Vypnutí vypršení časového limitu hostitele konfigurace nastavení při použití webového hostitele. Další informace naleznete v tématu <xref:fundamentals/host/web-host#shutdown-timeout>.

Hostovaná služba je aktivována jednou při spuštění aplikace a řádné ukončení při vypnutí aplikace. Pokud dojde k chybě při provádění úlohy na pozadí, `Dispose` by měla být volána i v případě `StopAsync` není volána.

## <a name="timed-background-tasks"></a>Úlohy na pozadí vypršel časový limit

Úlohy na pozadí vypršel časový limit využívá [System.Threading.Timer](xref:System.Threading.Timer) třídy. Časovač spustí úkolu `DoWork` metody. Časovač je zakázáno na `StopAsync` a uvolněno při uvolnění má v kontejneru služby `Dispose`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

Není registrován v `Startup.ConfigureServices` s `AddHostedService` – metoda rozšíření:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Vymezené služby v rámci úlohy na pozadí

Použití vymezené služby v rámci `IHostedService`, vytvoření oboru. Ve výchozím nastavení je vytvořen žádný obor pro hostovanou službu.

Služba úloh na pozadí s vymezeným oborem obsahuje logiku úlohy na pozadí. V následujícím příkladu <xref:Microsoft.Extensions.Logging.ILogger> se vloží do služby:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ScopedProcessingService.cs?name=snippet1)]

Hostovaná služba vytvoří obor vyřešit služba úloh na pozadí s vymezeným oborem volat jeho `DoWork` metody:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

Služby jsou registrované ve `Startup.ConfigureServices`. `IHostedService` Zaregistruje implementaci `AddHostedService` – metoda rozšíření:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Úlohy na pozadí ve frontě

Fronta úloh na pozadí je založená na platformě .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([nezávazně naplánované být integrované pro ASP.NET Core 3.0](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/BackgroundTaskQueue.cs?name=snippet1)]

V `QueueHostedService`, úlohy na pozadí ve frontě jsou odstraněné z fronty a provést, protože <xref:Microsoft.Extensions.Hosting.BackgroundService>, což je základní třída pro implementaci dlouho běžící `IHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Služby jsou registrované ve `Startup.ConfigureServices`. `IHostedService` Zaregistruje implementaci `AddHostedService` – metoda rozšíření:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet3)]

V třídě modelu Index stránky `IBackgroundTaskQueue` se vloží do konstruktoru a přiřadí do `Queue`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet1)]

Když **přidat úkol** výběru tlačítka na indexovou stránku, `OnPostAddTask` provedení metody. `QueueBackgroundWorkItem` je volána k zařazení do fronty pracovní položku:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="additional-resources"></a>Další zdroje

* [Implementace úloh na pozadí v mikroslužbách s IHostedService a BackgroundService třídy](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [System.Threading.Timer](xref:System.Threading.Timer)
