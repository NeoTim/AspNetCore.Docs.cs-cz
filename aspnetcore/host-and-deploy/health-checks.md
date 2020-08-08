---
title: Kontroly stavu v ASP.NET Core
author: rick-anderson
description: Naučte se, jak nastavit kontroly stavu pro infrastrukturu ASP.NET Core, jako jsou aplikace a databáze.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 06/22/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/health-checks
ms.openlocfilehash: a74d31107d30ce04448e30f1c53e5ac475127870
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015683"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="b122a-103">Kontroly stavu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b122a-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="b122a-104">Od [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="b122a-104">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b122a-105">ASP.NET Core nabízí kontrolu stavu middlewaru a knihovny pro vytváření sestav stavu součástí infrastruktury aplikace.</span><span class="sxs-lookup"><span data-stu-id="b122a-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="b122a-106">Kontroly stavu jsou zpřístupněné aplikací jako koncové body HTTP.</span><span class="sxs-lookup"><span data-stu-id="b122a-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="b122a-107">Koncové body kontroly stavu je možné nakonfigurovat pro nejrůznější scénáře monitorování v reálném čase:</span><span class="sxs-lookup"><span data-stu-id="b122a-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="b122a-108">Sondy stavu můžou použít orchestrace kontejnerů a nástroje pro vyrovnávání zatížení ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b122a-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="b122a-109">Například produkt Orchestrator může reagovat na selhání kontroly stavu zastavením zavedení nasazení nebo restartováním kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b122a-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="b122a-110">Nástroj pro vyrovnávání zatížení může reagovat na poškozenou aplikaci směrováním provozu mimo instanci selhání do stavu v pořádku.</span><span class="sxs-lookup"><span data-stu-id="b122a-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="b122a-111">Použití paměti, disku a dalších prostředků fyzického serveru se dá monitorovat v dobrém stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="b122a-112">Kontroly stavu můžou testovat závislosti aplikace, jako jsou databáze a externí koncové body služby, aby se potvrdila dostupnost a normální fungování.</span><span class="sxs-lookup"><span data-stu-id="b122a-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="b122a-113">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b122a-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b122a-114">Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b122a-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="b122a-115">Pokud chcete pro daný scénář spustit ukázkovou aplikaci, použijte příkaz [dotnet Run](/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="b122a-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="b122a-116">Podrobnosti o tom, jak používat ukázkovou aplikaci, najdete v souboru *Readme.MD* ukázkové aplikace a v popisech scénářů v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b122a-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b122a-117">Požadavky</span><span class="sxs-lookup"><span data-stu-id="b122a-117">Prerequisites</span></span>

<span data-ttu-id="b122a-118">Kontroly stavu se obvykle používají s externí službou monitorování nebo nástrojem Orchestrator Container Service ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b122a-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="b122a-119">Před přidáním kontrol stavu do aplikace se rozhodněte, který monitorovací systém se má použít.</span><span class="sxs-lookup"><span data-stu-id="b122a-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="b122a-120">Monitorovací systém vyhodnotí, jaké typy kontrol stavu se mají vytvořit a jak nakonfigurovat jejich koncové body.</span><span class="sxs-lookup"><span data-stu-id="b122a-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="b122a-121">Na balíček [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) se odkazuje implicitně pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="b122a-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="b122a-122">Chcete-li provádět kontroly stavu pomocí Entity Framework Core, přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="b122a-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="b122a-123">Ukázková aplikace poskytuje spouštěcí kód k předvedení kontrol stavu pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="b122a-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="b122a-124">Scénář [testu databáze](#database-probe) kontroluje stav připojení k databázi pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="b122a-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="b122a-125">Scénář [testu DbContext](#entity-framework-core-dbcontext-probe) zkontroluje databázi pomocí EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="b122a-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="b122a-126">K prozkoumání scénářů databáze se jedná o ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="b122a-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="b122a-127">Vytvoří databázi a poskytne připojovací řetězec v *appsettings.js* souboru.</span><span class="sxs-lookup"><span data-stu-id="b122a-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="b122a-128">Obsahuje následující odkazy na balíček v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="b122a-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="b122a-129">AspNetCore. HealthChecks. SqlServer</span><span class="sxs-lookup"><span data-stu-id="b122a-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="b122a-130">Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="b122a-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="b122a-131">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="b122a-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="b122a-132">Další scénář kontroly stavu ukazuje, jak filtrovat kontroly stavu na port pro správu.</span><span class="sxs-lookup"><span data-stu-id="b122a-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="b122a-133">Ukázková aplikace vyžaduje, abyste v souboru, který obsahuje adresu URL pro správu a port pro správu, vytvořili *vlastnosti nebo launchSettings.js* .</span><span class="sxs-lookup"><span data-stu-id="b122a-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="b122a-134">Další informace naleznete v části [Filter by port](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="b122a-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="b122a-135">Základní sonda stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-135">Basic health probe</span></span>

<span data-ttu-id="b122a-136">U mnoha aplikací je základní konfigurace sondy stavu, která oznamuje dostupnost aplikace pro zpracování požadavků (*živý*), ke zjištění stavu aplikace dostačující.</span><span class="sxs-lookup"><span data-stu-id="b122a-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="b122a-137">Základní konfigurace registruje služby kontroly stavu a volá middleware kontroly stavu, aby reagovala na koncový bod adresy URL s odpovědí na stav.</span><span class="sxs-lookup"><span data-stu-id="b122a-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="b122a-138">Ve výchozím nastavení nejsou k otestování konkrétní závislosti nebo subsystému registrovány žádné konkrétní kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="b122a-139">Aplikace se považuje za v pořádku, pokud dokáže reagovat na adresu URL koncového bodu stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="b122a-140">Výchozí zapisovač odpovědí zapisuje stav ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) jako odpověď ve formátu prostého textu zpátky klientovi, což značí buď [funkčnosti. dobrý](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)stav, [funkčnosti. Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status není v pořádku.</span><span class="sxs-lookup"><span data-stu-id="b122a-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="b122a-141">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b122a-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b122a-142">Vytvořte koncový bod kontroly stavu voláním `MapHealthChecks` v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b122a-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="b122a-143">V ukázkové aplikaci se koncový bod kontroly stavu vytvoří `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="b122a-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="b122a-144">Pokud chcete spustit scénář základní konfigurace pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="b122a-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="b122a-145">Ukázka Docker</span><span class="sxs-lookup"><span data-stu-id="b122a-145">Docker example</span></span>

<span data-ttu-id="b122a-146">[Docker](xref:host-and-deploy/docker/index) nabízí vestavěnou `HEALTHCHECK` direktivu, která se dá použít ke kontrole stavu aplikace, která používá konfiguraci základní kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="b122a-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="b122a-147">Vytvořit kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-147">Create health checks</span></span>

<span data-ttu-id="b122a-148">Kontroly stavu jsou vytvářeny implementací <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b122a-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="b122a-149"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>Metoda vrátí hodnotu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> , která označuje stav jako `Healthy` , `Degraded` nebo `Unhealthy` .</span><span class="sxs-lookup"><span data-stu-id="b122a-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="b122a-150">Výsledek je zapsán jako odpověď v podobě prostého textu s konfigurovatelným stavovým kódem (konfigurace je popsána v části [Možnosti kontroly stavu](#health-check-options) ).</span><span class="sxs-lookup"><span data-stu-id="b122a-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="b122a-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>může také vracet volitelné páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="b122a-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="b122a-152">Následující `ExampleHealthCheck` Třída ukazuje rozložení kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="b122a-153">Logika kontrol stavu je umístěna v `CheckHealthAsync` metodě.</span><span class="sxs-lookup"><span data-stu-id="b122a-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="b122a-154">Následující příklad nastaví fiktivní proměnnou `healthCheckResultHealthy` na `true` .</span><span class="sxs-lookup"><span data-stu-id="b122a-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="b122a-155">Pokud je hodnota `healthCheckResultHealthy` nastavena na `false` , je vrácena [HealthCheckResult.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status není v pořádku.</span><span class="sxs-lookup"><span data-stu-id="b122a-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

## <a name="register-health-check-services"></a><span data-ttu-id="b122a-156">Registrovat služby kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-156">Register health check services</span></span>

<span data-ttu-id="b122a-157">`ExampleHealthCheck`Typ se přidá do služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b122a-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="b122a-158"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>Přetížení znázorněné v následujícím příkladu nastaví stav selhání ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) na hodnotu ohlásit, když při kontrole stavu nahlásí chybu.</span><span class="sxs-lookup"><span data-stu-id="b122a-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="b122a-159">Pokud je stav selhání nastavený na `null` (výchozí), [funkčnosti](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) se hlásí jako chybné.</span><span class="sxs-lookup"><span data-stu-id="b122a-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="b122a-160">Toto přetížení je užitečným scénářem pro autory knihoven, kde stav selhání uvedený v knihovně vynutila aplikace, když dojde k selhání kontroly stavu, pokud se při implementaci kontroly stavu splní nastavení.</span><span class="sxs-lookup"><span data-stu-id="b122a-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="b122a-161">*Značky* lze použít k filtrování kontrol stavu (popsaných dále v oddílu [Filtr kontrol stavu](#filter-health-checks) ).</span><span class="sxs-lookup"><span data-stu-id="b122a-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="b122a-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>lze také spustit funkci lambda.</span><span class="sxs-lookup"><span data-stu-id="b122a-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="b122a-163">V následujícím příkladu je název kontroly stavu zadán jako `Example` a zaškrtávací políčka vždy vrátí dobrý stav:</span><span class="sxs-lookup"><span data-stu-id="b122a-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="b122a-164">Volání <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> předání argumentů do implementace kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="b122a-165">V následujícím příkladu `TestHealthCheckWithArgs` akceptuje celé číslo a řetězec, který se použije při <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> volání:</span><span class="sxs-lookup"><span data-stu-id="b122a-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

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

<span data-ttu-id="b122a-166">`TestHealthCheckWithArgs`je zaregistrován voláním `AddTypeActivatedCheck` s celým číslem a řetězcem předaným implementaci:</span><span class="sxs-lookup"><span data-stu-id="b122a-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="b122a-167">Použití směrování kontrol stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-167">Use Health Checks Routing</span></span>

<span data-ttu-id="b122a-168">V `Startup.Configure` nástroji zavolejte `MapHealthChecks` na tvůrce koncového bodu s adresou URL koncového bodu nebo relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="b122a-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="b122a-169">Vyžadovat hostitele</span><span class="sxs-lookup"><span data-stu-id="b122a-169">Require host</span></span>

<span data-ttu-id="b122a-170">Zavolejte `RequireHost` k zadání jednoho nebo více povolených hostitelů pro koncový bod kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="b122a-171">Hostitelé by měli být místo punycodei Unicode a můžou zahrnovat port.</span><span class="sxs-lookup"><span data-stu-id="b122a-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="b122a-172">Pokud není zadaná kolekce, bude přijat libovolný hostitel.</span><span class="sxs-lookup"><span data-stu-id="b122a-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="b122a-173">Další informace naleznete v části [Filter by port](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="b122a-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="b122a-174">Vyžadovat autorizaci</span><span class="sxs-lookup"><span data-stu-id="b122a-174">Require authorization</span></span>

<span data-ttu-id="b122a-175">Voláním `RequireAuthorization` pro spuštění autorizačního middleware na koncovém bodu žádosti o kontrolu stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="b122a-176">`RequireAuthorization`Přetížení přijímá jednu nebo více autorizačních zásad.</span><span class="sxs-lookup"><span data-stu-id="b122a-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="b122a-177">Pokud zásada není zadaná, použije se výchozí zásada autorizace.</span><span class="sxs-lookup"><span data-stu-id="b122a-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="b122a-178">Povolení žádostí nepůvodního zdroje (CORS)</span><span class="sxs-lookup"><span data-stu-id="b122a-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="b122a-179">I když ruční provádění kontrol stavu z prohlížeče není běžným scénářem použití, je možné middleware CORS povolit voláním `RequireCors` na koncové body kontrol stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="b122a-180">`RequireCors`Přetížení přijímá delegáta tvůrců zásad CORS ( `CorsPolicyBuilder` ) nebo název zásady.</span><span class="sxs-lookup"><span data-stu-id="b122a-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="b122a-181">Pokud zásada není zadaná, použije se výchozí zásada CORS.</span><span class="sxs-lookup"><span data-stu-id="b122a-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="b122a-182">Další informace naleznete v tématu <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="b122a-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="b122a-183">Možnosti kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-183">Health check options</span></span>

<span data-ttu-id="b122a-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>Poskytněte možnost přizpůsobení chování kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="b122a-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="b122a-185">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="b122a-186">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="b122a-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="b122a-187">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="b122a-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="b122a-188">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="b122a-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="b122a-189">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-189">Filter health checks</span></span>

<span data-ttu-id="b122a-190">Ve výchozím nastavení middleware pro kontrolu stavu spouští všechny zaregistrované kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="b122a-191">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která vrací logickou hodnotu pro <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> možnost.</span><span class="sxs-lookup"><span data-stu-id="b122a-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="b122a-192">V následujícím příkladu `Bar` je kontrole stavu Filtrováno podle značky ( `bar_tag` ) v podmíněném příkazu funkce, kde `true` je vrácena pouze v případě, že <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> odpovídá vlastnost kontroly stavu `foo_tag` nebo `baz_tag` :</span><span class="sxs-lookup"><span data-stu-id="b122a-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="b122a-193">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b122a-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="b122a-194">V nástroji `Startup.Configure` `Predicate` odfiltruje kontrolu stavu pruhů.</span><span class="sxs-lookup"><span data-stu-id="b122a-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="b122a-195">Pouze foo a Baz Execute:</span><span class="sxs-lookup"><span data-stu-id="b122a-195">Only Foo and Baz execute.:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="b122a-196">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="b122a-196">Customize the HTTP status code</span></span>

<span data-ttu-id="b122a-197">Slouží <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> k přizpůsobení mapování stavu na stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="b122a-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="b122a-198">Následující <xref:Microsoft.AspNetCore.Http.StatusCodes> přiřazení jsou výchozí hodnoty používané middlewarem.</span><span class="sxs-lookup"><span data-stu-id="b122a-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="b122a-199">Změňte hodnoty stavového kódu tak, aby splňovaly vaše požadavky.</span><span class="sxs-lookup"><span data-stu-id="b122a-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="b122a-200">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b122a-200">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="b122a-201">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="b122a-201">Suppress cache headers</span></span>

<span data-ttu-id="b122a-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Určuje, zda middleware kontroluje stav přidává hlavičky protokolu HTTP do odezvy sondy, aby nedocházelo k ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b122a-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="b122a-203">Pokud je hodnota `false` (výchozí), middleware nastaví nebo přepíše `Cache-Control` `Expires` hlavičky, a, `Pragma` aby nedocházelo k ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b122a-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="b122a-204">Pokud je hodnota `true` , middleware neupraví hlavičky mezipaměti odpovědi.</span><span class="sxs-lookup"><span data-stu-id="b122a-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="b122a-205">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b122a-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="b122a-206">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="b122a-206">Customize output</span></span>

<span data-ttu-id="b122a-207">V `Startup.Configure` sadě nastavte možnost [HealthCheckOptions. ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) na delegáta pro zápis odpovědi:</span><span class="sxs-lookup"><span data-stu-id="b122a-207">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="b122a-208">Výchozí delegát zapisuje minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="b122a-208">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="b122a-209">Následující vlastní Delegáti výstupují vlastní odpověď JSON.</span><span class="sxs-lookup"><span data-stu-id="b122a-209">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="b122a-210">První příklad z ukázkové aplikace ukazuje, jak používat <xref:System.Text.Json?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="b122a-210">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="b122a-211">Druhý příklad ukazuje, jak použít [Newtonsoft.Jsna](https://www.nuget.org/packages/Newtonsoft.Json/):</span><span class="sxs-lookup"><span data-stu-id="b122a-211">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="b122a-212">V ukázkové aplikaci zakomentujte `SYSTEM_TEXT_JSON` [direktivu preprocesoru](xref:index#preprocessor-directives-in-sample-code) v *CustomWriterStartup.cs* , abyste povolili `Newtonsoft.Json` verzi `WriteResponse` .</span><span class="sxs-lookup"><span data-stu-id="b122a-212">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="b122a-213">Rozhraní API pro kontroly stavu neposkytuje vestavěnou podporu pro komplexní formáty vrácených hodnot JSON, protože formát je specifický pro váš výběr systému monitorování.</span><span class="sxs-lookup"><span data-stu-id="b122a-213">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="b122a-214">Podle potřeby upravte odpověď v předchozích příkladech.</span><span class="sxs-lookup"><span data-stu-id="b122a-214">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="b122a-215">Další informace o serializaci JSON s `System.Text.Json` najdete [v tématu jak serializovat a deserializovat JSON v rozhraní .NET](/dotnet/standard/serialization/system-text-json-how-to).</span><span class="sxs-lookup"><span data-stu-id="b122a-215">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="b122a-216">Test databáze</span><span class="sxs-lookup"><span data-stu-id="b122a-216">Database probe</span></span>

<span data-ttu-id="b122a-217">Kontrola stavu může určit databázový dotaz, který se má spustit jako logický test, aby označoval, zda databáze obvykle reaguje.</span><span class="sxs-lookup"><span data-stu-id="b122a-217">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="b122a-218">Ukázková aplikace pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), knihovny kontroly stavu pro aplikace ASP.NET Core, provede kontrolu stavu databáze SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b122a-218">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="b122a-219">`AspNetCore.Diagnostics.HealthChecks`spustí `SELECT 1` dotaz na databázi, aby se ověřilo, že připojení k databázi je v pořádku.</span><span class="sxs-lookup"><span data-stu-id="b122a-219">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="b122a-220">Při kontrole připojení k databázi pomocí dotazu vyberte dotaz, který se rychle vrátí.</span><span class="sxs-lookup"><span data-stu-id="b122a-220">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="b122a-221">Přístup k dotazům spouští riziko přetížení databáze a snížení výkonu.</span><span class="sxs-lookup"><span data-stu-id="b122a-221">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="b122a-222">Ve většině případů není nutné spustit testovací dotaz.</span><span class="sxs-lookup"><span data-stu-id="b122a-222">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="b122a-223">Stačí pouze provést úspěšné připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="b122a-223">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="b122a-224">Pokud zjistíte, že je nutné spustit dotaz, zvolte jednoduchý dotaz SELECT, například `SELECT 1` .</span><span class="sxs-lookup"><span data-stu-id="b122a-224">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="b122a-225">Zahrňte odkaz na balíček do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="b122a-225">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="b122a-226">Zadejte platný připojovací řetězec databáze do *appsettings.jsv* souboru ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="b122a-226">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="b122a-227">Aplikace používá SQL Server databázi s názvem `HealthCheckSample` :</span><span class="sxs-lookup"><span data-stu-id="b122a-227">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="b122a-228">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b122a-228">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b122a-229">Ukázková aplikace volá `AddSqlServer` metodu s připojovacím řetězcem databáze (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="b122a-229">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="b122a-230">Koncový bod kontroly stavu se vytvoří voláním `MapHealthChecks` v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="b122a-230">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="b122a-231">Pokud chcete spustit scénář testu databáze pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="b122a-231">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="b122a-232">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="b122a-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="b122a-233">Test Entity Framework Core DbContext</span><span class="sxs-lookup"><span data-stu-id="b122a-233">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="b122a-234">Tato `DbContext` kontrolu potvrdí, že aplikace může komunikovat s databází nakonfigurovanou pro EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="b122a-234">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="b122a-235">Tato `DbContext` kontrolu je podporovaná v aplikacích, které:</span><span class="sxs-lookup"><span data-stu-id="b122a-235">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="b122a-236">Použijte [jádro Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="b122a-236">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="b122a-237">Zahrňte odkaz na balíček [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="b122a-237">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="b122a-238">`AddDbContextCheck<TContext>`zaregistruje kontrolu stavu pro `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="b122a-238">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="b122a-239">`DbContext`Je dodán jako `TContext` Metoda metodě.</span><span class="sxs-lookup"><span data-stu-id="b122a-239">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="b122a-240">K dispozici je přetížení pro konfiguraci stavu selhání, značek a vlastního testovacího dotazu.</span><span class="sxs-lookup"><span data-stu-id="b122a-240">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="b122a-241">Ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="b122a-241">By default:</span></span>

* <span data-ttu-id="b122a-242">`DbContextHealthCheck`Volá `CanConnectAsync` metodu EF Core.</span><span class="sxs-lookup"><span data-stu-id="b122a-242">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="b122a-243">Můžete přizpůsobit, která operace je spuštěna při kontrole stavu pomocí `AddDbContextCheck` přetížení metod.</span><span class="sxs-lookup"><span data-stu-id="b122a-243">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="b122a-244">Název kontroly stavu je název `TContext` typu.</span><span class="sxs-lookup"><span data-stu-id="b122a-244">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="b122a-245">V ukázkové aplikaci `AppDbContext` je k dispozici `AddDbContextCheck` a zaregistrována jako služba `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="b122a-245">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="b122a-246">Koncový bod kontroly stavu se vytvoří voláním `MapHealthChecks` v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="b122a-246">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="b122a-247">Pokud chcete spustit `DbContext` scénář sondy pomocí ukázkové aplikace, zkontrolujte, že databáze určená připojovacím řetězcem v instanci SQL Server neexistuje.</span><span class="sxs-lookup"><span data-stu-id="b122a-247">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="b122a-248">Pokud databáze existuje, odstraňte ji.</span><span class="sxs-lookup"><span data-stu-id="b122a-248">If the database exists, delete it.</span></span>

<span data-ttu-id="b122a-249">Ze složky projektu v příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b122a-249">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="b122a-250">Po spuštění aplikace ověřte stav tak, že v prohlížeči vytvoříte požadavek na `/health` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="b122a-250">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="b122a-251">Databáze a `AppDbContext` neexistuje, takže aplikace nabízí následující odpověď:</span><span class="sxs-lookup"><span data-stu-id="b122a-251">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="b122a-252">Aktivujte ukázkovou aplikaci pro vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="b122a-252">Trigger the sample app to create the database.</span></span> <span data-ttu-id="b122a-253">Vytvořte žádost na `/createdatabase` .</span><span class="sxs-lookup"><span data-stu-id="b122a-253">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="b122a-254">Aplikace odpoví:</span><span class="sxs-lookup"><span data-stu-id="b122a-254">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="b122a-255">Vytvořte požadavek na `/health` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="b122a-255">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="b122a-256">Databáze a kontext existují, takže aplikace reaguje:</span><span class="sxs-lookup"><span data-stu-id="b122a-256">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="b122a-257">Aktivujte ukázkovou aplikaci, aby se odstranila databáze.</span><span class="sxs-lookup"><span data-stu-id="b122a-257">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="b122a-258">Vytvořte žádost na `/deletedatabase` .</span><span class="sxs-lookup"><span data-stu-id="b122a-258">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="b122a-259">Aplikace odpoví:</span><span class="sxs-lookup"><span data-stu-id="b122a-259">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="b122a-260">Vytvořte požadavek na `/health` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="b122a-260">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="b122a-261">Aplikace poskytuje poškozenou reakci:</span><span class="sxs-lookup"><span data-stu-id="b122a-261">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="b122a-262">Samostatné sondy pro připravenost a živý provoz</span><span class="sxs-lookup"><span data-stu-id="b122a-262">Separate readiness and liveness probes</span></span>

<span data-ttu-id="b122a-263">V některých scénářích hostování se používá pár kontrol stavu, který rozlišuje dva stavy aplikace:</span><span class="sxs-lookup"><span data-stu-id="b122a-263">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="b122a-264">*Připravenost* indikuje, jestli je aplikace spuštěná normálně, ale není připravená na příjem požadavků.</span><span class="sxs-lookup"><span data-stu-id="b122a-264">*Readiness* indicates if the app is running normally but isn't ready to receive requests.</span></span>
* <span data-ttu-id="b122a-265">*Živý* vyjadřuje, jestli došlo k chybě aplikace a že se musí restartovat.</span><span class="sxs-lookup"><span data-stu-id="b122a-265">*Liveness* indicates if an app has crashed and must be restarted.</span></span>

<span data-ttu-id="b122a-266">Vezměte v úvahu následující příklad: aplikace musí stáhnout rozsáhlý konfigurační soubor předtím, než bude připraveno zpracovat požadavky.</span><span class="sxs-lookup"><span data-stu-id="b122a-266">Consider the following example: An app must download a large configuration file before it's ready to process requests.</span></span> <span data-ttu-id="b122a-267">Nechceme, aby se aplikace restartovala v případě, že se prvotní stažení nepovede, protože aplikace může zkusit znovu stáhnout soubor několikrát.</span><span class="sxs-lookup"><span data-stu-id="b122a-267">We don't want the app to be restarted if the initial download fails because the app can retry downloading the file several times.</span></span> <span data-ttu-id="b122a-268">K popsání živého procesu používáme *test živého života* . neprovádí se žádné další kontroly.</span><span class="sxs-lookup"><span data-stu-id="b122a-268">We use a *liveness probe* to describe the liveness of the process, no additional checks are performed.</span></span> <span data-ttu-id="b122a-269">Chceme také zabránit posílání požadavků do aplikace před tím, než stahování konfiguračního souboru proběhlo úspěšně.</span><span class="sxs-lookup"><span data-stu-id="b122a-269">We also want to prevent requests from being sent to the app before the configuration file download has succeeded.</span></span> <span data-ttu-id="b122a-270">K označení "Nepřipraveno" se používá *test připravenosti* , dokud se stahování nezdaří a aplikace je připravená přijímat požadavky.</span><span class="sxs-lookup"><span data-stu-id="b122a-270">We use a *readiness probe* to indicate a "not ready" state until the download succeeds and the app is ready to receive requests.</span></span>

<span data-ttu-id="b122a-271">Ukázková aplikace obsahuje kontrolu stavu, která oznamuje dokončení dlouhotrvající úlohy po spuštění v [hostované službě](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="b122a-271">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="b122a-272">`StartupHostedServiceHealthCheck`Zpřístupňuje vlastnost, `StartupTaskCompleted` která hostovaná služba může nastavit na hodnotu `true` po dokončení její dlouhotrvající úlohy (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="b122a-272">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="b122a-273">Dlouhotrvající úloha na pozadí se spouští v [hostované službě](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="b122a-273">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="b122a-274">Při uzavírání úkolu `StartupHostedServiceHealthCheck.StartupTaskCompleted` je nastaveno na `true` :</span><span class="sxs-lookup"><span data-stu-id="b122a-274">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="b122a-275">Kontroly stavu jsou registrovány <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> ve `Startup.ConfigureServices` službě společně s hostovanou službou.</span><span class="sxs-lookup"><span data-stu-id="b122a-275">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="b122a-276">Vzhledem k tomu, že hostovaná služba musí nastavit vlastnost pro kontrolu stavu, je tato kontrolu také zaregistrována v kontejneru služby (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="b122a-276">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="b122a-277">Koncový bod kontroly stavu je vytvořen voláním `MapHealthChecks` v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b122a-277">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="b122a-278">V ukázkové aplikaci jsou koncové body kontroly stavu vytvořeny v:</span><span class="sxs-lookup"><span data-stu-id="b122a-278">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="b122a-279">`/health/ready`pro kontrolu připravenosti.</span><span class="sxs-lookup"><span data-stu-id="b122a-279">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="b122a-280">Kontrola připravenosti filtruje kontrolu stavu na kontrolu stavu se `ready` značkou.</span><span class="sxs-lookup"><span data-stu-id="b122a-280">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="b122a-281">`/health/live`pro kontrolu živého provozu.</span><span class="sxs-lookup"><span data-stu-id="b122a-281">`/health/live` for the liveness check.</span></span> <span data-ttu-id="b122a-282">Kontrola živého filtrování odfiltruje `StartupHostedServiceHealthCheck` vrácením `false` do [HealthCheckOptions. predikátu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Další informace najdete v tématu [filtrování kontrol stavu](#filter-health-checks)).</span><span class="sxs-lookup"><span data-stu-id="b122a-282">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="b122a-283">V následujícím příkladu kódu:</span><span class="sxs-lookup"><span data-stu-id="b122a-283">In the following example code:</span></span>

* <span data-ttu-id="b122a-284">Kontrola připravenosti používá všechny registrované kontroly se značkou ' Read '.</span><span class="sxs-lookup"><span data-stu-id="b122a-284">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="b122a-285">`Predicate`Vyloučí všechny kontroly a vrátí 200 – OK.</span><span class="sxs-lookup"><span data-stu-id="b122a-285">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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

<span data-ttu-id="b122a-286">Pokud chcete spustit scénář konfigurace připravenosti/živých aplikací pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="b122a-286">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="b122a-287">V prohlížeči navštivte několikrát, `/health/ready` dokud neuplyne 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="b122a-287">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="b122a-288">Zprávy o kontrole stavu nejsou v *pořádku* po dobu prvních 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="b122a-288">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="b122a-289">Po 15 sekundách koncový bod hlásí stav *v pořádku*, který odráží dokončení dlouhotrvající úlohy hostované službou.</span><span class="sxs-lookup"><span data-stu-id="b122a-289">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="b122a-290">V tomto příkladu se vytvoří také Vydavatel kontroly stavu ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace), který spouští první kontrolu připravenosti se dvěma sekundami.</span><span class="sxs-lookup"><span data-stu-id="b122a-290">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="b122a-291">Další informace najdete v části [Vydavatel kontroly stavu](#health-check-publisher) .</span><span class="sxs-lookup"><span data-stu-id="b122a-291">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="b122a-292">Příklad Kubernetes</span><span class="sxs-lookup"><span data-stu-id="b122a-292">Kubernetes example</span></span>

<span data-ttu-id="b122a-293">Používání samostatné kontroly připravenosti a živých kontrol je užitečné v prostředí, jako je [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="b122a-293">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="b122a-294">V Kubernetes může být aplikace nutná k provádění časově náročné práce při spuštění před přijetím požadavků, jako je například test dostupnosti podkladové databáze.</span><span class="sxs-lookup"><span data-stu-id="b122a-294">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="b122a-295">Použití samostatných kontrol umožňuje nástroji Orchestrator rozlišit, jestli aplikace funguje, ale ještě není připravená, nebo jestli se aplikace nedala spustit.</span><span class="sxs-lookup"><span data-stu-id="b122a-295">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="b122a-296">Další informace o testování připravenosti a provozní sondy v Kubernetes najdete v tématu [Konfigurace živých a připravených sond](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) v dokumentaci k Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="b122a-296">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="b122a-297">Následující příklad ukazuje konfiguraci testu připravenosti na Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="b122a-297">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```yml
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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="b122a-298">Test založený na metrikách s vlastním zapisovačem odpovědí</span><span class="sxs-lookup"><span data-stu-id="b122a-298">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="b122a-299">Ukázková aplikace ukazuje kontrolu stavu paměti pomocí vlastního zapisovače odpovědí.</span><span class="sxs-lookup"><span data-stu-id="b122a-299">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="b122a-300">`MemoryHealthCheck`hlásí snížený stav, pokud aplikace používá více než určenou prahovou hodnotu paměti (1 GB v ukázkové aplikaci).</span><span class="sxs-lookup"><span data-stu-id="b122a-300">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="b122a-301"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>Zahrnuje informace o uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="b122a-301">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="b122a-302">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b122a-302">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b122a-303">Místo povolení kontroly stavu předáním <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `MemoryHealthCheck` jako služby je zaregistrována jako služba.</span><span class="sxs-lookup"><span data-stu-id="b122a-303">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="b122a-304">Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrované služby jsou k dispozici pro služby kontroly stavu a middleware.</span><span class="sxs-lookup"><span data-stu-id="b122a-304">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="b122a-305">Službu pro kontrolu stavu doporučujeme zaregistrovat jako služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="b122a-305">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="b122a-306">V *CustomWriterStartup.cs* ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="b122a-306">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="b122a-307">Koncový bod kontroly stavu je vytvořen voláním `MapHealthChecks` v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b122a-307">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="b122a-308">`WriteResponse`Delegát je k dispozici pro <vlastnost Microsoft. AspNetCore. Diagnostics. HealthChecks. HealthCheckOptions.> ResponseWriter pro výstup vlastní odpovědi JSON, když se spustí kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="b122a-308">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="b122a-309">`WriteResponse`Delegát naformátuje `CompositeHealthCheckResult` objekt do objektu JSON a pro odpověď na kontrolu stavu vrátí výstup JSON.</span><span class="sxs-lookup"><span data-stu-id="b122a-309">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="b122a-310">Další informace najdete v části [přizpůsobení výstupu](#customize-output) .</span><span class="sxs-lookup"><span data-stu-id="b122a-310">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="b122a-311">Pokud chcete spustit test založený na metrikách s vlastním výstupem zapisovače odpovědí pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="b122a-311">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="b122a-312">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje scénáře kontroly stavu založené na metrikách, včetně úložného prostoru na disku a maximální hodnoty živé kontroly.</span><span class="sxs-lookup"><span data-stu-id="b122a-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="b122a-313">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="b122a-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="b122a-314">Filtrovat podle portu</span><span class="sxs-lookup"><span data-stu-id="b122a-314">Filter by port</span></span>

<span data-ttu-id="b122a-315">Zavolejte `RequireHost` `MapHealthChecks` pomocí vzoru adresy URL, který určuje port pro omezení požadavků na kontrolu stavu na zadaný port.</span><span class="sxs-lookup"><span data-stu-id="b122a-315">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="b122a-316">Obvykle se používá v prostředí kontejneru k vystavení portu pro monitorovací služby.</span><span class="sxs-lookup"><span data-stu-id="b122a-316">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="b122a-317">Ukázková aplikace nakonfiguruje port pomocí [poskytovatele konfigurace proměnné prostředí](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="b122a-317">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="b122a-318">Port je nastaven v *launchSettings.jsv* souboru a předán poskytovateli konfigurace prostřednictvím proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b122a-318">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="b122a-319">Také je nutné nakonfigurovat server tak, aby naslouchal žádosti na portu pro správu.</span><span class="sxs-lookup"><span data-stu-id="b122a-319">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="b122a-320">Pokud chcete ukázkovou aplikaci použít k předvedení konfigurace portů pro správu, vytvořte *launchSettings.js* v souboru ve složce *Properties (vlastnosti* ).</span><span class="sxs-lookup"><span data-stu-id="b122a-320">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="b122a-321">Následující *vlastnosti/launchSettings.jsv* souboru v ukázkové aplikaci nejsou součástí souborů projektu ukázkové aplikace a je nutné je vytvořit ručně:</span><span class="sxs-lookup"><span data-stu-id="b122a-321">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="b122a-322">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b122a-322">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b122a-323">Vytvořte koncový bod kontroly stavu voláním `MapHealthChecks` v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b122a-323">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="b122a-324">V ukázkové aplikaci volání `RequireHost` na koncový bod v `Startup.Configure` Určuje port pro správu z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="b122a-324">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="b122a-325">Koncové body se vytvoří v ukázkové aplikaci v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b122a-325">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="b122a-326">V následujícím příkladu kódu:</span><span class="sxs-lookup"><span data-stu-id="b122a-326">In the following example code:</span></span>

* <span data-ttu-id="b122a-327">Kontrola připravenosti používá všechny registrované kontroly se značkou ' Read '.</span><span class="sxs-lookup"><span data-stu-id="b122a-327">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="b122a-328">`Predicate`Vyloučí všechny kontroly a vrátí 200 – OK.</span><span class="sxs-lookup"><span data-stu-id="b122a-328">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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
> <span data-ttu-id="b122a-329">Můžete se vyhnout vytváření *launchSettings.js* v souboru v ukázkové aplikaci nastavením portu pro správu explicitně v kódu.</span><span class="sxs-lookup"><span data-stu-id="b122a-329">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="b122a-330">V *program.cs* , kde <xref:Microsoft.Extensions.Hosting.HostBuilder> je vytvořena, přidejte volání do <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> a poskytněte koncový bod portu pro správu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b122a-330">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="b122a-331">V `Configure` *ManagementPortStartup.cs*zadejte port pro správu pomocí `RequireHost` :</span><span class="sxs-lookup"><span data-stu-id="b122a-331">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="b122a-332">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b122a-332">*Program.cs*:</span></span>
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
> <span data-ttu-id="b122a-333">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b122a-333">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="b122a-334">Pokud chcete spustit scénář konfigurace portu pro správu pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="b122a-334">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="b122a-335">Distribuce knihovny kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-335">Distribute a health check library</span></span>

<span data-ttu-id="b122a-336">Postup při distribuci kontroly stavu jako knihovny:</span><span class="sxs-lookup"><span data-stu-id="b122a-336">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="b122a-337">Zapište kontrolu stavu, která implementuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní jako samostatnou třídu.</span><span class="sxs-lookup"><span data-stu-id="b122a-337">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="b122a-338">Třída může spoléhat na [vkládání závislostí (di)](xref:fundamentals/dependency-injection), aktivovat typ a [pojmenované možnosti](xref:fundamentals/configuration/options) pro přístup k datům konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b122a-338">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="b122a-339">V logice kontroly stavu pro `CheckHealthAsync` :</span><span class="sxs-lookup"><span data-stu-id="b122a-339">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="b122a-340">`data1`a `data2` jsou použity v metodě pro spuštění logiky kontroly stavu testu.</span><span class="sxs-lookup"><span data-stu-id="b122a-340">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="b122a-341">`AccessViolationException`je zpracována.</span><span class="sxs-lookup"><span data-stu-id="b122a-341">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="b122a-342">V případě <xref:System.AccessViolationException> , že dojde k chybě, se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> vrátí s a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> umožní uživatelům konfigurovat stav selhání kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-342">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="b122a-343">Zapište metodu rozšíření s parametry, které aplikace spotřebovávají ve své `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="b122a-343">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="b122a-344">V následujícím příkladu předpokládejme následující signatura metody kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="b122a-344">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="b122a-345">Předchozí signatura indikuje, že `ExampleHealthCheck` pro zpracování logiky sondy kontroly stavu vyžaduje další data.</span><span class="sxs-lookup"><span data-stu-id="b122a-345">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="b122a-346">Data jsou k dispozici delegátovi použitému k vytvoření instance kontroly stavu při registraci kontroly stavu s metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="b122a-346">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="b122a-347">V následujícím příkladu volající určuje nepovinné:</span><span class="sxs-lookup"><span data-stu-id="b122a-347">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="b122a-348">název kontroly stavu ( `name` ).</span><span class="sxs-lookup"><span data-stu-id="b122a-348">health check name (`name`).</span></span> <span data-ttu-id="b122a-349">Pokud `null` se `example_health_check` používá.</span><span class="sxs-lookup"><span data-stu-id="b122a-349">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="b122a-350">datový bod řetězce pro kontrolu stavu ( `data1` ).</span><span class="sxs-lookup"><span data-stu-id="b122a-350">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="b122a-351">celočíselný datový bod pro kontrolu stavu ( `data2` ).</span><span class="sxs-lookup"><span data-stu-id="b122a-351">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="b122a-352">Pokud `null` se `1` používá.</span><span class="sxs-lookup"><span data-stu-id="b122a-352">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="b122a-353">stav selhání ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ).</span><span class="sxs-lookup"><span data-stu-id="b122a-353">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="b122a-354">Výchozí formát je `null`.</span><span class="sxs-lookup"><span data-stu-id="b122a-354">The default is `null`.</span></span> <span data-ttu-id="b122a-355">Pokud `null` je [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) není v pořádku, je hlášen stav selhání.</span><span class="sxs-lookup"><span data-stu-id="b122a-355">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="b122a-356">značky ( `IEnumerable<string>` ).</span><span class="sxs-lookup"><span data-stu-id="b122a-356">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="b122a-357">Vydavatel kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-357">Health Check Publisher</span></span>

<span data-ttu-id="b122a-358">Když <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> se přidá do kontejneru služby, systém kontroly stavu pravidelně spouští kontroly stavu a volání `PublishAsync` s výsledkem.</span><span class="sxs-lookup"><span data-stu-id="b122a-358">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="b122a-359">To je užitečné ve scénáři sledování stavu založeném na nabízených oznámeních, který očekává, že každý proces bude pravidelně volat monitorovací systém, aby se zjistil stav.</span><span class="sxs-lookup"><span data-stu-id="b122a-359">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="b122a-360"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>Rozhraní má jedinou metodu:</span><span class="sxs-lookup"><span data-stu-id="b122a-360">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="b122a-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>umožňuje nastavit:</span><span class="sxs-lookup"><span data-stu-id="b122a-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="b122a-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Počáteční zpoždění použité po spuštění aplikace před spuštěním <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instancí.</span><span class="sxs-lookup"><span data-stu-id="b122a-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="b122a-363">Zpoždění se použije jednou při spuštění a nevztahuje se na další iterace.</span><span class="sxs-lookup"><span data-stu-id="b122a-363">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="b122a-364">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="b122a-364">The default value is five seconds.</span></span>
* <span data-ttu-id="b122a-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Období <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> provádění.</span><span class="sxs-lookup"><span data-stu-id="b122a-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="b122a-366">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="b122a-366">The default value is 30 seconds.</span></span>
* <span data-ttu-id="b122a-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> je `null` (výchozí), služba Vydavatel kontroly stavu spustí všechny zaregistrované kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="b122a-368">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která filtruje sadu kontrol.</span><span class="sxs-lookup"><span data-stu-id="b122a-368">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="b122a-369">Predikát je vyhodnocen každou periodu.</span><span class="sxs-lookup"><span data-stu-id="b122a-369">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="b122a-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Časový limit pro spuštění kontrol stavu pro všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance.</span><span class="sxs-lookup"><span data-stu-id="b122a-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="b122a-371">Použijte <xref:System.Threading.Timeout.InfiniteTimeSpan> k provedení bez časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b122a-371">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="b122a-372">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="b122a-372">The default value is 30 seconds.</span></span>

<span data-ttu-id="b122a-373">V ukázkové aplikaci `ReadinessPublisher` je <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace.</span><span class="sxs-lookup"><span data-stu-id="b122a-373">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="b122a-374">Stav kontroly stavu se protokoluje pro každou kontrolu na úrovni protokolu:</span><span class="sxs-lookup"><span data-stu-id="b122a-374">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="b122a-375">Informace ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ), pokud je stav kontrol stavu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .</span><span class="sxs-lookup"><span data-stu-id="b122a-375">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="b122a-376">Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ), pokud je stav buď <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> nebo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .</span><span class="sxs-lookup"><span data-stu-id="b122a-376">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="b122a-377">V příkladu ukázkové aplikace `LivenessProbeStartup` `StartupHostedService` má kontroler připravenosti dvě sekundy na spuštění a spustí kontrolu každých 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="b122a-377">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="b122a-378">Chcete-li aktivovat <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementaci, ukázka se registruje `ReadinessPublisher` jako služba s jedním prvkem v kontejneru [Injektáže (di) pro vkládání závislostí](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="b122a-378">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="b122a-379">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="b122a-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="b122a-380">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="b122a-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="b122a-381">Omezení kontrol stavu pomocí MapWhen</span><span class="sxs-lookup"><span data-stu-id="b122a-381">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="b122a-382">Použijte <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> k podmíněně větvení kanálu žádostí pro koncové body kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-382">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="b122a-383">V následujícím příkladu `MapWhen` větví kanál žádosti o aktivaci middlewaru pro kontrolu stavu, pokud se pro koncový bod přijme požadavek Get `api/HealthCheck` :</span><span class="sxs-lookup"><span data-stu-id="b122a-383">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

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

<span data-ttu-id="b122a-384">Další informace naleznete v tématu <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>.</span><span class="sxs-lookup"><span data-stu-id="b122a-384">For more information, see <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b122a-385">ASP.NET Core nabízí kontrolu stavu middlewaru a knihovny pro vytváření sestav stavu součástí infrastruktury aplikace.</span><span class="sxs-lookup"><span data-stu-id="b122a-385">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="b122a-386">Kontroly stavu jsou zpřístupněné aplikací jako koncové body HTTP.</span><span class="sxs-lookup"><span data-stu-id="b122a-386">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="b122a-387">Koncové body kontroly stavu je možné nakonfigurovat pro nejrůznější scénáře monitorování v reálném čase:</span><span class="sxs-lookup"><span data-stu-id="b122a-387">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="b122a-388">Sondy stavu můžou použít orchestrace kontejnerů a nástroje pro vyrovnávání zatížení ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b122a-388">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="b122a-389">Například produkt Orchestrator může reagovat na selhání kontroly stavu zastavením zavedení nasazení nebo restartováním kontejneru.</span><span class="sxs-lookup"><span data-stu-id="b122a-389">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="b122a-390">Nástroj pro vyrovnávání zatížení může reagovat na poškozenou aplikaci směrováním provozu mimo instanci selhání do stavu v pořádku.</span><span class="sxs-lookup"><span data-stu-id="b122a-390">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="b122a-391">Použití paměti, disku a dalších prostředků fyzického serveru se dá monitorovat v dobrém stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-391">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="b122a-392">Kontroly stavu můžou testovat závislosti aplikace, jako jsou databáze a externí koncové body služby, aby se potvrdila dostupnost a normální fungování.</span><span class="sxs-lookup"><span data-stu-id="b122a-392">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="b122a-393">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b122a-393">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b122a-394">Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b122a-394">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="b122a-395">Pokud chcete pro daný scénář spustit ukázkovou aplikaci, použijte příkaz [dotnet Run](/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="b122a-395">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="b122a-396">Podrobnosti o tom, jak používat ukázkovou aplikaci, najdete v souboru *Readme.MD* ukázkové aplikace a v popisech scénářů v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="b122a-396">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b122a-397">Požadavky</span><span class="sxs-lookup"><span data-stu-id="b122a-397">Prerequisites</span></span>

<span data-ttu-id="b122a-398">Kontroly stavu se obvykle používají s externí službou monitorování nebo nástrojem Orchestrator Container Service ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b122a-398">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="b122a-399">Před přidáním kontrol stavu do aplikace se rozhodněte, který monitorovací systém se má použít.</span><span class="sxs-lookup"><span data-stu-id="b122a-399">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="b122a-400">Monitorovací systém vyhodnotí, jaké typy kontrol stavu se mají vytvořit a jak nakonfigurovat jejich koncové body.</span><span class="sxs-lookup"><span data-stu-id="b122a-400">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="b122a-401">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) .</span><span class="sxs-lookup"><span data-stu-id="b122a-401">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="b122a-402">Ukázková aplikace poskytuje spouštěcí kód k předvedení kontrol stavu pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="b122a-402">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="b122a-403">Scénář [testu databáze](#database-probe) kontroluje stav připojení k databázi pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="b122a-403">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="b122a-404">Scénář [testu DbContext](#entity-framework-core-dbcontext-probe) zkontroluje databázi pomocí EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="b122a-404">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="b122a-405">K prozkoumání scénářů databáze se jedná o ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="b122a-405">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="b122a-406">Vytvoří databázi a poskytne připojovací řetězec v *appsettings.js* souboru.</span><span class="sxs-lookup"><span data-stu-id="b122a-406">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="b122a-407">Obsahuje následující odkazy na balíček v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="b122a-407">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="b122a-408">AspNetCore. HealthChecks. SqlServer</span><span class="sxs-lookup"><span data-stu-id="b122a-408">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="b122a-409">Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="b122a-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="b122a-410">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="b122a-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="b122a-411">Další scénář kontroly stavu ukazuje, jak filtrovat kontroly stavu na port pro správu.</span><span class="sxs-lookup"><span data-stu-id="b122a-411">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="b122a-412">Ukázková aplikace vyžaduje, abyste v souboru, který obsahuje adresu URL pro správu a port pro správu, vytvořili *vlastnosti nebo launchSettings.js* .</span><span class="sxs-lookup"><span data-stu-id="b122a-412">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="b122a-413">Další informace naleznete v části [Filter by port](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="b122a-413">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="b122a-414">Základní sonda stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-414">Basic health probe</span></span>

<span data-ttu-id="b122a-415">U mnoha aplikací je základní konfigurace sondy stavu, která oznamuje dostupnost aplikace pro zpracování požadavků (*živý*), ke zjištění stavu aplikace dostačující.</span><span class="sxs-lookup"><span data-stu-id="b122a-415">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="b122a-416">Základní konfigurace registruje služby kontroly stavu a volá middleware kontroly stavu, aby reagovala na koncový bod adresy URL s odpovědí na stav.</span><span class="sxs-lookup"><span data-stu-id="b122a-416">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="b122a-417">Ve výchozím nastavení nejsou k otestování konkrétní závislosti nebo subsystému registrovány žádné konkrétní kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-417">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="b122a-418">Aplikace se považuje za v pořádku, pokud dokáže reagovat na adresu URL koncového bodu stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-418">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="b122a-419">Výchozí zapisovač odpovědí zapisuje stav ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) jako odpověď ve formátu prostého textu zpátky klientovi, což značí buď [funkčnosti. dobrý](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)stav, [funkčnosti. Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status není v pořádku.</span><span class="sxs-lookup"><span data-stu-id="b122a-419">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="b122a-420">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b122a-420">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b122a-421">Přidejte koncový bod pro kontrolu stavu middleware s <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> v kanálu zpracování požadavků `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b122a-421">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="b122a-422">V ukázkové aplikaci se koncový bod kontroly stavu vytvoří `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="b122a-422">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="b122a-423">Pokud chcete spustit scénář základní konfigurace pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="b122a-423">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="b122a-424">Ukázka Docker</span><span class="sxs-lookup"><span data-stu-id="b122a-424">Docker example</span></span>

<span data-ttu-id="b122a-425">[Docker](xref:host-and-deploy/docker/index) nabízí vestavěnou `HEALTHCHECK` direktivu, která se dá použít ke kontrole stavu aplikace, která používá konfiguraci základní kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="b122a-425">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="b122a-426">Vytvořit kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-426">Create health checks</span></span>

<span data-ttu-id="b122a-427">Kontroly stavu jsou vytvářeny implementací <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b122a-427">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="b122a-428"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>Metoda vrátí hodnotu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> , která označuje stav jako `Healthy` , `Degraded` nebo `Unhealthy` .</span><span class="sxs-lookup"><span data-stu-id="b122a-428">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="b122a-429">Výsledek je zapsán jako odpověď v podobě prostého textu s konfigurovatelným stavovým kódem (konfigurace je popsána v části [Možnosti kontroly stavu](#health-check-options) ).</span><span class="sxs-lookup"><span data-stu-id="b122a-429">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="b122a-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>může také vracet volitelné páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="b122a-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="b122a-431">Příklad kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-431">Example health check</span></span>

<span data-ttu-id="b122a-432">Následující `ExampleHealthCheck` Třída ukazuje rozložení kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-432">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="b122a-433">Logika kontrol stavu je umístěna v `CheckHealthAsync` metodě.</span><span class="sxs-lookup"><span data-stu-id="b122a-433">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="b122a-434">Následující příklad nastaví fiktivní proměnnou `healthCheckResultHealthy` na `true` .</span><span class="sxs-lookup"><span data-stu-id="b122a-434">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="b122a-435">Pokud je hodnota `healthCheckResultHealthy` nastavena na `false` , je vrácena [HealthCheckResult.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status není v pořádku.</span><span class="sxs-lookup"><span data-stu-id="b122a-435">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

### <a name="register-health-check-services"></a><span data-ttu-id="b122a-436">Registrovat služby kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-436">Register health check services</span></span>

<span data-ttu-id="b122a-437">`ExampleHealthCheck`Typ se přidá do služby kontroly stavu v `Startup.ConfigureServices` nástroji <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> :</span><span class="sxs-lookup"><span data-stu-id="b122a-437">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="b122a-438"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>Přetížení znázorněné v následujícím příkladu nastaví stav selhání ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) na hodnotu ohlásit, když při kontrole stavu nahlásí chybu.</span><span class="sxs-lookup"><span data-stu-id="b122a-438">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="b122a-439">Pokud je stav selhání nastavený na `null` (výchozí), [funkčnosti](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) se hlásí jako chybné.</span><span class="sxs-lookup"><span data-stu-id="b122a-439">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="b122a-440">Toto přetížení je užitečným scénářem pro autory knihoven, kde stav selhání uvedený v knihovně vynutila aplikace, když dojde k selhání kontroly stavu, pokud se při implementaci kontroly stavu splní nastavení.</span><span class="sxs-lookup"><span data-stu-id="b122a-440">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="b122a-441">*Značky* lze použít k filtrování kontrol stavu (popsaných dále v oddílu [Filtr kontrol stavu](#filter-health-checks) ).</span><span class="sxs-lookup"><span data-stu-id="b122a-441">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="b122a-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>lze také spustit funkci lambda.</span><span class="sxs-lookup"><span data-stu-id="b122a-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="b122a-443">V následujícím `Startup.ConfigureServices` příkladu je název kontroly stavu zadán jako `Example` a zaškrtávací políčka vždy vrátí dobrý stav:</span><span class="sxs-lookup"><span data-stu-id="b122a-443">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="b122a-444">Použití middlewaru pro kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-444">Use Health Checks Middleware</span></span>

<span data-ttu-id="b122a-445">V aplikaci `Startup.Configure` volejte <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> kanál zpracování s adresou URL koncového bodu nebo relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="b122a-445">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="b122a-446">Pokud by kontroly stavu měly naslouchat na konkrétním portu, použijte přetížení <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> pro nastavení portu (popsáno dále v části [Filter by port](#filter-by-port) ):</span><span class="sxs-lookup"><span data-stu-id="b122a-446">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="b122a-447">Možnosti kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-447">Health check options</span></span>

<span data-ttu-id="b122a-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>Poskytněte možnost přizpůsobení chování kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="b122a-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="b122a-449">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-449">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="b122a-450">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="b122a-450">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="b122a-451">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="b122a-451">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="b122a-452">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="b122a-452">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="b122a-453">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-453">Filter health checks</span></span>

<span data-ttu-id="b122a-454">Ve výchozím nastavení middleware pro kontrolu stavu spouští všechny zaregistrované kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-454">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="b122a-455">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která vrací logickou hodnotu pro <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> možnost.</span><span class="sxs-lookup"><span data-stu-id="b122a-455">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="b122a-456">V následujícím příkladu `Bar` je kontrole stavu Filtrováno podle značky ( `bar_tag` ) v podmíněném příkazu funkce, kde `true` je vrácena pouze v případě, že <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> odpovídá vlastnost kontroly stavu `foo_tag` nebo `baz_tag` :</span><span class="sxs-lookup"><span data-stu-id="b122a-456">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="b122a-457">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="b122a-457">Customize the HTTP status code</span></span>

<span data-ttu-id="b122a-458">Slouží <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> k přizpůsobení mapování stavu na stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="b122a-458">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="b122a-459">Následující <xref:Microsoft.AspNetCore.Http.StatusCodes> přiřazení jsou výchozí hodnoty používané middlewarem.</span><span class="sxs-lookup"><span data-stu-id="b122a-459">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="b122a-460">Změňte hodnoty stavového kódu tak, aby splňovaly vaše požadavky.</span><span class="sxs-lookup"><span data-stu-id="b122a-460">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="b122a-461">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b122a-461">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="b122a-462">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="b122a-462">Suppress cache headers</span></span>

<span data-ttu-id="b122a-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Určuje, zda middleware kontroluje stav přidává hlavičky protokolu HTTP do odezvy sondy, aby nedocházelo k ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b122a-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="b122a-464">Pokud je hodnota `false` (výchozí), middleware nastaví nebo přepíše `Cache-Control` `Expires` hlavičky, a, `Pragma` aby nedocházelo k ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="b122a-464">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="b122a-465">Pokud je hodnota `true` , middleware neupraví hlavičky mezipaměti odpovědi.</span><span class="sxs-lookup"><span data-stu-id="b122a-465">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="b122a-466">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b122a-466">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="b122a-467">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="b122a-467">Customize output</span></span>

<span data-ttu-id="b122a-468"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter>Možnost Získá nebo nastaví delegáta použitý k zápisu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="b122a-468">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="b122a-469">Výchozí delegát zapisuje minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="b122a-469">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="b122a-470">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b122a-470">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="b122a-471">Výchozí delegát zapisuje minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="b122a-471">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="b122a-472">Následující vlastní delegát `WriteResponse` vytvoří výstup vlastní odpovědi JSON:</span><span class="sxs-lookup"><span data-stu-id="b122a-472">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

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

<span data-ttu-id="b122a-473">Systém kontroly stavu neposkytuje integrovanou podporu pro komplexní formáty vrácených hodnot JSON, protože formát je specifický pro váš výběr systému monitorování.</span><span class="sxs-lookup"><span data-stu-id="b122a-473">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="b122a-474">V předchozím příkladu si můžete přizpůsobit, `JObject` jak je to nutné pro splnění vašich požadavků.</span><span class="sxs-lookup"><span data-stu-id="b122a-474">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="b122a-475">Test databáze</span><span class="sxs-lookup"><span data-stu-id="b122a-475">Database probe</span></span>

<span data-ttu-id="b122a-476">Kontrola stavu může určit databázový dotaz, který se má spustit jako logický test, aby označoval, zda databáze obvykle reaguje.</span><span class="sxs-lookup"><span data-stu-id="b122a-476">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="b122a-477">Ukázková aplikace pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), knihovny kontroly stavu pro aplikace ASP.NET Core, provede kontrolu stavu databáze SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b122a-477">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="b122a-478">`AspNetCore.Diagnostics.HealthChecks`spustí `SELECT 1` dotaz na databázi, aby se ověřilo, že připojení k databázi je v pořádku.</span><span class="sxs-lookup"><span data-stu-id="b122a-478">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="b122a-479">Při kontrole připojení k databázi pomocí dotazu vyberte dotaz, který se rychle vrátí.</span><span class="sxs-lookup"><span data-stu-id="b122a-479">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="b122a-480">Přístup k dotazům spouští riziko přetížení databáze a snížení výkonu.</span><span class="sxs-lookup"><span data-stu-id="b122a-480">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="b122a-481">Ve většině případů není nutné spustit testovací dotaz.</span><span class="sxs-lookup"><span data-stu-id="b122a-481">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="b122a-482">Stačí pouze provést úspěšné připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="b122a-482">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="b122a-483">Pokud zjistíte, že je nutné spustit dotaz, zvolte jednoduchý dotaz SELECT, například `SELECT 1` .</span><span class="sxs-lookup"><span data-stu-id="b122a-483">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="b122a-484">Zahrňte odkaz na balíček do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="b122a-484">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="b122a-485">Zadejte platný připojovací řetězec databáze do *appsettings.jsv* souboru ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="b122a-485">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="b122a-486">Aplikace používá SQL Server databázi s názvem `HealthCheckSample` :</span><span class="sxs-lookup"><span data-stu-id="b122a-486">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="b122a-487">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b122a-487">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b122a-488">Ukázková aplikace volá `AddSqlServer` metodu s připojovacím řetězcem databáze (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="b122a-488">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="b122a-489">Ověřování stavu volání middlewaru v kanálu zpracování aplikace v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="b122a-489">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="b122a-490">Pokud chcete spustit scénář testu databáze pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="b122a-490">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="b122a-491">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="b122a-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="b122a-492">Test Entity Framework Core DbContext</span><span class="sxs-lookup"><span data-stu-id="b122a-492">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="b122a-493">Tato `DbContext` kontrolu potvrdí, že aplikace může komunikovat s databází nakonfigurovanou pro EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="b122a-493">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="b122a-494">Tato `DbContext` kontrolu je podporovaná v aplikacích, které:</span><span class="sxs-lookup"><span data-stu-id="b122a-494">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="b122a-495">Použijte [jádro Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="b122a-495">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="b122a-496">Zahrňte odkaz na balíček [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="b122a-496">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="b122a-497">`AddDbContextCheck<TContext>`zaregistruje kontrolu stavu pro `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="b122a-497">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="b122a-498">`DbContext`Je dodán jako `TContext` Metoda metodě.</span><span class="sxs-lookup"><span data-stu-id="b122a-498">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="b122a-499">K dispozici je přetížení pro konfiguraci stavu selhání, značek a vlastního testovacího dotazu.</span><span class="sxs-lookup"><span data-stu-id="b122a-499">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="b122a-500">Ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="b122a-500">By default:</span></span>

* <span data-ttu-id="b122a-501">`DbContextHealthCheck`Volá `CanConnectAsync` metodu EF Core.</span><span class="sxs-lookup"><span data-stu-id="b122a-501">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="b122a-502">Můžete přizpůsobit, která operace je spuštěna při kontrole stavu pomocí `AddDbContextCheck` přetížení metod.</span><span class="sxs-lookup"><span data-stu-id="b122a-502">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="b122a-503">Název kontroly stavu je název `TContext` typu.</span><span class="sxs-lookup"><span data-stu-id="b122a-503">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="b122a-504">V ukázkové aplikaci `AppDbContext` je k dispozici `AddDbContextCheck` a zaregistrována jako služba `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="b122a-504">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="b122a-505">V ukázkové aplikaci `UseHealthChecks` přidá modul pro kontrolu stavu middleware v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b122a-505">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="b122a-506">Pokud chcete spustit `DbContext` scénář sondy pomocí ukázkové aplikace, zkontrolujte, že databáze určená připojovacím řetězcem v instanci SQL Server neexistuje.</span><span class="sxs-lookup"><span data-stu-id="b122a-506">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="b122a-507">Pokud databáze existuje, odstraňte ji.</span><span class="sxs-lookup"><span data-stu-id="b122a-507">If the database exists, delete it.</span></span>

<span data-ttu-id="b122a-508">Ze složky projektu v příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="b122a-508">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="b122a-509">Po spuštění aplikace ověřte stav tak, že v prohlížeči vytvoříte požadavek na `/health` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="b122a-509">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="b122a-510">Databáze a `AppDbContext` neexistuje, takže aplikace nabízí následující odpověď:</span><span class="sxs-lookup"><span data-stu-id="b122a-510">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="b122a-511">Aktivujte ukázkovou aplikaci pro vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="b122a-511">Trigger the sample app to create the database.</span></span> <span data-ttu-id="b122a-512">Vytvořte žádost na `/createdatabase` .</span><span class="sxs-lookup"><span data-stu-id="b122a-512">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="b122a-513">Aplikace odpoví:</span><span class="sxs-lookup"><span data-stu-id="b122a-513">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="b122a-514">Vytvořte požadavek na `/health` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="b122a-514">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="b122a-515">Databáze a kontext existují, takže aplikace reaguje:</span><span class="sxs-lookup"><span data-stu-id="b122a-515">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="b122a-516">Aktivujte ukázkovou aplikaci, aby se odstranila databáze.</span><span class="sxs-lookup"><span data-stu-id="b122a-516">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="b122a-517">Vytvořte žádost na `/deletedatabase` .</span><span class="sxs-lookup"><span data-stu-id="b122a-517">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="b122a-518">Aplikace odpoví:</span><span class="sxs-lookup"><span data-stu-id="b122a-518">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="b122a-519">Vytvořte požadavek na `/health` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="b122a-519">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="b122a-520">Aplikace poskytuje poškozenou reakci:</span><span class="sxs-lookup"><span data-stu-id="b122a-520">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="b122a-521">Samostatné sondy pro připravenost a živý provoz</span><span class="sxs-lookup"><span data-stu-id="b122a-521">Separate readiness and liveness probes</span></span>

<span data-ttu-id="b122a-522">V některých scénářích hostování se používá pár kontrol stavu, který rozlišuje dva stavy aplikace:</span><span class="sxs-lookup"><span data-stu-id="b122a-522">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="b122a-523">*Připravenost* indikuje, jestli je aplikace spuštěná normálně, ale není připravená na příjem požadavků.</span><span class="sxs-lookup"><span data-stu-id="b122a-523">*Readiness* indicates if the app is running normally but isn't ready to receive requests.</span></span>
* <span data-ttu-id="b122a-524">*Živý* vyjadřuje, jestli došlo k chybě aplikace a že se musí restartovat.</span><span class="sxs-lookup"><span data-stu-id="b122a-524">*Liveness* indicates if an app has crashed and must be restarted.</span></span>

<span data-ttu-id="b122a-525">Vezměte v úvahu následující příklad: aplikace musí stáhnout rozsáhlý konfigurační soubor předtím, než bude připraveno zpracovat požadavky.</span><span class="sxs-lookup"><span data-stu-id="b122a-525">Consider the following example: An app must download a large configuration file before it's ready to process requests.</span></span> <span data-ttu-id="b122a-526">Nechceme, aby se aplikace restartovala v případě, že se prvotní stažení nepovede, protože aplikace může zkusit znovu stáhnout soubor několikrát.</span><span class="sxs-lookup"><span data-stu-id="b122a-526">We don't want the app to be restarted if the initial download fails because the app can retry downloading the file several times.</span></span> <span data-ttu-id="b122a-527">K popsání živého procesu používáme *test živého života* . neprovádí se žádné další kontroly.</span><span class="sxs-lookup"><span data-stu-id="b122a-527">We use a *liveness probe* to describe the liveness of the process, no additional checks are performed.</span></span> <span data-ttu-id="b122a-528">Chceme také zabránit posílání požadavků do aplikace před tím, než stahování konfiguračního souboru proběhlo úspěšně.</span><span class="sxs-lookup"><span data-stu-id="b122a-528">We also want to prevent requests from being sent to the app before the configuration file download has succeeded.</span></span> <span data-ttu-id="b122a-529">K označení "Nepřipraveno" se používá *test připravenosti* , dokud se stahování nezdaří a aplikace je připravená přijímat požadavky.</span><span class="sxs-lookup"><span data-stu-id="b122a-529">We use a *readiness probe* to indicate a "not ready" state until the download succeeds and the app is ready to receive requests.</span></span>

<span data-ttu-id="b122a-530">Ukázková aplikace obsahuje kontrolu stavu, která oznamuje dokončení dlouhotrvající úlohy po spuštění v [hostované službě](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="b122a-530">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="b122a-531">`StartupHostedServiceHealthCheck`Zpřístupňuje vlastnost, `StartupTaskCompleted` která hostovaná služba může nastavit na hodnotu `true` po dokončení její dlouhotrvající úlohy (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="b122a-531">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="b122a-532">Dlouhotrvající úloha na pozadí se spouští v [hostované službě](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="b122a-532">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="b122a-533">Při uzavírání úkolu `StartupHostedServiceHealthCheck.StartupTaskCompleted` je nastaveno na `true` :</span><span class="sxs-lookup"><span data-stu-id="b122a-533">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="b122a-534">Kontroly stavu jsou registrovány <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> ve `Startup.ConfigureServices` službě společně s hostovanou službou.</span><span class="sxs-lookup"><span data-stu-id="b122a-534">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="b122a-535">Vzhledem k tomu, že hostovaná služba musí nastavit vlastnost pro kontrolu stavu, je tato kontrolu také zaregistrována v kontejneru služby (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="b122a-535">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="b122a-536">V rámci kanálu zpracování aplikace v nástroji se kontrolují stav volání middleware `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b122a-536">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="b122a-537">V ukázkové aplikaci jsou koncové body kontroly stavu vytvořeny v `/health/ready` pro kontrolu připravenosti a `/health/live` pro kontrolu živého provozu.</span><span class="sxs-lookup"><span data-stu-id="b122a-537">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="b122a-538">Kontrola připravenosti filtruje kontrolu stavu na kontrolu stavu se `ready` značkou.</span><span class="sxs-lookup"><span data-stu-id="b122a-538">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="b122a-539">Kontrola živého odfiltruje `StartupHostedServiceHealthCheck` vrácení se `false` změnami v [HealthCheckOptions. predikátu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Další informace najdete v tématu [filtrování kontrol stavu](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="b122a-539">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

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

<span data-ttu-id="b122a-540">Pokud chcete spustit scénář konfigurace připravenosti/živých aplikací pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="b122a-540">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="b122a-541">V prohlížeči navštivte několikrát, `/health/ready` dokud neuplyne 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="b122a-541">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="b122a-542">Zprávy o kontrole stavu nejsou v *pořádku* po dobu prvních 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="b122a-542">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="b122a-543">Po 15 sekundách koncový bod hlásí stav *v pořádku*, který odráží dokončení dlouhotrvající úlohy hostované službou.</span><span class="sxs-lookup"><span data-stu-id="b122a-543">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="b122a-544">V tomto příkladu se vytvoří také Vydavatel kontroly stavu ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace), který spouští první kontrolu připravenosti se dvěma sekundami.</span><span class="sxs-lookup"><span data-stu-id="b122a-544">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="b122a-545">Další informace najdete v části [Vydavatel kontroly stavu](#health-check-publisher) .</span><span class="sxs-lookup"><span data-stu-id="b122a-545">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="b122a-546">Příklad Kubernetes</span><span class="sxs-lookup"><span data-stu-id="b122a-546">Kubernetes example</span></span>

<span data-ttu-id="b122a-547">Používání samostatné kontroly připravenosti a živých kontrol je užitečné v prostředí, jako je [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="b122a-547">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="b122a-548">V Kubernetes může být aplikace nutná k provádění časově náročné práce při spuštění před přijetím požadavků, jako je například test dostupnosti podkladové databáze.</span><span class="sxs-lookup"><span data-stu-id="b122a-548">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="b122a-549">Použití samostatných kontrol umožňuje nástroji Orchestrator rozlišit, jestli aplikace funguje, ale ještě není připravená, nebo jestli se aplikace nedala spustit.</span><span class="sxs-lookup"><span data-stu-id="b122a-549">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="b122a-550">Další informace o testování připravenosti a provozní sondy v Kubernetes najdete v tématu [Konfigurace živých a připravených sond](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) v dokumentaci k Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="b122a-550">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="b122a-551">Následující příklad ukazuje konfiguraci testu připravenosti na Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="b122a-551">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="b122a-552">Test založený na metrikách s vlastním zapisovačem odpovědí</span><span class="sxs-lookup"><span data-stu-id="b122a-552">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="b122a-553">Ukázková aplikace ukazuje kontrolu stavu paměti pomocí vlastního zapisovače odpovědí.</span><span class="sxs-lookup"><span data-stu-id="b122a-553">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="b122a-554">`MemoryHealthCheck`hlásí stav není v pořádku, pokud aplikace používá více než danou prahovou hodnotu paměti (1 GB v ukázkové aplikaci).</span><span class="sxs-lookup"><span data-stu-id="b122a-554">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="b122a-555"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>Zahrnuje informace o uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="b122a-555">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="b122a-556">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b122a-556">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b122a-557">Místo povolení kontroly stavu předáním <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `MemoryHealthCheck` jako služby je zaregistrována jako služba.</span><span class="sxs-lookup"><span data-stu-id="b122a-557">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="b122a-558">Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrované služby jsou k dispozici pro služby kontroly stavu a middleware.</span><span class="sxs-lookup"><span data-stu-id="b122a-558">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="b122a-559">Službu pro kontrolu stavu doporučujeme zaregistrovat jako služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="b122a-559">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="b122a-560">V ukázkové aplikaci (*CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="b122a-560">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="b122a-561">V rámci kanálu zpracování aplikace v nástroji se kontrolují stav volání middleware `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b122a-561">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="b122a-562">`WriteResponse`Delegát poskytne `ResponseWriter` vlastnost pro výstup vlastní odpovědi JSON, když se spustí kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="b122a-562">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

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

<span data-ttu-id="b122a-563">`WriteResponse`Metoda zformátuje `CompositeHealthCheckResult` objekt do objektu JSON a poskytne výstup JSON pro odpověď kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="b122a-563">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="b122a-564">Pokud chcete spustit test založený na metrikách s vlastním výstupem zapisovače odpovědí pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="b122a-564">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="b122a-565">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje scénáře kontroly stavu založené na metrikách, včetně úložného prostoru na disku a maximální hodnoty živé kontroly.</span><span class="sxs-lookup"><span data-stu-id="b122a-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="b122a-566">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="b122a-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="b122a-567">Filtrovat podle portu</span><span class="sxs-lookup"><span data-stu-id="b122a-567">Filter by port</span></span>

<span data-ttu-id="b122a-568">Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> s portem omezí požadavky na kontrolu stavu na zadaný port.</span><span class="sxs-lookup"><span data-stu-id="b122a-568">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="b122a-569">Obvykle se používá v prostředí kontejneru k vystavení portu pro monitorovací služby.</span><span class="sxs-lookup"><span data-stu-id="b122a-569">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="b122a-570">Ukázková aplikace nakonfiguruje port pomocí [poskytovatele konfigurace proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b122a-570">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="b122a-571">Port je nastaven v *launchSettings.jsv* souboru a předán poskytovateli konfigurace prostřednictvím proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="b122a-571">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="b122a-572">Také je nutné nakonfigurovat server tak, aby naslouchal žádosti na portu pro správu.</span><span class="sxs-lookup"><span data-stu-id="b122a-572">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="b122a-573">Pokud chcete ukázkovou aplikaci použít k předvedení konfigurace portů pro správu, vytvořte *launchSettings.js* v souboru ve složce *Properties (vlastnosti* ).</span><span class="sxs-lookup"><span data-stu-id="b122a-573">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="b122a-574">Následující *vlastnosti/launchSettings.jsv* souboru v ukázkové aplikaci nejsou součástí souborů projektu ukázkové aplikace a je nutné je vytvořit ručně:</span><span class="sxs-lookup"><span data-stu-id="b122a-574">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="b122a-575">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b122a-575">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b122a-576">Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> Určuje port pro správu (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="b122a-576">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="b122a-577">Vytváření *launchSettings.js* v souboru v ukázkové aplikaci se vyhnete tak, že v kódu nastavíte adresy URL a port pro správu explicitně.</span><span class="sxs-lookup"><span data-stu-id="b122a-577">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="b122a-578">V *program.cs* , kde <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> je vytvořena, přidejte volání do <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> a poskytněte normální koncový bod odpovědi aplikace a koncový bod portu pro správu.</span><span class="sxs-lookup"><span data-stu-id="b122a-578">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="b122a-579">V *ManagementPortStartup.cs* <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> , kde je volána, určete explicitně port pro správu.</span><span class="sxs-lookup"><span data-stu-id="b122a-579">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="b122a-580">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b122a-580">*Program.cs*:</span></span>
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
> <span data-ttu-id="b122a-581">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="b122a-581">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="b122a-582">Pokud chcete spustit scénář konfigurace portu pro správu pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="b122a-582">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="b122a-583">Distribuce knihovny kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-583">Distribute a health check library</span></span>

<span data-ttu-id="b122a-584">Postup při distribuci kontroly stavu jako knihovny:</span><span class="sxs-lookup"><span data-stu-id="b122a-584">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="b122a-585">Zapište kontrolu stavu, která implementuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní jako samostatnou třídu.</span><span class="sxs-lookup"><span data-stu-id="b122a-585">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="b122a-586">Třída může spoléhat na [vkládání závislostí (di)](xref:fundamentals/dependency-injection), aktivovat typ a [pojmenované možnosti](xref:fundamentals/configuration/options) pro přístup k datům konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b122a-586">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="b122a-587">V logice kontroly stavu pro `CheckHealthAsync` :</span><span class="sxs-lookup"><span data-stu-id="b122a-587">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="b122a-588">`data1`a `data2` jsou použity v metodě pro spuštění logiky kontroly stavu testu.</span><span class="sxs-lookup"><span data-stu-id="b122a-588">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="b122a-589">`AccessViolationException`je zpracována.</span><span class="sxs-lookup"><span data-stu-id="b122a-589">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="b122a-590">V případě <xref:System.AccessViolationException> , že dojde k chybě, se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> vrátí s a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> umožní uživatelům konfigurovat stav selhání kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-590">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="b122a-591">Zapište metodu rozšíření s parametry, které aplikace spotřebovávají ve své `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="b122a-591">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="b122a-592">V následujícím příkladu předpokládejme následující signatura metody kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="b122a-592">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="b122a-593">Předchozí signatura indikuje, že `ExampleHealthCheck` pro zpracování logiky sondy kontroly stavu vyžaduje další data.</span><span class="sxs-lookup"><span data-stu-id="b122a-593">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="b122a-594">Data jsou k dispozici delegátovi použitému k vytvoření instance kontroly stavu při registraci kontroly stavu s metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="b122a-594">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="b122a-595">V následujícím příkladu volající určuje nepovinné:</span><span class="sxs-lookup"><span data-stu-id="b122a-595">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="b122a-596">název kontroly stavu ( `name` ).</span><span class="sxs-lookup"><span data-stu-id="b122a-596">health check name (`name`).</span></span> <span data-ttu-id="b122a-597">Pokud `null` se `example_health_check` používá.</span><span class="sxs-lookup"><span data-stu-id="b122a-597">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="b122a-598">datový bod řetězce pro kontrolu stavu ( `data1` ).</span><span class="sxs-lookup"><span data-stu-id="b122a-598">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="b122a-599">celočíselný datový bod pro kontrolu stavu ( `data2` ).</span><span class="sxs-lookup"><span data-stu-id="b122a-599">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="b122a-600">Pokud `null` se `1` používá.</span><span class="sxs-lookup"><span data-stu-id="b122a-600">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="b122a-601">stav selhání ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ).</span><span class="sxs-lookup"><span data-stu-id="b122a-601">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="b122a-602">Výchozí formát je `null`.</span><span class="sxs-lookup"><span data-stu-id="b122a-602">The default is `null`.</span></span> <span data-ttu-id="b122a-603">Pokud `null` je [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) není v pořádku, je hlášen stav selhání.</span><span class="sxs-lookup"><span data-stu-id="b122a-603">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="b122a-604">značky ( `IEnumerable<string>` ).</span><span class="sxs-lookup"><span data-stu-id="b122a-604">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="b122a-605">Vydavatel kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="b122a-605">Health Check Publisher</span></span>

<span data-ttu-id="b122a-606">Když <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> se přidá do kontejneru služby, systém kontroly stavu pravidelně spouští kontroly stavu a volání `PublishAsync` s výsledkem.</span><span class="sxs-lookup"><span data-stu-id="b122a-606">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="b122a-607">To je užitečné ve scénáři sledování stavu založeném na nabízených oznámeních, který očekává, že každý proces bude pravidelně volat monitorovací systém, aby se zjistil stav.</span><span class="sxs-lookup"><span data-stu-id="b122a-607">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="b122a-608"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>Rozhraní má jedinou metodu:</span><span class="sxs-lookup"><span data-stu-id="b122a-608">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="b122a-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>umožňuje nastavit:</span><span class="sxs-lookup"><span data-stu-id="b122a-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="b122a-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Počáteční zpoždění použité po spuštění aplikace před spuštěním <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instancí.</span><span class="sxs-lookup"><span data-stu-id="b122a-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="b122a-611">Zpoždění se použije jednou při spuštění a nevztahuje se na další iterace.</span><span class="sxs-lookup"><span data-stu-id="b122a-611">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="b122a-612">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="b122a-612">The default value is five seconds.</span></span>
* <span data-ttu-id="b122a-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Období <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> provádění.</span><span class="sxs-lookup"><span data-stu-id="b122a-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="b122a-614">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="b122a-614">The default value is 30 seconds.</span></span>
* <span data-ttu-id="b122a-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> je `null` (výchozí), služba Vydavatel kontroly stavu spustí všechny zaregistrované kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="b122a-616">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která filtruje sadu kontrol.</span><span class="sxs-lookup"><span data-stu-id="b122a-616">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="b122a-617">Predikát je vyhodnocen každou periodu.</span><span class="sxs-lookup"><span data-stu-id="b122a-617">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="b122a-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Časový limit pro spuštění kontrol stavu pro všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance.</span><span class="sxs-lookup"><span data-stu-id="b122a-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="b122a-619">Použijte <xref:System.Threading.Timeout.InfiniteTimeSpan> k provedení bez časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b122a-619">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="b122a-620">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="b122a-620">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="b122a-621">Ve verzi ASP.NET Core 2,2 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> není nastavení dodrženo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementací. nastavuje hodnotu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> .</span><span class="sxs-lookup"><span data-stu-id="b122a-621">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="b122a-622">Tento problém byl vyřešen v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="b122a-622">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="b122a-623">V ukázkové aplikaci `ReadinessPublisher` je <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace.</span><span class="sxs-lookup"><span data-stu-id="b122a-623">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="b122a-624">Stav kontroly stavu se zaznamená do protokolu pro každé vrácení se změnami buď:</span><span class="sxs-lookup"><span data-stu-id="b122a-624">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="b122a-625">Informace ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ), pokud je stav kontrol stavu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .</span><span class="sxs-lookup"><span data-stu-id="b122a-625">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="b122a-626">Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ), pokud je stav buď <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> nebo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .</span><span class="sxs-lookup"><span data-stu-id="b122a-626">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="b122a-627">V příkladu ukázkové aplikace `LivenessProbeStartup` `StartupHostedService` má kontroler připravenosti dvě sekundy na spuštění a spustí kontrolu každých 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="b122a-627">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="b122a-628">Chcete-li aktivovat <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementaci, ukázka se registruje `ReadinessPublisher` jako služba s jedním prvkem v kontejneru [Injektáže (di) pro vkládání závislostí](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="b122a-628">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="b122a-629">Následující alternativní řešení povoluje přidání <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance do kontejneru služby, pokud již byla do aplikace přidána jedna nebo více dalších hostovaných služeb.</span><span class="sxs-lookup"><span data-stu-id="b122a-629">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="b122a-630">Toto řešení nebude v ASP.NET Core 3,0 vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="b122a-630">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
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
> <span data-ttu-id="b122a-631">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="b122a-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="b122a-632">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="b122a-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="b122a-633">Omezení kontrol stavu pomocí MapWhen</span><span class="sxs-lookup"><span data-stu-id="b122a-633">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="b122a-634">Použijte <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> k podmíněně větvení kanálu žádostí pro koncové body kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="b122a-634">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="b122a-635">V následujícím příkladu `MapWhen` větví kanál žádosti o aktivaci middlewaru pro kontrolu stavu, pokud se pro koncový bod přijme požadavek Get `api/HealthCheck` :</span><span class="sxs-lookup"><span data-stu-id="b122a-635">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="b122a-636">Další informace naleznete v tématu <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="b122a-636">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
