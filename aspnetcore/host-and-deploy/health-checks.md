---
title: Kontroly stavu v ASP.NET Core
author: guardrex
description: Naučte se, jak nastavit kontroly stavu pro infrastrukturu ASP.NET Core, jako jsou aplikace a databáze.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: cc2ee50cd887a14fba2141bee13d65e777c16232
ms.sourcegitcommit: 4b00e77f9984ce76356e829cfe7f75f0f61a7a8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70145751"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="7b105-103">Kontroly stavu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b105-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="7b105-104">Od [Luke Latham](https://github.com/guardrex) a [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="7b105-104">By [Luke Latham](https://github.com/guardrex) and [Glenn Condron](https://github.com/glennc)</span></span>

<span data-ttu-id="7b105-105">ASP.NET Core nabízí middleware pro kontrolu stavu a knihovny pro vytváření sestav stavu součástí infrastruktury aplikací.</span><span class="sxs-lookup"><span data-stu-id="7b105-105">ASP.NET Core offers Health Check Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="7b105-106">Kontroly stavu jsou zpřístupněné aplikací jako koncové body HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b105-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="7b105-107">Koncové body kontroly stavu je možné nakonfigurovat pro nejrůznější scénáře monitorování v reálném čase:</span><span class="sxs-lookup"><span data-stu-id="7b105-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="7b105-108">Sondy stavu můžou použít orchestrace kontejnerů a nástroje pro vyrovnávání zatížení ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="7b105-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="7b105-109">Například produkt Orchestrator může reagovat na selhání kontroly stavu zastavením zavedení nasazení nebo restartováním kontejneru.</span><span class="sxs-lookup"><span data-stu-id="7b105-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="7b105-110">Nástroj pro vyrovnávání zatížení může reagovat na poškozenou aplikaci směrováním provozu mimo instanci selhání do stavu v pořádku.</span><span class="sxs-lookup"><span data-stu-id="7b105-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="7b105-111">Použití paměti, disku a dalších prostředků fyzického serveru se dá monitorovat v dobrém stavu.</span><span class="sxs-lookup"><span data-stu-id="7b105-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="7b105-112">Kontroly stavu můžou testovat závislosti aplikace, jako jsou databáze a externí koncové body služby, aby se potvrdila dostupnost a normální fungování.</span><span class="sxs-lookup"><span data-stu-id="7b105-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="7b105-113">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7b105-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7b105-114">Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="7b105-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="7b105-115">Pokud chcete pro daný scénář spustit ukázkovou aplikaci, použijte příkaz [dotnet Run](/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="7b105-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="7b105-116">Podrobnosti o tom, jak používat ukázkovou aplikaci, najdete v souboru *Readme.MD* ukázkové aplikace a v popisech scénářů v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="7b105-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7b105-117">Požadavky</span><span class="sxs-lookup"><span data-stu-id="7b105-117">Prerequisites</span></span>

<span data-ttu-id="7b105-118">Kontroly stavu se obvykle používají s externí službou monitorování nebo nástrojem Orchestrator Container Service ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="7b105-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="7b105-119">Před přidáním kontrol stavu do aplikace se rozhodněte, který monitorovací systém se má použít.</span><span class="sxs-lookup"><span data-stu-id="7b105-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="7b105-120">Monitorovací systém vyhodnotí, jaké typy kontrol stavu se mají vytvořit a jak nakonfigurovat jejich koncové body.</span><span class="sxs-lookup"><span data-stu-id="7b105-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="7b105-121">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) .</span><span class="sxs-lookup"><span data-stu-id="7b105-121">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="7b105-122">Ukázková aplikace poskytuje spouštěcí kód k předvedení kontrol stavu pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="7b105-122">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="7b105-123">Scénář [testu databáze](#database-probe) kontroluje stav připojení k databázi pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="7b105-123">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="7b105-124">Scénář [testu DbContext](#entity-framework-core-dbcontext-probe) zkontroluje databázi pomocí EF Core `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="7b105-124">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="7b105-125">K prozkoumání scénářů databáze se jedná o ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="7b105-125">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="7b105-126">Vytvoří databázi a poskytne připojovací řetězec v souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="7b105-126">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="7b105-127">Obsahuje následující odkazy na balíček v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="7b105-127">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="7b105-128">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="7b105-128">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="7b105-129">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="7b105-129">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="7b105-130">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) je portem [BeatPulse](https://github.com/xabaril/beatpulse) , který společnost Microsoft neudržuje ani nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="7b105-130">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) is a port of [BeatPulse](https://github.com/xabaril/beatpulse) and isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="7b105-131">Další scénář kontroly stavu ukazuje, jak filtrovat kontroly stavu na port pro správu.</span><span class="sxs-lookup"><span data-stu-id="7b105-131">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="7b105-132">Ukázková aplikace vyžaduje, abyste vytvořili soubor *Properties/launchSettings. JSON* , který obsahuje adresu URL pro správu a port pro správu.</span><span class="sxs-lookup"><span data-stu-id="7b105-132">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="7b105-133">Další informace naleznete v části [Filter by port](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="7b105-133">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="7b105-134">Základní sonda stavu</span><span class="sxs-lookup"><span data-stu-id="7b105-134">Basic health probe</span></span>

<span data-ttu-id="7b105-135">U mnoha aplikací je základní konfigurace sondy stavu, která oznamuje dostupnost aplikace pro zpracování požadavků (*živý*), ke zjištění stavu aplikace dostačující.</span><span class="sxs-lookup"><span data-stu-id="7b105-135">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="7b105-136">Základní konfigurace registruje služby kontroly stavu a volá middleware kontroly stavu, aby reagoval na koncový bod adresy URL s odpovědí na stav.</span><span class="sxs-lookup"><span data-stu-id="7b105-136">The basic configuration registers health check services and calls the Health Check Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="7b105-137">Ve výchozím nastavení nejsou k otestování konkrétní závislosti nebo subsystému registrovány žádné konkrétní kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="7b105-137">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="7b105-138">Aplikace se považuje za v pořádku, pokud dokáže reagovat na adresu URL koncového bodu stavu.</span><span class="sxs-lookup"><span data-stu-id="7b105-138">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="7b105-139">Výchozí<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>zapisovač odpovědí zapisuje stav () jako odpověď ve formátu prostého textu zpátky klientovi, což značí buď [funkčnosti. dobrý](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)stav, [funkčnosti. Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status není v pořádku.</span><span class="sxs-lookup"><span data-stu-id="7b105-139">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="7b105-140">Registrovat služby kontroly stavu v <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> nástroji `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="7b105-140">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7b105-141">Přidejte middleware pro <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> kontrolu stavu v `Startup.Configure`kanálu zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="7b105-141">Add Health Check Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="7b105-142">V ukázkové aplikaci se koncový bod kontroly stavu vytvoří `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="7b105-142">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/BasicStartup.cs?name=snippet1&highlight=5,10)]

<span data-ttu-id="7b105-143">Pokud chcete spustit scénář základní konfigurace pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="7b105-143">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="7b105-144">Ukázka Docker</span><span class="sxs-lookup"><span data-stu-id="7b105-144">Docker example</span></span>

<span data-ttu-id="7b105-145">[Docker](xref:host-and-deploy/docker/index) nabízí vestavěnou `HEALTHCHECK` direktivu, která se dá použít ke kontrole stavu aplikace, která používá konfiguraci základní kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="7b105-145">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="7b105-146">Vytvořit kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="7b105-146">Create health checks</span></span>

<span data-ttu-id="7b105-147">Kontroly stavu jsou vytvářeny implementací <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="7b105-147">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="7b105-148"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> Metoda `Healthy` vrátíhodnotu,která`Degraded`označuje stav jako, nebo. `Unhealthy` `>` `Task<` <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult></span><span class="sxs-lookup"><span data-stu-id="7b105-148">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a `Task<` <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> `>` that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="7b105-149">Výsledek je zapsán jako odpověď v podobě prostého textu s konfigurovatelným stavovým kódem (konfigurace je popsána v části [Možnosti kontroly stavu](#health-check-options) ).</span><span class="sxs-lookup"><span data-stu-id="7b105-149">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="7b105-150"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>může také vracet volitelné páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="7b105-150"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="7b105-151">Příklad kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="7b105-151">Example health check</span></span>

<span data-ttu-id="7b105-152">Následující `ExampleHealthCheck` třída demonstruje rozložení kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="7b105-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check:</span></span>

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

### <a name="register-health-check-services"></a><span data-ttu-id="7b105-153">Registrovat služby kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="7b105-153">Register health check services</span></span>

<span data-ttu-id="7b105-154">Typ se přidá do služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>: `ExampleHealthCheck`</span><span class="sxs-lookup"><span data-stu-id="7b105-154">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck<ExampleHealthCheck>("example_health_check");
}
```

<span data-ttu-id="7b105-155">Přetížení znázorněné v následujícím příkladu nastaví stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) na hodnotu ohlásit, když při kontrole stavu nahlásí chybu. <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*></span><span class="sxs-lookup"><span data-stu-id="7b105-155">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="7b105-156">Pokud je stav selhání nastavený na `null` (výchozí), funkčnosti se hlásí jako [chybné](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .</span><span class="sxs-lookup"><span data-stu-id="7b105-156">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="7b105-157">Toto přetížení je užitečným scénářem pro autory knihoven, kde stav selhání uvedený v knihovně vynutila aplikace, když dojde k selhání kontroly stavu, pokud se při implementaci kontroly stavu splní nastavení.</span><span class="sxs-lookup"><span data-stu-id="7b105-157">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="7b105-158">*Značky* lze použít k filtrování kontrol stavu (popsaných dále v oddílu [Filtr kontrol stavu](#filter-health-checks) ).</span><span class="sxs-lookup"><span data-stu-id="7b105-158">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="7b105-159"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>lze také spustit funkci lambda.</span><span class="sxs-lookup"><span data-stu-id="7b105-159"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="7b105-160">V následujícím příkladu je název kontroly stavu zadán jako `Example` a zaškrtávací políčka vždy vrátí dobrý stav:</span><span class="sxs-lookup"><span data-stu-id="7b105-160">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Example", () =>
            HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" })
}
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="7b105-161">Použití middlewaru pro kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="7b105-161">Use Health Checks Middleware</span></span>

<span data-ttu-id="7b105-162">V `Startup.Configure`aplikaci volejte <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> kanál zpracování s adresou URL koncového bodu nebo relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="7b105-162">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health");
}
```

<span data-ttu-id="7b105-163">Pokud by kontroly stavu měly naslouchat na konkrétním portu, použijte přetížení <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> pro nastavení portu (popsáno dále v části [Filter by port](#filter-by-port) ):</span><span class="sxs-lookup"><span data-stu-id="7b105-163">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

<span data-ttu-id="7b105-164">Middleware pro kontrolu stavu je middleware *terminálu* v kanálu zpracování požadavků aplikace.</span><span class="sxs-lookup"><span data-stu-id="7b105-164">Health Checks Middleware is a *terminal middleware* in the app's request processing pipeline.</span></span> <span data-ttu-id="7b105-165">První koncový bod kontroly stavu, který se přesně shoduje s adresou URL požadavku, je spuštěný a má krátké okruhy ve zbývající části kanálu middlewaru.</span><span class="sxs-lookup"><span data-stu-id="7b105-165">The first health check endpoint encountered that's an exact match to the request URL executes and short-circuits the rest of the middleware pipeline.</span></span> <span data-ttu-id="7b105-166">Při krátkém okruhu se neprovede žádné middleware, které následují po odpovídající kontrole stavu.</span><span class="sxs-lookup"><span data-stu-id="7b105-166">When short-circuiting occurs, no middleware following the matched health check executes.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="7b105-167">Možnosti kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="7b105-167">Health check options</span></span>

<span data-ttu-id="7b105-168"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>Poskytněte možnost přizpůsobení chování kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="7b105-168"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="7b105-169">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="7b105-169">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="7b105-170">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="7b105-170">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="7b105-171">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="7b105-171">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="7b105-172">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="7b105-172">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="7b105-173">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="7b105-173">Filter health checks</span></span>

<span data-ttu-id="7b105-174">Ve výchozím nastavení middleware pro kontrolu stavu spouští všechny zaregistrované kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="7b105-174">By default, Health Check Middleware runs all registered health checks.</span></span> <span data-ttu-id="7b105-175">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která vrací logickou <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> hodnotu pro možnost.</span><span class="sxs-lookup"><span data-stu-id="7b105-175">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="7b105-176">V `Bar` následujícím příkladu je kontrole stavu Filtrováno podle značky ( `true` `bar_tag`) v podmíněném příkazu funkce, kde <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> je vrácena pouze v případě, že odpovídá `foo_tag` vlastnost kontroly stavu nebo `baz_tag`:</span><span class="sxs-lookup"><span data-stu-id="7b105-176">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="7b105-177">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="7b105-177">Customize the HTTP status code</span></span>

<span data-ttu-id="7b105-178">Slouží <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> k přizpůsobení mapování stavu na stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="7b105-178">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="7b105-179">Následující <xref:Microsoft.AspNetCore.Http.StatusCodes> přiřazení jsou výchozí hodnoty používané middlewarem.</span><span class="sxs-lookup"><span data-stu-id="7b105-179">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="7b105-180">Změňte hodnoty stavového kódu tak, aby splňovaly vaše požadavky.</span><span class="sxs-lookup"><span data-stu-id="7b105-180">Change the status code values to meet your requirements.</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="7b105-181">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="7b105-181">Suppress cache headers</span></span>

<span data-ttu-id="7b105-182"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Určuje, jestli middleware pro kontrolu stavu přidá hlavičky HTTP do odezvy testu, aby nedocházelo k ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7b105-182"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Check Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="7b105-183">Pokud je `false` hodnota (výchozí), middleware nastaví nebo `Cache-Control`přepíše hlavičky, `Expires`a `Pragma` , aby nedocházelo k ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="7b105-183">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="7b105-184">Pokud je `true`hodnota, middleware neupraví hlavičky mezipaměti odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7b105-184">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

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

### <a name="customize-output"></a><span data-ttu-id="7b105-185">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="7b105-185">Customize output</span></span>

<span data-ttu-id="7b105-186"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> Možnost Získá nebo nastaví delegáta použitý k zápisu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="7b105-186">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="7b105-187">Výchozí delegát zapisuje minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="7b105-187">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

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

## <a name="database-probe"></a><span data-ttu-id="7b105-188">Test databáze</span><span class="sxs-lookup"><span data-stu-id="7b105-188">Database probe</span></span>

<span data-ttu-id="7b105-189">Kontrola stavu může určit databázový dotaz, který se má spustit jako logický test, aby označoval, zda databáze obvykle reaguje.</span><span class="sxs-lookup"><span data-stu-id="7b105-189">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="7b105-190">Ukázková aplikace pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), knihovny kontroly stavu pro aplikace ASP.NET Core, provede kontrolu stavu databáze SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7b105-190">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="7b105-191">`AspNetCore.Diagnostics.HealthChecks``SELECT 1` spustí dotaz na databázi, aby se ověřilo, že připojení k databázi je v pořádku.</span><span class="sxs-lookup"><span data-stu-id="7b105-191">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="7b105-192">Při kontrole připojení k databázi pomocí dotazu vyberte dotaz, který se rychle vrátí.</span><span class="sxs-lookup"><span data-stu-id="7b105-192">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="7b105-193">Přístup k dotazům spouští riziko přetížení databáze a snížení výkonu.</span><span class="sxs-lookup"><span data-stu-id="7b105-193">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="7b105-194">Ve většině případů není nutné spustit testovací dotaz.</span><span class="sxs-lookup"><span data-stu-id="7b105-194">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="7b105-195">Stačí pouze provést úspěšné připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="7b105-195">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="7b105-196">Pokud zjistíte, že je nutné spustit dotaz, zvolte jednoduchý dotaz SELECT, například `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="7b105-196">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="7b105-197">Zahrňte odkaz na balíček do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="7b105-197">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="7b105-198">Zadejte platný připojovací řetězec databáze do souboru *appSettings. JSON* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="7b105-198">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="7b105-199">Aplikace používá SQL Server databázi s názvem `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="7b105-199">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="7b105-200">Registrovat služby kontroly stavu v <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> nástroji `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="7b105-200">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7b105-201">Ukázková aplikace volá `AddSqlServer` metodu s připojovacím řetězcem databáze (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="7b105-201">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7b105-202">Zavolá middleware pro kontrolu stavu v kanálu zpracování `Startup.Configure`aplikace v:</span><span class="sxs-lookup"><span data-stu-id="7b105-202">Call Health Check Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_Configure)]

<span data-ttu-id="7b105-203">Pokud chcete spustit scénář testu databáze pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="7b105-203">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="7b105-204">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) je portem [BeatPulse](https://github.com/xabaril/beatpulse) , který společnost Microsoft neudržuje ani nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="7b105-204">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) is a port of [BeatPulse](https://github.com/xabaril/beatpulse) and isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="7b105-205">Test Entity Framework Core DbContext</span><span class="sxs-lookup"><span data-stu-id="7b105-205">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="7b105-206">Tato `DbContext` kontrolu potvrdí, že aplikace může komunikovat s databází nakonfigurovanou pro EF Core `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="7b105-206">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="7b105-207">Tato `DbContext` kontrolu je podporovaná v aplikacích, které:</span><span class="sxs-lookup"><span data-stu-id="7b105-207">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="7b105-208">Použijte [jádro Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="7b105-208">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="7b105-209">Zahrňte odkaz na balíček [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="7b105-209">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="7b105-210">`AddDbContextCheck<TContext>`zaregistruje kontrolu stavu pro `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="7b105-210">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="7b105-211">`DbContext` Je dodán`TContext` jako metoda metodě.</span><span class="sxs-lookup"><span data-stu-id="7b105-211">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="7b105-212">K dispozici je přetížení pro konfiguraci stavu selhání, značek a vlastního testovacího dotazu.</span><span class="sxs-lookup"><span data-stu-id="7b105-212">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="7b105-213">Ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="7b105-213">By default:</span></span>

* <span data-ttu-id="7b105-214">`DbContextHealthCheck` Volá metodu`CanConnectAsync` EF Core.</span><span class="sxs-lookup"><span data-stu-id="7b105-214">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="7b105-215">Můžete přizpůsobit, která operace je spuštěna při kontrole stavu pomocí `AddDbContextCheck` přetížení metod.</span><span class="sxs-lookup"><span data-stu-id="7b105-215">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="7b105-216">Název kontroly stavu je název `TContext` typu.</span><span class="sxs-lookup"><span data-stu-id="7b105-216">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="7b105-217">V ukázkové aplikaci `AppDbContext` je k `AddDbContextCheck` dispozici a zaregistrováno jako služba v `Startup.ConfigureServices`nástroji.</span><span class="sxs-lookup"><span data-stu-id="7b105-217">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="7b105-218">*DbContextHealthStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7b105-218">*DbContextHealthStartup.cs*:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7b105-219">V ukázkové aplikaci `UseHealthChecks` přidá middleware pro kontrolu stavu v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="7b105-219">In the sample app, `UseHealthChecks` adds the Health Check Middleware in `Startup.Configure`.</span></span>

<span data-ttu-id="7b105-220">*DbContextHealthStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7b105-220">*DbContextHealthStartup.cs*:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_Configure)]

<span data-ttu-id="7b105-221">Pokud chcete spustit `DbContext` scénář sondy pomocí ukázkové aplikace, zkontrolujte, že databáze určená připojovacím řetězcem v instanci SQL Server neexistuje.</span><span class="sxs-lookup"><span data-stu-id="7b105-221">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="7b105-222">Pokud databáze existuje, odstraňte ji.</span><span class="sxs-lookup"><span data-stu-id="7b105-222">If the database exists, delete it.</span></span>

<span data-ttu-id="7b105-223">Ze složky projektu v příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="7b105-223">Execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario dbcontext
```

<span data-ttu-id="7b105-224">Po spuštění aplikace ověřte stav tak, že v prohlížeči vytvoříte požadavek na `/health` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="7b105-224">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="7b105-225">Databáze a `AppDbContext` neexistuje, takže aplikace nabízí následující odpověď:</span><span class="sxs-lookup"><span data-stu-id="7b105-225">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="7b105-226">Aktivujte ukázkovou aplikaci pro vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="7b105-226">Trigger the sample app to create the database.</span></span> <span data-ttu-id="7b105-227">Vytvořte žádost na `/createdatabase`.</span><span class="sxs-lookup"><span data-stu-id="7b105-227">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="7b105-228">Aplikace odpoví:</span><span class="sxs-lookup"><span data-stu-id="7b105-228">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="7b105-229">Vytvořte požadavek na `/health` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="7b105-229">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="7b105-230">Databáze a kontext existují, takže aplikace reaguje:</span><span class="sxs-lookup"><span data-stu-id="7b105-230">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="7b105-231">Aktivujte ukázkovou aplikaci, aby se odstranila databáze.</span><span class="sxs-lookup"><span data-stu-id="7b105-231">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="7b105-232">Vytvořte žádost na `/deletedatabase`.</span><span class="sxs-lookup"><span data-stu-id="7b105-232">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="7b105-233">Aplikace odpoví:</span><span class="sxs-lookup"><span data-stu-id="7b105-233">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="7b105-234">Vytvořte požadavek na `/health` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="7b105-234">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="7b105-235">Aplikace poskytuje poškozenou reakci:</span><span class="sxs-lookup"><span data-stu-id="7b105-235">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="7b105-236">Samostatné sondy pro připravenost a živý provoz</span><span class="sxs-lookup"><span data-stu-id="7b105-236">Separate readiness and liveness probes</span></span>

<span data-ttu-id="7b105-237">V některých scénářích hostování se používá pár kontrol stavu, který rozlišuje dva stavy aplikace:</span><span class="sxs-lookup"><span data-stu-id="7b105-237">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="7b105-238">Aplikace funguje, ale ještě není připravená na příjem požadavků.</span><span class="sxs-lookup"><span data-stu-id="7b105-238">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="7b105-239">Tento stav je *připravenost*aplikace.</span><span class="sxs-lookup"><span data-stu-id="7b105-239">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="7b105-240">Aplikace funguje a reaguje na požadavky.</span><span class="sxs-lookup"><span data-stu-id="7b105-240">The app is functioning and responding to requests.</span></span> <span data-ttu-id="7b105-241">Tento stav je živými aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="7b105-241">This state is the app's *liveness*.</span></span>

<span data-ttu-id="7b105-242">Kontrola připravenosti obvykle provádí rozsáhlejší a časově náročnou sadu kontrol, aby bylo možné zjistit, zda jsou k dispozici všechny subsystémy a prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="7b105-242">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="7b105-243">K dispozici je pouze rychlá kontrolu, která určuje, zda je aplikace k dispozici pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="7b105-243">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="7b105-244">Po úspěšném provedení kontroly připravenosti aplikace nemusíte aplikaci navýšit dál s nákladnými sadami kontrol&mdash;připravenosti, které vyžadují kontrolu živého ověření.</span><span class="sxs-lookup"><span data-stu-id="7b105-244">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="7b105-245">Ukázková aplikace obsahuje kontrolu stavu, která oznamuje dokončení dlouhotrvající úlohy po spuštění v [hostované službě](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="7b105-245">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="7b105-246">Zpřístupňuje `true` vlastnost, která hostovaná služba může nastavit na hodnotu po dokončení její dlouhotrvající úlohy (StartupHostedServiceHealthCheck.cs): `StartupTaskCompleted` `StartupHostedServiceHealthCheck`</span><span class="sxs-lookup"><span data-stu-id="7b105-246">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="7b105-247">Dlouhotrvající úloha na pozadí se spouští v [hostované službě](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="7b105-247">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="7b105-248">Při uzavírání úkolu `StartupHostedServiceHealthCheck.StartupTaskCompleted` je nastaveno na `true`:</span><span class="sxs-lookup"><span data-stu-id="7b105-248">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="7b105-249">Kontroly stavu jsou registrovány <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> ve `Startup.ConfigureServices` službě společně s hostovanou službou.</span><span class="sxs-lookup"><span data-stu-id="7b105-249">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="7b105-250">Vzhledem k tomu, že hostovaná služba musí nastavit vlastnost pro kontrolu stavu, je tato kontrolu také zaregistrována v kontejneru služby (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="7b105-250">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7b105-251">V kanálu pro zpracování aplikací v `Startup.Configure`se zavolá middleware pro kontrolu stavu.</span><span class="sxs-lookup"><span data-stu-id="7b105-251">Call Health Check Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="7b105-252">V ukázkové aplikaci jsou koncové body kontroly stavu vytvořeny v `/health/ready` pro kontrolu připravenosti a `/health/live` pro kontrolu živého provozu.</span><span class="sxs-lookup"><span data-stu-id="7b105-252">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="7b105-253">Kontrola připravenosti filtruje kontrolu stavu na kontrolu stavu se `ready` značkou.</span><span class="sxs-lookup"><span data-stu-id="7b105-253">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="7b105-254">Kontrola živého odfiltruje vrácení `StartupHostedServiceHealthCheck` `false` se změnami v [HealthCheckOptions. predikátu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Další informace najdete v tématu [filtrování kontrol stavu](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="7b105-254">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_Configure)]

<span data-ttu-id="7b105-255">Pokud chcete spustit scénář konfigurace připravenosti/živých aplikací pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="7b105-255">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario liveness
```

<span data-ttu-id="7b105-256">V prohlížeči navštivte `/health/ready` několikrát, dokud neuplyne 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="7b105-256">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="7b105-257">Zprávy o kontrole stavu nejsou v *pořádku* po dobu prvních 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="7b105-257">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="7b105-258">Po 15 sekundách koncový bod hlásí stav *v pořádku*, který odráží dokončení dlouhotrvající úlohy hostované službou.</span><span class="sxs-lookup"><span data-stu-id="7b105-258">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="7b105-259">V tomto příkladu se vytvoří také Vydavatel kontroly stavu<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> (implementace), který spouští první kontrolu připravenosti se dvěma sekundami.</span><span class="sxs-lookup"><span data-stu-id="7b105-259">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="7b105-260">Další informace najdete v části [Vydavatel kontroly stavu](#health-check-publisher) .</span><span class="sxs-lookup"><span data-stu-id="7b105-260">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="7b105-261">Příklad Kubernetes</span><span class="sxs-lookup"><span data-stu-id="7b105-261">Kubernetes example</span></span>

<span data-ttu-id="7b105-262">Používání samostatné kontroly připravenosti a živých kontrol je užitečné v prostředí, jako je [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="7b105-262">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="7b105-263">V Kubernetes může být aplikace nutná k provádění časově náročné práce při spuštění před přijetím požadavků, jako je například test dostupnosti podkladové databáze.</span><span class="sxs-lookup"><span data-stu-id="7b105-263">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="7b105-264">Použití samostatných kontrol umožňuje nástroji Orchestrator rozlišit, jestli aplikace funguje, ale ještě není připravená, nebo jestli se aplikace nedala spustit.</span><span class="sxs-lookup"><span data-stu-id="7b105-264">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="7b105-265">Další informace o testování připravenosti a provozní sondy v Kubernetes najdete v tématu [Konfigurace živých a připravených sond](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) v dokumentaci k Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="7b105-265">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="7b105-266">Následující příklad ukazuje konfiguraci testu připravenosti na Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="7b105-266">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="7b105-267">Test založený na metrikách s vlastním zapisovačem odpovědí</span><span class="sxs-lookup"><span data-stu-id="7b105-267">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="7b105-268">Ukázková aplikace ukazuje kontrolu stavu paměti pomocí vlastního zapisovače odpovědí.</span><span class="sxs-lookup"><span data-stu-id="7b105-268">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="7b105-269">`MemoryHealthCheck`hlásí stav není v pořádku, pokud aplikace používá více než danou prahovou hodnotu paměti (1 GB v ukázkové aplikaci).</span><span class="sxs-lookup"><span data-stu-id="7b105-269">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="7b105-270">Zahrnuje informace o uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs):* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult></span><span class="sxs-lookup"><span data-stu-id="7b105-270">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="7b105-271">Registrovat služby kontroly stavu v <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> nástroji `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="7b105-271">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7b105-272">Místo povolení kontroly stavu předáním <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `MemoryHealthCheck` jako služby je zaregistrována jako služba.</span><span class="sxs-lookup"><span data-stu-id="7b105-272">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="7b105-273">Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrované služby jsou k dispozici pro služby kontroly stavu a middleware.</span><span class="sxs-lookup"><span data-stu-id="7b105-273">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="7b105-274">Službu pro kontrolu stavu doporučujeme zaregistrovat jako služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="7b105-274">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="7b105-275">*CustomWriterStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7b105-275">*CustomWriterStartup.cs*:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="7b105-276">V kanálu pro zpracování aplikací v `Startup.Configure`se zavolá middleware pro kontrolu stavu.</span><span class="sxs-lookup"><span data-stu-id="7b105-276">Call Health Check Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="7b105-277">`WriteResponse` Delegát poskytne`ResponseWriter` vlastnost pro výstup vlastní odpovědi JSON, když se spustí kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="7b105-277">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_Configure&highlight=6)]

<span data-ttu-id="7b105-278">`WriteResponse` Metoda zformátujeobjektdoobjektuJSONaposkytnevýstupJSON`CompositeHealthCheckResult` pro odpověď kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="7b105-278">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="7b105-279">Pokud chcete spustit test založený na metrikách s vlastním výstupem zapisovače odpovědí pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="7b105-279">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="7b105-280">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje scénáře kontroly stavu založené na metrikách, včetně úložného prostoru na disku a maximální hodnoty živé kontroly.</span><span class="sxs-lookup"><span data-stu-id="7b105-280">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="7b105-281">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) je portem [BeatPulse](https://github.com/xabaril/beatpulse) , který společnost Microsoft neudržuje ani nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="7b105-281">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) is a port of [BeatPulse](https://github.com/xabaril/beatpulse) and isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="7b105-282">Filtrovat podle portu</span><span class="sxs-lookup"><span data-stu-id="7b105-282">Filter by port</span></span>

<span data-ttu-id="7b105-283">Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> s portem omezí požadavky na kontrolu stavu na zadaný port.</span><span class="sxs-lookup"><span data-stu-id="7b105-283">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="7b105-284">Obvykle se používá v prostředí kontejneru k vystavení portu pro monitorovací služby.</span><span class="sxs-lookup"><span data-stu-id="7b105-284">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="7b105-285">Ukázková aplikace nakonfiguruje port pomocí [poskytovatele konfigurace proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7b105-285">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="7b105-286">Port je nastaven v souboru *launchSettings. JSON* a předán poskytovateli konfigurace prostřednictvím proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="7b105-286">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="7b105-287">Také je nutné nakonfigurovat server tak, aby naslouchal žádosti na portu pro správu.</span><span class="sxs-lookup"><span data-stu-id="7b105-287">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="7b105-288">Pokud chcete ukázkovou aplikaci použít k předvedení konfigurace portů pro správu, vytvořte soubor *launchSettings. JSON* ve složce *Properties (vlastnosti* ).</span><span class="sxs-lookup"><span data-stu-id="7b105-288">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="7b105-289">Následující soubor *launchSettings. JSON* není zahrnutý v souborech projektu ukázkové aplikace a je nutné ho vytvořit ručně.</span><span class="sxs-lookup"><span data-stu-id="7b105-289">The following *launchSettings.json* file isn't included in the sample app's project files and must be created manually.</span></span>

<span data-ttu-id="7b105-290">*Properties/launchSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="7b105-290">*Properties/launchSettings.json*:</span></span>

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

<span data-ttu-id="7b105-291">Registrovat služby kontroly stavu v <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> nástroji `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="7b105-291">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7b105-292">Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> Určuje port pro správu (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="7b105-292">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=12,18)]

> [!NOTE]
> <span data-ttu-id="7b105-293">Vytvoření souboru *launchSettings. JSON* v ukázkové aplikaci se vyhnete tak, že v kódu nastavíte adresy URL a port pro správu explicitně.</span><span class="sxs-lookup"><span data-stu-id="7b105-293">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="7b105-294">V *program.cs* , kde <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> je vytvořena, přidejte volání do <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> a poskytněte normální koncový bod odpovědi aplikace a koncový bod portu pro správu.</span><span class="sxs-lookup"><span data-stu-id="7b105-294">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="7b105-295">V *ManagementPortStartup.cs* , <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> kde je volána, určete explicitně port pro správu.</span><span class="sxs-lookup"><span data-stu-id="7b105-295">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="7b105-296">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7b105-296">*Program.cs*:</span></span>
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
> <span data-ttu-id="7b105-297">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7b105-297">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="7b105-298">Pokud chcete spustit scénář konfigurace portu pro správu pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="7b105-298">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```console
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="7b105-299">Distribuce knihovny kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="7b105-299">Distribute a health check library</span></span>

<span data-ttu-id="7b105-300">Postup při distribuci kontroly stavu jako knihovny:</span><span class="sxs-lookup"><span data-stu-id="7b105-300">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="7b105-301">Zapište kontrolu stavu, která implementuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní jako samostatnou třídu.</span><span class="sxs-lookup"><span data-stu-id="7b105-301">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="7b105-302">Třída může spoléhat na [vkládání závislostí (di)](xref:fundamentals/dependency-injection), aktivovat typ a [pojmenované možnosti](xref:fundamentals/configuration/options) pro přístup k datům konfigurace.</span><span class="sxs-lookup"><span data-stu-id="7b105-302">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

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

1. <span data-ttu-id="7b105-303">Zapište metodu rozšíření s parametry, které aplikace spotřebovávají ve své `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="7b105-303">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="7b105-304">V následujícím příkladu předpokládejme následující signatura metody kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="7b105-304">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="7b105-305">Předchozí signatura indikuje, že `ExampleHealthCheck` pro zpracování logiky sondy kontroly stavu vyžaduje další data.</span><span class="sxs-lookup"><span data-stu-id="7b105-305">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="7b105-306">Data jsou k dispozici delegátovi použitému k vytvoření instance kontroly stavu při registraci kontroly stavu s metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="7b105-306">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="7b105-307">V následujícím příkladu volající určuje nepovinné:</span><span class="sxs-lookup"><span data-stu-id="7b105-307">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="7b105-308">název kontroly stavu (`name`).</span><span class="sxs-lookup"><span data-stu-id="7b105-308">health check name (`name`).</span></span> <span data-ttu-id="7b105-309">Pokud `null`sepoužívá. `example_health_check`</span><span class="sxs-lookup"><span data-stu-id="7b105-309">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="7b105-310">datový bod řetězce pro kontrolu stavu (`data1`).</span><span class="sxs-lookup"><span data-stu-id="7b105-310">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="7b105-311">celočíselný datový bod pro kontrolu stavu (`data2`).</span><span class="sxs-lookup"><span data-stu-id="7b105-311">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="7b105-312">Pokud `null`sepoužívá. `1`</span><span class="sxs-lookup"><span data-stu-id="7b105-312">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="7b105-313">stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="7b105-313">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="7b105-314">Výchozí hodnota je `null`.</span><span class="sxs-lookup"><span data-stu-id="7b105-314">The default is `null`.</span></span> <span data-ttu-id="7b105-315">Pokud `null`je [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) není v pořádku, je hlášen stav selhání.</span><span class="sxs-lookup"><span data-stu-id="7b105-315">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="7b105-316">značky (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="7b105-316">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="7b105-317">Vydavatel kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="7b105-317">Health Check Publisher</span></span>

<span data-ttu-id="7b105-318">Když se přidá do kontejneru služby, systém kontroly stavu pravidelně spouští kontroly stavu a volání `PublishAsync` s výsledkem. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher></span><span class="sxs-lookup"><span data-stu-id="7b105-318">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="7b105-319">To je užitečné ve scénáři sledování stavu založeném na nabízených oznámeních, který očekává, že každý proces bude pravidelně volat monitorovací systém, aby se zjistil stav.</span><span class="sxs-lookup"><span data-stu-id="7b105-319">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="7b105-320"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Rozhraní má jedinou metodu:</span><span class="sxs-lookup"><span data-stu-id="7b105-320">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="7b105-321"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>umožňuje nastavit:</span><span class="sxs-lookup"><span data-stu-id="7b105-321"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="7b105-322"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>Počáteční zpoždění použité po spuštění aplikace před spuštěním <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instancí. &ndash;</span><span class="sxs-lookup"><span data-stu-id="7b105-322"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="7b105-323">Zpoždění se použije jednou při spuštění a nevztahuje se na další iterace.</span><span class="sxs-lookup"><span data-stu-id="7b105-323">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="7b105-324">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="7b105-324">The default value is five seconds.</span></span>
* <span data-ttu-id="7b105-325"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; Doba spuštění.<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher></span><span class="sxs-lookup"><span data-stu-id="7b105-325"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="7b105-326">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="7b105-326">The default value is 30 seconds.</span></span>
* <span data-ttu-id="7b105-327"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; Pokud je(`null` výchozí), spustí služba Vydavatel kontroly stavu všechny zaregistrované kontroly stavu. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate></span><span class="sxs-lookup"><span data-stu-id="7b105-327"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="7b105-328">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která filtruje sadu kontrol.</span><span class="sxs-lookup"><span data-stu-id="7b105-328">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="7b105-329">Predikát je vyhodnocen každou periodu.</span><span class="sxs-lookup"><span data-stu-id="7b105-329">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="7b105-330"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>Časový limit pro spuštění kontrol stavu pro všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance. &ndash;</span><span class="sxs-lookup"><span data-stu-id="7b105-330"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="7b105-331">Použijte <xref:System.Threading.Timeout.InfiniteTimeSpan> k provedení bez časového limitu.</span><span class="sxs-lookup"><span data-stu-id="7b105-331">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="7b105-332">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="7b105-332">The default value is 30 seconds.</span></span>

::: moniker range="= aspnetcore-2.2"

> [!WARNING]
> <span data-ttu-id="7b105-333">Ve verzi ASP.NET Core 2,2 není nastavení <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> dodrženo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementací. nastavuje hodnotu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span><span class="sxs-lookup"><span data-stu-id="7b105-333">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="7b105-334">Tento problém bude opravený v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="7b105-334">This issue will be fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="7b105-335">Další informace naleznete v tématu [HealthCheckPublisherOptions. period nastaví hodnotu. Zpoždění](https://github.com/aspnet/Extensions/issues/1041).</span><span class="sxs-lookup"><span data-stu-id="7b105-335">For more information, see [HealthCheckPublisherOptions.Period sets the value of .Delay](https://github.com/aspnet/Extensions/issues/1041).</span></span>

::: moniker-end

<span data-ttu-id="7b105-336">V ukázkové aplikaci `ReadinessPublisher` <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> je implementace.</span><span class="sxs-lookup"><span data-stu-id="7b105-336">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="7b105-337">Stav kontroly stavu se zaznamenává `Entries` a protokoluje pro každou kontrolu:</span><span class="sxs-lookup"><span data-stu-id="7b105-337">The health check status is recorded in `Entries` and logged for each check:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=20,22-23)]

<span data-ttu-id="7b105-338">V `LivenessProbeStartup` příkladu`StartupHostedService` ukázkové aplikace má kontroler připravenosti dvě sekundy na spuštění a spustí kontrolu každých 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="7b105-338">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="7b105-339">Chcete-li <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> aktivovat implementaci, ukázka se `ReadinessPublisher` registruje jako služba s jedním prvkem v kontejneru [Injektáže (di) pro vkládání závislostí](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="7b105-339">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

::: moniker range="= aspnetcore-2.2"

> [!NOTE]
> <span data-ttu-id="7b105-340">Následující alternativní řešení povoluje přidání <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance do kontejneru služby, pokud již byla do aplikace přidána jedna nebo více dalších hostovaných služeb.</span><span class="sxs-lookup"><span data-stu-id="7b105-340">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="7b105-341">Toto řešení se nebude vyžadovat u vydání ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="7b105-341">This workaround won't be required with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="7b105-342">Další informace najdete v tématu: https://github.com/aspnet/Extensions/issues/639.</span><span class="sxs-lookup"><span data-stu-id="7b105-342">For more information, see: https://github.com/aspnet/Extensions/issues/639.</span></span>
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
> <span data-ttu-id="7b105-343">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="7b105-343">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="7b105-344">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) je portem [BeatPulse](https://github.com/xabaril/beatpulse) , který společnost Microsoft neudržuje ani nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="7b105-344">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) is a port of [BeatPulse](https://github.com/xabaril/beatpulse) and isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="7b105-345">Omezení kontrol stavu pomocí MapWhen</span><span class="sxs-lookup"><span data-stu-id="7b105-345">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="7b105-346">Použijte <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> k podmíněně větvení kanálu žádostí pro koncové body kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="7b105-346">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="7b105-347">V následujícím příkladu větví kanál `MapWhen` žádosti o aktivaci middlewaru kontroly stavu, pokud se `api/HealthCheck` pro koncový bod přijme požadavek GET:</span><span class="sxs-lookup"><span data-stu-id="7b105-347">In the following example, `MapWhen` branches the request pipeline to activate Health Check Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="7b105-348">Další informace naleznete v tématu <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="7b105-348">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>
