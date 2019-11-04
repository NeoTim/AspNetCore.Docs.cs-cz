---
title: Kontroly stavu v ASP.NET Core
author: guardrex
description: Naučte se, jak nastavit kontroly stavu pro infrastrukturu ASP.NET Core, jako jsou aplikace a databáze.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 11/03/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: c7cf1c432d2186f0e2f9f5082e8a2229d8a5ef8f
ms.sourcegitcommit: 9e85c2562df5e108d7933635c830297f484bb775
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73463015"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="a2d1a-103">Kontroly stavu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a2d1a-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="a2d1a-104">Od [Luke Latham](https://github.com/guardrex) a [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="a2d1a-104">By [Luke Latham](https://github.com/guardrex) and [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a2d1a-105">ASP.NET Core nabízí kontrolu stavu middlewaru a knihovny pro vytváření sestav stavu součástí infrastruktury aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="a2d1a-106">Kontroly stavu jsou zpřístupněné aplikací jako koncové body HTTP.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="a2d1a-107">Koncové body kontroly stavu je možné nakonfigurovat pro nejrůznější scénáře monitorování v reálném čase:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="a2d1a-108">Sondy stavu můžou použít orchestrace kontejnerů a nástroje pro vyrovnávání zatížení ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="a2d1a-109">Například produkt Orchestrator může reagovat na selhání kontroly stavu zastavením zavedení nasazení nebo restartováním kontejneru.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="a2d1a-110">Nástroj pro vyrovnávání zatížení může reagovat na poškozenou aplikaci směrováním provozu mimo instanci selhání do stavu v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="a2d1a-111">Použití paměti, disku a dalších prostředků fyzického serveru se dá monitorovat v dobrém stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="a2d1a-112">Kontroly stavu můžou testovat závislosti aplikace, jako jsou databáze a externí koncové body služby, aby se potvrdila dostupnost a normální fungování.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="a2d1a-113">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a2d1a-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a2d1a-114">Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="a2d1a-115">Pokud chcete pro daný scénář spustit ukázkovou aplikaci, použijte příkaz [dotnet Run](/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="a2d1a-116">Podrobnosti o tom, jak používat ukázkovou aplikaci, najdete v souboru *Readme.MD* ukázkové aplikace a v popisech scénářů v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a2d1a-117">Požadavky</span><span class="sxs-lookup"><span data-stu-id="a2d1a-117">Prerequisites</span></span>

<span data-ttu-id="a2d1a-118">Kontroly stavu se obvykle používají s externí službou monitorování nebo nástrojem Orchestrator Container Service ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="a2d1a-119">Před přidáním kontrol stavu do aplikace se rozhodněte, který monitorovací systém se má použít.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="a2d1a-120">Monitorovací systém vyhodnotí, jaké typy kontrol stavu se mají vytvořit a jak nakonfigurovat jejich koncové body.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="a2d1a-121">Na balíček [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) se odkazuje implicitně pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="a2d1a-122">Chcete-li provádět kontroly stavu pomocí Entity Framework Core, přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="a2d1a-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="a2d1a-123">Ukázková aplikace poskytuje spouštěcí kód k předvedení kontrol stavu pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="a2d1a-124">Scénář [testu databáze](#database-probe) kontroluje stav připojení k databázi pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="a2d1a-125">Scénář [testu DbContext](#entity-framework-core-dbcontext-probe) zkontroluje databázi pomocí `DbContext`EF Core.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="a2d1a-126">K prozkoumání scénářů databáze se jedná o ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="a2d1a-127">Vytvoří databázi a poskytne připojovací řetězec v souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="a2d1a-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="a2d1a-128">Obsahuje následující odkazy na balíček v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="a2d1a-129">AspNetCore. HealthChecks. SqlServer</span><span class="sxs-lookup"><span data-stu-id="a2d1a-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="a2d1a-130">Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="a2d1a-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="a2d1a-131">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="a2d1a-132">Další scénář kontroly stavu ukazuje, jak filtrovat kontroly stavu na port pro správu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="a2d1a-133">Ukázková aplikace vyžaduje, abyste vytvořili soubor *Properties/launchSettings. JSON* , který obsahuje adresu URL pro správu a port pro správu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="a2d1a-134">Další informace naleznete v části [Filter by port](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="a2d1a-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="a2d1a-135">Základní sonda stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-135">Basic health probe</span></span>

<span data-ttu-id="a2d1a-136">U mnoha aplikací je základní konfigurace sondy stavu, která oznamuje dostupnost aplikace pro zpracování požadavků (*živý*), ke zjištění stavu aplikace dostačující.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="a2d1a-137">Základní konfigurace registruje služby kontroly stavu a volá middleware kontroly stavu, aby reagovala na koncový bod adresy URL s odpovědí na stav.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="a2d1a-138">Ve výchozím nastavení nejsou k otestování konkrétní závislosti nebo subsystému registrovány žádné konkrétní kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="a2d1a-139">Aplikace se považuje za v pořádku, pokud dokáže reagovat na adresu URL koncového bodu stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="a2d1a-140">Výchozí zapisovač odpovědí zapisuje stav (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) jako odpověď v podobě prostého textu zpátky klientovi, což značí buď [funkčnosti. dobrý](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)stav, [funkčnosti. Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status není v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="a2d1a-141">Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a2d1a-142">Vytvořte koncový bod kontroly stavu voláním `MapHealthChecks` v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="a2d1a-143">V ukázkové aplikaci se koncový bod kontroly stavu vytvoří při `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="a2d1a-144">Pokud chcete spustit scénář základní konfigurace pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="a2d1a-145">Ukázka Docker</span><span class="sxs-lookup"><span data-stu-id="a2d1a-145">Docker example</span></span>

<span data-ttu-id="a2d1a-146">[Docker](xref:host-and-deploy/docker/index) nabízí integrovanou `HEALTHCHECK` direktivu, která se dá použít ke kontrole stavu aplikace, která používá konfiguraci základní kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="a2d1a-147">Vytvořit kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-147">Create health checks</span></span>

<span data-ttu-id="a2d1a-148">Kontroly stavu jsou vytvářeny implementací rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="a2d1a-149">Metoda <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> vrátí <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, která označuje stav jako `Healthy`, `Degraded`nebo `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="a2d1a-150">Výsledek je zapsán jako odpověď v podobě prostého textu s konfigurovatelným stavovým kódem (konfigurace je popsána v části [Možnosti kontroly stavu](#health-check-options) ).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="a2d1a-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> může také vracet volitelné páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="a2d1a-152">Následující třída `ExampleHealthCheck` ukazuje rozložení kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="a2d1a-153">Logika kontrol stavu je umístěna v metodě `CheckHealthAsync`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="a2d1a-154">Následující příklad nastaví fiktivní proměnnou `healthCheckResultHealthy``true`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="a2d1a-155">Pokud je hodnota `healthCheckResultHealthy` nastavena na `false`, vrátí [HealthCheckResult.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) stav není v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

## <a name="register-health-check-services"></a><span data-ttu-id="a2d1a-156">Registrovat služby kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-156">Register health check services</span></span>

<span data-ttu-id="a2d1a-157">`ExampleHealthCheck` typ se přidá do služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="a2d1a-158">Přetížení <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> znázorněné v následujícím příkladu nastaví stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) na hlášení, že při kontrole stavu hlásí chybu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="a2d1a-159">Je-li stav selhání nastaven na `null` (výchozí), [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) je hlášena hodnota není v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="a2d1a-160">Toto přetížení je užitečným scénářem pro autory knihoven, kde stav selhání uvedený v knihovně vynutila aplikace, když dojde k selhání kontroly stavu, pokud se při implementaci kontroly stavu splní nastavení.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="a2d1a-161">*Značky* lze použít k filtrování kontrol stavu (popsaných dále v oddílu [Filtr kontrol stavu](#filter-health-checks) ).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="a2d1a-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> může také spustit funkci lambda.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="a2d1a-163">V následujícím příkladu je název kontroly stavu zadán jako `Example` a funkce check vždy vrátí dobrý stav:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="a2d1a-164">Použití směrování kontrol stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-164">Use Health Checks Routing</span></span>

<span data-ttu-id="a2d1a-165">V `Startup.Configure` volejte `MapHealthChecks` na Tvůrci koncových bodů s adresou URL koncového bodu nebo relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-165">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="a2d1a-166">Vyžadovat hostitele</span><span class="sxs-lookup"><span data-stu-id="a2d1a-166">Require host</span></span>

<span data-ttu-id="a2d1a-167">Zavolejte `RequireHost` k zadání jednoho nebo více povolených hostitelů pro koncový bod kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-167">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="a2d1a-168">Hostitelé by měli být místo punycodei Unicode a můžou zahrnovat port.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-168">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="a2d1a-169">Pokud není zadaná kolekce, bude přijat libovolný hostitel.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-169">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="a2d1a-170">Další informace naleznete v části [Filter by port](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="a2d1a-170">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="a2d1a-171">Vyžadovat autorizaci</span><span class="sxs-lookup"><span data-stu-id="a2d1a-171">Require authorization</span></span>

<span data-ttu-id="a2d1a-172">Zavolejte `RequireAuthorization` pro spuštění autorizačního middlewaru na koncovém bodu žádosti o kontrolu stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-172">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="a2d1a-173">Přetížení `RequireAuthorization` přijímá jednu nebo více zásad autorizace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-173">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="a2d1a-174">Pokud zásada není zadaná, použije se výchozí zásada autorizace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-174">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="a2d1a-175">Povolení žádostí nepůvodního zdroje (CORS)</span><span class="sxs-lookup"><span data-stu-id="a2d1a-175">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="a2d1a-176">I když ruční provádění kontrol stavu z prohlížeče není běžným scénářem použití, je možné middleware CORS povolit voláním `RequireCors` v koncových bodech pro kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-176">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="a2d1a-177">Přetížení `RequireCors` přijímá delegáta zásad CORS (`CorsPolicyBuilder`) nebo název zásady.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-177">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="a2d1a-178">Pokud zásada není zadaná, použije se výchozí zásada CORS.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-178">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="a2d1a-179">Další informace najdete v tématu <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-179">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="a2d1a-180">Možnosti kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-180">Health check options</span></span>

<span data-ttu-id="a2d1a-181"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> poskytují příležitost k přizpůsobení chování kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-181"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="a2d1a-182">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-182">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="a2d1a-183">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="a2d1a-183">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="a2d1a-184">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="a2d1a-184">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="a2d1a-185">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-185">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="a2d1a-186">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-186">Filter health checks</span></span>

<span data-ttu-id="a2d1a-187">Ve výchozím nastavení middleware pro kontrolu stavu spouští všechny zaregistrované kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-187">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="a2d1a-188">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která vrací logickou hodnotu pro <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> možnost.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-188">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="a2d1a-189">V následujícím příkladu je `Bar` kontrole stavu vyfiltrováno pomocí značky (`bar_tag`) v podmíněném příkazu funkce, kde `true` je vrácena pouze v případě, že <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> vlastnost pro kontrolu stavu odpovídá `foo_tag` nebo `baz_tag`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-189">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="a2d1a-190">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-190">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="a2d1a-191">V `Startup.Configure``Predicate` filtruje kontrolu stavu "bar".</span><span class="sxs-lookup"><span data-stu-id="a2d1a-191">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="a2d1a-192">Pouze foo a Baz Execute:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-192">Only Foo and Baz execute.:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="a2d1a-193">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="a2d1a-193">Customize the HTTP status code</span></span>

<span data-ttu-id="a2d1a-194">K přizpůsobení mapování stavu na stavové kódy HTTP použijte <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-194">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="a2d1a-195">Následující přiřazení <xref:Microsoft.AspNetCore.Http.StatusCodes> jsou výchozími hodnotami, které používá middleware.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-195">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="a2d1a-196">Změňte hodnoty stavového kódu tak, aby splňovaly vaše požadavky.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-196">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="a2d1a-197">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-197">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="a2d1a-198">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="a2d1a-198">Suppress cache headers</span></span>

<span data-ttu-id="a2d1a-199"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> určuje, zda middleware pro kontrolu stavu přidává hlavičky HTTP do odezvy testu, aby nedocházelo k ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-199"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="a2d1a-200">Pokud je hodnota `false` (výchozí), middleware nastaví nebo přepíše hlavičky `Cache-Control`, `Expires`a `Pragma`, aby nedocházelo k ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-200">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="a2d1a-201">Pokud je hodnota `true`, middleware neupraví hlavičky mezipaměti odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-201">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="a2d1a-202">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-202">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="a2d1a-203">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-203">Customize output</span></span>

<span data-ttu-id="a2d1a-204">Možnost <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> Získá nebo nastaví delegáta použitý k zápisu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-204">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span>

<span data-ttu-id="a2d1a-205">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="a2d1a-206">Výchozí delegát zapisuje minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-206">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="a2d1a-207">Následující vlastní delegát, `WriteResponse`, vypíše vlastní odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-207">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

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

<span data-ttu-id="a2d1a-208">Systém kontroly stavu neposkytuje integrovanou podporu pro komplexní formáty vrácených hodnot JSON, protože formát je specifický pro váš výběr systému monitorování.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-208">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="a2d1a-209">V předchozím příkladu si můžete přizpůsobit `JObject` podle potřeby tak, aby vyhovovaly vašim potřebám.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-209">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="a2d1a-210">Test databáze</span><span class="sxs-lookup"><span data-stu-id="a2d1a-210">Database probe</span></span>

<span data-ttu-id="a2d1a-211">Kontrola stavu může určit databázový dotaz, který se má spustit jako logický test, aby označoval, zda databáze obvykle reaguje.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-211">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="a2d1a-212">Ukázková aplikace pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), knihovny kontroly stavu pro aplikace ASP.NET Core, provede kontrolu stavu databáze SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-212">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="a2d1a-213">`AspNetCore.Diagnostics.HealthChecks` spustí dotaz `SELECT 1` na databázi, aby bylo možné potvrdit, že připojení k databázi je v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-213">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="a2d1a-214">Při kontrole připojení k databázi pomocí dotazu vyberte dotaz, který se rychle vrátí.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-214">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="a2d1a-215">Přístup k dotazům spouští riziko přetížení databáze a snížení výkonu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-215">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="a2d1a-216">Ve většině případů není nutné spustit testovací dotaz.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-216">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="a2d1a-217">Stačí pouze provést úspěšné připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-217">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="a2d1a-218">Pokud zjistíte, že je nutné spustit dotaz, zvolte jednoduchý dotaz SELECT, například `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-218">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="a2d1a-219">Zahrňte odkaz na balíček do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-219">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="a2d1a-220">Zadejte platný připojovací řetězec databáze do souboru *appSettings. JSON* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-220">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="a2d1a-221">Aplikace používá databázi SQL Server s názvem `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-221">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="a2d1a-222">Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-222">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a2d1a-223">Ukázková aplikace volá metodu `AddSqlServer` s připojovacím řetězcem databáze (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-223">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a2d1a-224">Koncový bod kontroly stavu je vytvořen voláním `MapHealthChecks` v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-224">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="a2d1a-225">Pokud chcete spustit scénář testu databáze pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-225">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="a2d1a-226">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-226">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="a2d1a-227">Test Entity Framework Core DbContext</span><span class="sxs-lookup"><span data-stu-id="a2d1a-227">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="a2d1a-228">`DbContext` ověří, jestli aplikace může komunikovat s databází nakonfigurovanou pro EF Core `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-228">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="a2d1a-229">Ověření `DbContext` je podporované v aplikacích, které:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-229">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="a2d1a-230">Použijte [jádro Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-230">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="a2d1a-231">Zahrňte odkaz na balíček [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-231">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="a2d1a-232">`AddDbContextCheck<TContext>` registruje kontrolu stavu pro `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-232">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="a2d1a-233">`DbContext` je dodána jako `TContext` metody.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-233">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="a2d1a-234">K dispozici je přetížení pro konfiguraci stavu selhání, značek a vlastního testovacího dotazu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-234">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="a2d1a-235">Ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-235">By default:</span></span>

* <span data-ttu-id="a2d1a-236">`DbContextHealthCheck` volá metodu `CanConnectAsync` EF Core.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-236">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="a2d1a-237">Můžete přizpůsobit, která operace je spuštěna při kontrole stavu pomocí přetížení metod `AddDbContextCheck`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-237">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="a2d1a-238">Název kontroly stavu je název `TContext`ho typu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-238">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="a2d1a-239">V ukázkové aplikaci je `AppDbContext` k dispozici `AddDbContextCheck` a zaregistrováno jako služba v `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-239">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a2d1a-240">Koncový bod kontroly stavu je vytvořen voláním `MapHealthChecks` v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-240">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="a2d1a-241">Pokud chcete spustit scénář `DbContext` testu pomocí ukázkové aplikace, zkontrolujte, že databáze určená připojovacím řetězcem v instanci SQL Server neexistuje.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-241">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="a2d1a-242">Pokud databáze existuje, odstraňte ji.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-242">If the database exists, delete it.</span></span>

<span data-ttu-id="a2d1a-243">Ze složky projektu v příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-243">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="a2d1a-244">Po spuštění aplikace ověřte stav tak, že v prohlížeči vytvoříte požadavek na koncový bod `/health`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-244">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="a2d1a-245">Databáze a `AppDbContext` neexistují, takže aplikace nabízí následující odpověď:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-245">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="a2d1a-246">Aktivujte ukázkovou aplikaci pro vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-246">Trigger the sample app to create the database.</span></span> <span data-ttu-id="a2d1a-247">Vytvořte žádost o `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-247">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="a2d1a-248">Aplikace odpoví:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-248">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="a2d1a-249">Vytvořte požadavek na koncový bod `/health`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-249">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="a2d1a-250">Databáze a kontext existují, takže aplikace reaguje:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-250">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="a2d1a-251">Aktivujte ukázkovou aplikaci, aby se odstranila databáze.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-251">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="a2d1a-252">Vytvořte žádost o `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-252">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="a2d1a-253">Aplikace odpoví:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-253">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="a2d1a-254">Vytvořte požadavek na koncový bod `/health`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-254">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="a2d1a-255">Aplikace poskytuje poškozenou reakci:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-255">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="a2d1a-256">Samostatné sondy pro připravenost a živý provoz</span><span class="sxs-lookup"><span data-stu-id="a2d1a-256">Separate readiness and liveness probes</span></span>

<span data-ttu-id="a2d1a-257">V některých scénářích hostování se používá pár kontrol stavu, který rozlišuje dva stavy aplikace:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-257">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="a2d1a-258">Aplikace funguje, ale ještě není připravená na příjem požadavků.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-258">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="a2d1a-259">Tento stav je *připravenost*aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-259">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="a2d1a-260">Aplikace funguje a reaguje na požadavky.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-260">The app is functioning and responding to requests.</span></span> <span data-ttu-id="a2d1a-261">Tento stav je *živými*aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-261">This state is the app's *liveness*.</span></span>

<span data-ttu-id="a2d1a-262">Kontrola připravenosti obvykle provádí rozsáhlejší a časově náročnou sadu kontrol, aby bylo možné zjistit, zda jsou k dispozici všechny subsystémy a prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-262">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="a2d1a-263">K dispozici je pouze rychlá kontrolu, která určuje, zda je aplikace k dispozici pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-263">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="a2d1a-264">Po úspěšném provedení kontroly připravenosti aplikace nemusíte aplikaci dále zatěžovat s nákladným nastavením kontrol připravenosti&mdash;další kontroly vyžadují jenom kontrolu živého provozu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-264">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="a2d1a-265">Ukázková aplikace obsahuje kontrolu stavu, která oznamuje dokončení dlouhotrvající úlohy po spuštění v [hostované službě](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-265">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="a2d1a-266">`StartupHostedServiceHealthCheck` zpřístupňuje vlastnost, `StartupTaskCompleted`, kterou může hostovaná služba nastavit na `true` po dokončení dlouhotrvající úlohy (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-266">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="a2d1a-267">Dlouhotrvající úloha na pozadí se spouští v [hostované službě](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-267">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="a2d1a-268">Po uzavření úlohy je `StartupHostedServiceHealthCheck.StartupTaskCompleted` nastaveno na `true`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-268">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="a2d1a-269">Tato kontrolu stavu je zaregistrovaná ve službě <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> v `Startup.ConfigureServices` spolu s hostovanou službou.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-269">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="a2d1a-270">Vzhledem k tomu, že hostovaná služba musí nastavit vlastnost pro kontrolu stavu, je tato kontrolu také zaregistrována v kontejneru služby (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-270">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a2d1a-271">Koncový bod kontroly stavu je vytvořen voláním `MapHealthChecks` v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-271">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="a2d1a-272">V ukázkové aplikaci jsou koncové body kontroly stavu vytvořeny v:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-272">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="a2d1a-273">`/health/ready` pro kontrolu připravenosti.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-273">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="a2d1a-274">Kontrola připravenosti filtruje kontrolu stavu na kontrolu stavu pomocí značky `ready`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-274">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="a2d1a-275">`/health/live` pro kontrolu živého provozu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-275">`/health/live` for the liveness check.</span></span> <span data-ttu-id="a2d1a-276">Kontrola živého odfiltruje `StartupHostedServiceHealthCheck` vrácením `false` v [HealthCheckOptions. predikátu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Další informace najdete v tématu [filtrování kontrol stavu](#filter-health-checks)).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-276">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="a2d1a-277">V následujícím příkladu kódu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-277">In the following example code:</span></span>

* <span data-ttu-id="a2d1a-278">Kontrola připravenosti používá všechny registrované kontroly se značkou ' Read '.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-278">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="a2d1a-279">`Predicate` vyloučí všechny kontroly a vrátí 200 – OK.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-279">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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

<span data-ttu-id="a2d1a-280">Pokud chcete spustit scénář konfigurace připravenosti/živých aplikací pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-280">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="a2d1a-281">V prohlížeči navštivte `/health/ready` několikrát, dokud neuplyne 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-281">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="a2d1a-282">Zprávy o kontrole stavu nejsou v *pořádku* po dobu prvních 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-282">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="a2d1a-283">Po 15 sekundách koncový bod hlásí stav *v pořádku*, který odráží dokončení dlouhotrvající úlohy hostované službou.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-283">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="a2d1a-284">V tomto příkladu se vytvoří také Vydavatel kontroly stavu (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace), který spouští první kontrolu připravenosti se dvěma sekundami.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-284">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="a2d1a-285">Další informace najdete v části [Vydavatel kontroly stavu](#health-check-publisher) .</span><span class="sxs-lookup"><span data-stu-id="a2d1a-285">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="a2d1a-286">Příklad Kubernetes</span><span class="sxs-lookup"><span data-stu-id="a2d1a-286">Kubernetes example</span></span>

<span data-ttu-id="a2d1a-287">Používání samostatné kontroly připravenosti a živých kontrol je užitečné v prostředí, jako je [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-287">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="a2d1a-288">V Kubernetes může být aplikace nutná k provádění časově náročné práce při spuštění před přijetím požadavků, jako je například test dostupnosti podkladové databáze.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-288">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="a2d1a-289">Použití samostatných kontrol umožňuje nástroji Orchestrator rozlišit, jestli aplikace funguje, ale ještě není připravená, nebo jestli se aplikace nedala spustit.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-289">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="a2d1a-290">Další informace o testování připravenosti a provozní sondy v Kubernetes najdete v tématu [Konfigurace živých a připravených sond](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) v dokumentaci k Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-290">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="a2d1a-291">Následující příklad ukazuje konfiguraci testu připravenosti na Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-291">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="a2d1a-292">Test založený na metrikách s vlastním zapisovačem odpovědí</span><span class="sxs-lookup"><span data-stu-id="a2d1a-292">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="a2d1a-293">Ukázková aplikace ukazuje kontrolu stavu paměti pomocí vlastního zapisovače odpovědí.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-293">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="a2d1a-294">`MemoryHealthCheck` hlásí snížený stav, pokud aplikace používá více než určenou prahovou hodnotu paměti (1 GB v ukázkové aplikaci).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-294">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="a2d1a-295"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> obsahuje informace o uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-295">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="a2d1a-296">Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-296">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a2d1a-297">Místo povolení kontroly stavu předáním do <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>je `MemoryHealthCheck` zaregistrován jako služba.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-297">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="a2d1a-298">Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrované služby jsou k dispozici pro služby kontroly stavu a middleware.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-298">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="a2d1a-299">Službu pro kontrolu stavu doporučujeme zaregistrovat jako služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-299">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="a2d1a-300">V ukázkové aplikaci (*CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-300">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="a2d1a-301">Koncový bod kontroly stavu je vytvořen voláním `MapHealthChecks` v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-301">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="a2d1a-302">`WriteResponse` delegátovi je poskytnuta vlastnost `ResponseWriter` pro výstup vlastní odpovědi JSON, když se spustí kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-302">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="a2d1a-303">Metoda `WriteResponse` formátuje `CompositeHealthCheckResult` na objekt JSON a poskytuje výstup JSON pro odpověď kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-303">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="a2d1a-304">Pokud chcete spustit test založený na metrikách s vlastním výstupem zapisovače odpovědí pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-304">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="a2d1a-305">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje scénáře kontroly stavu založené na metrikách, včetně úložného prostoru na disku a maximální hodnoty živé kontroly.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-305">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="a2d1a-306">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-306">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="a2d1a-307">Filtrovat podle portu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-307">Filter by port</span></span>

<span data-ttu-id="a2d1a-308">Zavolejte `RequireHost` na `MapHealthChecks` pomocí vzoru adresy URL, který určuje port pro omezení požadavků na kontrolu stavu na zadaný port.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-308">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="a2d1a-309">Obvykle se používá v prostředí kontejneru k vystavení portu pro monitorovací služby.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-309">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="a2d1a-310">Ukázková aplikace nakonfiguruje port pomocí [poskytovatele konfigurace proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-310">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="a2d1a-311">Port je nastaven v souboru *launchSettings. JSON* a předán poskytovateli konfigurace prostřednictvím proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-311">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="a2d1a-312">Také je nutné nakonfigurovat server tak, aby naslouchal žádosti na portu pro správu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-312">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="a2d1a-313">Pokud chcete ukázkovou aplikaci použít k předvedení konfigurace portů pro správu, vytvořte soubor *launchSettings. JSON* ve složce *Properties (vlastnosti* ).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-313">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="a2d1a-314">Následující *vlastnosti/soubor launchSettings. JSON* v ukázkové aplikaci nejsou součástí souborů projektu ukázkové aplikace a je nutné je vytvořit ručně:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-314">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="a2d1a-315">Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-315">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a2d1a-316">Vytvořte koncový bod kontroly stavu voláním `MapHealthChecks` v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-316">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="a2d1a-317">V ukázkové aplikaci volání `RequireHost` na koncovém bodu v `Startup.Configure` Určuje port pro správu z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-317">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="a2d1a-318">Koncové body se vytvářejí v ukázkové aplikaci v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-318">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="a2d1a-319">V následujícím příkladu kódu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-319">In the following example code:</span></span>

* <span data-ttu-id="a2d1a-320">Kontrola připravenosti používá všechny registrované kontroly se značkou ' Read '.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-320">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="a2d1a-321">`Predicate` vyloučí všechny kontroly a vrátí 200 – OK.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-321">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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
> <span data-ttu-id="a2d1a-322">Vytvoření souboru *launchSettings. JSON* v ukázkové aplikaci se vyhnete tak, že v kódu nastavíte port pro správu explicitně.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-322">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="a2d1a-323">V *program.cs* , kde se vytvoří <xref:Microsoft.Extensions.Hosting.HostBuilder>, přidejte volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> a zadejte koncový bod portu pro správu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-323">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="a2d1a-324">V `Configure` *ManagementPortStartup.cs*zadejte port pro správu `RequireHost`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-324">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="a2d1a-325">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-325">*Program.cs*:</span></span>
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
> <span data-ttu-id="a2d1a-326">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-326">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="a2d1a-327">Pokud chcete spustit scénář konfigurace portu pro správu pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-327">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="a2d1a-328">Distribuce knihovny kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-328">Distribute a health check library</span></span>

<span data-ttu-id="a2d1a-329">Postup při distribuci kontroly stavu jako knihovny:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-329">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="a2d1a-330">Zapište kontrolu stavu, která implementuje rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> jako samostatnou třídu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-330">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="a2d1a-331">Třída může spoléhat na [vkládání závislostí (di)](xref:fundamentals/dependency-injection), aktivovat typ a [pojmenované možnosti](xref:fundamentals/configuration/options) pro přístup k datům konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-331">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="a2d1a-332">V `CheckHealthAsync`logiky kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-332">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="a2d1a-333">`data1` a `data2` se používají v metodě ke spuštění logiky kontroly stavu testu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-333">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="a2d1a-334">`AccessViolationException` se zpracovává.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-334">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="a2d1a-335">Když dojde k <xref:System.AccessViolationException>, vrátí se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, aby uživatelé mohli konfigurovat stav selhání kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-335">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="a2d1a-336">Napište metodu rozšíření s parametry, které aplikace spotřebovávají v metodě `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-336">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="a2d1a-337">V následujícím příkladu předpokládejme následující signatura metody kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-337">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="a2d1a-338">Předchozí signatura indikuje, že `ExampleHealthCheck` vyžaduje další data pro zpracování logiky sondy kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-338">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="a2d1a-339">Data jsou k dispozici delegátovi použitému k vytvoření instance kontroly stavu při registraci kontroly stavu s metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-339">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="a2d1a-340">V následujícím příkladu volající určuje nepovinné:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-340">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="a2d1a-341">název kontroly stavu (`name`).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-341">health check name (`name`).</span></span> <span data-ttu-id="a2d1a-342">Při `null`se používá `example_health_check`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-342">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="a2d1a-343">datový bod řetězce pro kontrolu stavu (`data1`).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-343">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="a2d1a-344">celočíselný datový bod pro kontrolu stavu (`data2`).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-344">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="a2d1a-345">Při `null`se používá `1`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-345">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="a2d1a-346">stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-346">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="a2d1a-347">Výchozí hodnota je `null`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-347">The default is `null`.</span></span> <span data-ttu-id="a2d1a-348">Pokud `null`, [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) není v pořádku, je hlášen stav selhání.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-348">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="a2d1a-349">značky (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-349">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="a2d1a-350">Vydavatel kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-350">Health Check Publisher</span></span>

<span data-ttu-id="a2d1a-351">Když se do kontejneru služby přidá <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, systém kontroly stavu pravidelně spouští kontroly stavu a volání `PublishAsync` s výsledkem.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-351">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="a2d1a-352">To je užitečné ve scénáři sledování stavu založeném na nabízených oznámeních, který očekává, že každý proces bude pravidelně volat monitorovací systém, aby se zjistil stav.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-352">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="a2d1a-353">Rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> má jedinou metodu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-353">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="a2d1a-354"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> vám umožní nastavit:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-354"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="a2d1a-355"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; počáteční prodlevu použitou po spuštění aplikace před spuštěním <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instancí.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-355"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="a2d1a-356">Zpoždění se použije jednou při spuštění a nevztahuje se na další iterace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-356">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="a2d1a-357">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-357">The default value is five seconds.</span></span>
* <span data-ttu-id="a2d1a-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; období provádění <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="a2d1a-359">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-359">The default value is 30 seconds.</span></span>
* <span data-ttu-id="a2d1a-360"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; Pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` (výchozí), služba Vydavatel kontroly stavu spustí všechny zaregistrované kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-360"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="a2d1a-361">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která filtruje sadu kontrol.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-361">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="a2d1a-362">Predikát je vyhodnocen každou periodu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-362">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="a2d1a-363"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; časový limit pro provádění kontrol stavu pro všechny instance <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-363"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="a2d1a-364">Použijte <xref:System.Threading.Timeout.InfiniteTimeSpan> k provedení bez časového limitu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-364">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="a2d1a-365">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-365">The default value is 30 seconds.</span></span>

<span data-ttu-id="a2d1a-366">V ukázkové aplikaci `ReadinessPublisher` je implementace <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-366">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="a2d1a-367">Stav kontroly stavu se protokoluje pro každou kontrolu na úrovni protokolu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-367">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="a2d1a-368">Informace (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>), pokud je stav kontrol stavu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-368">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="a2d1a-369">Chyba (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>), pokud je stav buď <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> nebo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-369">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="a2d1a-370">V ukázkovém `LivenessProbeStartup` ukázkové aplikace má `StartupHostedService` kontrolu připravenosti dvě druhé zpoždění při spuštění a tato kontrolu proběhne každých 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-370">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="a2d1a-371">Chcete-li aktivovat <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementaci, ukázky registruje `ReadinessPublisher` jako službu s jedním prvkem v kontejneru [Injektáže (di) pro vkládání závislostí](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="a2d1a-371">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="a2d1a-372">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-372">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="a2d1a-373">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-373">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="a2d1a-374">Omezení kontrol stavu pomocí MapWhen</span><span class="sxs-lookup"><span data-stu-id="a2d1a-374">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="a2d1a-375">Použijte <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> k podmíněně větvení kanálu požadavků pro koncové body kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-375">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="a2d1a-376">V následujícím příkladu `MapWhen` větvení kanálu žádosti o aktivaci middlewaru pro kontrolu stavu, pokud se přijme požadavek GET pro `api/HealthCheck` koncový bod:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-376">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

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

<span data-ttu-id="a2d1a-377">Další informace najdete v tématu <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-377">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a2d1a-378">ASP.NET Core nabízí kontrolu stavu middlewaru a knihovny pro vytváření sestav stavu součástí infrastruktury aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-378">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="a2d1a-379">Kontroly stavu jsou zpřístupněné aplikací jako koncové body HTTP.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-379">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="a2d1a-380">Koncové body kontroly stavu je možné nakonfigurovat pro nejrůznější scénáře monitorování v reálném čase:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-380">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="a2d1a-381">Sondy stavu můžou použít orchestrace kontejnerů a nástroje pro vyrovnávání zatížení ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-381">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="a2d1a-382">Například produkt Orchestrator může reagovat na selhání kontroly stavu zastavením zavedení nasazení nebo restartováním kontejneru.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-382">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="a2d1a-383">Nástroj pro vyrovnávání zatížení může reagovat na poškozenou aplikaci směrováním provozu mimo instanci selhání do stavu v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-383">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="a2d1a-384">Použití paměti, disku a dalších prostředků fyzického serveru se dá monitorovat v dobrém stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-384">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="a2d1a-385">Kontroly stavu můžou testovat závislosti aplikace, jako jsou databáze a externí koncové body služby, aby se potvrdila dostupnost a normální fungování.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-385">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="a2d1a-386">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a2d1a-386">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a2d1a-387">Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-387">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="a2d1a-388">Pokud chcete pro daný scénář spustit ukázkovou aplikaci, použijte příkaz [dotnet Run](/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-388">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="a2d1a-389">Podrobnosti o tom, jak používat ukázkovou aplikaci, najdete v souboru *Readme.MD* ukázkové aplikace a v popisech scénářů v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-389">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a2d1a-390">Požadavky</span><span class="sxs-lookup"><span data-stu-id="a2d1a-390">Prerequisites</span></span>

<span data-ttu-id="a2d1a-391">Kontroly stavu se obvykle používají s externí službou monitorování nebo nástrojem Orchestrator Container Service ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-391">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="a2d1a-392">Před přidáním kontrol stavu do aplikace se rozhodněte, který monitorovací systém se má použít.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-392">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="a2d1a-393">Monitorovací systém vyhodnotí, jaké typy kontrol stavu se mají vytvořit a jak nakonfigurovat jejich koncové body.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-393">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="a2d1a-394">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) .</span><span class="sxs-lookup"><span data-stu-id="a2d1a-394">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="a2d1a-395">Ukázková aplikace poskytuje spouštěcí kód k předvedení kontrol stavu pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-395">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="a2d1a-396">Scénář [testu databáze](#database-probe) kontroluje stav připojení k databázi pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-396">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="a2d1a-397">Scénář [testu DbContext](#entity-framework-core-dbcontext-probe) zkontroluje databázi pomocí `DbContext`EF Core.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-397">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="a2d1a-398">K prozkoumání scénářů databáze se jedná o ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-398">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="a2d1a-399">Vytvoří databázi a poskytne připojovací řetězec v souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="a2d1a-399">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="a2d1a-400">Obsahuje následující odkazy na balíček v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-400">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="a2d1a-401">AspNetCore. HealthChecks. SqlServer</span><span class="sxs-lookup"><span data-stu-id="a2d1a-401">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="a2d1a-402">Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="a2d1a-402">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="a2d1a-403">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-403">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="a2d1a-404">Další scénář kontroly stavu ukazuje, jak filtrovat kontroly stavu na port pro správu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-404">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="a2d1a-405">Ukázková aplikace vyžaduje, abyste vytvořili soubor *Properties/launchSettings. JSON* , který obsahuje adresu URL pro správu a port pro správu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-405">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="a2d1a-406">Další informace naleznete v části [Filter by port](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="a2d1a-406">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="a2d1a-407">Základní sonda stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-407">Basic health probe</span></span>

<span data-ttu-id="a2d1a-408">U mnoha aplikací je základní konfigurace sondy stavu, která oznamuje dostupnost aplikace pro zpracování požadavků (*živý*), ke zjištění stavu aplikace dostačující.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-408">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="a2d1a-409">Základní konfigurace registruje služby kontroly stavu a volá middleware kontroly stavu, aby reagovala na koncový bod adresy URL s odpovědí na stav.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-409">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="a2d1a-410">Ve výchozím nastavení nejsou k otestování konkrétní závislosti nebo subsystému registrovány žádné konkrétní kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-410">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="a2d1a-411">Aplikace se považuje za v pořádku, pokud dokáže reagovat na adresu URL koncového bodu stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-411">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="a2d1a-412">Výchozí zapisovač odpovědí zapisuje stav (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) jako odpověď v podobě prostého textu zpátky klientovi, což značí buď [funkčnosti. dobrý](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)stav, [funkčnosti. Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status není v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-412">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="a2d1a-413">Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-413">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a2d1a-414">Přidejte koncový bod pro kontrolu stavu middleware s <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> v kanálu zpracování žádosti `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-414">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="a2d1a-415">V ukázkové aplikaci se koncový bod kontroly stavu vytvoří při `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-415">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="a2d1a-416">Pokud chcete spustit scénář základní konfigurace pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-416">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="a2d1a-417">Ukázka Docker</span><span class="sxs-lookup"><span data-stu-id="a2d1a-417">Docker example</span></span>

<span data-ttu-id="a2d1a-418">[Docker](xref:host-and-deploy/docker/index) nabízí integrovanou `HEALTHCHECK` direktivu, která se dá použít ke kontrole stavu aplikace, která používá konfiguraci základní kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-418">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="a2d1a-419">Vytvořit kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-419">Create health checks</span></span>

<span data-ttu-id="a2d1a-420">Kontroly stavu jsou vytvářeny implementací rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-420">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="a2d1a-421">Metoda <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> vrátí <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, která označuje stav jako `Healthy`, `Degraded`nebo `Unhealthy`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-421">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="a2d1a-422">Výsledek je zapsán jako odpověď v podobě prostého textu s konfigurovatelným stavovým kódem (konfigurace je popsána v části [Možnosti kontroly stavu](#health-check-options) ).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-422">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="a2d1a-423"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> může také vracet volitelné páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-423"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="a2d1a-424">Příklad kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-424">Example health check</span></span>

<span data-ttu-id="a2d1a-425">Následující třída `ExampleHealthCheck` ukazuje rozložení kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-425">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="a2d1a-426">Logika kontrol stavu je umístěna v metodě `CheckHealthAsync`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-426">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="a2d1a-427">Následující příklad nastaví fiktivní proměnnou `healthCheckResultHealthy``true`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-427">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="a2d1a-428">Pokud je hodnota `healthCheckResultHealthy` nastavena na `false`, vrátí [HealthCheckResult.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) stav není v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-428">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

### <a name="register-health-check-services"></a><span data-ttu-id="a2d1a-429">Registrovat služby kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-429">Register health check services</span></span>

<span data-ttu-id="a2d1a-430">`ExampleHealthCheck` typ se přidá do služby kontroly stavu v `Startup.ConfigureServices` s <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-430">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="a2d1a-431">Přetížení <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> znázorněné v následujícím příkladu nastaví stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) na hlášení, že při kontrole stavu hlásí chybu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-431">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="a2d1a-432">Je-li stav selhání nastaven na `null` (výchozí), [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) je hlášena hodnota není v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-432">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="a2d1a-433">Toto přetížení je užitečným scénářem pro autory knihoven, kde stav selhání uvedený v knihovně vynutila aplikace, když dojde k selhání kontroly stavu, pokud se při implementaci kontroly stavu splní nastavení.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-433">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="a2d1a-434">*Značky* lze použít k filtrování kontrol stavu (popsaných dále v oddílu [Filtr kontrol stavu](#filter-health-checks) ).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-434">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="a2d1a-435"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> může také spustit funkci lambda.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-435"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="a2d1a-436">V následujícím příkladu `Startup.ConfigureServices` název kontroly stavu zadaný jako `Example` a zaškrtávací políčka vždy vrátí dobrý stav:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-436">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="a2d1a-437">Použití middlewaru pro kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-437">Use Health Checks Middleware</span></span>

<span data-ttu-id="a2d1a-438">V `Startup.Configure`volejte <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> v kanálu zpracování s adresou URL koncového bodu nebo relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-438">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="a2d1a-439">Pokud mají kontroly stavu naslouchat na konkrétním portu, použijte přetížení <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> k nastavení portu (popsáno dále v části [Filter by port](#filter-by-port) ):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-439">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="a2d1a-440">Možnosti kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-440">Health check options</span></span>

<span data-ttu-id="a2d1a-441"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> poskytují příležitost k přizpůsobení chování kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-441"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="a2d1a-442">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-442">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="a2d1a-443">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="a2d1a-443">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="a2d1a-444">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="a2d1a-444">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="a2d1a-445">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-445">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="a2d1a-446">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-446">Filter health checks</span></span>

<span data-ttu-id="a2d1a-447">Ve výchozím nastavení middleware pro kontrolu stavu spouští všechny zaregistrované kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-447">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="a2d1a-448">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která vrací logickou hodnotu pro <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> možnost.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-448">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="a2d1a-449">V následujícím příkladu je `Bar` kontrole stavu vyfiltrováno pomocí značky (`bar_tag`) v podmíněném příkazu funkce, kde `true` je vrácena pouze v případě, že <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> vlastnost pro kontrolu stavu odpovídá `foo_tag` nebo `baz_tag`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-449">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="a2d1a-450">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="a2d1a-450">Customize the HTTP status code</span></span>

<span data-ttu-id="a2d1a-451">K přizpůsobení mapování stavu na stavové kódy HTTP použijte <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-451">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="a2d1a-452">Následující přiřazení <xref:Microsoft.AspNetCore.Http.StatusCodes> jsou výchozími hodnotami, které používá middleware.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-452">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="a2d1a-453">Změňte hodnoty stavového kódu tak, aby splňovaly vaše požadavky.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-453">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="a2d1a-454">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-454">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="a2d1a-455">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="a2d1a-455">Suppress cache headers</span></span>

<span data-ttu-id="a2d1a-456"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> určuje, zda middleware pro kontrolu stavu přidává hlavičky HTTP do odezvy testu, aby nedocházelo k ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-456"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="a2d1a-457">Pokud je hodnota `false` (výchozí), middleware nastaví nebo přepíše hlavičky `Cache-Control`, `Expires`a `Pragma`, aby nedocházelo k ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-457">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="a2d1a-458">Pokud je hodnota `true`, middleware neupraví hlavičky mezipaměti odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-458">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="a2d1a-459">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-459">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="a2d1a-460">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-460">Customize output</span></span>

<span data-ttu-id="a2d1a-461">Možnost <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> Získá nebo nastaví delegáta použitý k zápisu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-461">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="a2d1a-462">Výchozí delegát zapisuje minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-462">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="a2d1a-463">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-463">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="a2d1a-464">Výchozí delegát zapisuje minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-464">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="a2d1a-465">Následující vlastní delegát, `WriteResponse`, vypíše vlastní odpověď JSON:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-465">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

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

<span data-ttu-id="a2d1a-466">Systém kontroly stavu neposkytuje integrovanou podporu pro komplexní formáty vrácených hodnot JSON, protože formát je specifický pro váš výběr systému monitorování.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-466">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="a2d1a-467">V předchozím příkladu si můžete přizpůsobit `JObject` podle potřeby tak, aby vyhovovaly vašim potřebám.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-467">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="a2d1a-468">Test databáze</span><span class="sxs-lookup"><span data-stu-id="a2d1a-468">Database probe</span></span>

<span data-ttu-id="a2d1a-469">Kontrola stavu může určit databázový dotaz, který se má spustit jako logický test, aby označoval, zda databáze obvykle reaguje.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-469">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="a2d1a-470">Ukázková aplikace pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), knihovny kontroly stavu pro aplikace ASP.NET Core, provede kontrolu stavu databáze SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-470">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="a2d1a-471">`AspNetCore.Diagnostics.HealthChecks` spustí dotaz `SELECT 1` na databázi, aby bylo možné potvrdit, že připojení k databázi je v pořádku.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-471">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="a2d1a-472">Při kontrole připojení k databázi pomocí dotazu vyberte dotaz, který se rychle vrátí.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-472">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="a2d1a-473">Přístup k dotazům spouští riziko přetížení databáze a snížení výkonu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-473">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="a2d1a-474">Ve většině případů není nutné spustit testovací dotaz.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-474">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="a2d1a-475">Stačí pouze provést úspěšné připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-475">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="a2d1a-476">Pokud zjistíte, že je nutné spustit dotaz, zvolte jednoduchý dotaz SELECT, například `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-476">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="a2d1a-477">Zahrňte odkaz na balíček do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-477">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="a2d1a-478">Zadejte platný připojovací řetězec databáze do souboru *appSettings. JSON* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-478">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="a2d1a-479">Aplikace používá databázi SQL Server s názvem `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-479">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="a2d1a-480">Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-480">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a2d1a-481">Ukázková aplikace volá metodu `AddSqlServer` s připojovacím řetězcem databáze (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-481">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a2d1a-482">Kontrola stavu volání middlewaru v kanálu zpracování aplikace v `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-482">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="a2d1a-483">Pokud chcete spustit scénář testu databáze pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-483">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="a2d1a-484">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-484">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="a2d1a-485">Test Entity Framework Core DbContext</span><span class="sxs-lookup"><span data-stu-id="a2d1a-485">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="a2d1a-486">`DbContext` ověří, jestli aplikace může komunikovat s databází nakonfigurovanou pro EF Core `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-486">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="a2d1a-487">Ověření `DbContext` je podporované v aplikacích, které:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-487">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="a2d1a-488">Použijte [jádro Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-488">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="a2d1a-489">Zahrňte odkaz na balíček [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-489">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="a2d1a-490">`AddDbContextCheck<TContext>` registruje kontrolu stavu pro `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-490">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="a2d1a-491">`DbContext` je dodána jako `TContext` metody.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-491">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="a2d1a-492">K dispozici je přetížení pro konfiguraci stavu selhání, značek a vlastního testovacího dotazu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-492">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="a2d1a-493">Ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-493">By default:</span></span>

* <span data-ttu-id="a2d1a-494">`DbContextHealthCheck` volá metodu `CanConnectAsync` EF Core.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-494">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="a2d1a-495">Můžete přizpůsobit, která operace je spuštěna při kontrole stavu pomocí přetížení metod `AddDbContextCheck`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-495">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="a2d1a-496">Název kontroly stavu je název `TContext`ho typu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-496">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="a2d1a-497">V ukázkové aplikaci je `AppDbContext` k dispozici `AddDbContextCheck` a zaregistrováno jako služba v `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-497">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a2d1a-498">V ukázkové aplikaci `UseHealthChecks` přidává do `Startup.Configure`middleware kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-498">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="a2d1a-499">Pokud chcete spustit scénář `DbContext` testu pomocí ukázkové aplikace, zkontrolujte, že databáze určená připojovacím řetězcem v instanci SQL Server neexistuje.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-499">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="a2d1a-500">Pokud databáze existuje, odstraňte ji.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-500">If the database exists, delete it.</span></span>

<span data-ttu-id="a2d1a-501">Ze složky projektu v příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-501">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="a2d1a-502">Po spuštění aplikace ověřte stav tak, že v prohlížeči vytvoříte požadavek na koncový bod `/health`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-502">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="a2d1a-503">Databáze a `AppDbContext` neexistují, takže aplikace nabízí následující odpověď:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-503">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="a2d1a-504">Aktivujte ukázkovou aplikaci pro vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-504">Trigger the sample app to create the database.</span></span> <span data-ttu-id="a2d1a-505">Vytvořte žádost o `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-505">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="a2d1a-506">Aplikace odpoví:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-506">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="a2d1a-507">Vytvořte požadavek na koncový bod `/health`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-507">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="a2d1a-508">Databáze a kontext existují, takže aplikace reaguje:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-508">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="a2d1a-509">Aktivujte ukázkovou aplikaci, aby se odstranila databáze.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-509">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="a2d1a-510">Vytvořte žádost o `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-510">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="a2d1a-511">Aplikace odpoví:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-511">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="a2d1a-512">Vytvořte požadavek na koncový bod `/health`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-512">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="a2d1a-513">Aplikace poskytuje poškozenou reakci:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-513">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="a2d1a-514">Samostatné sondy pro připravenost a živý provoz</span><span class="sxs-lookup"><span data-stu-id="a2d1a-514">Separate readiness and liveness probes</span></span>

<span data-ttu-id="a2d1a-515">V některých scénářích hostování se používá pár kontrol stavu, který rozlišuje dva stavy aplikace:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-515">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="a2d1a-516">Aplikace funguje, ale ještě není připravená na příjem požadavků.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-516">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="a2d1a-517">Tento stav je *připravenost*aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-517">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="a2d1a-518">Aplikace funguje a reaguje na požadavky.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-518">The app is functioning and responding to requests.</span></span> <span data-ttu-id="a2d1a-519">Tento stav je *živými*aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-519">This state is the app's *liveness*.</span></span>

<span data-ttu-id="a2d1a-520">Kontrola připravenosti obvykle provádí rozsáhlejší a časově náročnou sadu kontrol, aby bylo možné zjistit, zda jsou k dispozici všechny subsystémy a prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-520">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="a2d1a-521">K dispozici je pouze rychlá kontrolu, která určuje, zda je aplikace k dispozici pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-521">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="a2d1a-522">Po úspěšném provedení kontroly připravenosti aplikace nemusíte aplikaci dále zatěžovat s nákladným nastavením kontrol připravenosti&mdash;další kontroly vyžadují jenom kontrolu živého provozu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-522">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="a2d1a-523">Ukázková aplikace obsahuje kontrolu stavu, která oznamuje dokončení dlouhotrvající úlohy po spuštění v [hostované službě](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-523">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="a2d1a-524">`StartupHostedServiceHealthCheck` zpřístupňuje vlastnost, `StartupTaskCompleted`, kterou může hostovaná služba nastavit na `true` po dokončení dlouhotrvající úlohy (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-524">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="a2d1a-525">Dlouhotrvající úloha na pozadí se spouští v [hostované službě](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-525">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="a2d1a-526">Po uzavření úlohy je `StartupHostedServiceHealthCheck.StartupTaskCompleted` nastaveno na `true`:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-526">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="a2d1a-527">Tato kontrolu stavu je zaregistrovaná ve službě <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> v `Startup.ConfigureServices` spolu s hostovanou službou.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-527">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="a2d1a-528">Vzhledem k tomu, že hostovaná služba musí nastavit vlastnost pro kontrolu stavu, je tato kontrolu také zaregistrována v kontejneru služby (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-528">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="a2d1a-529">Ověřování stavu volání middlewaru v kanálu zpracování aplikace v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-529">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="a2d1a-530">V ukázkové aplikaci jsou koncové body kontroly stavu vytvářeny na `/health/ready` pro kontrolu připravenosti a `/health/live` pro kontrolu živých.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-530">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="a2d1a-531">Kontrola připravenosti filtruje kontrolu stavu na kontrolu stavu pomocí značky `ready`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-531">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="a2d1a-532">Kontrola živého odfiltruje `StartupHostedServiceHealthCheck` vrácením `false` v [HealthCheckOptions. predikátu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Další informace najdete v tématu [filtrování kontrol stavu](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-532">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

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

<span data-ttu-id="a2d1a-533">Pokud chcete spustit scénář konfigurace připravenosti/živých aplikací pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-533">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="a2d1a-534">V prohlížeči navštivte `/health/ready` několikrát, dokud neuplyne 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-534">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="a2d1a-535">Zprávy o kontrole stavu nejsou v *pořádku* po dobu prvních 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-535">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="a2d1a-536">Po 15 sekundách koncový bod hlásí stav *v pořádku*, který odráží dokončení dlouhotrvající úlohy hostované službou.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-536">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="a2d1a-537">V tomto příkladu se vytvoří také Vydavatel kontroly stavu (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace), který spouští první kontrolu připravenosti se dvěma sekundami.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-537">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="a2d1a-538">Další informace najdete v části [Vydavatel kontroly stavu](#health-check-publisher) .</span><span class="sxs-lookup"><span data-stu-id="a2d1a-538">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="a2d1a-539">Příklad Kubernetes</span><span class="sxs-lookup"><span data-stu-id="a2d1a-539">Kubernetes example</span></span>

<span data-ttu-id="a2d1a-540">Používání samostatné kontroly připravenosti a živých kontrol je užitečné v prostředí, jako je [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-540">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="a2d1a-541">V Kubernetes může být aplikace nutná k provádění časově náročné práce při spuštění před přijetím požadavků, jako je například test dostupnosti podkladové databáze.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-541">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="a2d1a-542">Použití samostatných kontrol umožňuje nástroji Orchestrator rozlišit, jestli aplikace funguje, ale ještě není připravená, nebo jestli se aplikace nedala spustit.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-542">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="a2d1a-543">Další informace o testování připravenosti a provozní sondy v Kubernetes najdete v tématu [Konfigurace živých a připravených sond](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) v dokumentaci k Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-543">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="a2d1a-544">Následující příklad ukazuje konfiguraci testu připravenosti na Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-544">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="a2d1a-545">Test založený na metrikách s vlastním zapisovačem odpovědí</span><span class="sxs-lookup"><span data-stu-id="a2d1a-545">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="a2d1a-546">Ukázková aplikace ukazuje kontrolu stavu paměti pomocí vlastního zapisovače odpovědí.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-546">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="a2d1a-547">`MemoryHealthCheck` hlásí stav není v pořádku, pokud aplikace používá více než určenou prahovou hodnotu paměti (1 GB v ukázkové aplikaci).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-547">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="a2d1a-548"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> obsahuje informace o uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-548">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="a2d1a-549">Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-549">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a2d1a-550">Místo povolení kontroly stavu předáním do <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>je `MemoryHealthCheck` zaregistrován jako služba.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-550">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="a2d1a-551">Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrované služby jsou k dispozici pro služby kontroly stavu a middleware.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-551">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="a2d1a-552">Službu pro kontrolu stavu doporučujeme zaregistrovat jako služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-552">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="a2d1a-553">V ukázkové aplikaci (*CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-553">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="a2d1a-554">Ověřování stavu volání middlewaru v kanálu zpracování aplikace v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-554">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="a2d1a-555">`WriteResponse` delegátovi je poskytnuta vlastnost `ResponseWriter` pro výstup vlastní odpovědi JSON, když se spustí kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-555">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

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

<span data-ttu-id="a2d1a-556">Metoda `WriteResponse` formátuje `CompositeHealthCheckResult` na objekt JSON a poskytuje výstup JSON pro odpověď kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-556">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="a2d1a-557">Pokud chcete spustit test založený na metrikách s vlastním výstupem zapisovače odpovědí pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-557">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="a2d1a-558">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje scénáře kontroly stavu založené na metrikách, včetně úložného prostoru na disku a maximální hodnoty živé kontroly.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-558">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="a2d1a-559">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-559">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="a2d1a-560">Filtrovat podle portu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-560">Filter by port</span></span>

<span data-ttu-id="a2d1a-561">Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> s portem omezuje požadavky na kontrolu stavu na zadaný port.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-561">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="a2d1a-562">Obvykle se používá v prostředí kontejneru k vystavení portu pro monitorovací služby.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-562">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="a2d1a-563">Ukázková aplikace nakonfiguruje port pomocí [poskytovatele konfigurace proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-563">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="a2d1a-564">Port je nastaven v souboru *launchSettings. JSON* a předán poskytovateli konfigurace prostřednictvím proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-564">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="a2d1a-565">Také je nutné nakonfigurovat server tak, aby naslouchal žádosti na portu pro správu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-565">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="a2d1a-566">Pokud chcete ukázkovou aplikaci použít k předvedení konfigurace portů pro správu, vytvořte soubor *launchSettings. JSON* ve složce *Properties (vlastnosti* ).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-566">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="a2d1a-567">Následující *vlastnosti/soubor launchSettings. JSON* v ukázkové aplikaci nejsou součástí souborů projektu ukázkové aplikace a je nutné je vytvořit ručně:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-567">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="a2d1a-568">Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-568">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a2d1a-569">Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> Určuje port pro správu (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="a2d1a-569">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="a2d1a-570">Vytvoření souboru *launchSettings. JSON* v ukázkové aplikaci se vyhnete tak, že v kódu nastavíte adresy URL a port pro správu explicitně.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-570">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="a2d1a-571">V *program.cs* , kde je vytvořena <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>, přidejte volání <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> a zadejte normální koncový bod odpovědi aplikace a koncový bod portu pro správu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-571">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="a2d1a-572">V *ManagementPortStartup.cs* , kde se volá <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>, zadejte port pro správu explicitně.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-572">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="a2d1a-573">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-573">*Program.cs*:</span></span>
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
> <span data-ttu-id="a2d1a-574">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-574">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="a2d1a-575">Pokud chcete spustit scénář konfigurace portu pro správu pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-575">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="a2d1a-576">Distribuce knihovny kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-576">Distribute a health check library</span></span>

<span data-ttu-id="a2d1a-577">Postup při distribuci kontroly stavu jako knihovny:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-577">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="a2d1a-578">Zapište kontrolu stavu, která implementuje rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> jako samostatnou třídu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-578">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="a2d1a-579">Třída může spoléhat na [vkládání závislostí (di)](xref:fundamentals/dependency-injection), aktivovat typ a [pojmenované možnosti](xref:fundamentals/configuration/options) pro přístup k datům konfigurace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-579">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="a2d1a-580">V `CheckHealthAsync`logiky kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-580">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="a2d1a-581">`data1` a `data2` se používají v metodě ke spuštění logiky kontroly stavu testu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-581">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="a2d1a-582">`AccessViolationException` se zpracovává.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-582">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="a2d1a-583">Když dojde k <xref:System.AccessViolationException>, vrátí se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, aby uživatelé mohli konfigurovat stav selhání kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-583">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="a2d1a-584">Napište metodu rozšíření s parametry, které aplikace spotřebovávají v metodě `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-584">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="a2d1a-585">V následujícím příkladu předpokládejme následující signatura metody kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-585">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="a2d1a-586">Předchozí signatura indikuje, že `ExampleHealthCheck` vyžaduje další data pro zpracování logiky sondy kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-586">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="a2d1a-587">Data jsou k dispozici delegátovi použitému k vytvoření instance kontroly stavu při registraci kontroly stavu s metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-587">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="a2d1a-588">V následujícím příkladu volající určuje nepovinné:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-588">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="a2d1a-589">název kontroly stavu (`name`).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-589">health check name (`name`).</span></span> <span data-ttu-id="a2d1a-590">Při `null`se používá `example_health_check`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-590">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="a2d1a-591">datový bod řetězce pro kontrolu stavu (`data1`).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-591">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="a2d1a-592">celočíselný datový bod pro kontrolu stavu (`data2`).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-592">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="a2d1a-593">Při `null`se používá `1`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-593">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="a2d1a-594">stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-594">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="a2d1a-595">Výchozí hodnota je `null`.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-595">The default is `null`.</span></span> <span data-ttu-id="a2d1a-596">Pokud `null`, [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) není v pořádku, je hlášen stav selhání.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-596">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="a2d1a-597">značky (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-597">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="a2d1a-598">Vydavatel kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="a2d1a-598">Health Check Publisher</span></span>

<span data-ttu-id="a2d1a-599">Když se do kontejneru služby přidá <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, systém kontroly stavu pravidelně spouští kontroly stavu a volání `PublishAsync` s výsledkem.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-599">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="a2d1a-600">To je užitečné ve scénáři sledování stavu založeném na nabízených oznámeních, který očekává, že každý proces bude pravidelně volat monitorovací systém, aby se zjistil stav.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-600">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="a2d1a-601">Rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> má jedinou metodu:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-601">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="a2d1a-602"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> vám umožní nastavit:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-602"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="a2d1a-603"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; počáteční prodlevu použitou po spuštění aplikace před spuštěním <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instancí.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-603"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="a2d1a-604">Zpoždění se použije jednou při spuštění a nevztahuje se na další iterace.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-604">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="a2d1a-605">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-605">The default value is five seconds.</span></span>
* <span data-ttu-id="a2d1a-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; období provádění <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="a2d1a-607">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-607">The default value is 30 seconds.</span></span>
* <span data-ttu-id="a2d1a-608"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; Pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` (výchozí), služba Vydavatel kontroly stavu spustí všechny zaregistrované kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-608"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="a2d1a-609">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která filtruje sadu kontrol.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-609">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="a2d1a-610">Predikát je vyhodnocen každou periodu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-610">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="a2d1a-611"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; časový limit pro provádění kontrol stavu pro všechny instance <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-611"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="a2d1a-612">Použijte <xref:System.Threading.Timeout.InfiniteTimeSpan> k provedení bez časového limitu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-612">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="a2d1a-613">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-613">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="a2d1a-614">V ASP.NET Core vydání 2,2 se nastavení <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> nedodržuje v <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementaci; nastaví hodnotu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-614">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="a2d1a-615">Tento problém byl vyřešen v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-615">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="a2d1a-616">V ukázkové aplikaci `ReadinessPublisher` je implementace <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-616">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="a2d1a-617">Stav kontroly stavu se zaznamená do protokolu pro každé vrácení se změnami buď:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-617">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="a2d1a-618">Informace (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>), pokud je stav kontrol stavu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-618">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="a2d1a-619">Chyba (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>), pokud je stav buď <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> nebo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-619">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="a2d1a-620">V ukázkovém `LivenessProbeStartup` ukázkové aplikace má `StartupHostedService` kontrolu připravenosti dvě druhé zpoždění při spuštění a tato kontrolu proběhne každých 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-620">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="a2d1a-621">Chcete-li aktivovat <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementaci, ukázky registruje `ReadinessPublisher` jako službu s jedním prvkem v kontejneru [Injektáže (di) pro vkládání závislostí](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="a2d1a-621">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="a2d1a-622">Následující alternativní řešení povoluje přidání instance <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> do kontejneru služby, pokud již byla do aplikace přidána jedna nebo více dalších hostovaných služeb.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-622">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="a2d1a-623">Toto řešení nebude v ASP.NET Core 3,0 vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-623">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
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
> <span data-ttu-id="a2d1a-624">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="a2d1a-624">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="a2d1a-625">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-625">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="a2d1a-626">Omezení kontrol stavu pomocí MapWhen</span><span class="sxs-lookup"><span data-stu-id="a2d1a-626">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="a2d1a-627">Použijte <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> k podmíněně větvení kanálu požadavků pro koncové body kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-627">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="a2d1a-628">V následujícím příkladu `MapWhen` větvení kanálu žádosti o aktivaci middlewaru pro kontrolu stavu, pokud se přijme požadavek GET pro `api/HealthCheck` koncový bod:</span><span class="sxs-lookup"><span data-stu-id="a2d1a-628">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="a2d1a-629">Další informace najdete v tématu <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="a2d1a-629">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
