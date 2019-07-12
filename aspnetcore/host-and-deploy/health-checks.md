---
title: Doplněk pro kontroly stavu v ASP.NET Core
author: guardrex
description: Další informace o nastavení kontroly stavu pro ASP.NET Core infrastruktury, jako jsou aplikace a databáze.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: 43b6c3b55170eaf3a989d0f2779edac5290df823
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67855914"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="f7d7b-103">Doplněk pro kontroly stavu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7d7b-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="f7d7b-104">Podle [Luke Latham](https://github.com/guardrex) a [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="f7d7b-104">By [Luke Latham](https://github.com/guardrex) and [Glenn Condron](https://github.com/glennc)</span></span>

<span data-ttu-id="f7d7b-105">ASP.NET Core nabízí Middleware zkontrolovat stav a knihovny pro vytváření sestav stavu komponent infrastruktury aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-105">ASP.NET Core offers Health Check Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="f7d7b-106">Kontroly stavu jsou vystaveny aplikací jako koncových bodů HTTP.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="f7d7b-107">Kontroly stavu koncových bodů lze nakonfigurovat pro různé scénáře monitorování v reálném čase:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="f7d7b-108">Sondy stavu služby mohou být využívána orchestrátorů kontejnerů a zkontrolovat stav vaší aplikace Vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="f7d7b-109">Například orchestrátor kontejnerů může reagovat na stav selhání zkontrolovat, že zastavení nasazení se zajištěním provozu nebo kontejner se restartuje.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="f7d7b-110">Nástroj pro vyrovnávání zatížení může reagovat na není v pořádku aplikace můžete provoz nasměrovat od selhání instance na instanci v pořádku.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="f7d7b-111">Použití paměti, disku a další prostředky fyzického serveru je možné monitorovat stav v pořádku.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="f7d7b-112">Kontroly stavu můžete otestovat závislostí vaší aplikace, jako jsou databáze a externí služby koncových bodů, abyste zkontrolovali dostupnost a správně fungovat.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="f7d7b-113">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f7d7b-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f7d7b-114">Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="f7d7b-115">Chcete-li spustit ukázkovou aplikaci v daném scénáři, použijte [dotnet spustit](/dotnet/core/tools/dotnet-run) příkaz ze složky projektu v příkazovém řádku.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="f7d7b-116">Zobrazit ukázkovou aplikaci *README.md* souboru a popisy scénářů v tomto tématu informace o tom, jak použít ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f7d7b-117">Požadavky</span><span class="sxs-lookup"><span data-stu-id="f7d7b-117">Prerequisites</span></span>

<span data-ttu-id="f7d7b-118">Kontroly stavu se obvykle používají s externí monitorování orchestrator služby nebo kontejneru a zkontrolujte stav aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="f7d7b-119">Před přidáním kontroly stavu aplikace, rozhodněte, na který monitorovací systém použít.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="f7d7b-120">Systém monitorování Určuje, jaké typy kontroly stavu k vytvoření a konfigurace jejich koncové body.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="f7d7b-121">Odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) nebo přidat odkaz na balíček [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) balíčku.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-121">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="f7d7b-122">Ukázková aplikace poskytuje při spuštění kód pro demonstraci kontroly stavu pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-122">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="f7d7b-123">[Databáze sondy](#database-probe) scénář zkontroluje stav připojení databáze pomocí [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-123">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="f7d7b-124">[DbContext sondy](#entity-framework-core-dbcontext-probe) scénář zkontroluje databázi pomocí EF Core `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-124">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="f7d7b-125">Prozkoumat scénáře databázi, ukázková aplikace:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-125">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="f7d7b-126">Vytvoří databázi a obsahuje ve svém připojovacím řetězci *appsettings.json* souboru.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-126">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="f7d7b-127">Má následující odkazy na balíčky v jeho souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-127">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="f7d7b-128">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="f7d7b-128">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="f7d7b-129">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="f7d7b-129">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="f7d7b-130">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) je port [BeatPulse](https://github.com/xabaril/beatpulse) a není zachována nebo podporovaný společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-130">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) is a port of [BeatPulse](https://github.com/xabaril/beatpulse) and isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="f7d7b-131">Další možností kontroly stavu ukazuje, jak filtrovat kontroly stavu port pro správu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-131">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="f7d7b-132">Ukázková aplikace je potřeba vytvořit *Properties/launchSettings.json* soubor, který obsahuje adresu URL pro správu a port pro správu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-132">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="f7d7b-133">Další informace najdete v tématu [filtrovat podle port](#filter-by-port) oddílu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-133">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="f7d7b-134">Sonda stavu základní</span><span class="sxs-lookup"><span data-stu-id="f7d7b-134">Basic health probe</span></span>

<span data-ttu-id="f7d7b-135">U mnoha aplikací konfiguraci sondy základní stav, který bude hlásit dostupnost aplikace ke zpracování požadavků (*aktivity*) ke zjištění stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-135">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="f7d7b-136">Základní konfigurace zaregistruje služeb kontroly stavu a volá Middleware Kontrola stavu reagovat na koncový bod adresy URL odpovědí stavu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-136">The basic configuration registers health check services and calls the Health Check Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="f7d7b-137">Ve výchozím nastavení jsou registrovány žádné konkrétní stav kontroly testování žádné konkrétní závislost nebo subsystému.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-137">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="f7d7b-138">Aplikace se považuje za v pořádku, pokud je schopný reagovat na adresu URL koncového bodu stavu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-138">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="f7d7b-139">Zapíše zapisovače odpovědí výchozí stav (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) jako odpověď ve formátu prostého textu zpět do klienta, která [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [ HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) stav.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-139">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="f7d7b-140">Registrace služby kontroly stavu s <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-140">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f7d7b-141">Přidat stav zkontrolujte Middleware s <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> v kanálu zpracování žádosti o `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-141">Add Health Check Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="f7d7b-142">V ukázkové aplikaci, je vytvořen koncový bod stavu zaškrtnutí v `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="f7d7b-142">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/BasicStartup.cs?name=snippet1&highlight=5,10)]

<span data-ttu-id="f7d7b-143">Ke spuštění základní konfiguraci scénář s využitím ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-143">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="f7d7b-144">Příklad dockeru</span><span class="sxs-lookup"><span data-stu-id="f7d7b-144">Docker example</span></span>

<span data-ttu-id="f7d7b-145">[Docker](xref:host-and-deploy/docker/index) nabízí integrované `HEALTHCHECK` směrnice, kterého chcete zkontrolovat stav, který používá základní stav Zkontrolujte konfiguraci aplikace:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-145">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="f7d7b-146">Vytvoření kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="f7d7b-146">Create health checks</span></span>

<span data-ttu-id="f7d7b-147">Doplněk pro kontroly stavu jsou vytvořeny pomocí implementace <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-147">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="f7d7b-148"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> Metoda vrátí hodnotu `Task<` <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> `>` , který označuje stav jako `Healthy`, `Degraded`, nebo `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-148">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a `Task<` <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> `>` that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="f7d7b-149">Výsledkem je zapsán jako odpověď ve formátu prostého textu se dají konfigurovat stavovým kódem (konfigurace je popsaná v [možnosti kontroly stavu](#health-check-options) části).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-149">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="f7d7b-150"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> Můžete také vrátit volitelné páry klíč hodnota.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-150"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="f7d7b-151">Kontrola stavu příklad</span><span class="sxs-lookup"><span data-stu-id="f7d7b-151">Example health check</span></span>

<span data-ttu-id="f7d7b-152">Následující `ExampleHealthCheck` třídy ukazuje rozložení kontrolou stavu:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check:</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public ExampleHealthCheck()
    {
        // Use dependency injection (DI) to supply any required services to the
        // health check.
    }

    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        // Execute health check logic here. This example sets a dummy
        // variable to true.
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a><span data-ttu-id="f7d7b-153">Registrace služeb kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="f7d7b-153">Register health check services</span></span>

<span data-ttu-id="f7d7b-154">`ExampleHealthCheck` Typ je přidat do služby kontroly stavu s <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-154">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck<ExampleHealthCheck>("example_health_check");
}
```

<span data-ttu-id="f7d7b-155"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> Přetížení je znázorněno v následujícím příkladu nastaví stav chyby (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) k sestavě, když je kontrola stavu hlásí selhání.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-155">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="f7d7b-156">Pokud je nastaven stav selhání `null` (výchozí), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) se použije v hlášení.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-156">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="f7d7b-157">Toto přetížení je užitečný scénář pro autory knihoven, kde stav selhání indikován knihovny vynucuje aplikace, když dojde k selhání kontroly stavu, pokud provádění kontroly stavu respektuje nastavení.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-157">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="f7d7b-158">*Značky* můžete použít k filtrování kontroly stavu (popsáno dále v [filtrovat kontroly stavu](#filter-health-checks) části).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-158">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="f7d7b-159"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> Můžete také spustit funkci lambda.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-159"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="f7d7b-160">V následujícím příkladu je zadán název kontroly stavu jako `Example` a kontrola vždy vrátí hodnotu stavu v pořádku:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-160">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Example", () =>
            HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" })
}
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="f7d7b-161">Použití middlewaru kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="f7d7b-161">Use Health Checks Middleware</span></span>

<span data-ttu-id="f7d7b-162">V `Startup.Configure`, volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> v kanálu zpracování se adresa URL koncového bodu nebo relativní cesta:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-162">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health");
}
```

<span data-ttu-id="f7d7b-163">Pokud kontroly stavu naslouchat požadavkům na konkrétní port, použijte přetížení <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> nastavení portu (popsány dále v [filtrovat podle port](#filter-by-port) části):</span><span class="sxs-lookup"><span data-stu-id="f7d7b-163">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

<span data-ttu-id="f7d7b-164">Middleware ověří stavu je *terminálu middleware* v kanálu zpracování žádosti o aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-164">Health Checks Middleware is a *terminal middleware* in the app's request processing pipeline.</span></span> <span data-ttu-id="f7d7b-165">První stavu vrácení koncového bodu došlo k, který přesně odpovídá adrese URL požadavku spustí a zkratům middleware zbytku.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-165">The first health check endpoint encountered that's an exact match to the request URL executes and short-circuits the rest of the middleware pipeline.</span></span> <span data-ttu-id="f7d7b-166">Dojde-li krátký cyklus, spustí žádné middleware následující kontroly stavu odpovídající.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-166">When short-circuiting occurs, no middleware following the matched health check executes.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="f7d7b-167">Možnosti kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="f7d7b-167">Health check options</span></span>

<span data-ttu-id="f7d7b-168"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> poskytnout příležitosti k přizpůsobení chování kontrolu stavu:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-168"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="f7d7b-169">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="f7d7b-169">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="f7d7b-170">Přizpůsobení stavový kód HTTP</span><span class="sxs-lookup"><span data-stu-id="f7d7b-170">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="f7d7b-171">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="f7d7b-171">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="f7d7b-172">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="f7d7b-172">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="f7d7b-173">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="f7d7b-173">Filter health checks</span></span>

<span data-ttu-id="f7d7b-174">Ve výchozím nastavení spustí všechny kontroly stavu registrované Middleware zkontrolovat stav.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-174">By default, Health Check Middleware runs all registered health checks.</span></span> <span data-ttu-id="f7d7b-175">Pro spuštění podmnožiny kontroly stavu, poskytují funkce, která vrátí logickou hodnotu k <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> možnost.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-175">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="f7d7b-176">V následujícím příkladu `Bar` Kontrola stavu je odfiltrována podle jeho značky (`bar_tag`) v podmíněném příkazu funkce, kde `true` je vrácena pouze v případě kontrola stavu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> odpovídá vlastnosti `foo_tag` nebo `baz_tag`:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-176">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // Filter out the 'Bar' health check. Only Foo and Baz execute.
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="f7d7b-177">Přizpůsobení stavový kód HTTP</span><span class="sxs-lookup"><span data-stu-id="f7d7b-177">Customize the HTTP status code</span></span>

<span data-ttu-id="f7d7b-178">Použití <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> přizpůsobit mapování stavu stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-178">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="f7d7b-179">Následující <xref:Microsoft.AspNetCore.Http.StatusCodes> přiřazení jsou výchozí hodnoty pro daný middleware.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-179">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="f7d7b-180">Změňte hodnoty stavu kódu podle svých požadavků.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-180">Change the status code values to meet your requirements.</span></span>

```csharp
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // The following StatusCodes are the default assignments for
        // the HealthStatus properties.
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
}
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="f7d7b-181">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="f7d7b-181">Suppress cache headers</span></span>

<span data-ttu-id="f7d7b-182"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> Určuje, zda Middleware zkontrolovat stav přidá do odpovědi test zabránit ukládání do mezipaměti odpovědi hlavičky protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-182"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Check Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="f7d7b-183">Pokud je hodnota `false` (výchozí), middleware Nastaví nebo přepíše `Cache-Control`, `Expires`, a `Pragma` záhlaví zabránit ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-183">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="f7d7b-184">Pokud je hodnota `true`, middleware nemění mezipaměti hlaviček odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-184">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

```csharp
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // The default value is false.
        AllowCachingResponses = false
    });
}
```

### <a name="customize-output"></a><span data-ttu-id="f7d7b-185">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="f7d7b-185">Customize output</span></span>

<span data-ttu-id="f7d7b-186"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> Možnost získá nebo nastaví delegáta použitý k zápisu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-186">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="f7d7b-187">Výchozí delegáta zapíše odpověď o minimální ve formátu prostého textu s řetězcovou hodnotu [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-187">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

```csharp
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // WriteResponse is a delegate used to write the response.
        ResponseWriter = WriteResponse
    });
}

private static Task WriteResponse(HttpContext httpContext,
    HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

## <a name="database-probe"></a><span data-ttu-id="f7d7b-188">Test databáze</span><span class="sxs-lookup"><span data-stu-id="f7d7b-188">Database probe</span></span>

<span data-ttu-id="f7d7b-189">Kontrola stavu můžete zadat dotaz na databázi pro spuštění jako logický test k označení, pokud je databáze obvykle reagovat.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-189">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="f7d7b-190">Tato ukázková aplikace používá [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), knihovna kontroly stavu pro aplikace ASP.NET Core, chcete-li provést kontrolu stavu v databázi serveru SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-190">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="f7d7b-191">`AspNetCore.Diagnostics.HealthChecks` Spustí `SELECT 1` dotaz na databázi a zkontrolujte připojení k databázi je v pořádku.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-191">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="f7d7b-192">Při kontrole připojení databáze pomocí dotazu, vyberte dotaz, který vrací rychle.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-192">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="f7d7b-193">Přístup dotaz spustí riziko přetížení databáze a snížení jeho výkon.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-193">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="f7d7b-194">Ve většině případů není nutné spuštění testu dotazu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-194">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="f7d7b-195">Stačí pouze provedení úspěšného připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-195">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="f7d7b-196">Pokud zjistíte potřebné ke spuštění dotazu, zvolte jednoduchého dotazu SELECT, jako například `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-196">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="f7d7b-197">Zahrnout odkaz na balíček pro [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-197">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="f7d7b-198">Zadejte platnou databázi připojovacího řetězce v *appsettings.json* souboru ukázkovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-198">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="f7d7b-199">Aplikace používá databázi systému SQL Server s názvem `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-199">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="f7d7b-200">Registrace služby kontroly stavu s <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-200">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f7d7b-201">Volání ukázkové aplikace `AddSqlServer` metoda připojovacím řetězcem databázi (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="f7d7b-201">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="f7d7b-202">Volat stavu zkontrolujte Middleware v kanálu zpracování aplikace v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-202">Call Health Check Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_Configure)]

<span data-ttu-id="f7d7b-203">Pokud chcete spustit scénář testu databáze pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-203">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="f7d7b-204">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) je port [BeatPulse](https://github.com/xabaril/beatpulse) a není zachována nebo podporovaný společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-204">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) is a port of [BeatPulse](https://github.com/xabaril/beatpulse) and isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="f7d7b-205">Entity Framework Core DbContext testu</span><span class="sxs-lookup"><span data-stu-id="f7d7b-205">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="f7d7b-206">`DbContext` Kontrola potvrdí, že aplikace mohla komunikovat s databází, které jsou nakonfigurované pro EF Core `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-206">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="f7d7b-207">`DbContext` Kontrola se podporuje v aplikacích, které:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-207">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="f7d7b-208">Použití [Entity Framework (EF) Core](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-208">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="f7d7b-209">Zahrnout odkaz na balíček pro [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-209">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="f7d7b-210">`AddDbContextCheck<TContext>` zaregistruje kontrolu stavu `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-210">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="f7d7b-211">`DbContext` Je předána jako `TContext` metody.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-211">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="f7d7b-212">Přetížení je možné konfigurovat stav chyby, značky a vlastní testovat dotaz.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-212">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="f7d7b-213">Ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-213">By default:</span></span>

* <span data-ttu-id="f7d7b-214">`DbContextHealthCheck` Volá EF Core `CanConnectAsync` metody.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-214">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="f7d7b-215">Můžete přizpůsobit, jaké operace je spuštěna při kontrole stavu pomocí `AddDbContextCheck` přetížení metody.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-215">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="f7d7b-216">Název kontroly stavu je název `TContext` typu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-216">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="f7d7b-217">V ukázkové aplikaci `AppDbContext` je poskytnuta `AddDbContextCheck` a zaregistrováno jako služba v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-217">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="f7d7b-218">*DbContextHealthStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-218">*DbContextHealthStartup.cs*:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="f7d7b-219">V ukázkové aplikaci `UseHealthChecks` přidá Middleware zkontrolovat stav v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-219">In the sample app, `UseHealthChecks` adds the Health Check Middleware in `Startup.Configure`.</span></span>

<span data-ttu-id="f7d7b-220">*DbContextHealthStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-220">*DbContextHealthStartup.cs*:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_Configure)]

<span data-ttu-id="f7d7b-221">Ke spuštění `DbContext` scénář s využitím ukázkové aplikace pro zjišťování, potvrďte, že zadaná pomocí připojovacího řetězce databáze neexistuje v instanci systému SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-221">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="f7d7b-222">Pokud databáze existuje, odstraňte ho.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-222">If the database exists, delete it.</span></span>

<span data-ttu-id="f7d7b-223">Ze složky projektu v příkazovém řádku spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-223">Execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario dbcontext
```

<span data-ttu-id="f7d7b-224">Jakmile je aplikace spuštěna, kontroluje stav tak, že požadavek na `/health` koncový bod v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-224">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="f7d7b-225">Databáze a `AppDbContext` neexistují, takže aplikace poskytuje odpovědi na následující:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-225">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="f7d7b-226">Spusťte ukázkovou aplikaci k vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-226">Trigger the sample app to create the database.</span></span> <span data-ttu-id="f7d7b-227">Vytvořit žádost o `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-227">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="f7d7b-228">Reakce aplikace:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-228">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="f7d7b-229">Vytvořit žádost o `/health` koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-229">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="f7d7b-230">Databáze a kontext existují, takže reakce aplikace:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-230">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="f7d7b-231">Spusťte ukázkovou aplikaci odstranit databázi.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-231">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="f7d7b-232">Vytvořit žádost o `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-232">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="f7d7b-233">Reakce aplikace:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-233">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="f7d7b-234">Vytvořit žádost o `/health` koncového bodu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-234">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="f7d7b-235">Aplikace poskytuje odpověď není v pořádku:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-235">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="f7d7b-236">Samostatné připravenost a testům aktivity</span><span class="sxs-lookup"><span data-stu-id="f7d7b-236">Separate readiness and liveness probes</span></span>

<span data-ttu-id="f7d7b-237">V některých scénářích hostování se používají pár kontroly stavu, který odlišit dvou stavů – stav aplikace:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-237">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="f7d7b-238">Aplikace je funkční, ale není ještě připraven pro příjem požadavků.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-238">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="f7d7b-239">Tento stav se aplikace *připravenosti*.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-239">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="f7d7b-240">Fungovat a reagovat na požadavky aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-240">The app is functioning and responding to requests.</span></span> <span data-ttu-id="f7d7b-241">Tento stav se aplikace *aktivity*.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-241">This state is the app's *liveness*.</span></span>

<span data-ttu-id="f7d7b-242">Kontrola připravenosti obvykle provádí rozsáhlé a časově náročné sadu kontroly, aby zjistil, zda všechny subsystémy aplikace a prostředky jsou k dispozici.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-242">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="f7d7b-243">Kontrola aktivity provádí pouze Rychlá kontrola k určení, zda je k dispozici pro zpracování požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-243">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="f7d7b-244">Po aplikaci předává jeho kontroly připravenosti, je nemusí ovlivnit aplikaci dále sadou nákladné kontroly připravenosti&mdash;další kontroly vyžadují jenom tehdy, vyhledávají se aktivity.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-244">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="f7d7b-245">Ukázková aplikace obsahuje kontrolou stavu k dokončení dlouho běžící úloha po spuštění v hlášení [hostovanou službu](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-245">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="f7d7b-246">`StartupHostedServiceHealthCheck` Zpřístupňuje vlastnost, `StartupTaskCompleted`, který hostovanou službu, můžete nastavit na `true` po dokončení jeho dlouho běžící úlohy (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="f7d7b-246">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="f7d7b-247">Dlouho běžící úlohy na pozadí se spustí [hostovanou službu](xref:fundamentals/host/hosted-services) (*služby/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-247">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="f7d7b-248">Na závěr úkolu `StartupHostedServiceHealthCheck.StartupTaskCompleted` je nastavena na `true`:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-248">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="f7d7b-249">Kontrola stavu je registrovaný pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> v `Startup.ConfigureServices` spolu s hostovanou službu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-249">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="f7d7b-250">Protože hostovanou službu, musíte nastavit vlastnost na kontrolu stavu, kontrola stavu se registruje v kontejneru služby (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="f7d7b-250">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="f7d7b-251">Volat stavu zkontrolujte Middleware v kanálu zpracování aplikace v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-251">Call Health Check Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="f7d7b-252">V ukázkové aplikaci koncových bodů kontroly stavu jsou vytvářeny na `/health/ready` pro kontrolu připravenosti a `/health/live` pro kontroly aktivity.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-252">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="f7d7b-253">Filtry kontroly připravenosti stav doplněk pro kontroly stavu, obraťte se `ready` značky.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-253">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="f7d7b-254">Kontrola aktivity filtruje `StartupHostedServiceHealthCheck` vrácením `false` v [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Další informace najdete v tématu [filtrovat kontroly stavu](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="f7d7b-254">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_Configure)]

<span data-ttu-id="f7d7b-255">Pokud chcete spustit scénář konfigurace připravenosti/aktivity pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-255">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario liveness
```

<span data-ttu-id="f7d7b-256">V prohlížeči, navštivte `/health/ready` několikrát až do 15 sekund prošly.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-256">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="f7d7b-257">Zkontrolujte stav sestavy *není v pořádku* po dobu prvních 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-257">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="f7d7b-258">Po 15 sekundách koncový bod sestav *pořádku*, která odráží dokončení dlouho běžící úlohy v hostované službě.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-258">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="f7d7b-259">Tento příklad také vytvoří vydavatel zkontrolovat stav (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace), který spustí první Kontrola připravenosti s dvěma druhý zpoždění.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-259">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="f7d7b-260">Další informace najdete v tématu [vydavatele zkontrolovat stav](#health-check-publisher) oddílu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-260">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="f7d7b-261">Příklad Kubernetes</span><span class="sxs-lookup"><span data-stu-id="f7d7b-261">Kubernetes example</span></span>

<span data-ttu-id="f7d7b-262">Použití samostatné kontroly připravenosti a aktivity je užitečná v prostředí, jako [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-262">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="f7d7b-263">V systému Kubernetes může být aplikace vyžadují k provedení práce časově náročné spuštění před přijetím požadavků, jako je test podkladové databázi dostupnosti.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-263">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="f7d7b-264">Použití samostatné kontroly umožňuje orchestrator k rozlišení, zda je funkční, ale není ještě připraven aplikace nebo pokud aplikace se nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-264">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="f7d7b-265">Další informace o připravenosti a testům aktivity v Kubernetes najdete v tématu [konfigurace aktivity a testy připravenosti](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) v dokumentaci ke Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-265">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="f7d7b-266">Následující příklad ukazuje konfiguraci sondy připravenosti Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-266">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="f7d7b-267">Kontrolu založenou na metriky se zapisovačem vlastní odpovědi</span><span class="sxs-lookup"><span data-stu-id="f7d7b-267">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="f7d7b-268">Ukázková aplikace předvádí kontrolou stavu paměti se zapisovačem vlastní odpovědi.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-268">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="f7d7b-269">`MemoryHealthCheck` sestavy snížený výkon, pokud aplikace používá více než dané prahové hodnoty paměti (v ukázkové aplikaci 1 GB).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-269">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="f7d7b-270"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> Obsahuje informace o systému uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="f7d7b-270">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="f7d7b-271">Registrace služby kontroly stavu s <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-271">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f7d7b-272">Místo povolení stav zkontrolovat, že předáte <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` je registrována jako služba.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-272">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="f7d7b-273">Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrovaných služeb jsou k dispozici služby kontroly stavu a middlewarem.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-273">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="f7d7b-274">Doporučujeme, abyste registrace služby kontroly stavu jako deklarace služeb typu Singleton.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-274">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="f7d7b-275">*CustomWriterStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-275">*CustomWriterStartup.cs*:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="f7d7b-276">Volat stavu zkontrolujte Middleware v kanálu zpracování aplikace v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-276">Call Health Check Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="f7d7b-277">A `WriteResponse` delegáta je k dispozici na `ResponseWriter` vlastnost výstup vlastní odpověď ve formátu JSON, kdy se spustí Kontrola stavu:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-277">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_Configure&highlight=6)]

<span data-ttu-id="f7d7b-278">`WriteResponse` Metoda formáty `CompositeHealthCheckResult` do JSON objekt a vrátí výstup JSON pro odpověď na kontrolu stavu:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-278">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="f7d7b-279">Pokud chcete spustit kontrolu založenou na metriku s výstupní zapisovač vlastní odpovědi pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-279">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="f7d7b-280">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje na základě metrik stavu zaškrtnutí scénářů, včetně disk storage a maximální hodnota aktivity kontroly.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-280">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="f7d7b-281">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) je port [BeatPulse](https://github.com/xabaril/beatpulse) a není zachována nebo podporovaný společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-281">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) is a port of [BeatPulse](https://github.com/xabaril/beatpulse) and isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="f7d7b-282">Filtrovat podle portu</span><span class="sxs-lookup"><span data-stu-id="f7d7b-282">Filter by port</span></span>

<span data-ttu-id="f7d7b-283">Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> s portem omezuje žádostí o kontrolu stavu na zadaný port.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-283">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="f7d7b-284">To se obvykle používá v prostředí kontejneru zpřístupňuje porty pro monitorování služby.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-284">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="f7d7b-285">Ukázková aplikace se nakonfiguruje port pomocí [poskytovatele konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-285">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="f7d7b-286">Port je nastavena v *launchSettings.json* souboru a předána zprostředkovateli konfigurace přes proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-286">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="f7d7b-287">Musíte také nakonfigurovat server tak, aby naslouchala na žádosti na portu pro správu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-287">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="f7d7b-288">Ukázková aplikace použít k předvedení správy konfigurace portů, vytvořte *launchSettings.json* ve *vlastnosti* složky.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-288">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="f7d7b-289">Následující *launchSettings.json* soubor není zahrnutý v souborech projektu ukázkovou aplikaci a musí být vytvořeny ručně.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-289">The following *launchSettings.json* file isn't included in the sample app's project files and must be created manually.</span></span>

<span data-ttu-id="f7d7b-290">*Properties/launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-290">*Properties/launchSettings.json*:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="f7d7b-291">Registrace služby kontroly stavu s <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-291">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f7d7b-292">Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> Určuje port pro správu (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="f7d7b-292">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=12,18)]

> [!NOTE]
> <span data-ttu-id="f7d7b-293">Vytváří se můžete vyhnout *launchSettings.json* soubor v ukázkové aplikaci tak, že nastavíte adresy URL a port pro správu explicitně v kódu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-293">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="f7d7b-294">V *Program.cs* kde <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> je vytvořen, přidejte volání do <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> a poskytovat normální odpovědi koncového bodu aplikace a portu bodu správy.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-294">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="f7d7b-295">V *ManagementPortStartup.cs* kde <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> je volána, explicitně zadat port pro správu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-295">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="f7d7b-296">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-296">*Program.cs*:</span></span>
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> <span data-ttu-id="f7d7b-297">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-297">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="f7d7b-298">Pokud chcete spustit scénář konfigurace portu správy pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém řádku:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-298">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="f7d7b-299">Distribuovat knihovny kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="f7d7b-299">Distribute a health check library</span></span>

<span data-ttu-id="f7d7b-300">Kontrola stavu distribuce jako knihovna:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-300">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="f7d7b-301">Zápis kontrolou stavu, který implementuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní jako samostatná třída.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-301">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="f7d7b-302">Třídu můžete spolehnout na [injektáž závislostí (DI)](xref:fundamentals/dependency-injection), zadejte aktivace, a [s názvem možnosti](xref:fundamentals/configuration/options) pro přístup k datům konfigurace.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-302">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   // Health check logic
                   //
                   // data1 and data2 are used in the method to
                   // run the probe's health check logic.

                   // Assume that it's possible for this health check
                   // to throw an AccessViolationException.

                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. <span data-ttu-id="f7d7b-303">Zápis metody rozšíření s parametry, které náročné aplikace volá operaci v jeho `Startup.Configure` metoda.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-303">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="f7d7b-304">V následujícím příkladu předpokládejme následující podpis metody kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-304">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="f7d7b-305">Předchozí signatura Určuje, který `ExampleHealthCheck` vyžaduje další data k procesu stav zkontrolujte logiku testu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-305">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="f7d7b-306">Data se poskytuje pro delegáta, používá k vytvoření instance kontroly stavu, když je kontrola stavu registrovaný pomocí metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-306">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="f7d7b-307">V následujícím příkladu Určuje volitelný volající:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-307">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="f7d7b-308">Název kontroly stavu (`name`).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-308">health check name (`name`).</span></span> <span data-ttu-id="f7d7b-309">Pokud `null`, `example_health_check` se používá.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-309">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="f7d7b-310">řetězcový datový bod pro kontroly stavu (`data1`).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-310">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="f7d7b-311">celočíselný datový bod pro kontroly stavu (`data2`).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-311">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="f7d7b-312">Pokud `null`, `1` se používá.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-312">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="f7d7b-313">stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-313">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="f7d7b-314">Výchozí hodnota je `null`.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-314">The default is `null`.</span></span> <span data-ttu-id="f7d7b-315">Pokud `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) se použije v hlášení stavu selhání.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-315">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="f7d7b-316">značky (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-316">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string NAME = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? NAME,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="f7d7b-317">Vydavatel kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="f7d7b-317">Health Check Publisher</span></span>

<span data-ttu-id="f7d7b-318">Když <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> se přidá do kontejneru služby systém kontroly stavu pravidelně provádí kontroly stavu a volání `PublishAsync` k výsledku.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-318">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="f7d7b-319">To je užitečné v monitorování systému scénář, který očekává, že každý proces pro volání systém sledování pravidelně aby bylo možné zjistit stav nabízené stavu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-319">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="f7d7b-320"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Rozhraní obsahuje jedinou metodu:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-320">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="f7d7b-321"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> Můžete tak nastavit:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-321"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="f7d7b-322"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; Počáteční zpoždění byla aplikována za aplikace se spustí před spuštěním <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instancí.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-322"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="f7d7b-323">Zpoždění je použít jednou při spuštění a neplatí pro dalších iteracích.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-323">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="f7d7b-324">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-324">The default value is five seconds.</span></span>
* <span data-ttu-id="f7d7b-325"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; Období <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> spuštění.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-325"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="f7d7b-326">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-326">The default value is 30 seconds.</span></span>
* <span data-ttu-id="f7d7b-327"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; Pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> je `null` (výchozí), služba stavu zaškrtnutí vydavatele běží všechny kontroly stavu registrované.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-327"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="f7d7b-328">Pro spuštění podmnožiny kontroly stavu, poskytují funkce, která filtruje sadu kontroly.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-328">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="f7d7b-329">Predikát je vyhodnocen jednotlivých období.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-329">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="f7d7b-330"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; Časový limit pro provedení stavu kontroluje všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instancí.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-330"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="f7d7b-331">Použití <xref:System.Threading.Timeout.InfiniteTimeSpan> provádět bez časového limitu.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-331">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="f7d7b-332">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-332">The default value is 30 seconds.</span></span>

::: moniker range="= aspnetcore-2.2"

> [!WARNING]
> <span data-ttu-id="f7d7b-333">Ve verzi 2.2 technologie ASP.NET Core, nastavení <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> není kompilátorem respektovány <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> provádění; nastaví hodnotu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-333">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="f7d7b-334">Tento problém bude opraven v ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-334">This issue will be fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="f7d7b-335">Další informace najdete v tématu [HealthCheckPublisherOptions.Period nastaví hodnotu vlastnosti. Zpoždění](https://github.com/aspnet/Extensions/issues/1041).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-335">For more information, see [HealthCheckPublisherOptions.Period sets the value of .Delay](https://github.com/aspnet/Extensions/issues/1041).</span></span>

::: moniker-end

<span data-ttu-id="f7d7b-336">V ukázkové aplikaci `ReadinessPublisher` je <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-336">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="f7d7b-337">Kontrola stavu se zaznamená do `Entries` nebude úspěšné a pro každou kontrolu:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-337">The health check status is recorded in `Entries` and logged for each check:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=20,22-23)]

<span data-ttu-id="f7d7b-338">V ukázkové aplikaci `LivenessProbeStartup` například `StartupHostedService` kontroly připravenosti má dvě druhý zpoždění spuštění a spustí kontrolu každých 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-338">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="f7d7b-339">K aktivaci <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> zaregistruje implementaci vzorku `ReadinessPublisher` jako služba typu singleton v [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-339">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

::: moniker range="= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="f7d7b-340">Následující alternativní řešení umožňuje přidat <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance služby kontejneru, když jeden nebo více jiných hostované služby již byly přidány do aplikace.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-340">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="f7d7b-341">Toto řešení nevyžaduje verzi technologie ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-341">This workaround won't be required with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="f7d7b-342">Další informace najdete v tématu: https://github.com/aspnet/Extensions/issues/639.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-342">For more information, see: https://github.com/aspnet/Extensions/issues/639.</span></span>
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="f7d7b-343">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="f7d7b-343">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="f7d7b-344">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) je port [BeatPulse](https://github.com/xabaril/beatpulse) a není zachována nebo podporovaný společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-344">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) is a port of [BeatPulse](https://github.com/xabaril/beatpulse) and isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="f7d7b-345">Omezení kontroly stavu s MapWhen</span><span class="sxs-lookup"><span data-stu-id="f7d7b-345">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="f7d7b-346">Použití <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> podmíněné větvi požadavku kanálu pro stav zkontrolujte koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-346">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="f7d7b-347">V následujícím příkladu `MapWhen` větve k aktivaci Middleware zkontrolovat stav, pokud požadavek GET pro kanál požadavku `api/HealthCheck` koncový bod:</span><span class="sxs-lookup"><span data-stu-id="f7d7b-347">In the following example, `MapWhen` branches the request pipeline to activate Health Check Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="f7d7b-348">Další informace naleznete v tématu <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="f7d7b-348">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>
