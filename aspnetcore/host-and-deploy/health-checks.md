---
title: Zdravotní kontroly v ASP.NET Core
author: rick-anderson
description: Přečtěte si, jak nastavit kontroly stavu pro ASP.NET základní infrastruktury, jako jsou aplikace a databáze.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: 314e55c818cddf1dad2e3ec74d4d1e041ce7366f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664883"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="a8edd-103">Zdravotní kontroly v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8edd-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="a8edd-104">Podle [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="a8edd-104">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a8edd-105">ASP.NET Core nabízí middlewar a knihovny kontroly stavu pro vykazování stavu součástí infrastruktury aplikací.</span><span class="sxs-lookup"><span data-stu-id="a8edd-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="a8edd-106">Kontroly stavu jsou vystaveny aplikací jako koncové body HTTP.</span><span class="sxs-lookup"><span data-stu-id="a8edd-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="a8edd-107">Koncové body kontroly stavu lze nakonfigurovat pro různé scénáře monitorování v reálném čase:</span><span class="sxs-lookup"><span data-stu-id="a8edd-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="a8edd-108">Sondy stavu lze použít orchestrátory kontejnerů a nástroj pro vyrovnávání zatížení ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="a8edd-109">Orchestrátor kontejneru může například reagovat na selhávající kontrolu stavu zastavením postupného nasazení nebo restartováním kontejneru.</span><span class="sxs-lookup"><span data-stu-id="a8edd-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="a8edd-110">Vyrovnávání zatížení může reagovat na nefunkční aplikace směrováním provozu od instance selhání do instance v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a8edd-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="a8edd-111">Použití paměti, disku a dalších prostředků fyzického serveru lze sledovat z důvodu stavu v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a8edd-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="a8edd-112">Kontroly stavu můžete otestovat závislosti aplikace, jako jsou databáze a koncové body externí služby, k potvrzení dostupnosti a normální fungování.</span><span class="sxs-lookup"><span data-stu-id="a8edd-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="a8edd-113">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="a8edd-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a8edd-114">Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="a8edd-115">Chcete-li spustit ukázkovou aplikaci pro daný scénář, použijte příkaz [dotnet run](/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a8edd-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="a8edd-116">Podrobnosti o tom, jak používat ukázkovou aplikaci, najdete v *README.md* souboru ukázkové aplikace a v popisech scénářů v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a8edd-117">Požadavky</span><span class="sxs-lookup"><span data-stu-id="a8edd-117">Prerequisites</span></span>

<span data-ttu-id="a8edd-118">Kontroly stavu se obvykle používají s externí monitorovací služby nebo kontejner orchestrátor u zkontrolovat stav aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="a8edd-119">Před přidáním kontrolstavu do aplikace se rozhodněte, který monitorovací systém chcete použít.</span><span class="sxs-lookup"><span data-stu-id="a8edd-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="a8edd-120">Systém monitorování určuje, jaké typy kontrol stavu chcete vytvořit a jak nakonfigurovat jejich koncové body.</span><span class="sxs-lookup"><span data-stu-id="a8edd-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="a8edd-121">Balíček [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) je implicitně odkazován pro aplikace ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8edd-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="a8edd-122">Chcete-li provádět kontroly stavu pomocí core entity frameworku, přidejte odkaz na balíček [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore.](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="a8edd-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="a8edd-123">Ukázková aplikace poskytuje spouštěcí kód pro demonstraci kontroly stavu pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="a8edd-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="a8edd-124">Scénář [databázové sondy](#database-probe) kontroluje stav připojení k databázi pomocí [aspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="a8edd-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="a8edd-125">[Scénář sondy DbContext](#entity-framework-core-dbcontext-probe) zkontroluje `DbContext`databázi pomocí EF Core .</span><span class="sxs-lookup"><span data-stu-id="a8edd-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="a8edd-126">Chcete-li prozkoumat scénáře databáze, ukázková aplikace:</span><span class="sxs-lookup"><span data-stu-id="a8edd-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="a8edd-127">Vytvoří databázi a poskytuje její připojovací řetězec v souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="a8edd-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="a8edd-128">Má následující odkazy na balíček v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="a8edd-129">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="a8edd-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="a8edd-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="a8edd-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="a8edd-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a8edd-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="a8edd-132">Jiný scénář kontroly stavu ukazuje, jak filtrovat kontroly stavu na port správy.</span><span class="sxs-lookup"><span data-stu-id="a8edd-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="a8edd-133">Ukázková aplikace vyžaduje vytvoření souboru *Properties/launchSettings.json,* který obsahuje adresu URL pro správu a port pro správu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="a8edd-134">Další informace naleznete v části [Filtr podle portu.](#filter-by-port)</span><span class="sxs-lookup"><span data-stu-id="a8edd-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="a8edd-135">Základní zdravotní sonda</span><span class="sxs-lookup"><span data-stu-id="a8edd-135">Basic health probe</span></span>

<span data-ttu-id="a8edd-136">Pro mnoho aplikací stačí ke zjištění stavu aplikace základní konfigurace sondy stavu, která hlásí dostupnost aplikace pro zpracování požadavků *(živost).*</span><span class="sxs-lookup"><span data-stu-id="a8edd-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="a8edd-137">Základní konfigurace registruje služby kontroly stavu a volá middleware kontroly stavu, aby odpověděl v koncovém bodě adresy URL s odpovědí na stav.</span><span class="sxs-lookup"><span data-stu-id="a8edd-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="a8edd-138">Ve výchozím nastavení nejsou registrovány žádné konkrétní kontroly stavu k testování jakékoli konkrétní závislosti nebo subsystému.</span><span class="sxs-lookup"><span data-stu-id="a8edd-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="a8edd-139">Aplikace je považována za v pořádku, pokud je schopná reagovat na adresu URL koncového bodu stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="a8edd-140">Výchozí zapisovač odpovědí<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>zapíše stav ( ) jako odpověď ve formátu prostého textu zpět klientovi, což označuje stav [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [HealthStatus.Unhealthy.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)</span><span class="sxs-lookup"><span data-stu-id="a8edd-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="a8edd-141">Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v .</span><span class="sxs-lookup"><span data-stu-id="a8edd-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a8edd-142">Vytvořte koncový bod kontroly stavu voláním `MapHealthChecks` v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a8edd-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="a8edd-143">V ukázkové aplikaci se vytvoří koncový `/health` bod kontroly stavu na (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a8edd-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

<span data-ttu-id="a8edd-144">Chcete-li spustit základní scénář konfigurace pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a8edd-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="a8edd-145">Příklad Dockeru</span><span class="sxs-lookup"><span data-stu-id="a8edd-145">Docker example</span></span>

<span data-ttu-id="a8edd-146">[Docker](xref:host-and-deploy/docker/index) nabízí integrovanou `HEALTHCHECK` direktivu, kterou lze použít ke kontrole stavu aplikace, která používá základní konfiguraci kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="a8edd-147">Vytvořit kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-147">Create health checks</span></span>

<span data-ttu-id="a8edd-148">Kontroly stavu jsou vytvořeny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> implementací rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a8edd-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="a8edd-149">Metoda <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> vrátí, která označuje `Healthy`stav `Degraded`jako `Unhealthy`, nebo .</span><span class="sxs-lookup"><span data-stu-id="a8edd-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="a8edd-150">Výsledek je zapsán jako odpověď ve formátu prostého textu s konfigurovatelným stavovým kódem (konfigurace je popsána v části [Možnosti kontroly stavu).](#health-check-options)</span><span class="sxs-lookup"><span data-stu-id="a8edd-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="a8edd-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>můžete také vrátit volitelné dvojice klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="a8edd-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="a8edd-152">Následující `ExampleHealthCheck` třída ukazuje rozložení kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="a8edd-153">Logika kontroly stavu `CheckHealthAsync` je umístěn v metodě.</span><span class="sxs-lookup"><span data-stu-id="a8edd-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="a8edd-154">Následující příklad nastaví fiktivní `healthCheckResultHealthy`proměnnou `true`, na .</span><span class="sxs-lookup"><span data-stu-id="a8edd-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="a8edd-155">Pokud `healthCheckResultHealthy` je hodnota nastavena na `false`, je vrácen stav [HealthCheckResult.Unhealthy.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*)</span><span class="sxs-lookup"><span data-stu-id="a8edd-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a><span data-ttu-id="a8edd-156">Registrace služeb kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-156">Register health check services</span></span>

<span data-ttu-id="a8edd-157">Typ `ExampleHealthCheck` je přidán do služeb <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `Startup.ConfigureServices`kontroly stavu s v :</span><span class="sxs-lookup"><span data-stu-id="a8edd-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="a8edd-158">Přetížení <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> uvedené v následujícím příkladu nastaví stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) hlásit, když kontrola stavu hlásí selhání.</span><span class="sxs-lookup"><span data-stu-id="a8edd-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="a8edd-159">Pokud je stav selhání `null` nastaven na (výchozí), [healthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) je hlášena.</span><span class="sxs-lookup"><span data-stu-id="a8edd-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="a8edd-160">Toto přetížení je užitečný scénář pro autory knihovny, kde stav selhání označený knihovnou je vynuceno aplikací při selhání kontroly stavu dojde, pokud implementace kontroly stavu respektuje nastavení.</span><span class="sxs-lookup"><span data-stu-id="a8edd-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="a8edd-161">*Značky* lze použít k filtrování kontrol stavu (popsané dále v části [Kontroly stavu filtru).](#filter-health-checks)</span><span class="sxs-lookup"><span data-stu-id="a8edd-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="a8edd-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>může také spustit funkci lambda.</span><span class="sxs-lookup"><span data-stu-id="a8edd-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="a8edd-163">V následujícím příkladu je zadán název `Example` kontroly stavu a kontrola vždy vrátí stav v pořádku:</span><span class="sxs-lookup"><span data-stu-id="a8edd-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="a8edd-164">Volání <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> předat argumenty implementace kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="a8edd-165">V následujícím příkladu `TestHealthCheckWithArgs` přijímá celé číslo a řetězec <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> pro použití při volání:</span><span class="sxs-lookup"><span data-stu-id="a8edd-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

<span data-ttu-id="a8edd-166">`TestHealthCheckWithArgs`je registrován `AddTypeActivatedCheck` voláním s celé číslo a řetězec předán do implementace:</span><span class="sxs-lookup"><span data-stu-id="a8edd-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="a8edd-167">Použít směrování kontrol stavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-167">Use Health Checks Routing</span></span>

<span data-ttu-id="a8edd-168">V `Startup.Configure`aplikace `MapHealthChecks` volejte tvůrce koncového bodu s adresou URL koncového bodu nebo relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="a8edd-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="a8edd-169">Vyžadovat hostitele</span><span class="sxs-lookup"><span data-stu-id="a8edd-169">Require host</span></span>

<span data-ttu-id="a8edd-170">Volání `RequireHost` k určení jednoho nebo více povolených hostitelů pro koncový bod kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="a8edd-171">Hostitelé by měl být Unicode spíše než punycode a může obsahovat port.</span><span class="sxs-lookup"><span data-stu-id="a8edd-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="a8edd-172">Pokud kolekce není zadána, je přijat libovolný hostitel.</span><span class="sxs-lookup"><span data-stu-id="a8edd-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="a8edd-173">Další informace naleznete v části [Filtr podle portu.](#filter-by-port)</span><span class="sxs-lookup"><span data-stu-id="a8edd-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="a8edd-174">Vyžadovat autorizaci</span><span class="sxs-lookup"><span data-stu-id="a8edd-174">Require authorization</span></span>

<span data-ttu-id="a8edd-175">Volání `RequireAuthorization` ke spuštění nástroje Middleware autorizace v koncovém bodě požadavku na kontrolu stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="a8edd-176">Přetížení `RequireAuthorization` přijímá jednu nebo více zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="a8edd-177">Pokud zásada není k dispozici, použije se výchozí zásady autorizace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="a8edd-178">Povolení žádostí nepůvodního zdroje (CORS)</span><span class="sxs-lookup"><span data-stu-id="a8edd-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="a8edd-179">Přestože ruční provádění kontrol stavu z prohlížeče není běžný mů e se používá, může být middlewar CORS povolen voláním `RequireCors` koncových bodů kontrol stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="a8edd-180">Přetížení `RequireCors` přijímá delegáta tvůrce zásad`CorsPolicyBuilder`CORS ( ) nebo název zásady.</span><span class="sxs-lookup"><span data-stu-id="a8edd-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="a8edd-181">Pokud zásada není k dispozici, použije se výchozí zásada CORS.</span><span class="sxs-lookup"><span data-stu-id="a8edd-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="a8edd-182">Další informace naleznete v tématu <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="a8edd-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="a8edd-183">Možnosti kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-183">Health check options</span></span>

<span data-ttu-id="a8edd-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>poskytnout možnost přizpůsobit chování kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="a8edd-185">Filtrování kontrol stavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="a8edd-186">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="a8edd-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="a8edd-187">Potlačit záhlaví mezipaměti</span><span class="sxs-lookup"><span data-stu-id="a8edd-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="a8edd-188">Přizpůsobit výstup</span><span class="sxs-lookup"><span data-stu-id="a8edd-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="a8edd-189">Filtrování kontrol stavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-189">Filter health checks</span></span>

<span data-ttu-id="a8edd-190">Ve výchozím nastavení middleware kontroly stavu spouští všechny registrované kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="a8edd-191">Chcete-li spustit podmnožinu kontrol stavu, zadejte <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> funkci, která vrátí logickou hodnotu této možnosti.</span><span class="sxs-lookup"><span data-stu-id="a8edd-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="a8edd-192">V následujícím příkladu `Bar` je kontrola stavu odfiltrována`bar_tag`podle značky ( ) `true` v podmíněném příkazu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> funkce, kde je vrácena pouze v případě, že vlastnost kontroly stavu odpovídá `foo_tag` nebo `baz_tag`:</span><span class="sxs-lookup"><span data-stu-id="a8edd-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="a8edd-193">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a8edd-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="a8edd-194">V `Startup.Configure`oblasti `Predicate` filtruje kontrolu stavu "Bar".</span><span class="sxs-lookup"><span data-stu-id="a8edd-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="a8edd-195">Pouze Foo a Baz provést.:</span><span class="sxs-lookup"><span data-stu-id="a8edd-195">Only Foo and Baz execute.:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="a8edd-196">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="a8edd-196">Customize the HTTP status code</span></span>

<span data-ttu-id="a8edd-197">Slouží <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> k přizpůsobení mapování stavu na stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="a8edd-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="a8edd-198">Následující <xref:Microsoft.AspNetCore.Http.StatusCodes> přiřazení jsou výchozí hodnoty používané middleware.</span><span class="sxs-lookup"><span data-stu-id="a8edd-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="a8edd-199">Změňte hodnoty stavového kódu tak, aby vyhovovaly vašim požadavkům.</span><span class="sxs-lookup"><span data-stu-id="a8edd-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="a8edd-200">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a8edd-200">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="a8edd-201">Potlačit záhlaví mezipaměti</span><span class="sxs-lookup"><span data-stu-id="a8edd-201">Suppress cache headers</span></span>

<span data-ttu-id="a8edd-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>určuje, zda middleware kontroly stavu přidá do odpovědi sondy hlavičky HTTP, aby se zabránilo ukládání odpovědi do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="a8edd-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="a8edd-203">Pokud `false` je hodnota (výchozí), middleware nastaví `Cache-Control` `Expires`nebo `Pragma` přepíše , a záhlaví, aby se zabránilo ukládání odpovědi do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="a8edd-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="a8edd-204">Pokud je `true`hodnota , middleware nemění cache záhlaví odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a8edd-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="a8edd-205">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a8edd-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="a8edd-206">Přizpůsobit výstup</span><span class="sxs-lookup"><span data-stu-id="a8edd-206">Customize output</span></span>

<span data-ttu-id="a8edd-207">V `Startup.Configure`aplikaci nastavte možnost [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) na delegáta pro zápis odpovědi:</span><span class="sxs-lookup"><span data-stu-id="a8edd-207">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="a8edd-208">Výchozí delegát zapíše minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [Stavu.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="a8edd-208">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="a8edd-209">Následující vlastní delegáti výstup vlastní odpověď JSON.</span><span class="sxs-lookup"><span data-stu-id="a8edd-209">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="a8edd-210">První příklad ukázkové aplikace ukazuje, <xref:System.Text.Json?displayProperty=fullName>jak používat :</span><span class="sxs-lookup"><span data-stu-id="a8edd-210">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="a8edd-211">Druhý příklad ukazuje, jak používat [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span><span class="sxs-lookup"><span data-stu-id="a8edd-211">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="a8edd-212">V ukázkové aplikaci `SYSTEM_TEXT_JSON` zakomentujte [direktivu preprocesoru](xref:index#preprocessor-directives-in-sample-code) v *CustomWriterStartup.cs* povolit `Newtonsoft.Json` verzi aplikace `WriteResponse`.</span><span class="sxs-lookup"><span data-stu-id="a8edd-212">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="a8edd-213">Rozhraní API pro kontrolu stavu neposkytuje integrovanou podporu pro složité formáty vrácení JSON, protože formát je specifický pro váš výběr monitorovacího systému.</span><span class="sxs-lookup"><span data-stu-id="a8edd-213">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="a8edd-214">Podle potřeby upravte odpověď v předchozích příkladech.</span><span class="sxs-lookup"><span data-stu-id="a8edd-214">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="a8edd-215">Další informace o serializaci JSON pomocí `System.Text.Json`naleznete v [tématu Serializace a deserializace zařízení JSON v rozhraní .NET](/dotnet/standard/serialization/system-text-json-how-to).</span><span class="sxs-lookup"><span data-stu-id="a8edd-215">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="a8edd-216">Databázová sonda</span><span class="sxs-lookup"><span data-stu-id="a8edd-216">Database probe</span></span>

<span data-ttu-id="a8edd-217">Kontrola stavu můžete zadat databázový dotaz spustit jako logický test k označení, zda databáze reaguje normálně.</span><span class="sxs-lookup"><span data-stu-id="a8edd-217">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="a8edd-218">Ukázková aplikace používá [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), knihovnu kontroly stavu pro ASP.NET aplikace Core, k provedení kontroly stavu v databázi serveru SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a8edd-218">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="a8edd-219">`AspNetCore.Diagnostics.HealthChecks`provede `SELECT 1` dotaz proti databázi k potvrzení připojení k databázi je v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a8edd-219">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="a8edd-220">Při kontrole připojení k databázi s dotazem zvolte dotaz, který se vrací rychle.</span><span class="sxs-lookup"><span data-stu-id="a8edd-220">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="a8edd-221">Přístup dotazu spustí riziko přetížení databáze a snížení výkonu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-221">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="a8edd-222">Ve většině případů není nutné spustit testovací dotaz.</span><span class="sxs-lookup"><span data-stu-id="a8edd-222">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="a8edd-223">Pouze vytvoření úspěšného připojení k databázi je dostačující.</span><span class="sxs-lookup"><span data-stu-id="a8edd-223">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="a8edd-224">Pokud zjistíte, že je nutné spustit dotaz, zvolte jednoduchý dotaz SELECT, například `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="a8edd-224">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="a8edd-225">Zahrňte odkaz na balíček [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="a8edd-225">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="a8edd-226">Zadej platný připojovací řetězec databáze do souboru *appsettings.json* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-226">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="a8edd-227">Aplikace používá databázi SERVERU `HealthCheckSample`SQL Server s názvem :</span><span class="sxs-lookup"><span data-stu-id="a8edd-227">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="a8edd-228">Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v .</span><span class="sxs-lookup"><span data-stu-id="a8edd-228">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a8edd-229">Ukázková aplikace `AddSqlServer` volá metodu s připojovacím řetězcem databáze (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a8edd-229">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a8edd-230">Koncový bod kontroly stavu `MapHealthChecks` se `Startup.Configure`vytvoří voláním :</span><span class="sxs-lookup"><span data-stu-id="a8edd-230">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="a8edd-231">Chcete-li spustit scénář databázové sondy pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a8edd-231">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="a8edd-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a8edd-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="a8edd-233">Sonda Core DbContext rozhraní entity frameworku</span><span class="sxs-lookup"><span data-stu-id="a8edd-233">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="a8edd-234">Kontrola `DbContext` potvrzuje, že aplikace může komunikovat s databází `DbContext`nakonfigurovanou pro jádro EF .</span><span class="sxs-lookup"><span data-stu-id="a8edd-234">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="a8edd-235">Kontrola `DbContext` je podporována v aplikacích, které:</span><span class="sxs-lookup"><span data-stu-id="a8edd-235">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="a8edd-236">Použít [jádro entity frameworku (EF).](/ef/core/)</span><span class="sxs-lookup"><span data-stu-id="a8edd-236">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="a8edd-237">Zahrňte odkaz na balíček [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="a8edd-237">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="a8edd-238">`AddDbContextCheck<TContext>`zaregistruje kontrolu stavu `DbContext`pro .</span><span class="sxs-lookup"><span data-stu-id="a8edd-238">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="a8edd-239">Dodává `DbContext` se jako `TContext` metoda.</span><span class="sxs-lookup"><span data-stu-id="a8edd-239">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="a8edd-240">Přetížení je k dispozici ke konfiguraci stavu selhání, značky a vlastní testovací dotaz.</span><span class="sxs-lookup"><span data-stu-id="a8edd-240">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="a8edd-241">Ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="a8edd-241">By default:</span></span>

* <span data-ttu-id="a8edd-242">Volání `DbContextHealthCheck` metody EF `CanConnectAsync` Core.</span><span class="sxs-lookup"><span data-stu-id="a8edd-242">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="a8edd-243">Můžete přizpůsobit, co operace je spuštěna při kontrole stavu pomocí `AddDbContextCheck` přetížení metody.</span><span class="sxs-lookup"><span data-stu-id="a8edd-243">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="a8edd-244">Název kontroly stavu je název `TContext` typu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-244">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="a8edd-245">V ukázkové `AppDbContext` aplikaci je `AddDbContextCheck` poskytována a `Startup.ConfigureServices` registrována jako služba v (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a8edd-245">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a8edd-246">Koncový bod kontroly stavu `MapHealthChecks` se `Startup.Configure`vytvoří voláním :</span><span class="sxs-lookup"><span data-stu-id="a8edd-246">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="a8edd-247">Chcete-li `DbContext` spustit scénář probe pomocí ukázkové aplikace, zkontrolujte, že databáze určená připojovacím řetězcem neexistuje v instanci serveru SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a8edd-247">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="a8edd-248">Pokud databáze existuje, odstraňte ji.</span><span class="sxs-lookup"><span data-stu-id="a8edd-248">If the database exists, delete it.</span></span>

<span data-ttu-id="a8edd-249">Proveďte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a8edd-249">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="a8edd-250">Po spuštění aplikace zkontrolujte stav tím, že `/health` žádost o koncový bod v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="a8edd-250">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="a8edd-251">Databáze a `AppDbContext` neexistují, takže aplikace poskytuje následující odpověď:</span><span class="sxs-lookup"><span data-stu-id="a8edd-251">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="a8edd-252">Aktivujte ukázkovou aplikaci k vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="a8edd-252">Trigger the sample app to create the database.</span></span> <span data-ttu-id="a8edd-253">Požádejte o `/createdatabase`to, aby byla .</span><span class="sxs-lookup"><span data-stu-id="a8edd-253">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="a8edd-254">Aplikace odpovídá:</span><span class="sxs-lookup"><span data-stu-id="a8edd-254">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="a8edd-255">Vytvořte požadavek `/health` na koncový bod.</span><span class="sxs-lookup"><span data-stu-id="a8edd-255">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="a8edd-256">Databáze a kontext existují, takže aplikace odpovídá:</span><span class="sxs-lookup"><span data-stu-id="a8edd-256">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="a8edd-257">Spusťte ukázkovou aplikaci k odstranění databáze.</span><span class="sxs-lookup"><span data-stu-id="a8edd-257">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="a8edd-258">Požádejte o `/deletedatabase`to, aby byla .</span><span class="sxs-lookup"><span data-stu-id="a8edd-258">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="a8edd-259">Aplikace odpovídá:</span><span class="sxs-lookup"><span data-stu-id="a8edd-259">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="a8edd-260">Vytvořte požadavek `/health` na koncový bod.</span><span class="sxs-lookup"><span data-stu-id="a8edd-260">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="a8edd-261">Aplikace poskytuje nefunkční odpověď:</span><span class="sxs-lookup"><span data-stu-id="a8edd-261">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="a8edd-262">Samostatné sondy připravenosti a živosti</span><span class="sxs-lookup"><span data-stu-id="a8edd-262">Separate readiness and liveness probes</span></span>

<span data-ttu-id="a8edd-263">V některých scénářích hostování se používá dvojice kontrol stavu, které rozlišují dva stavy aplikace:</span><span class="sxs-lookup"><span data-stu-id="a8edd-263">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="a8edd-264">Aplikace funguje, ale ještě není připravena přijímat požadavky.</span><span class="sxs-lookup"><span data-stu-id="a8edd-264">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="a8edd-265">Tento stav je *připravenost*aplikace .</span><span class="sxs-lookup"><span data-stu-id="a8edd-265">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="a8edd-266">Aplikace funguje a odpovídá na požadavky.</span><span class="sxs-lookup"><span data-stu-id="a8edd-266">The app is functioning and responding to requests.</span></span> <span data-ttu-id="a8edd-267">Tento stav je *živost*aplikace .</span><span class="sxs-lookup"><span data-stu-id="a8edd-267">This state is the app's *liveness*.</span></span>

<span data-ttu-id="a8edd-268">Kontrola připravenosti obvykle provádí rozsáhlejší a časově náročnou sadu kontrol, aby zjistila, zda jsou k dispozici všechny subsystémy a prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-268">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="a8edd-269">Kontrola živosti pouze provádí rychlou kontrolu, aby zjistila, zda je aplikace k dispozici pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="a8edd-269">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="a8edd-270">Poté, co aplikace projde kontrolou připravenosti, není třeba aplikaci dále zatěžovat nákladnou sadou kontrol&mdash;připravenosti, další kontroly vyžadují pouze kontrolu živosti.</span><span class="sxs-lookup"><span data-stu-id="a8edd-270">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="a8edd-271">Ukázková aplikace obsahuje kontrolu stavu, která hlásí dokončení dlouhotrvající úlohy spuštění v [hostované službě](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="a8edd-271">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="a8edd-272">Zpřístupní `StartupHostedServiceHealthCheck` vlastnost, `StartupTaskCompleted`, že hostovaná služba `true` můžete nastavit, když je dokončena její dlouhotrvající úloha (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="a8edd-272">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="a8edd-273">Dlouhotrvající úloha na pozadí je [spuštěna hostovkou](xref:fundamentals/host/hosted-services) *(Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="a8edd-273">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="a8edd-274">Na závěr úkolu, `StartupHostedServiceHealthCheck.StartupTaskCompleted` je nastavena na `true`:</span><span class="sxs-lookup"><span data-stu-id="a8edd-274">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="a8edd-275">Kontrola stavu je <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> registrována spolu `Startup.ConfigureServices` s hostovkou služby.</span><span class="sxs-lookup"><span data-stu-id="a8edd-275">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="a8edd-276">Vzhledem k tomu, že hostovaná služba musí nastavit vlastnost při kontrole stavu, je kontrola stavu také registrována v kontejneru služby (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a8edd-276">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a8edd-277">Koncový bod kontroly stavu `MapHealthChecks` je `Startup.Configure`vytvořen voláním v .</span><span class="sxs-lookup"><span data-stu-id="a8edd-277">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="a8edd-278">V ukázkové aplikaci jsou koncové body kontroly stavu vytvořeny na adrese:</span><span class="sxs-lookup"><span data-stu-id="a8edd-278">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="a8edd-279">`/health/ready`pro kontrolu připravenosti.</span><span class="sxs-lookup"><span data-stu-id="a8edd-279">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="a8edd-280">Kontrola připravenosti filtruje kontroly stavu `ready` na kontrolu stavu se značkou.</span><span class="sxs-lookup"><span data-stu-id="a8edd-280">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="a8edd-281">`/health/live`pro kontrolu živosti.</span><span class="sxs-lookup"><span data-stu-id="a8edd-281">`/health/live` for the liveness check.</span></span> <span data-ttu-id="a8edd-282">Kontrola živosti filtruje `StartupHostedServiceHealthCheck` zpět `false` v [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (další informace naleznete v [tématu Filtrování kontrol stavu](#filter-health-checks))</span><span class="sxs-lookup"><span data-stu-id="a8edd-282">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="a8edd-283">V následujícím příkladu kódu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-283">In the following example code:</span></span>

* <span data-ttu-id="a8edd-284">Kontrola připravenosti používá všechny registrované šeky se značkou "ready".</span><span class="sxs-lookup"><span data-stu-id="a8edd-284">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="a8edd-285">Nezahrnuje `Predicate` všechny kontroly a vrátit 200-Ok.</span><span class="sxs-lookup"><span data-stu-id="a8edd-285">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

<span data-ttu-id="a8edd-286">Chcete-li spustit scénář konfigurace připravenosti a živosti pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a8edd-286">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="a8edd-287">V prohlížeči, `/health/ready` navštivte několikrát, dokud 15 sekund uplynulo.</span><span class="sxs-lookup"><span data-stu-id="a8edd-287">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="a8edd-288">Kontrola stavu hlásí *není v pořádku* po dobu prvních 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="a8edd-288">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="a8edd-289">Po 15 sekundách koncový bod hlásí *v pořádku*, což odráží dokončení dlouhotrvající úlohy hostované služby.</span><span class="sxs-lookup"><span data-stu-id="a8edd-289">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="a8edd-290">Tento příklad také vytvoří vydavatel<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> kontroly stavu (implementace), který spustí první kontrolu připravenosti s dvousekundové zpoždění.</span><span class="sxs-lookup"><span data-stu-id="a8edd-290">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="a8edd-291">Další informace naleznete v části [Health Check Publisher.](#health-check-publisher)</span><span class="sxs-lookup"><span data-stu-id="a8edd-291">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="a8edd-292">Příklad Kubernetes</span><span class="sxs-lookup"><span data-stu-id="a8edd-292">Kubernetes example</span></span>

<span data-ttu-id="a8edd-293">Použití samostatné připravenosti a kontroly živosti je užitečné v prostředí, jako je [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="a8edd-293">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="a8edd-294">V Kubernetes může být aplikace vyžadována k provedení časově náročné práce při spuštění před přijetím požadavků, jako je například test dostupnosti podkladové databáze.</span><span class="sxs-lookup"><span data-stu-id="a8edd-294">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="a8edd-295">Použití samostatných kontrol umožňuje orchestrator rozlišit, zda aplikace funguje, ale ještě není připravena nebo pokud se aplikace nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="a8edd-295">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="a8edd-296">Další informace o sondách připravenosti a živosti v Kubernetes najdete v [tématu Konfigurace sond živosti a připravenosti](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) v dokumentaci Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="a8edd-296">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="a8edd-297">Následující příklad ukazuje konfiguraci sondy připravenosti Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="a8edd-297">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="a8edd-298">Sonda založená na metrikách s vlastním zapisovačem odpovědí</span><span class="sxs-lookup"><span data-stu-id="a8edd-298">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="a8edd-299">Ukázková aplikace ukazuje kontrolu stavu paměti s vlastní zapisovač odpovědí.</span><span class="sxs-lookup"><span data-stu-id="a8edd-299">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="a8edd-300">`MemoryHealthCheck`hlásí stav se zhoršenou hodnotou, pokud aplikace používá více než danou prahovou hodnotu paměti (1 GB v ukázkové aplikaci).</span><span class="sxs-lookup"><span data-stu-id="a8edd-300">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="a8edd-301">Obsahuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> informace o uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="a8edd-301">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="a8edd-302">Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v .</span><span class="sxs-lookup"><span data-stu-id="a8edd-302">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a8edd-303">Namísto povolení kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>předáním `MemoryHealthCheck` do , je registrován jako služba.</span><span class="sxs-lookup"><span data-stu-id="a8edd-303">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="a8edd-304">Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrované služby jsou k dispozici pro služby kontroly stavu a middleware.</span><span class="sxs-lookup"><span data-stu-id="a8edd-304">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="a8edd-305">Doporučujeme zaregistrovat služby kontroly stavu jako služby Singleton.</span><span class="sxs-lookup"><span data-stu-id="a8edd-305">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="a8edd-306">V *CustomWriterStartup.cs* ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="a8edd-306">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="a8edd-307">Koncový bod kontroly stavu `MapHealthChecks` je `Startup.Configure`vytvořen voláním v .</span><span class="sxs-lookup"><span data-stu-id="a8edd-307">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="a8edd-308">Delegát `WriteResponse` je k dispozici <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> vlastnost k výstupu vlastní odezvy JSON při spuštění kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-308">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="a8edd-309">Delegát `WriteResponse` naformátuje `CompositeHealthCheckResult` do objektu JSON a dává výstup JSON pro odpověď kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-309">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="a8edd-310">Další informace naleznete v části [Přizpůsobit výstup.](#customize-output)</span><span class="sxs-lookup"><span data-stu-id="a8edd-310">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="a8edd-311">Chcete-li spustit sondu založenou na metrikách s výstupem pro zápis vlastní odpovědi pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a8edd-311">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="a8edd-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje scénáře kontroly stavu založené na metrikách, včetně diskového úložiště a kontroly živosti s maximální hodnotou.</span><span class="sxs-lookup"><span data-stu-id="a8edd-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="a8edd-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a8edd-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="a8edd-314">Filtrovat podle portu</span><span class="sxs-lookup"><span data-stu-id="a8edd-314">Filter by port</span></span>

<span data-ttu-id="a8edd-315">Volání `RequireHost` `MapHealthChecks` se vzorem adresy URL, který určuje port pro omezení požadavků na kontrolu stavu na zadaný port.</span><span class="sxs-lookup"><span data-stu-id="a8edd-315">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="a8edd-316">To se obvykle používá v prostředí kontejneru vystavit port pro monitorování služeb.</span><span class="sxs-lookup"><span data-stu-id="a8edd-316">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="a8edd-317">Ukázková aplikace konfiguruje port pomocí [zprostředkovatele konfigurace proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a8edd-317">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="a8edd-318">Port je nastaven v souboru *launchSettings.json* a předán poskytovateli konfigurace prostřednictvím proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a8edd-318">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="a8edd-319">Je také nutné nakonfigurovat server pro naslouchání požadavkům na portu pro správu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-319">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="a8edd-320">Chcete-li pomocí ukázkové aplikace předvést konfiguraci portu pro správu, vytvořte soubor *launchSettings.json* ve složce *Vlastnosti.*</span><span class="sxs-lookup"><span data-stu-id="a8edd-320">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="a8edd-321">Následující soubor *Vlastnosti/launchSettings.json* v ukázkové aplikaci není součástí souborů projektu ukázkové aplikace a musí být vytvořen ručně:</span><span class="sxs-lookup"><span data-stu-id="a8edd-321">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="a8edd-322">Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v .</span><span class="sxs-lookup"><span data-stu-id="a8edd-322">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a8edd-323">Vytvořte koncový bod kontroly stavu voláním `MapHealthChecks` v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a8edd-323">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="a8edd-324">V ukázkové aplikaci `RequireHost` volání na koncovém bodu v `Startup.Configure` určuje port pro správu z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a8edd-324">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="a8edd-325">Koncové body se vytvářejí v `Startup.Configure`ukázkové aplikaci v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="a8edd-325">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="a8edd-326">V následujícím příkladu kódu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-326">In the following example code:</span></span>

* <span data-ttu-id="a8edd-327">Kontrola připravenosti používá všechny registrované šeky se značkou "ready".</span><span class="sxs-lookup"><span data-stu-id="a8edd-327">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="a8edd-328">Nezahrnuje `Predicate` všechny kontroly a vrátit 200-Ok.</span><span class="sxs-lookup"><span data-stu-id="a8edd-328">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> <span data-ttu-id="a8edd-329">Můžete se vyhnout vytváření souboru *launchSettings.json* v ukázkové aplikaci nastavením portu pro správu explicitně v kódu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-329">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="a8edd-330">V *Program.cs,* kde <xref:Microsoft.Extensions.Hosting.HostBuilder> je vytvořen, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> přidejte volání a zadejte koncový bod portu pro správu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-330">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="a8edd-331">V `Configure` *ManagementPortStartup.cs*, uveďte `RequireHost`port pro správu s :</span><span class="sxs-lookup"><span data-stu-id="a8edd-331">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="a8edd-332">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8edd-332">*Program.cs*:</span></span>
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> <span data-ttu-id="a8edd-333">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8edd-333">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="a8edd-334">Chcete-li spustit scénář konfigurace portu správy pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a8edd-334">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="a8edd-335">Distribuce knihovny kontrolstavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-335">Distribute a health check library</span></span>

<span data-ttu-id="a8edd-336">Distribuce kontroly stavu jako knihovny:</span><span class="sxs-lookup"><span data-stu-id="a8edd-336">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="a8edd-337">Napište kontrolu stavu, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> která implementuje rozhraní jako samostatnou třídu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-337">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="a8edd-338">Třída se může spolehnout na [vkládání závislostí (DI),](xref:fundamentals/dependency-injection)aktivaci typu a [pojmenované možnosti](xref:fundamentals/configuration/options) pro přístup ke konfiguračním datům.</span><span class="sxs-lookup"><span data-stu-id="a8edd-338">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="a8edd-339">V logice zdravotních kontrol : `CheckHealthAsync`</span><span class="sxs-lookup"><span data-stu-id="a8edd-339">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="a8edd-340">`data1`a `data2` jsou použity v metodě ke spuštění logiky kontroly stavu sondy.</span><span class="sxs-lookup"><span data-stu-id="a8edd-340">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="a8edd-341">`AccessViolationException`je manipulováno.</span><span class="sxs-lookup"><span data-stu-id="a8edd-341">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="a8edd-342">Dojde-li <xref:System.AccessViolationException> k, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> je vrácena s <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> umožnit uživatelům konfigurovat stav selhání kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-342">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="a8edd-343">Napište metodu rozšíření s parametry, které `Startup.Configure` ve své metodě volá náročná aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-343">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="a8edd-344">V následujícím příkladu předpokládejme následující podpis metody kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-344">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="a8edd-345">Předchozí podpis označuje, `ExampleHealthCheck` že vyžaduje další data pro zpracování logiky sondy kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-345">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="a8edd-346">Data jsou poskytována delegátovi, který slouží k vytvoření instance kontroly stavu při registraci kontroly stavu pomocí metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="a8edd-346">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="a8edd-347">V následujícím příkladu volající určuje volitelné:</span><span class="sxs-lookup"><span data-stu-id="a8edd-347">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="a8edd-348">název kontroly`name`stavu ( ).</span><span class="sxs-lookup"><span data-stu-id="a8edd-348">health check name (`name`).</span></span> <span data-ttu-id="a8edd-349">Pokud `null` `example_health_check` se používá .</span><span class="sxs-lookup"><span data-stu-id="a8edd-349">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="a8edd-350">datový bod řetězce pro`data1`kontrolu stavu ( ).</span><span class="sxs-lookup"><span data-stu-id="a8edd-350">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="a8edd-351">datový bod celého čísla pro`data2`kontrolu stavu ( ).</span><span class="sxs-lookup"><span data-stu-id="a8edd-351">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="a8edd-352">Pokud `null` `1` se používá .</span><span class="sxs-lookup"><span data-stu-id="a8edd-352">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="a8edd-353">stav selhání<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ).</span><span class="sxs-lookup"><span data-stu-id="a8edd-353">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="a8edd-354">Výchozí formát je `null`.</span><span class="sxs-lookup"><span data-stu-id="a8edd-354">The default is `null`.</span></span> <span data-ttu-id="a8edd-355">Pokud `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) je hlášena pro stav selhání.</span><span class="sxs-lookup"><span data-stu-id="a8edd-355">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="a8edd-356">značky`IEnumerable<string>`( ).</span><span class="sxs-lookup"><span data-stu-id="a8edd-356">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="a8edd-357">Vydavatel kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-357">Health Check Publisher</span></span>

<span data-ttu-id="a8edd-358">Při <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> přidání kontejneru služby systém kontroly stavu pravidelně provádí kontroly stavu a volání `PublishAsync` s výsledkem.</span><span class="sxs-lookup"><span data-stu-id="a8edd-358">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="a8edd-359">To je užitečné ve scénáři systému monitorování stavu na základě nabízených, který očekává, že každý proces bude pravidelně volat monitorovací systém za účelem určení stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-359">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="a8edd-360">Rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> má jedinou metodu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-360">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="a8edd-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>umožňují nastavit:</span><span class="sxs-lookup"><span data-stu-id="a8edd-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="a8edd-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Počáteční zpoždění použité po spuštění aplikace <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> před spuštěním instancí.</span><span class="sxs-lookup"><span data-stu-id="a8edd-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="a8edd-363">Zpoždění se použije jednou při spuštění a nevztahuje se na následné iterace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-363">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="a8edd-364">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="a8edd-364">The default value is five seconds.</span></span>
* <span data-ttu-id="a8edd-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; Doba popravy. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher></span><span class="sxs-lookup"><span data-stu-id="a8edd-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="a8edd-366">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="a8edd-366">The default value is 30 seconds.</span></span>
* <span data-ttu-id="a8edd-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; Pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` je (výchozí), služba vydavatele kontroly stavu spustí všechny kontroly registrovaného stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="a8edd-368">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která filtruje sadu kontrol.</span><span class="sxs-lookup"><span data-stu-id="a8edd-368">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="a8edd-369">Predikát se vyhodnocuje každé období.</span><span class="sxs-lookup"><span data-stu-id="a8edd-369">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="a8edd-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Časový čas pro provádění kontroly <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> stavu pro všechny instance.</span><span class="sxs-lookup"><span data-stu-id="a8edd-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="a8edd-371">Slouží <xref:System.Threading.Timeout.InfiniteTimeSpan> k provedení bez časového času.</span><span class="sxs-lookup"><span data-stu-id="a8edd-371">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="a8edd-372">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="a8edd-372">The default value is 30 seconds.</span></span>

<span data-ttu-id="a8edd-373">V ukázkové `ReadinessPublisher` aplikaci <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> je implementace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-373">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="a8edd-374">Stav kontroly stavu je zaznamenán pro každou kontrolu na úrovni protokolu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-374">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="a8edd-375">Informace<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ) je-li <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>stav zdravotních kontrol .</span><span class="sxs-lookup"><span data-stu-id="a8edd-375">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="a8edd-376">Chyba<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ), pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> je <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>stav buď nebo .</span><span class="sxs-lookup"><span data-stu-id="a8edd-376">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="a8edd-377">V příkladu ukázkové `LivenessProbeStartup` aplikace `StartupHostedService` má kontrola připravenosti dvě sekundy zpoždění při spuštění a spustí kontrolu každých 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="a8edd-377">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="a8edd-378">Chcete-li <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> aktivovat implementaci, `ReadinessPublisher` ukázka registruje jako oslavá služba v kontejneru [vkládání závislostí (DI):](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="a8edd-378">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="a8edd-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="a8edd-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="a8edd-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a8edd-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="a8edd-381">Omezit kontroly stavu pomocí MapWhen</span><span class="sxs-lookup"><span data-stu-id="a8edd-381">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="a8edd-382">Slouží <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> k podmíněnému větvení kanálu požadavků pro koncové body kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-382">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="a8edd-383">V následujícím příkladu `MapWhen` pobočky kanálu požadavku k aktivaci middleware kontroly `api/HealthCheck` stavu, pokud je přijat požadavek GET pro koncový bod:</span><span class="sxs-lookup"><span data-stu-id="a8edd-383">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

<span data-ttu-id="a8edd-384">Další informace naleznete v tématu <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="a8edd-384">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a8edd-385">ASP.NET Core nabízí middlewar a knihovny kontroly stavu pro vykazování stavu součástí infrastruktury aplikací.</span><span class="sxs-lookup"><span data-stu-id="a8edd-385">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="a8edd-386">Kontroly stavu jsou vystaveny aplikací jako koncové body HTTP.</span><span class="sxs-lookup"><span data-stu-id="a8edd-386">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="a8edd-387">Koncové body kontroly stavu lze nakonfigurovat pro různé scénáře monitorování v reálném čase:</span><span class="sxs-lookup"><span data-stu-id="a8edd-387">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="a8edd-388">Sondy stavu lze použít orchestrátory kontejnerů a nástroj pro vyrovnávání zatížení ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-388">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="a8edd-389">Orchestrátor kontejneru může například reagovat na selhávající kontrolu stavu zastavením postupného nasazení nebo restartováním kontejneru.</span><span class="sxs-lookup"><span data-stu-id="a8edd-389">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="a8edd-390">Vyrovnávání zatížení může reagovat na nefunkční aplikace směrováním provozu od instance selhání do instance v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a8edd-390">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="a8edd-391">Použití paměti, disku a dalších prostředků fyzického serveru lze sledovat z důvodu stavu v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a8edd-391">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="a8edd-392">Kontroly stavu můžete otestovat závislosti aplikace, jako jsou databáze a koncové body externí služby, k potvrzení dostupnosti a normální fungování.</span><span class="sxs-lookup"><span data-stu-id="a8edd-392">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="a8edd-393">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="a8edd-393">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a8edd-394">Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-394">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="a8edd-395">Chcete-li spustit ukázkovou aplikaci pro daný scénář, použijte příkaz [dotnet run](/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a8edd-395">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="a8edd-396">Podrobnosti o tom, jak používat ukázkovou aplikaci, najdete v *README.md* souboru ukázkové aplikace a v popisech scénářů v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-396">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a8edd-397">Požadavky</span><span class="sxs-lookup"><span data-stu-id="a8edd-397">Prerequisites</span></span>

<span data-ttu-id="a8edd-398">Kontroly stavu se obvykle používají s externí monitorovací služby nebo kontejner orchestrátor u zkontrolovat stav aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-398">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="a8edd-399">Před přidáním kontrolstavu do aplikace se rozhodněte, který monitorovací systém chcete použít.</span><span class="sxs-lookup"><span data-stu-id="a8edd-399">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="a8edd-400">Systém monitorování určuje, jaké typy kontrol stavu chcete vytvořit a jak nakonfigurovat jejich koncové body.</span><span class="sxs-lookup"><span data-stu-id="a8edd-400">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="a8edd-401">Odkazna [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček [microsoft.aspNetCore.Diagnostics.HealthChecks.](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks)</span><span class="sxs-lookup"><span data-stu-id="a8edd-401">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="a8edd-402">Ukázková aplikace poskytuje spouštěcí kód pro demonstraci kontroly stavu pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="a8edd-402">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="a8edd-403">Scénář [databázové sondy](#database-probe) kontroluje stav připojení k databázi pomocí [aspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="a8edd-403">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="a8edd-404">[Scénář sondy DbContext](#entity-framework-core-dbcontext-probe) zkontroluje `DbContext`databázi pomocí EF Core .</span><span class="sxs-lookup"><span data-stu-id="a8edd-404">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="a8edd-405">Chcete-li prozkoumat scénáře databáze, ukázková aplikace:</span><span class="sxs-lookup"><span data-stu-id="a8edd-405">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="a8edd-406">Vytvoří databázi a poskytuje její připojovací řetězec v souboru *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="a8edd-406">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="a8edd-407">Má následující odkazy na balíček v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-407">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="a8edd-408">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="a8edd-408">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="a8edd-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="a8edd-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="a8edd-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a8edd-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="a8edd-411">Jiný scénář kontroly stavu ukazuje, jak filtrovat kontroly stavu na port správy.</span><span class="sxs-lookup"><span data-stu-id="a8edd-411">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="a8edd-412">Ukázková aplikace vyžaduje vytvoření souboru *Properties/launchSettings.json,* který obsahuje adresu URL pro správu a port pro správu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-412">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="a8edd-413">Další informace naleznete v části [Filtr podle portu.](#filter-by-port)</span><span class="sxs-lookup"><span data-stu-id="a8edd-413">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="a8edd-414">Základní zdravotní sonda</span><span class="sxs-lookup"><span data-stu-id="a8edd-414">Basic health probe</span></span>

<span data-ttu-id="a8edd-415">Pro mnoho aplikací stačí ke zjištění stavu aplikace základní konfigurace sondy stavu, která hlásí dostupnost aplikace pro zpracování požadavků *(živost).*</span><span class="sxs-lookup"><span data-stu-id="a8edd-415">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="a8edd-416">Základní konfigurace registruje služby kontroly stavu a volá middleware kontroly stavu, aby odpověděl v koncovém bodě adresy URL s odpovědí na stav.</span><span class="sxs-lookup"><span data-stu-id="a8edd-416">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="a8edd-417">Ve výchozím nastavení nejsou registrovány žádné konkrétní kontroly stavu k testování jakékoli konkrétní závislosti nebo subsystému.</span><span class="sxs-lookup"><span data-stu-id="a8edd-417">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="a8edd-418">Aplikace je považována za v pořádku, pokud je schopná reagovat na adresu URL koncového bodu stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-418">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="a8edd-419">Výchozí zapisovač odpovědí<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>zapíše stav ( ) jako odpověď ve formátu prostého textu zpět klientovi, což označuje stav [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [HealthStatus.Unhealthy.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)</span><span class="sxs-lookup"><span data-stu-id="a8edd-419">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="a8edd-420">Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v .</span><span class="sxs-lookup"><span data-stu-id="a8edd-420">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a8edd-421">Přidejte koncový bod pro kontroly stavu Middleware s <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> v kanálu zpracování požadavků . `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="a8edd-421">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="a8edd-422">V ukázkové aplikaci se vytvoří koncový `/health` bod kontroly stavu na (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a8edd-422">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

<span data-ttu-id="a8edd-423">Chcete-li spustit základní scénář konfigurace pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a8edd-423">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="a8edd-424">Příklad Dockeru</span><span class="sxs-lookup"><span data-stu-id="a8edd-424">Docker example</span></span>

<span data-ttu-id="a8edd-425">[Docker](xref:host-and-deploy/docker/index) nabízí integrovanou `HEALTHCHECK` direktivu, kterou lze použít ke kontrole stavu aplikace, která používá základní konfiguraci kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-425">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="a8edd-426">Vytvořit kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-426">Create health checks</span></span>

<span data-ttu-id="a8edd-427">Kontroly stavu jsou vytvořeny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> implementací rozhraní.</span><span class="sxs-lookup"><span data-stu-id="a8edd-427">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="a8edd-428">Metoda <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> vrátí, která označuje `Healthy`stav `Degraded`jako `Unhealthy`, nebo .</span><span class="sxs-lookup"><span data-stu-id="a8edd-428">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="a8edd-429">Výsledek je zapsán jako odpověď ve formátu prostého textu s konfigurovatelným stavovým kódem (konfigurace je popsána v části [Možnosti kontroly stavu).](#health-check-options)</span><span class="sxs-lookup"><span data-stu-id="a8edd-429">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="a8edd-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>můžete také vrátit volitelné dvojice klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="a8edd-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="a8edd-431">Příklad kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-431">Example health check</span></span>

<span data-ttu-id="a8edd-432">Následující `ExampleHealthCheck` třída ukazuje rozložení kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-432">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="a8edd-433">Logika kontroly stavu `CheckHealthAsync` je umístěn v metodě.</span><span class="sxs-lookup"><span data-stu-id="a8edd-433">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="a8edd-434">Následující příklad nastaví fiktivní `healthCheckResultHealthy`proměnnou `true`, na .</span><span class="sxs-lookup"><span data-stu-id="a8edd-434">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="a8edd-435">Pokud `healthCheckResultHealthy` je hodnota nastavena na `false`, je vrácen stav [HealthCheckResult.Unhealthy.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*)</span><span class="sxs-lookup"><span data-stu-id="a8edd-435">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
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

### <a name="register-health-check-services"></a><span data-ttu-id="a8edd-436">Registrace služeb kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-436">Register health check services</span></span>

<span data-ttu-id="a8edd-437">Typ `ExampleHealthCheck` je přidán do služeb `Startup.ConfigureServices` <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>kontroly stavu v:</span><span class="sxs-lookup"><span data-stu-id="a8edd-437">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="a8edd-438">Přetížení <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> uvedené v následujícím příkladu nastaví stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) hlásit, když kontrola stavu hlásí selhání.</span><span class="sxs-lookup"><span data-stu-id="a8edd-438">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="a8edd-439">Pokud je stav selhání `null` nastaven na (výchozí), [healthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) je hlášena.</span><span class="sxs-lookup"><span data-stu-id="a8edd-439">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="a8edd-440">Toto přetížení je užitečný scénář pro autory knihovny, kde stav selhání označený knihovnou je vynuceno aplikací při selhání kontroly stavu dojde, pokud implementace kontroly stavu respektuje nastavení.</span><span class="sxs-lookup"><span data-stu-id="a8edd-440">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="a8edd-441">*Značky* lze použít k filtrování kontrol stavu (popsané dále v části [Kontroly stavu filtru).](#filter-health-checks)</span><span class="sxs-lookup"><span data-stu-id="a8edd-441">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="a8edd-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>může také spustit funkci lambda.</span><span class="sxs-lookup"><span data-stu-id="a8edd-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="a8edd-443">V následujícím `Startup.ConfigureServices` příkladu je zadán název `Example` kontroly stavu a kontrola vždy vrátí stav v pořádku:</span><span class="sxs-lookup"><span data-stu-id="a8edd-443">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="a8edd-444">Použití middlewaru kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-444">Use Health Checks Middleware</span></span>

<span data-ttu-id="a8edd-445">V `Startup.Configure`aplikace <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> volejte v kanálu zpracování s adresou URL koncového bodu nebo relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="a8edd-445">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="a8edd-446">Pokud by měly kontroly stavu naslouchat na <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> konkrétním portu, použijte přetížení k nastavení portu (dále popsané v části [Filtr podle portu):](#filter-by-port)</span><span class="sxs-lookup"><span data-stu-id="a8edd-446">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="a8edd-447">Možnosti kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-447">Health check options</span></span>

<span data-ttu-id="a8edd-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>poskytnout možnost přizpůsobit chování kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="a8edd-449">Filtrování kontrol stavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-449">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="a8edd-450">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="a8edd-450">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="a8edd-451">Potlačit záhlaví mezipaměti</span><span class="sxs-lookup"><span data-stu-id="a8edd-451">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="a8edd-452">Přizpůsobit výstup</span><span class="sxs-lookup"><span data-stu-id="a8edd-452">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="a8edd-453">Filtrování kontrol stavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-453">Filter health checks</span></span>

<span data-ttu-id="a8edd-454">Ve výchozím nastavení middleware kontroly stavu spouští všechny registrované kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-454">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="a8edd-455">Chcete-li spustit podmnožinu kontrol stavu, zadejte <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> funkci, která vrátí logickou hodnotu této možnosti.</span><span class="sxs-lookup"><span data-stu-id="a8edd-455">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="a8edd-456">V následujícím příkladu `Bar` je kontrola stavu odfiltrována`bar_tag`podle značky ( ) `true` v podmíněném příkazu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> funkce, kde je vrácena pouze v případě, že vlastnost kontroly stavu odpovídá `foo_tag` nebo `baz_tag`:</span><span class="sxs-lookup"><span data-stu-id="a8edd-456">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

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
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="a8edd-457">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="a8edd-457">Customize the HTTP status code</span></span>

<span data-ttu-id="a8edd-458">Slouží <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> k přizpůsobení mapování stavu na stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="a8edd-458">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="a8edd-459">Následující <xref:Microsoft.AspNetCore.Http.StatusCodes> přiřazení jsou výchozí hodnoty používané middleware.</span><span class="sxs-lookup"><span data-stu-id="a8edd-459">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="a8edd-460">Změňte hodnoty stavového kódu tak, aby vyhovovaly vašim požadavkům.</span><span class="sxs-lookup"><span data-stu-id="a8edd-460">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="a8edd-461">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a8edd-461">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="a8edd-462">Potlačit záhlaví mezipaměti</span><span class="sxs-lookup"><span data-stu-id="a8edd-462">Suppress cache headers</span></span>

<span data-ttu-id="a8edd-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>určuje, zda middleware kontroly stavu přidá do odpovědi sondy hlavičky HTTP, aby se zabránilo ukládání odpovědi do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="a8edd-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="a8edd-464">Pokud `false` je hodnota (výchozí), middleware nastaví `Cache-Control` `Expires`nebo `Pragma` přepíše , a záhlaví, aby se zabránilo ukládání odpovědi do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="a8edd-464">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="a8edd-465">Pokud je `true`hodnota , middleware nemění cache záhlaví odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a8edd-465">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="a8edd-466">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a8edd-466">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="a8edd-467">Přizpůsobit výstup</span><span class="sxs-lookup"><span data-stu-id="a8edd-467">Customize output</span></span>

<span data-ttu-id="a8edd-468">Možnost <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> získá nebo nastaví delegáta, který se používá k zápisu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a8edd-468">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="a8edd-469">Výchozí delegát zapíše minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [Stavu.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="a8edd-469">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="a8edd-470">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a8edd-470">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="a8edd-471">Výchozí delegát zapíše minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [Stavu.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="a8edd-471">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="a8edd-472">Následující vlastní delegát `WriteResponse`, výstupy vlastní odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="a8edd-472">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
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

<span data-ttu-id="a8edd-473">Systém kontrol stavu neposkytuje integrovanou podporu pro složité formáty vrácení JSON, protože formát je specifický pro váš výběr monitorovacího systému.</span><span class="sxs-lookup"><span data-stu-id="a8edd-473">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="a8edd-474">Neváhejte a `JObject` přizpůsobit v předchozím příkladu podle potřeby, aby vyhovoval vašim potřebám.</span><span class="sxs-lookup"><span data-stu-id="a8edd-474">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="a8edd-475">Databázová sonda</span><span class="sxs-lookup"><span data-stu-id="a8edd-475">Database probe</span></span>

<span data-ttu-id="a8edd-476">Kontrola stavu můžete zadat databázový dotaz spustit jako logický test k označení, zda databáze reaguje normálně.</span><span class="sxs-lookup"><span data-stu-id="a8edd-476">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="a8edd-477">Ukázková aplikace používá [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), knihovnu kontroly stavu pro ASP.NET aplikace Core, k provedení kontroly stavu v databázi serveru SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a8edd-477">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="a8edd-478">`AspNetCore.Diagnostics.HealthChecks`provede `SELECT 1` dotaz proti databázi k potvrzení připojení k databázi je v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a8edd-478">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="a8edd-479">Při kontrole připojení k databázi s dotazem zvolte dotaz, který se vrací rychle.</span><span class="sxs-lookup"><span data-stu-id="a8edd-479">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="a8edd-480">Přístup dotazu spustí riziko přetížení databáze a snížení výkonu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-480">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="a8edd-481">Ve většině případů není nutné spustit testovací dotaz.</span><span class="sxs-lookup"><span data-stu-id="a8edd-481">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="a8edd-482">Pouze vytvoření úspěšného připojení k databázi je dostačující.</span><span class="sxs-lookup"><span data-stu-id="a8edd-482">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="a8edd-483">Pokud zjistíte, že je nutné spustit dotaz, zvolte jednoduchý dotaz SELECT, například `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="a8edd-483">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="a8edd-484">Zahrňte odkaz na balíček [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="a8edd-484">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="a8edd-485">Zadej platný připojovací řetězec databáze do souboru *appsettings.json* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-485">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="a8edd-486">Aplikace používá databázi SERVERU `HealthCheckSample`SQL Server s názvem :</span><span class="sxs-lookup"><span data-stu-id="a8edd-486">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="a8edd-487">Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v .</span><span class="sxs-lookup"><span data-stu-id="a8edd-487">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a8edd-488">Ukázková aplikace `AddSqlServer` volá metodu s připojovacím řetězcem databáze (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a8edd-488">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a8edd-489">Volání kontroly stavu Middleware v `Startup.Configure`kanálu zpracování aplikace v :</span><span class="sxs-lookup"><span data-stu-id="a8edd-489">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="a8edd-490">Chcete-li spustit scénář databázové sondy pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a8edd-490">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="a8edd-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a8edd-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="a8edd-492">Sonda Core DbContext rozhraní entity frameworku</span><span class="sxs-lookup"><span data-stu-id="a8edd-492">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="a8edd-493">Kontrola `DbContext` potvrzuje, že aplikace může komunikovat s databází `DbContext`nakonfigurovanou pro jádro EF .</span><span class="sxs-lookup"><span data-stu-id="a8edd-493">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="a8edd-494">Kontrola `DbContext` je podporována v aplikacích, které:</span><span class="sxs-lookup"><span data-stu-id="a8edd-494">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="a8edd-495">Použít [jádro entity frameworku (EF).](/ef/core/)</span><span class="sxs-lookup"><span data-stu-id="a8edd-495">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="a8edd-496">Zahrňte odkaz na balíček [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="a8edd-496">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="a8edd-497">`AddDbContextCheck<TContext>`zaregistruje kontrolu stavu `DbContext`pro .</span><span class="sxs-lookup"><span data-stu-id="a8edd-497">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="a8edd-498">Dodává `DbContext` se jako `TContext` metoda.</span><span class="sxs-lookup"><span data-stu-id="a8edd-498">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="a8edd-499">Přetížení je k dispozici ke konfiguraci stavu selhání, značky a vlastní testovací dotaz.</span><span class="sxs-lookup"><span data-stu-id="a8edd-499">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="a8edd-500">Ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="a8edd-500">By default:</span></span>

* <span data-ttu-id="a8edd-501">Volání `DbContextHealthCheck` metody EF `CanConnectAsync` Core.</span><span class="sxs-lookup"><span data-stu-id="a8edd-501">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="a8edd-502">Můžete přizpůsobit, co operace je spuštěna při kontrole stavu pomocí `AddDbContextCheck` přetížení metody.</span><span class="sxs-lookup"><span data-stu-id="a8edd-502">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="a8edd-503">Název kontroly stavu je název `TContext` typu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-503">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="a8edd-504">V ukázkové `AppDbContext` aplikaci je `AddDbContextCheck` poskytována a `Startup.ConfigureServices` registrována jako služba v (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a8edd-504">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a8edd-505">V ukázkové `UseHealthChecks` aplikaci přidá middleware `Startup.Configure`kontroly stavu v aplikaci .</span><span class="sxs-lookup"><span data-stu-id="a8edd-505">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="a8edd-506">Chcete-li `DbContext` spustit scénář probe pomocí ukázkové aplikace, zkontrolujte, že databáze určená připojovacím řetězcem neexistuje v instanci serveru SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a8edd-506">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="a8edd-507">Pokud databáze existuje, odstraňte ji.</span><span class="sxs-lookup"><span data-stu-id="a8edd-507">If the database exists, delete it.</span></span>

<span data-ttu-id="a8edd-508">Proveďte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a8edd-508">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="a8edd-509">Po spuštění aplikace zkontrolujte stav tím, že `/health` žádost o koncový bod v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="a8edd-509">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="a8edd-510">Databáze a `AppDbContext` neexistují, takže aplikace poskytuje následující odpověď:</span><span class="sxs-lookup"><span data-stu-id="a8edd-510">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="a8edd-511">Aktivujte ukázkovou aplikaci k vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="a8edd-511">Trigger the sample app to create the database.</span></span> <span data-ttu-id="a8edd-512">Požádejte o `/createdatabase`to, aby byla .</span><span class="sxs-lookup"><span data-stu-id="a8edd-512">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="a8edd-513">Aplikace odpovídá:</span><span class="sxs-lookup"><span data-stu-id="a8edd-513">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="a8edd-514">Vytvořte požadavek `/health` na koncový bod.</span><span class="sxs-lookup"><span data-stu-id="a8edd-514">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="a8edd-515">Databáze a kontext existují, takže aplikace odpovídá:</span><span class="sxs-lookup"><span data-stu-id="a8edd-515">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="a8edd-516">Spusťte ukázkovou aplikaci k odstranění databáze.</span><span class="sxs-lookup"><span data-stu-id="a8edd-516">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="a8edd-517">Požádejte o `/deletedatabase`to, aby byla .</span><span class="sxs-lookup"><span data-stu-id="a8edd-517">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="a8edd-518">Aplikace odpovídá:</span><span class="sxs-lookup"><span data-stu-id="a8edd-518">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="a8edd-519">Vytvořte požadavek `/health` na koncový bod.</span><span class="sxs-lookup"><span data-stu-id="a8edd-519">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="a8edd-520">Aplikace poskytuje nefunkční odpověď:</span><span class="sxs-lookup"><span data-stu-id="a8edd-520">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="a8edd-521">Samostatné sondy připravenosti a živosti</span><span class="sxs-lookup"><span data-stu-id="a8edd-521">Separate readiness and liveness probes</span></span>

<span data-ttu-id="a8edd-522">V některých scénářích hostování se používá dvojice kontrol stavu, které rozlišují dva stavy aplikace:</span><span class="sxs-lookup"><span data-stu-id="a8edd-522">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="a8edd-523">Aplikace funguje, ale ještě není připravena přijímat požadavky.</span><span class="sxs-lookup"><span data-stu-id="a8edd-523">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="a8edd-524">Tento stav je *připravenost*aplikace .</span><span class="sxs-lookup"><span data-stu-id="a8edd-524">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="a8edd-525">Aplikace funguje a odpovídá na požadavky.</span><span class="sxs-lookup"><span data-stu-id="a8edd-525">The app is functioning and responding to requests.</span></span> <span data-ttu-id="a8edd-526">Tento stav je *živost*aplikace .</span><span class="sxs-lookup"><span data-stu-id="a8edd-526">This state is the app's *liveness*.</span></span>

<span data-ttu-id="a8edd-527">Kontrola připravenosti obvykle provádí rozsáhlejší a časově náročnou sadu kontrol, aby zjistila, zda jsou k dispozici všechny subsystémy a prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-527">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="a8edd-528">Kontrola živosti pouze provádí rychlou kontrolu, aby zjistila, zda je aplikace k dispozici pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="a8edd-528">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="a8edd-529">Poté, co aplikace projde kontrolou připravenosti, není třeba aplikaci dále zatěžovat nákladnou sadou kontrol&mdash;připravenosti, další kontroly vyžadují pouze kontrolu živosti.</span><span class="sxs-lookup"><span data-stu-id="a8edd-529">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="a8edd-530">Ukázková aplikace obsahuje kontrolu stavu, která hlásí dokončení dlouhotrvající úlohy spuštění v [hostované službě](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="a8edd-530">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="a8edd-531">Zpřístupní `StartupHostedServiceHealthCheck` vlastnost, `StartupTaskCompleted`, že hostovaná služba `true` můžete nastavit, když je dokončena její dlouhotrvající úloha (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="a8edd-531">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="a8edd-532">Dlouhotrvající úloha na pozadí je [spuštěna hostovkou](xref:fundamentals/host/hosted-services) *(Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="a8edd-532">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="a8edd-533">Na závěr úkolu, `StartupHostedServiceHealthCheck.StartupTaskCompleted` je nastavena na `true`:</span><span class="sxs-lookup"><span data-stu-id="a8edd-533">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="a8edd-534">Kontrola stavu je <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> registrována spolu `Startup.ConfigureServices` s hostovkou služby.</span><span class="sxs-lookup"><span data-stu-id="a8edd-534">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="a8edd-535">Vzhledem k tomu, že hostovaná služba musí nastavit vlastnost při kontrole stavu, je kontrola stavu také registrována v kontejneru služby (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a8edd-535">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a8edd-536">Volání kontroly stavu Middleware v `Startup.Configure`kanálu zpracování aplikace v .</span><span class="sxs-lookup"><span data-stu-id="a8edd-536">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="a8edd-537">V ukázkové aplikaci jsou vytvořeny koncové `/health/ready` body kontroly `/health/live` stavu pro kontrolu připravenosti a pro kontrolu živosti.</span><span class="sxs-lookup"><span data-stu-id="a8edd-537">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="a8edd-538">Kontrola připravenosti filtruje kontroly stavu `ready` na kontrolu stavu se značkou.</span><span class="sxs-lookup"><span data-stu-id="a8edd-538">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="a8edd-539">Kontrola živosti filtruje `StartupHostedServiceHealthCheck` zpět `false` v [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (další informace naleznete v [tématu Filtrování kontrol stavu](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="a8edd-539">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

<span data-ttu-id="a8edd-540">Chcete-li spustit scénář konfigurace připravenosti a živosti pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a8edd-540">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="a8edd-541">V prohlížeči, `/health/ready` navštivte několikrát, dokud 15 sekund uplynulo.</span><span class="sxs-lookup"><span data-stu-id="a8edd-541">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="a8edd-542">Kontrola stavu hlásí *není v pořádku* po dobu prvních 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="a8edd-542">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="a8edd-543">Po 15 sekundách koncový bod hlásí *v pořádku*, což odráží dokončení dlouhotrvající úlohy hostované služby.</span><span class="sxs-lookup"><span data-stu-id="a8edd-543">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="a8edd-544">Tento příklad také vytvoří vydavatel<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> kontroly stavu (implementace), který spustí první kontrolu připravenosti s dvousekundové zpoždění.</span><span class="sxs-lookup"><span data-stu-id="a8edd-544">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="a8edd-545">Další informace naleznete v části [Health Check Publisher.](#health-check-publisher)</span><span class="sxs-lookup"><span data-stu-id="a8edd-545">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="a8edd-546">Příklad Kubernetes</span><span class="sxs-lookup"><span data-stu-id="a8edd-546">Kubernetes example</span></span>

<span data-ttu-id="a8edd-547">Použití samostatné připravenosti a kontroly živosti je užitečné v prostředí, jako je [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="a8edd-547">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="a8edd-548">V Kubernetes může být aplikace vyžadována k provedení časově náročné práce při spuštění před přijetím požadavků, jako je například test dostupnosti podkladové databáze.</span><span class="sxs-lookup"><span data-stu-id="a8edd-548">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="a8edd-549">Použití samostatných kontrol umožňuje orchestrator rozlišit, zda aplikace funguje, ale ještě není připravena nebo pokud se aplikace nepodařilo spustit.</span><span class="sxs-lookup"><span data-stu-id="a8edd-549">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="a8edd-550">Další informace o sondách připravenosti a živosti v Kubernetes najdete v [tématu Konfigurace sond živosti a připravenosti](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) v dokumentaci Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="a8edd-550">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="a8edd-551">Následující příklad ukazuje konfiguraci sondy připravenosti Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="a8edd-551">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="a8edd-552">Sonda založená na metrikách s vlastním zapisovačem odpovědí</span><span class="sxs-lookup"><span data-stu-id="a8edd-552">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="a8edd-553">Ukázková aplikace ukazuje kontrolu stavu paměti s vlastní zapisovač odpovědí.</span><span class="sxs-lookup"><span data-stu-id="a8edd-553">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="a8edd-554">`MemoryHealthCheck`hlásí stav není v pořádku, pokud aplikace používá více než daný práh paměti (1 GB v ukázkové aplikaci).</span><span class="sxs-lookup"><span data-stu-id="a8edd-554">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="a8edd-555">Obsahuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> informace o uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="a8edd-555">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="a8edd-556">Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v .</span><span class="sxs-lookup"><span data-stu-id="a8edd-556">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a8edd-557">Namísto povolení kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>předáním `MemoryHealthCheck` do , je registrován jako služba.</span><span class="sxs-lookup"><span data-stu-id="a8edd-557">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="a8edd-558">Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrované služby jsou k dispozici pro služby kontroly stavu a middleware.</span><span class="sxs-lookup"><span data-stu-id="a8edd-558">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="a8edd-559">Doporučujeme zaregistrovat služby kontroly stavu jako služby Singleton.</span><span class="sxs-lookup"><span data-stu-id="a8edd-559">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="a8edd-560">V ukázkové aplikaci (*CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a8edd-560">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="a8edd-561">Volání kontroly stavu Middleware v `Startup.Configure`kanálu zpracování aplikace v .</span><span class="sxs-lookup"><span data-stu-id="a8edd-561">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="a8edd-562">Delegát `WriteResponse` je k dispozici `ResponseWriter` vlastnost k výstupu vlastní odpověď JSON při provádění kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-562">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="a8edd-563">Metoda `WriteResponse` naformátuje `CompositeHealthCheckResult` do objektu JSON a dává výstup JSON pro odpověď kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-563">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="a8edd-564">Chcete-li spustit sondu založenou na metrikách s výstupem pro zápis vlastní odpovědi pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a8edd-564">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="a8edd-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje scénáře kontroly stavu založené na metrikách, včetně diskového úložiště a kontroly živosti s maximální hodnotou.</span><span class="sxs-lookup"><span data-stu-id="a8edd-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="a8edd-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a8edd-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="a8edd-567">Filtrovat podle portu</span><span class="sxs-lookup"><span data-stu-id="a8edd-567">Filter by port</span></span>

<span data-ttu-id="a8edd-568">Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> s portem omezuje požadavky na kontrolu stavu na zadaný port.</span><span class="sxs-lookup"><span data-stu-id="a8edd-568">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="a8edd-569">To se obvykle používá v prostředí kontejneru vystavit port pro monitorování služeb.</span><span class="sxs-lookup"><span data-stu-id="a8edd-569">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="a8edd-570">Ukázková aplikace konfiguruje port pomocí [zprostředkovatele konfigurace proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a8edd-570">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="a8edd-571">Port je nastaven v souboru *launchSettings.json* a předán poskytovateli konfigurace prostřednictvím proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a8edd-571">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="a8edd-572">Je také nutné nakonfigurovat server pro naslouchání požadavkům na portu pro správu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-572">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="a8edd-573">Chcete-li pomocí ukázkové aplikace předvést konfiguraci portu pro správu, vytvořte soubor *launchSettings.json* ve složce *Vlastnosti.*</span><span class="sxs-lookup"><span data-stu-id="a8edd-573">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="a8edd-574">Následující soubor *Vlastnosti/launchSettings.json* v ukázkové aplikaci není součástí souborů projektu ukázkové aplikace a musí být vytvořen ručně:</span><span class="sxs-lookup"><span data-stu-id="a8edd-574">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="a8edd-575">Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v .</span><span class="sxs-lookup"><span data-stu-id="a8edd-575">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a8edd-576">Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> určuje port pro správu (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a8edd-576">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="a8edd-577">Vytvoření souboru *launchSettings.json* v ukázkové aplikaci se můžete vyhnout nastavením adres URL a portu pro správu explicitně v kódu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-577">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="a8edd-578">V *Program.cs,* kde <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> je vytvořen, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> přidejte volání a zadejte koncový bod normální odezvy aplikace a koncový bod portu pro správu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-578">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="a8edd-579">V *ManagementPortStartup.cs* ManagementPortStartup.cs <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> kde je volána, zadejte port pro správu explicitně.</span><span class="sxs-lookup"><span data-stu-id="a8edd-579">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="a8edd-580">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8edd-580">*Program.cs*:</span></span>
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
> <span data-ttu-id="a8edd-581">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a8edd-581">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="a8edd-582">Chcete-li spustit scénář konfigurace portu správy pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a8edd-582">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="a8edd-583">Distribuce knihovny kontrolstavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-583">Distribute a health check library</span></span>

<span data-ttu-id="a8edd-584">Distribuce kontroly stavu jako knihovny:</span><span class="sxs-lookup"><span data-stu-id="a8edd-584">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="a8edd-585">Napište kontrolu stavu, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> která implementuje rozhraní jako samostatnou třídu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-585">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="a8edd-586">Třída se může spolehnout na [vkládání závislostí (DI),](xref:fundamentals/dependency-injection)aktivaci typu a [pojmenované možnosti](xref:fundamentals/configuration/options) pro přístup ke konfiguračním datům.</span><span class="sxs-lookup"><span data-stu-id="a8edd-586">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="a8edd-587">V logice zdravotních kontrol : `CheckHealthAsync`</span><span class="sxs-lookup"><span data-stu-id="a8edd-587">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="a8edd-588">`data1`a `data2` jsou použity v metodě ke spuštění logiky kontroly stavu sondy.</span><span class="sxs-lookup"><span data-stu-id="a8edd-588">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="a8edd-589">`AccessViolationException`je manipulováno.</span><span class="sxs-lookup"><span data-stu-id="a8edd-589">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="a8edd-590">Dojde-li <xref:System.AccessViolationException> k, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> je vrácena s <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> umožnit uživatelům konfigurovat stav selhání kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-590">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

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
   ```

1. <span data-ttu-id="a8edd-591">Napište metodu rozšíření s parametry, které `Startup.Configure` ve své metodě volá náročná aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-591">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="a8edd-592">V následujícím příkladu předpokládejme následující podpis metody kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-592">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="a8edd-593">Předchozí podpis označuje, `ExampleHealthCheck` že vyžaduje další data pro zpracování logiky sondy kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-593">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="a8edd-594">Data jsou poskytována delegátovi, který slouží k vytvoření instance kontroly stavu při registraci kontroly stavu pomocí metody rozšíření.</span><span class="sxs-lookup"><span data-stu-id="a8edd-594">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="a8edd-595">V následujícím příkladu volající určuje volitelné:</span><span class="sxs-lookup"><span data-stu-id="a8edd-595">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="a8edd-596">název kontroly`name`stavu ( ).</span><span class="sxs-lookup"><span data-stu-id="a8edd-596">health check name (`name`).</span></span> <span data-ttu-id="a8edd-597">Pokud `null` `example_health_check` se používá .</span><span class="sxs-lookup"><span data-stu-id="a8edd-597">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="a8edd-598">datový bod řetězce pro`data1`kontrolu stavu ( ).</span><span class="sxs-lookup"><span data-stu-id="a8edd-598">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="a8edd-599">datový bod celého čísla pro`data2`kontrolu stavu ( ).</span><span class="sxs-lookup"><span data-stu-id="a8edd-599">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="a8edd-600">Pokud `null` `1` se používá .</span><span class="sxs-lookup"><span data-stu-id="a8edd-600">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="a8edd-601">stav selhání<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ).</span><span class="sxs-lookup"><span data-stu-id="a8edd-601">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="a8edd-602">Výchozí formát je `null`.</span><span class="sxs-lookup"><span data-stu-id="a8edd-602">The default is `null`.</span></span> <span data-ttu-id="a8edd-603">Pokud `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) je hlášena pro stav selhání.</span><span class="sxs-lookup"><span data-stu-id="a8edd-603">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="a8edd-604">značky`IEnumerable<string>`( ).</span><span class="sxs-lookup"><span data-stu-id="a8edd-604">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="a8edd-605">Vydavatel kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a8edd-605">Health Check Publisher</span></span>

<span data-ttu-id="a8edd-606">Při <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> přidání kontejneru služby systém kontroly stavu pravidelně provádí kontroly stavu a volání `PublishAsync` s výsledkem.</span><span class="sxs-lookup"><span data-stu-id="a8edd-606">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="a8edd-607">To je užitečné ve scénáři systému monitorování stavu na základě nabízených, který očekává, že každý proces bude pravidelně volat monitorovací systém za účelem určení stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-607">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="a8edd-608">Rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> má jedinou metodu:</span><span class="sxs-lookup"><span data-stu-id="a8edd-608">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="a8edd-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>umožňují nastavit:</span><span class="sxs-lookup"><span data-stu-id="a8edd-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="a8edd-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Počáteční zpoždění použité po spuštění aplikace <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> před spuštěním instancí.</span><span class="sxs-lookup"><span data-stu-id="a8edd-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="a8edd-611">Zpoždění se použije jednou při spuštění a nevztahuje se na následné iterace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-611">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="a8edd-612">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="a8edd-612">The default value is five seconds.</span></span>
* <span data-ttu-id="a8edd-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; Doba popravy. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher></span><span class="sxs-lookup"><span data-stu-id="a8edd-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="a8edd-614">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="a8edd-614">The default value is 30 seconds.</span></span>
* <span data-ttu-id="a8edd-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; Pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` je (výchozí), služba vydavatele kontroly stavu spustí všechny kontroly registrovaného stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="a8edd-616">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která filtruje sadu kontrol.</span><span class="sxs-lookup"><span data-stu-id="a8edd-616">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="a8edd-617">Predikát se vyhodnocuje každé období.</span><span class="sxs-lookup"><span data-stu-id="a8edd-617">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="a8edd-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Časový čas pro provádění kontroly <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> stavu pro všechny instance.</span><span class="sxs-lookup"><span data-stu-id="a8edd-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="a8edd-619">Slouží <xref:System.Threading.Timeout.InfiniteTimeSpan> k provedení bez časového času.</span><span class="sxs-lookup"><span data-stu-id="a8edd-619">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="a8edd-620">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="a8edd-620">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="a8edd-621">Ve verzi ASP.NET Core 2.2 není nastavení <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementováno; nastavuje hodnotu . <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay></span><span class="sxs-lookup"><span data-stu-id="a8edd-621">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="a8edd-622">Tento problém byl vyřešen v ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="a8edd-622">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="a8edd-623">V ukázkové `ReadinessPublisher` aplikaci <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> je implementace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-623">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="a8edd-624">Stav kontroly stavu je zaznamenán pro každou kontrolu buď takto:</span><span class="sxs-lookup"><span data-stu-id="a8edd-624">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="a8edd-625">Informace<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ) je-li <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>stav zdravotních kontrol .</span><span class="sxs-lookup"><span data-stu-id="a8edd-625">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="a8edd-626">Chyba<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ), pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> je <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>stav buď nebo .</span><span class="sxs-lookup"><span data-stu-id="a8edd-626">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="a8edd-627">V příkladu ukázkové `LivenessProbeStartup` aplikace `StartupHostedService` má kontrola připravenosti dvě sekundy zpoždění při spuštění a spustí kontrolu každých 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="a8edd-627">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="a8edd-628">Chcete-li <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> aktivovat implementaci, `ReadinessPublisher` ukázka registruje jako oslavá služba v kontejneru [vkládání závislostí (DI):](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="a8edd-628">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="a8edd-629">Následující řešení umožňuje přidání <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance do kontejneru služby, když jedna nebo více jiných hostovaných služeb již byly přidány do aplikace.</span><span class="sxs-lookup"><span data-stu-id="a8edd-629">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="a8edd-630">Toto řešení není vyžadováno v ASP.NET Jádrem 3.0.</span><span class="sxs-lookup"><span data-stu-id="a8edd-630">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
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

> [!NOTE]
> <span data-ttu-id="a8edd-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="a8edd-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="a8edd-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.</span><span class="sxs-lookup"><span data-stu-id="a8edd-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="a8edd-633">Omezit kontroly stavu pomocí MapWhen</span><span class="sxs-lookup"><span data-stu-id="a8edd-633">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="a8edd-634">Slouží <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> k podmíněnému větvení kanálu požadavků pro koncové body kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a8edd-634">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="a8edd-635">V následujícím příkladu `MapWhen` pobočky kanálu požadavku k aktivaci middleware kontroly `api/HealthCheck` stavu, pokud je přijat požadavek GET pro koncový bod:</span><span class="sxs-lookup"><span data-stu-id="a8edd-635">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="a8edd-636">Další informace naleznete v tématu <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="a8edd-636">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
