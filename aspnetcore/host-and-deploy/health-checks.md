---
<span data-ttu-id="36bd3-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="36bd3-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="36bd3-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="36bd3-102">'Blazor'</span></span>
- <span data-ttu-id="36bd3-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="36bd3-103">'Identity'</span></span>
- <span data-ttu-id="36bd3-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="36bd3-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="36bd3-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="36bd3-105">'Razor'</span></span>
- <span data-ttu-id="36bd3-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="36bd3-106">'SignalR' uid:</span></span> 

---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="36bd3-107">Kontroly stavu v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="36bd3-107">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="36bd3-108">Od [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="36bd3-108">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36bd3-109">ASP.NET Core nabízí kontrolu stavu middlewaru a knihovny pro vytváření sestav stavu součástí infrastruktury aplikace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-109">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="36bd3-110">Kontroly stavu jsou zpřístupněné aplikací jako koncové body HTTP.</span><span class="sxs-lookup"><span data-stu-id="36bd3-110">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="36bd3-111">Koncové body kontroly stavu je možné nakonfigurovat pro nejrůznější scénáře monitorování v reálném čase:</span><span class="sxs-lookup"><span data-stu-id="36bd3-111">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="36bd3-112">Sondy stavu můžou použít orchestrace kontejnerů a nástroje pro vyrovnávání zatížení ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-112">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="36bd3-113">Například produkt Orchestrator může reagovat na selhání kontroly stavu zastavením zavedení nasazení nebo restartováním kontejneru.</span><span class="sxs-lookup"><span data-stu-id="36bd3-113">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="36bd3-114">Nástroj pro vyrovnávání zatížení může reagovat na poškozenou aplikaci směrováním provozu mimo instanci selhání do stavu v pořádku.</span><span class="sxs-lookup"><span data-stu-id="36bd3-114">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="36bd3-115">Použití paměti, disku a dalších prostředků fyzického serveru se dá monitorovat v dobrém stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-115">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="36bd3-116">Kontroly stavu můžou testovat závislosti aplikace, jako jsou databáze a externí koncové body služby, aby se potvrdila dostupnost a normální fungování.</span><span class="sxs-lookup"><span data-stu-id="36bd3-116">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="36bd3-117">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36bd3-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="36bd3-118">Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-118">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="36bd3-119">Pokud chcete pro daný scénář spustit ukázkovou aplikaci, použijte příkaz [dotnet Run](/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="36bd3-119">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="36bd3-120">Podrobnosti o tom, jak používat ukázkovou aplikaci, najdete v souboru *Readme.MD* ukázkové aplikace a v popisech scénářů v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-120">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="36bd3-121">Požadavky</span><span class="sxs-lookup"><span data-stu-id="36bd3-121">Prerequisites</span></span>

<span data-ttu-id="36bd3-122">Kontroly stavu se obvykle používají s externí službou monitorování nebo nástrojem Orchestrator Container Service ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-122">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="36bd3-123">Před přidáním kontrol stavu do aplikace se rozhodněte, který monitorovací systém se má použít.</span><span class="sxs-lookup"><span data-stu-id="36bd3-123">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="36bd3-124">Monitorovací systém vyhodnotí, jaké typy kontrol stavu se mají vytvořit a jak nakonfigurovat jejich koncové body.</span><span class="sxs-lookup"><span data-stu-id="36bd3-124">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="36bd3-125">Na balíček [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) se odkazuje implicitně pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-125">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="36bd3-126">Chcete-li provádět kontroly stavu pomocí Entity Framework Core, přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) .</span><span class="sxs-lookup"><span data-stu-id="36bd3-126">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="36bd3-127">Ukázková aplikace poskytuje spouštěcí kód k předvedení kontrol stavu pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="36bd3-127">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="36bd3-128">Scénář [testu databáze](#database-probe) kontroluje stav připojení k databázi pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="36bd3-128">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="36bd3-129">Scénář [testu DbContext](#entity-framework-core-dbcontext-probe) zkontroluje databázi pomocí EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-129">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="36bd3-130">K prozkoumání scénářů databáze se jedná o ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="36bd3-130">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="36bd3-131">Vytvoří databázi a poskytne připojovací řetězec v souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="36bd3-131">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="36bd3-132">Obsahuje následující odkazy na balíček v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-132">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="36bd3-133">AspNetCore. HealthChecks. SqlServer</span><span class="sxs-lookup"><span data-stu-id="36bd3-133">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="36bd3-134">Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="36bd3-134">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="36bd3-135">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="36bd3-135">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="36bd3-136">Další scénář kontroly stavu ukazuje, jak filtrovat kontroly stavu na port pro správu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-136">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="36bd3-137">Ukázková aplikace vyžaduje, abyste vytvořili soubor *Properties/launchSettings. JSON* , který obsahuje adresu URL pro správu a port pro správu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-137">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="36bd3-138">Další informace naleznete v části [Filter by port](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="36bd3-138">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="36bd3-139">Základní sonda stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-139">Basic health probe</span></span>

<span data-ttu-id="36bd3-140">U mnoha aplikací je základní konfigurace sondy stavu, která oznamuje dostupnost aplikace pro zpracování požadavků (*živý*), ke zjištění stavu aplikace dostačující.</span><span class="sxs-lookup"><span data-stu-id="36bd3-140">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="36bd3-141">Základní konfigurace registruje služby kontroly stavu a volá middleware kontroly stavu, aby reagovala na koncový bod adresy URL s odpovědí na stav.</span><span class="sxs-lookup"><span data-stu-id="36bd3-141">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="36bd3-142">Ve výchozím nastavení nejsou k otestování konkrétní závislosti nebo subsystému registrovány žádné konkrétní kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-142">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="36bd3-143">Aplikace se považuje za v pořádku, pokud dokáže reagovat na adresu URL koncového bodu stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-143">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="36bd3-144">Výchozí zapisovač odpovědí zapisuje stav ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) jako odpověď ve formátu prostého textu zpátky klientovi, což značí buď [funkčnosti. dobrý](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)stav, [funkčnosti. Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status není v pořádku.</span><span class="sxs-lookup"><span data-stu-id="36bd3-144">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="36bd3-145">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-145">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="36bd3-146">Vytvořte koncový bod kontroly stavu voláním `MapHealthChecks` v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-146">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="36bd3-147">V ukázkové aplikaci se koncový bod kontroly stavu vytvoří `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bd3-147">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="36bd3-148">Pokud chcete spustit scénář základní konfigurace pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="36bd3-148">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="36bd3-149">Ukázka Docker</span><span class="sxs-lookup"><span data-stu-id="36bd3-149">Docker example</span></span>

<span data-ttu-id="36bd3-150">[Docker](xref:host-and-deploy/docker/index) nabízí vestavěnou `HEALTHCHECK` direktivu, která se dá použít ke kontrole stavu aplikace, která používá konfiguraci základní kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-150">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="36bd3-151">Vytvořit kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-151">Create health checks</span></span>

<span data-ttu-id="36bd3-152">Kontroly stavu jsou vytvářeny implementací <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="36bd3-152">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="36bd3-153"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>Metoda vrátí hodnotu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> , která označuje stav jako `Healthy` , `Degraded` nebo `Unhealthy` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-153">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="36bd3-154">Výsledek je zapsán jako odpověď v podobě prostého textu s konfigurovatelným stavovým kódem (konfigurace je popsána v části [Možnosti kontroly stavu](#health-check-options) ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-154">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="36bd3-155"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>může také vracet volitelné páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="36bd3-155"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="36bd3-156">Následující `ExampleHealthCheck` Třída ukazuje rozložení kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-156">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="36bd3-157">Logika kontrol stavu je umístěna v `CheckHealthAsync` metodě.</span><span class="sxs-lookup"><span data-stu-id="36bd3-157">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="36bd3-158">Následující příklad nastaví fiktivní proměnnou `healthCheckResultHealthy` na `true` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-158">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="36bd3-159">Pokud je hodnota `healthCheckResultHealthy` nastavena na `false` , je vrácena [HealthCheckResult.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status není v pořádku.</span><span class="sxs-lookup"><span data-stu-id="36bd3-159">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

## <a name="register-health-check-services"></a><span data-ttu-id="36bd3-160">Registrovat služby kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-160">Register health check services</span></span>

<span data-ttu-id="36bd3-161">`ExampleHealthCheck`Typ se přidá do služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="36bd3-161">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="36bd3-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>Přetížení znázorněné v následujícím příkladu nastaví stav selhání ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) na hodnotu ohlásit, když při kontrole stavu nahlásí chybu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-162">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="36bd3-163">Pokud je stav selhání nastavený na `null` (výchozí), [funkčnosti](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) se hlásí jako chybné.</span><span class="sxs-lookup"><span data-stu-id="36bd3-163">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="36bd3-164">Toto přetížení je užitečným scénářem pro autory knihoven, kde stav selhání uvedený v knihovně vynutila aplikace, když dojde k selhání kontroly stavu, pokud se při implementaci kontroly stavu splní nastavení.</span><span class="sxs-lookup"><span data-stu-id="36bd3-164">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="36bd3-165">*Značky* lze použít k filtrování kontrol stavu (popsaných dále v oddílu [Filtr kontrol stavu](#filter-health-checks) ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-165">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="36bd3-166"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>lze také spustit funkci lambda.</span><span class="sxs-lookup"><span data-stu-id="36bd3-166"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="36bd3-167">V následujícím příkladu je název kontroly stavu zadán jako `Example` a zaškrtávací políčka vždy vrátí dobrý stav:</span><span class="sxs-lookup"><span data-stu-id="36bd3-167">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="36bd3-168">Volání <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> předání argumentů do implementace kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-168">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="36bd3-169">V následujícím příkladu `TestHealthCheckWithArgs` akceptuje celé číslo a řetězec, který se použije při <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> volání:</span><span class="sxs-lookup"><span data-stu-id="36bd3-169">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

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

<span data-ttu-id="36bd3-170">`TestHealthCheckWithArgs`je zaregistrován voláním `AddTypeActivatedCheck` s celým číslem a řetězcem předaným implementaci:</span><span class="sxs-lookup"><span data-stu-id="36bd3-170">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="36bd3-171">Použití směrování kontrol stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-171">Use Health Checks Routing</span></span>

<span data-ttu-id="36bd3-172">V `Startup.Configure` nástroji zavolejte `MapHealthChecks` na tvůrce koncového bodu s adresou URL koncového bodu nebo relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="36bd3-172">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="36bd3-173">Vyžadovat hostitele</span><span class="sxs-lookup"><span data-stu-id="36bd3-173">Require host</span></span>

<span data-ttu-id="36bd3-174">Zavolejte `RequireHost` k zadání jednoho nebo více povolených hostitelů pro koncový bod kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-174">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="36bd3-175">Hostitelé by měli být místo punycodei Unicode a můžou zahrnovat port.</span><span class="sxs-lookup"><span data-stu-id="36bd3-175">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="36bd3-176">Pokud není zadaná kolekce, bude přijat libovolný hostitel.</span><span class="sxs-lookup"><span data-stu-id="36bd3-176">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="36bd3-177">Další informace naleznete v části [Filter by port](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="36bd3-177">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="36bd3-178">Vyžadovat autorizaci</span><span class="sxs-lookup"><span data-stu-id="36bd3-178">Require authorization</span></span>

<span data-ttu-id="36bd3-179">Voláním `RequireAuthorization` pro spuštění autorizačního middleware na koncovém bodu žádosti o kontrolu stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-179">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="36bd3-180">`RequireAuthorization`Přetížení přijímá jednu nebo více autorizačních zásad.</span><span class="sxs-lookup"><span data-stu-id="36bd3-180">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="36bd3-181">Pokud zásada není zadaná, použije se výchozí zásada autorizace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-181">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="36bd3-182">Povolení žádostí nepůvodního zdroje (CORS)</span><span class="sxs-lookup"><span data-stu-id="36bd3-182">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="36bd3-183">I když ruční provádění kontrol stavu z prohlížeče není běžným scénářem použití, je možné middleware CORS povolit voláním `RequireCors` na koncové body kontrol stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-183">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="36bd3-184">`RequireCors`Přetížení přijímá delegáta tvůrců zásad CORS ( `CorsPolicyBuilder` ) nebo název zásady.</span><span class="sxs-lookup"><span data-stu-id="36bd3-184">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="36bd3-185">Pokud zásada není zadaná, použije se výchozí zásada CORS.</span><span class="sxs-lookup"><span data-stu-id="36bd3-185">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="36bd3-186">Další informace naleznete v tématu <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="36bd3-186">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="36bd3-187">Možnosti kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-187">Health check options</span></span>

<span data-ttu-id="36bd3-188"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>Poskytněte možnost přizpůsobení chování kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-188"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="36bd3-189">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-189">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="36bd3-190">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="36bd3-190">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="36bd3-191">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="36bd3-191">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="36bd3-192">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="36bd3-192">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="36bd3-193">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-193">Filter health checks</span></span>

<span data-ttu-id="36bd3-194">Ve výchozím nastavení middleware pro kontrolu stavu spouští všechny zaregistrované kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-194">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="36bd3-195">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která vrací logickou hodnotu pro <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> možnost.</span><span class="sxs-lookup"><span data-stu-id="36bd3-195">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="36bd3-196">V následujícím příkladu `Bar` je kontrole stavu Filtrováno podle značky ( `bar_tag` ) v podmíněném příkazu funkce, kde `true` je vrácena pouze v případě, že <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> odpovídá vlastnost kontroly stavu `foo_tag` nebo `baz_tag` :</span><span class="sxs-lookup"><span data-stu-id="36bd3-196">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="36bd3-197">V `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36bd3-197">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="36bd3-198">V nástroji `Startup.Configure` `Predicate` odfiltruje kontrolu stavu pruhů.</span><span class="sxs-lookup"><span data-stu-id="36bd3-198">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="36bd3-199">Pouze foo a Baz Execute:</span><span class="sxs-lookup"><span data-stu-id="36bd3-199">Only Foo and Baz execute.:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="36bd3-200">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="36bd3-200">Customize the HTTP status code</span></span>

<span data-ttu-id="36bd3-201">Slouží <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> k přizpůsobení mapování stavu na stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="36bd3-201">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="36bd3-202">Následující <xref:Microsoft.AspNetCore.Http.StatusCodes> přiřazení jsou výchozí hodnoty používané middlewarem.</span><span class="sxs-lookup"><span data-stu-id="36bd3-202">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="36bd3-203">Změňte hodnoty stavového kódu tak, aby splňovaly vaše požadavky.</span><span class="sxs-lookup"><span data-stu-id="36bd3-203">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="36bd3-204">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="36bd3-204">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="36bd3-205">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="36bd3-205">Suppress cache headers</span></span>

<span data-ttu-id="36bd3-206"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Určuje, zda middleware kontroluje stav přidává hlavičky protokolu HTTP do odezvy sondy, aby nedocházelo k ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="36bd3-206"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="36bd3-207">Pokud je hodnota `false` (výchozí), middleware nastaví nebo přepíše `Cache-Control` `Expires` hlavičky, a, `Pragma` aby nedocházelo k ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="36bd3-207">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="36bd3-208">Pokud je hodnota `true` , middleware neupraví hlavičky mezipaměti odpovědi.</span><span class="sxs-lookup"><span data-stu-id="36bd3-208">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="36bd3-209">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="36bd3-209">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="36bd3-210">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="36bd3-210">Customize output</span></span>

<span data-ttu-id="36bd3-211">V `Startup.Configure` sadě nastavte možnost [HealthCheckOptions. ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) na delegáta pro zápis odpovědi:</span><span class="sxs-lookup"><span data-stu-id="36bd3-211">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="36bd3-212">Výchozí delegát zapisuje minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="36bd3-212">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="36bd3-213">Následující vlastní Delegáti výstupují vlastní odpověď JSON.</span><span class="sxs-lookup"><span data-stu-id="36bd3-213">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="36bd3-214">První příklad z ukázkové aplikace ukazuje, jak používat <xref:System.Text.Json?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="36bd3-214">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="36bd3-215">Druhý příklad ukazuje, jak používat [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/):</span><span class="sxs-lookup"><span data-stu-id="36bd3-215">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="36bd3-216">V ukázkové aplikaci zakomentujte `SYSTEM_TEXT_JSON` [direktivu preprocesoru](xref:index#preprocessor-directives-in-sample-code) v *CustomWriterStartup.cs* , abyste povolili `Newtonsoft.Json` verzi `WriteResponse` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-216">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="36bd3-217">Rozhraní API pro kontroly stavu neposkytuje vestavěnou podporu pro komplexní formáty vrácených hodnot JSON, protože formát je specifický pro váš výběr systému monitorování.</span><span class="sxs-lookup"><span data-stu-id="36bd3-217">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="36bd3-218">Podle potřeby upravte odpověď v předchozích příkladech.</span><span class="sxs-lookup"><span data-stu-id="36bd3-218">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="36bd3-219">Další informace o serializaci JSON s `System.Text.Json` najdete [v tématu jak serializovat a deserializovat JSON v rozhraní .NET](/dotnet/standard/serialization/system-text-json-how-to).</span><span class="sxs-lookup"><span data-stu-id="36bd3-219">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="36bd3-220">Test databáze</span><span class="sxs-lookup"><span data-stu-id="36bd3-220">Database probe</span></span>

<span data-ttu-id="36bd3-221">Kontrola stavu může určit databázový dotaz, který se má spustit jako logický test, aby označoval, zda databáze obvykle reaguje.</span><span class="sxs-lookup"><span data-stu-id="36bd3-221">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="36bd3-222">Ukázková aplikace pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), knihovny kontroly stavu pro aplikace ASP.NET Core, provede kontrolu stavu databáze SQL Server.</span><span class="sxs-lookup"><span data-stu-id="36bd3-222">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="36bd3-223">`AspNetCore.Diagnostics.HealthChecks`spustí `SELECT 1` dotaz na databázi, aby se ověřilo, že připojení k databázi je v pořádku.</span><span class="sxs-lookup"><span data-stu-id="36bd3-223">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="36bd3-224">Při kontrole připojení k databázi pomocí dotazu vyberte dotaz, který se rychle vrátí.</span><span class="sxs-lookup"><span data-stu-id="36bd3-224">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="36bd3-225">Přístup k dotazům spouští riziko přetížení databáze a snížení výkonu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-225">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="36bd3-226">Ve většině případů není nutné spustit testovací dotaz.</span><span class="sxs-lookup"><span data-stu-id="36bd3-226">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="36bd3-227">Stačí pouze provést úspěšné připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="36bd3-227">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="36bd3-228">Pokud zjistíte, že je nutné spustit dotaz, zvolte jednoduchý dotaz SELECT, například `SELECT 1` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-228">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="36bd3-229">Zahrňte odkaz na balíček do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="36bd3-229">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="36bd3-230">Zadejte platný připojovací řetězec databáze do souboru *appSettings. JSON* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-230">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="36bd3-231">Aplikace používá SQL Server databázi s názvem `HealthCheckSample` :</span><span class="sxs-lookup"><span data-stu-id="36bd3-231">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="36bd3-232">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-232">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="36bd3-233">Ukázková aplikace volá `AddSqlServer` metodu s připojovacím řetězcem databáze (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bd3-233">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="36bd3-234">Koncový bod kontroly stavu se vytvoří voláním `MapHealthChecks` v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="36bd3-234">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="36bd3-235">Pokud chcete spustit scénář testu databáze pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="36bd3-235">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="36bd3-236">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="36bd3-236">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="36bd3-237">Test Entity Framework Core DbContext</span><span class="sxs-lookup"><span data-stu-id="36bd3-237">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="36bd3-238">Tato `DbContext` kontrolu potvrdí, že aplikace může komunikovat s databází nakonfigurovanou pro EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-238">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="36bd3-239">Tato `DbContext` kontrolu je podporovaná v aplikacích, které:</span><span class="sxs-lookup"><span data-stu-id="36bd3-239">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="36bd3-240">Použijte [jádro Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="36bd3-240">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="36bd3-241">Zahrňte odkaz na balíček [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="36bd3-241">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="36bd3-242">`AddDbContextCheck<TContext>`zaregistruje kontrolu stavu pro `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-242">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="36bd3-243">`DbContext`Je dodán jako `TContext` Metoda metodě.</span><span class="sxs-lookup"><span data-stu-id="36bd3-243">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="36bd3-244">K dispozici je přetížení pro konfiguraci stavu selhání, značek a vlastního testovacího dotazu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-244">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="36bd3-245">Ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="36bd3-245">By default:</span></span>

* <span data-ttu-id="36bd3-246">`DbContextHealthCheck`Volá `CanConnectAsync` metodu EF Core.</span><span class="sxs-lookup"><span data-stu-id="36bd3-246">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="36bd3-247">Můžete přizpůsobit, která operace je spuštěna při kontrole stavu pomocí `AddDbContextCheck` přetížení metod.</span><span class="sxs-lookup"><span data-stu-id="36bd3-247">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="36bd3-248">Název kontroly stavu je název `TContext` typu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-248">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="36bd3-249">V ukázkové aplikaci `AppDbContext` je k dispozici `AddDbContextCheck` a zaregistrována jako služba `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bd3-249">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="36bd3-250">Koncový bod kontroly stavu se vytvoří voláním `MapHealthChecks` v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="36bd3-250">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="36bd3-251">Pokud chcete spustit `DbContext` scénář sondy pomocí ukázkové aplikace, zkontrolujte, že databáze určená připojovacím řetězcem v instanci SQL Server neexistuje.</span><span class="sxs-lookup"><span data-stu-id="36bd3-251">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="36bd3-252">Pokud databáze existuje, odstraňte ji.</span><span class="sxs-lookup"><span data-stu-id="36bd3-252">If the database exists, delete it.</span></span>

<span data-ttu-id="36bd3-253">Ze složky projektu v příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="36bd3-253">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="36bd3-254">Po spuštění aplikace ověřte stav tak, že v prohlížeči vytvoříte požadavek na `/health` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="36bd3-254">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="36bd3-255">Databáze a `AppDbContext` neexistuje, takže aplikace nabízí následující odpověď:</span><span class="sxs-lookup"><span data-stu-id="36bd3-255">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="36bd3-256">Aktivujte ukázkovou aplikaci pro vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="36bd3-256">Trigger the sample app to create the database.</span></span> <span data-ttu-id="36bd3-257">Vytvořte žádost na `/createdatabase` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-257">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="36bd3-258">Aplikace odpoví:</span><span class="sxs-lookup"><span data-stu-id="36bd3-258">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="36bd3-259">Vytvořte požadavek na `/health` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="36bd3-259">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="36bd3-260">Databáze a kontext existují, takže aplikace reaguje:</span><span class="sxs-lookup"><span data-stu-id="36bd3-260">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="36bd3-261">Aktivujte ukázkovou aplikaci, aby se odstranila databáze.</span><span class="sxs-lookup"><span data-stu-id="36bd3-261">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="36bd3-262">Vytvořte žádost na `/deletedatabase` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-262">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="36bd3-263">Aplikace odpoví:</span><span class="sxs-lookup"><span data-stu-id="36bd3-263">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="36bd3-264">Vytvořte požadavek na `/health` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="36bd3-264">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="36bd3-265">Aplikace poskytuje poškozenou reakci:</span><span class="sxs-lookup"><span data-stu-id="36bd3-265">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="36bd3-266">Samostatné sondy pro připravenost a živý provoz</span><span class="sxs-lookup"><span data-stu-id="36bd3-266">Separate readiness and liveness probes</span></span>

<span data-ttu-id="36bd3-267">V některých scénářích hostování se používá pár kontrol stavu, který rozlišuje dva stavy aplikace:</span><span class="sxs-lookup"><span data-stu-id="36bd3-267">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="36bd3-268">Aplikace funguje, ale ještě není připravená na příjem požadavků.</span><span class="sxs-lookup"><span data-stu-id="36bd3-268">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="36bd3-269">Tento stav je *připravenost*aplikace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-269">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="36bd3-270">Aplikace funguje a reaguje na požadavky.</span><span class="sxs-lookup"><span data-stu-id="36bd3-270">The app is functioning and responding to requests.</span></span> <span data-ttu-id="36bd3-271">Tento stav je *živými*aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="36bd3-271">This state is the app's *liveness*.</span></span>

<span data-ttu-id="36bd3-272">Kontrola připravenosti obvykle provádí rozsáhlejší a časově náročnou sadu kontrol, aby bylo možné zjistit, zda jsou k dispozici všechny subsystémy a prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-272">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="36bd3-273">K dispozici je pouze rychlá kontrolu, která určuje, zda je aplikace k dispozici pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="36bd3-273">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="36bd3-274">Po úspěšném provedení kontroly připravenosti aplikace nemusíte aplikaci navýšit dál s nákladnými sadami kontrol připravenosti, které &mdash; vyžadují kontrolu živého ověření.</span><span class="sxs-lookup"><span data-stu-id="36bd3-274">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="36bd3-275">Ukázková aplikace obsahuje kontrolu stavu, která oznamuje dokončení dlouhotrvající úlohy po spuštění v [hostované službě](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="36bd3-275">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="36bd3-276">`StartupHostedServiceHealthCheck`Zpřístupňuje vlastnost, `StartupTaskCompleted` která hostovaná služba může nastavit na hodnotu `true` po dokončení její dlouhotrvající úlohy (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bd3-276">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="36bd3-277">Dlouhotrvající úloha na pozadí se spouští v [hostované službě](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="36bd3-277">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="36bd3-278">Při uzavírání úkolu `StartupHostedServiceHealthCheck.StartupTaskCompleted` je nastaveno na `true` :</span><span class="sxs-lookup"><span data-stu-id="36bd3-278">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="36bd3-279">Kontroly stavu jsou registrovány <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> ve `Startup.ConfigureServices` službě společně s hostovanou službou.</span><span class="sxs-lookup"><span data-stu-id="36bd3-279">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="36bd3-280">Vzhledem k tomu, že hostovaná služba musí nastavit vlastnost pro kontrolu stavu, je tato kontrolu také zaregistrována v kontejneru služby (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bd3-280">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="36bd3-281">Koncový bod kontroly stavu je vytvořen voláním `MapHealthChecks` v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-281">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="36bd3-282">V ukázkové aplikaci jsou koncové body kontroly stavu vytvořeny v:</span><span class="sxs-lookup"><span data-stu-id="36bd3-282">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="36bd3-283">`/health/ready`pro kontrolu připravenosti.</span><span class="sxs-lookup"><span data-stu-id="36bd3-283">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="36bd3-284">Kontrola připravenosti filtruje kontrolu stavu na kontrolu stavu se `ready` značkou.</span><span class="sxs-lookup"><span data-stu-id="36bd3-284">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="36bd3-285">`/health/live`pro kontrolu živého provozu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-285">`/health/live` for the liveness check.</span></span> <span data-ttu-id="36bd3-286">Kontrola živého filtrování odfiltruje `StartupHostedServiceHealthCheck` vrácením `false` do [HealthCheckOptions. predikátu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Další informace najdete v tématu [filtrování kontrol stavu](#filter-health-checks)).</span><span class="sxs-lookup"><span data-stu-id="36bd3-286">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="36bd3-287">V následujícím příkladu kódu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-287">In the following example code:</span></span>

* <span data-ttu-id="36bd3-288">Kontrola připravenosti používá všechny registrované kontroly se značkou ' Read '.</span><span class="sxs-lookup"><span data-stu-id="36bd3-288">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="36bd3-289">`Predicate`Vyloučí všechny kontroly a vrátí 200 – OK.</span><span class="sxs-lookup"><span data-stu-id="36bd3-289">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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

<span data-ttu-id="36bd3-290">Pokud chcete spustit scénář konfigurace připravenosti/živých aplikací pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="36bd3-290">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="36bd3-291">V prohlížeči navštivte několikrát, `/health/ready` dokud neuplyne 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="36bd3-291">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="36bd3-292">Zprávy o kontrole stavu nejsou v *pořádku* po dobu prvních 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="36bd3-292">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="36bd3-293">Po 15 sekundách koncový bod hlásí stav *v pořádku*, který odráží dokončení dlouhotrvající úlohy hostované službou.</span><span class="sxs-lookup"><span data-stu-id="36bd3-293">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="36bd3-294">V tomto příkladu se vytvoří také Vydavatel kontroly stavu ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace), který spouští první kontrolu připravenosti se dvěma sekundami.</span><span class="sxs-lookup"><span data-stu-id="36bd3-294">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="36bd3-295">Další informace najdete v části [Vydavatel kontroly stavu](#health-check-publisher) .</span><span class="sxs-lookup"><span data-stu-id="36bd3-295">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="36bd3-296">Příklad Kubernetes</span><span class="sxs-lookup"><span data-stu-id="36bd3-296">Kubernetes example</span></span>

<span data-ttu-id="36bd3-297">Používání samostatné kontroly připravenosti a živých kontrol je užitečné v prostředí, jako je [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="36bd3-297">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="36bd3-298">V Kubernetes může být aplikace nutná k provádění časově náročné práce při spuštění před přijetím požadavků, jako je například test dostupnosti podkladové databáze.</span><span class="sxs-lookup"><span data-stu-id="36bd3-298">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="36bd3-299">Použití samostatných kontrol umožňuje nástroji Orchestrator rozlišit, jestli aplikace funguje, ale ještě není připravená, nebo jestli se aplikace nedala spustit.</span><span class="sxs-lookup"><span data-stu-id="36bd3-299">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="36bd3-300">Další informace o testování připravenosti a provozní sondy v Kubernetes najdete v tématu [Konfigurace živých a připravených sond](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) v dokumentaci k Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="36bd3-300">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="36bd3-301">Následující příklad ukazuje konfiguraci testu připravenosti na Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="36bd3-301">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="36bd3-302">Test založený na metrikách s vlastním zapisovačem odpovědí</span><span class="sxs-lookup"><span data-stu-id="36bd3-302">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="36bd3-303">Ukázková aplikace ukazuje kontrolu stavu paměti pomocí vlastního zapisovače odpovědí.</span><span class="sxs-lookup"><span data-stu-id="36bd3-303">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="36bd3-304">`MemoryHealthCheck`hlásí snížený stav, pokud aplikace používá více než určenou prahovou hodnotu paměti (1 GB v ukázkové aplikaci).</span><span class="sxs-lookup"><span data-stu-id="36bd3-304">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="36bd3-305"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>Zahrnuje informace o uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bd3-305">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="36bd3-306">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-306">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="36bd3-307">Místo povolení kontroly stavu předáním <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `MemoryHealthCheck` jako služby je zaregistrována jako služba.</span><span class="sxs-lookup"><span data-stu-id="36bd3-307">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="36bd3-308">Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrované služby jsou k dispozici pro služby kontroly stavu a middleware.</span><span class="sxs-lookup"><span data-stu-id="36bd3-308">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="36bd3-309">Službu pro kontrolu stavu doporučujeme zaregistrovat jako služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="36bd3-309">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="36bd3-310">V *CustomWriterStartup.cs* ukázkové aplikace:</span><span class="sxs-lookup"><span data-stu-id="36bd3-310">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="36bd3-311">Koncový bod kontroly stavu je vytvořen voláním `MapHealthChecks` v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-311">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="36bd3-312">`WriteResponse`Delegát je k dispozici pro <vlastnost Microsoft. AspNetCore. Diagnostics. HealthChecks. HealthCheckOptions.> ResponseWriter pro výstup vlastní odpovědi JSON, když se spustí kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-312">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="36bd3-313">`WriteResponse`Delegát naformátuje `CompositeHealthCheckResult` objekt do objektu JSON a pro odpověď na kontrolu stavu vrátí výstup JSON.</span><span class="sxs-lookup"><span data-stu-id="36bd3-313">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="36bd3-314">Další informace najdete v části [přizpůsobení výstupu](#customize-output) .</span><span class="sxs-lookup"><span data-stu-id="36bd3-314">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="36bd3-315">Pokud chcete spustit test založený na metrikách s vlastním výstupem zapisovače odpovědí pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="36bd3-315">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="36bd3-316">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje scénáře kontroly stavu založené na metrikách, včetně úložného prostoru na disku a maximální hodnoty živé kontroly.</span><span class="sxs-lookup"><span data-stu-id="36bd3-316">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="36bd3-317">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="36bd3-317">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="36bd3-318">Filtrovat podle portu</span><span class="sxs-lookup"><span data-stu-id="36bd3-318">Filter by port</span></span>

<span data-ttu-id="36bd3-319">Zavolejte `RequireHost` `MapHealthChecks` pomocí vzoru adresy URL, který určuje port pro omezení požadavků na kontrolu stavu na zadaný port.</span><span class="sxs-lookup"><span data-stu-id="36bd3-319">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="36bd3-320">Obvykle se používá v prostředí kontejneru k vystavení portu pro monitorovací služby.</span><span class="sxs-lookup"><span data-stu-id="36bd3-320">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="36bd3-321">Ukázková aplikace nakonfiguruje port pomocí [poskytovatele konfigurace proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="36bd3-321">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="36bd3-322">Port je nastaven v souboru *launchSettings. JSON* a předán poskytovateli konfigurace prostřednictvím proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="36bd3-322">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="36bd3-323">Také je nutné nakonfigurovat server tak, aby naslouchal žádosti na portu pro správu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-323">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="36bd3-324">Pokud chcete ukázkovou aplikaci použít k předvedení konfigurace portů pro správu, vytvořte soubor *launchSettings. JSON* ve složce *Properties (vlastnosti* ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-324">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="36bd3-325">Následující *vlastnosti/soubor launchSettings. JSON* v ukázkové aplikaci nejsou součástí souborů projektu ukázkové aplikace a je nutné je vytvořit ručně:</span><span class="sxs-lookup"><span data-stu-id="36bd3-325">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="36bd3-326">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-326">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="36bd3-327">Vytvořte koncový bod kontroly stavu voláním `MapHealthChecks` v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-327">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="36bd3-328">V ukázkové aplikaci volání `RequireHost` na koncový bod v `Startup.Configure` Určuje port pro správu z konfigurace:</span><span class="sxs-lookup"><span data-stu-id="36bd3-328">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="36bd3-329">Koncové body se vytvoří v ukázkové aplikaci v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-329">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="36bd3-330">V následujícím příkladu kódu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-330">In the following example code:</span></span>

* <span data-ttu-id="36bd3-331">Kontrola připravenosti používá všechny registrované kontroly se značkou ' Read '.</span><span class="sxs-lookup"><span data-stu-id="36bd3-331">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="36bd3-332">`Predicate`Vyloučí všechny kontroly a vrátí 200 – OK.</span><span class="sxs-lookup"><span data-stu-id="36bd3-332">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

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
> <span data-ttu-id="36bd3-333">Vytvoření souboru *launchSettings. JSON* v ukázkové aplikaci se vyhnete tak, že v kódu nastavíte port pro správu explicitně.</span><span class="sxs-lookup"><span data-stu-id="36bd3-333">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="36bd3-334">V *program.cs* , kde <xref:Microsoft.Extensions.Hosting.HostBuilder> je vytvořena, přidejte volání do <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> a poskytněte koncový bod portu pro správu aplikace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-334">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="36bd3-335">V `Configure` *ManagementPortStartup.cs*zadejte port pro správu pomocí `RequireHost` :</span><span class="sxs-lookup"><span data-stu-id="36bd3-335">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="36bd3-336">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="36bd3-336">*Program.cs*:</span></span>
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
> <span data-ttu-id="36bd3-337">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="36bd3-337">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="36bd3-338">Pokud chcete spustit scénář konfigurace portu pro správu pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="36bd3-338">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="36bd3-339">Distribuce knihovny kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-339">Distribute a health check library</span></span>

<span data-ttu-id="36bd3-340">Postup při distribuci kontroly stavu jako knihovny:</span><span class="sxs-lookup"><span data-stu-id="36bd3-340">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="36bd3-341">Zapište kontrolu stavu, která implementuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní jako samostatnou třídu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-341">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="36bd3-342">Třída může spoléhat na [vkládání závislostí (di)](xref:fundamentals/dependency-injection), aktivovat typ a [pojmenované možnosti](xref:fundamentals/configuration/options) pro přístup k datům konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-342">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="36bd3-343">V logice kontroly stavu pro `CheckHealthAsync` :</span><span class="sxs-lookup"><span data-stu-id="36bd3-343">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="36bd3-344">`data1`a `data2` jsou použity v metodě pro spuštění logiky kontroly stavu testu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-344">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="36bd3-345">`AccessViolationException`je zpracována.</span><span class="sxs-lookup"><span data-stu-id="36bd3-345">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="36bd3-346">V případě <xref:System.AccessViolationException> , že dojde k chybě, se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> vrátí s a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> umožní uživatelům konfigurovat stav selhání kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-346">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="36bd3-347">Zapište metodu rozšíření s parametry, které aplikace spotřebovávají ve své `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="36bd3-347">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="36bd3-348">V následujícím příkladu předpokládejme následující signatura metody kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-348">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="36bd3-349">Předchozí signatura indikuje, že `ExampleHealthCheck` pro zpracování logiky sondy kontroly stavu vyžaduje další data.</span><span class="sxs-lookup"><span data-stu-id="36bd3-349">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="36bd3-350">Data jsou k dispozici delegátovi použitému k vytvoření instance kontroly stavu při registraci kontroly stavu s metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="36bd3-350">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="36bd3-351">V následujícím příkladu volající určuje nepovinné:</span><span class="sxs-lookup"><span data-stu-id="36bd3-351">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="36bd3-352">název kontroly stavu ( `name` ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-352">health check name (`name`).</span></span> <span data-ttu-id="36bd3-353">Pokud `null` se `example_health_check` používá.</span><span class="sxs-lookup"><span data-stu-id="36bd3-353">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="36bd3-354">datový bod řetězce pro kontrolu stavu ( `data1` ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-354">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="36bd3-355">celočíselný datový bod pro kontrolu stavu ( `data2` ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-355">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="36bd3-356">Pokud `null` se `1` používá.</span><span class="sxs-lookup"><span data-stu-id="36bd3-356">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="36bd3-357">stav selhání ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-357">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="36bd3-358">Výchozí formát je `null`.</span><span class="sxs-lookup"><span data-stu-id="36bd3-358">The default is `null`.</span></span> <span data-ttu-id="36bd3-359">Pokud `null` je [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) není v pořádku, je hlášen stav selhání.</span><span class="sxs-lookup"><span data-stu-id="36bd3-359">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="36bd3-360">značky ( `IEnumerable<string>` ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-360">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="36bd3-361">Vydavatel kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-361">Health Check Publisher</span></span>

<span data-ttu-id="36bd3-362">Když <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> se přidá do kontejneru služby, systém kontroly stavu pravidelně spouští kontroly stavu a volání `PublishAsync` s výsledkem.</span><span class="sxs-lookup"><span data-stu-id="36bd3-362">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="36bd3-363">To je užitečné ve scénáři sledování stavu založeném na nabízených oznámeních, který očekává, že každý proces bude pravidelně volat monitorovací systém, aby se zjistil stav.</span><span class="sxs-lookup"><span data-stu-id="36bd3-363">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="36bd3-364"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>Rozhraní má jedinou metodu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-364">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="36bd3-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>umožňuje nastavit:</span><span class="sxs-lookup"><span data-stu-id="36bd3-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="36bd3-366"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Počáteční zpoždění použité po spuštění aplikace před spuštěním <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instancí.</span><span class="sxs-lookup"><span data-stu-id="36bd3-366"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="36bd3-367">Zpoždění se použije jednou při spuštění a nevztahuje se na další iterace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-367">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="36bd3-368">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="36bd3-368">The default value is five seconds.</span></span>
* <span data-ttu-id="36bd3-369"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Období <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> provádění.</span><span class="sxs-lookup"><span data-stu-id="36bd3-369"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="36bd3-370">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="36bd3-370">The default value is 30 seconds.</span></span>
* <span data-ttu-id="36bd3-371"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> je `null` (výchozí), služba Vydavatel kontroly stavu spustí všechny zaregistrované kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-371"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="36bd3-372">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která filtruje sadu kontrol.</span><span class="sxs-lookup"><span data-stu-id="36bd3-372">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="36bd3-373">Predikát je vyhodnocen každou periodu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-373">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="36bd3-374"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Časový limit pro spuštění kontrol stavu pro všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance.</span><span class="sxs-lookup"><span data-stu-id="36bd3-374"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="36bd3-375">Použijte <xref:System.Threading.Timeout.InfiniteTimeSpan> k provedení bez časového limitu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-375">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="36bd3-376">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="36bd3-376">The default value is 30 seconds.</span></span>

<span data-ttu-id="36bd3-377">V ukázkové aplikaci `ReadinessPublisher` je <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-377">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="36bd3-378">Stav kontroly stavu se protokoluje pro každou kontrolu na úrovni protokolu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-378">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="36bd3-379">Informace ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ), pokud je stav kontrol stavu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .</span><span class="sxs-lookup"><span data-stu-id="36bd3-379">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="36bd3-380">Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ), pokud je stav buď <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> nebo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .</span><span class="sxs-lookup"><span data-stu-id="36bd3-380">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="36bd3-381">V příkladu ukázkové aplikace `LivenessProbeStartup` `StartupHostedService` má kontroler připravenosti dvě sekundy na spuštění a spustí kontrolu každých 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="36bd3-381">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="36bd3-382">Chcete-li aktivovat <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementaci, ukázka se registruje `ReadinessPublisher` jako služba s jedním prvkem v kontejneru [Injektáže (di) pro vkládání závislostí](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="36bd3-382">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="36bd3-383">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="36bd3-383">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="36bd3-384">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="36bd3-384">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="36bd3-385">Omezení kontrol stavu pomocí MapWhen</span><span class="sxs-lookup"><span data-stu-id="36bd3-385">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="36bd3-386">Použijte <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> k podmíněně větvení kanálu žádostí pro koncové body kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-386">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="36bd3-387">V následujícím příkladu `MapWhen` větví kanál žádosti o aktivaci middlewaru pro kontrolu stavu, pokud se pro koncový bod přijme požadavek Get `api/HealthCheck` :</span><span class="sxs-lookup"><span data-stu-id="36bd3-387">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

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

<span data-ttu-id="36bd3-388">Další informace naleznete v tématu <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="36bd3-388">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="36bd3-389">ASP.NET Core nabízí kontrolu stavu middlewaru a knihovny pro vytváření sestav stavu součástí infrastruktury aplikace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-389">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="36bd3-390">Kontroly stavu jsou zpřístupněné aplikací jako koncové body HTTP.</span><span class="sxs-lookup"><span data-stu-id="36bd3-390">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="36bd3-391">Koncové body kontroly stavu je možné nakonfigurovat pro nejrůznější scénáře monitorování v reálném čase:</span><span class="sxs-lookup"><span data-stu-id="36bd3-391">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="36bd3-392">Sondy stavu můžou použít orchestrace kontejnerů a nástroje pro vyrovnávání zatížení ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-392">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="36bd3-393">Například produkt Orchestrator může reagovat na selhání kontroly stavu zastavením zavedení nasazení nebo restartováním kontejneru.</span><span class="sxs-lookup"><span data-stu-id="36bd3-393">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="36bd3-394">Nástroj pro vyrovnávání zatížení může reagovat na poškozenou aplikaci směrováním provozu mimo instanci selhání do stavu v pořádku.</span><span class="sxs-lookup"><span data-stu-id="36bd3-394">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="36bd3-395">Použití paměti, disku a dalších prostředků fyzického serveru se dá monitorovat v dobrém stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-395">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="36bd3-396">Kontroly stavu můžou testovat závislosti aplikace, jako jsou databáze a externí koncové body služby, aby se potvrdila dostupnost a normální fungování.</span><span class="sxs-lookup"><span data-stu-id="36bd3-396">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="36bd3-397">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="36bd3-397">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="36bd3-398">Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-398">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="36bd3-399">Pokud chcete pro daný scénář spustit ukázkovou aplikaci, použijte příkaz [dotnet Run](/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí.</span><span class="sxs-lookup"><span data-stu-id="36bd3-399">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="36bd3-400">Podrobnosti o tom, jak používat ukázkovou aplikaci, najdete v souboru *Readme.MD* ukázkové aplikace a v popisech scénářů v tomto tématu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-400">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="36bd3-401">Požadavky</span><span class="sxs-lookup"><span data-stu-id="36bd3-401">Prerequisites</span></span>

<span data-ttu-id="36bd3-402">Kontroly stavu se obvykle používají s externí službou monitorování nebo nástrojem Orchestrator Container Service ke kontrole stavu aplikace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-402">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="36bd3-403">Před přidáním kontrol stavu do aplikace se rozhodněte, který monitorovací systém se má použít.</span><span class="sxs-lookup"><span data-stu-id="36bd3-403">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="36bd3-404">Monitorovací systém vyhodnotí, jaké typy kontrol stavu se mají vytvořit a jak nakonfigurovat jejich koncové body.</span><span class="sxs-lookup"><span data-stu-id="36bd3-404">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="36bd3-405">Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) .</span><span class="sxs-lookup"><span data-stu-id="36bd3-405">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="36bd3-406">Ukázková aplikace poskytuje spouštěcí kód k předvedení kontrol stavu pro několik scénářů.</span><span class="sxs-lookup"><span data-stu-id="36bd3-406">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="36bd3-407">Scénář [testu databáze](#database-probe) kontroluje stav připojení k databázi pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="36bd3-407">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="36bd3-408">Scénář [testu DbContext](#entity-framework-core-dbcontext-probe) zkontroluje databázi pomocí EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-408">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="36bd3-409">K prozkoumání scénářů databáze se jedná o ukázkovou aplikaci:</span><span class="sxs-lookup"><span data-stu-id="36bd3-409">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="36bd3-410">Vytvoří databázi a poskytne připojovací řetězec v souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="36bd3-410">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="36bd3-411">Obsahuje následující odkazy na balíček v souboru projektu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-411">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="36bd3-412">AspNetCore. HealthChecks. SqlServer</span><span class="sxs-lookup"><span data-stu-id="36bd3-412">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="36bd3-413">Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="36bd3-413">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="36bd3-414">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="36bd3-414">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="36bd3-415">Další scénář kontroly stavu ukazuje, jak filtrovat kontroly stavu na port pro správu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-415">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="36bd3-416">Ukázková aplikace vyžaduje, abyste vytvořili soubor *Properties/launchSettings. JSON* , který obsahuje adresu URL pro správu a port pro správu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-416">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="36bd3-417">Další informace naleznete v části [Filter by port](#filter-by-port) .</span><span class="sxs-lookup"><span data-stu-id="36bd3-417">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="36bd3-418">Základní sonda stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-418">Basic health probe</span></span>

<span data-ttu-id="36bd3-419">U mnoha aplikací je základní konfigurace sondy stavu, která oznamuje dostupnost aplikace pro zpracování požadavků (*živý*), ke zjištění stavu aplikace dostačující.</span><span class="sxs-lookup"><span data-stu-id="36bd3-419">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="36bd3-420">Základní konfigurace registruje služby kontroly stavu a volá middleware kontroly stavu, aby reagovala na koncový bod adresy URL s odpovědí na stav.</span><span class="sxs-lookup"><span data-stu-id="36bd3-420">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="36bd3-421">Ve výchozím nastavení nejsou k otestování konkrétní závislosti nebo subsystému registrovány žádné konkrétní kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-421">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="36bd3-422">Aplikace se považuje za v pořádku, pokud dokáže reagovat na adresu URL koncového bodu stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-422">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="36bd3-423">Výchozí zapisovač odpovědí zapisuje stav ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) jako odpověď ve formátu prostého textu zpátky klientovi, což značí buď [funkčnosti. dobrý](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)stav, [funkčnosti. Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status není v pořádku.</span><span class="sxs-lookup"><span data-stu-id="36bd3-423">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="36bd3-424">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-424">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="36bd3-425">Přidejte koncový bod pro kontrolu stavu middleware s <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> v kanálu zpracování požadavků `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-425">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="36bd3-426">V ukázkové aplikaci se koncový bod kontroly stavu vytvoří `/health` (*BasicStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bd3-426">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

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

<span data-ttu-id="36bd3-427">Pokud chcete spustit scénář základní konfigurace pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="36bd3-427">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="36bd3-428">Ukázka Docker</span><span class="sxs-lookup"><span data-stu-id="36bd3-428">Docker example</span></span>

<span data-ttu-id="36bd3-429">[Docker](xref:host-and-deploy/docker/index) nabízí vestavěnou `HEALTHCHECK` direktivu, která se dá použít ke kontrole stavu aplikace, která používá konfiguraci základní kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-429">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="36bd3-430">Vytvořit kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-430">Create health checks</span></span>

<span data-ttu-id="36bd3-431">Kontroly stavu jsou vytvářeny implementací <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní.</span><span class="sxs-lookup"><span data-stu-id="36bd3-431">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="36bd3-432"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>Metoda vrátí hodnotu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> , která označuje stav jako `Healthy` , `Degraded` nebo `Unhealthy` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-432">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="36bd3-433">Výsledek je zapsán jako odpověď v podobě prostého textu s konfigurovatelným stavovým kódem (konfigurace je popsána v části [Možnosti kontroly stavu](#health-check-options) ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-433">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="36bd3-434"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>může také vracet volitelné páry klíč-hodnota.</span><span class="sxs-lookup"><span data-stu-id="36bd3-434"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="36bd3-435">Příklad kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-435">Example health check</span></span>

<span data-ttu-id="36bd3-436">Následující `ExampleHealthCheck` Třída ukazuje rozložení kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-436">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="36bd3-437">Logika kontrol stavu je umístěna v `CheckHealthAsync` metodě.</span><span class="sxs-lookup"><span data-stu-id="36bd3-437">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="36bd3-438">Následující příklad nastaví fiktivní proměnnou `healthCheckResultHealthy` na `true` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-438">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="36bd3-439">Pokud je hodnota `healthCheckResultHealthy` nastavena na `false` , je vrácena [HealthCheckResult.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status není v pořádku.</span><span class="sxs-lookup"><span data-stu-id="36bd3-439">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

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

### <a name="register-health-check-services"></a><span data-ttu-id="36bd3-440">Registrovat služby kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-440">Register health check services</span></span>

<span data-ttu-id="36bd3-441">`ExampleHealthCheck`Typ se přidá do služby kontroly stavu v `Startup.ConfigureServices` nástroji <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> :</span><span class="sxs-lookup"><span data-stu-id="36bd3-441">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="36bd3-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>Přetížení znázorněné v následujícím příkladu nastaví stav selhání ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) na hodnotu ohlásit, když při kontrole stavu nahlásí chybu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-442">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="36bd3-443">Pokud je stav selhání nastavený na `null` (výchozí), [funkčnosti](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) se hlásí jako chybné.</span><span class="sxs-lookup"><span data-stu-id="36bd3-443">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="36bd3-444">Toto přetížení je užitečným scénářem pro autory knihoven, kde stav selhání uvedený v knihovně vynutila aplikace, když dojde k selhání kontroly stavu, pokud se při implementaci kontroly stavu splní nastavení.</span><span class="sxs-lookup"><span data-stu-id="36bd3-444">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="36bd3-445">*Značky* lze použít k filtrování kontrol stavu (popsaných dále v oddílu [Filtr kontrol stavu](#filter-health-checks) ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-445">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="36bd3-446"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>lze také spustit funkci lambda.</span><span class="sxs-lookup"><span data-stu-id="36bd3-446"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="36bd3-447">V následujícím `Startup.ConfigureServices` příkladu je název kontroly stavu zadán jako `Example` a zaškrtávací políčka vždy vrátí dobrý stav:</span><span class="sxs-lookup"><span data-stu-id="36bd3-447">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="36bd3-448">Použití middlewaru pro kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-448">Use Health Checks Middleware</span></span>

<span data-ttu-id="36bd3-449">V aplikaci `Startup.Configure` volejte <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> kanál zpracování s adresou URL koncového bodu nebo relativní cestou:</span><span class="sxs-lookup"><span data-stu-id="36bd3-449">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="36bd3-450">Pokud by kontroly stavu měly naslouchat na konkrétním portu, použijte přetížení <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> pro nastavení portu (popsáno dále v části [Filter by port](#filter-by-port) ):</span><span class="sxs-lookup"><span data-stu-id="36bd3-450">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="36bd3-451">Možnosti kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-451">Health check options</span></span>

<span data-ttu-id="36bd3-452"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>Poskytněte možnost přizpůsobení chování kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-452"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="36bd3-453">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-453">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="36bd3-454">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="36bd3-454">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="36bd3-455">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="36bd3-455">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="36bd3-456">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="36bd3-456">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="36bd3-457">Filtrovat kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-457">Filter health checks</span></span>

<span data-ttu-id="36bd3-458">Ve výchozím nastavení middleware pro kontrolu stavu spouští všechny zaregistrované kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-458">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="36bd3-459">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která vrací logickou hodnotu pro <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> možnost.</span><span class="sxs-lookup"><span data-stu-id="36bd3-459">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="36bd3-460">V následujícím příkladu `Bar` je kontrole stavu Filtrováno podle značky ( `bar_tag` ) v podmíněném příkazu funkce, kde `true` je vrácena pouze v případě, že <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> odpovídá vlastnost kontroly stavu `foo_tag` nebo `baz_tag` :</span><span class="sxs-lookup"><span data-stu-id="36bd3-460">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

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

### <a name="customize-the-http-status-code"></a><span data-ttu-id="36bd3-461">Přizpůsobení stavového kódu HTTP</span><span class="sxs-lookup"><span data-stu-id="36bd3-461">Customize the HTTP status code</span></span>

<span data-ttu-id="36bd3-462">Slouží <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> k přizpůsobení mapování stavu na stavové kódy HTTP.</span><span class="sxs-lookup"><span data-stu-id="36bd3-462">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="36bd3-463">Následující <xref:Microsoft.AspNetCore.Http.StatusCodes> přiřazení jsou výchozí hodnoty používané middlewarem.</span><span class="sxs-lookup"><span data-stu-id="36bd3-463">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="36bd3-464">Změňte hodnoty stavového kódu tak, aby splňovaly vaše požadavky.</span><span class="sxs-lookup"><span data-stu-id="36bd3-464">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="36bd3-465">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="36bd3-465">In `Startup.Configure`:</span></span>

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

### <a name="suppress-cache-headers"></a><span data-ttu-id="36bd3-466">Potlačit hlavičky mezipaměti</span><span class="sxs-lookup"><span data-stu-id="36bd3-466">Suppress cache headers</span></span>

<span data-ttu-id="36bd3-467"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Určuje, zda middleware kontroluje stav přidává hlavičky protokolu HTTP do odezvy sondy, aby nedocházelo k ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="36bd3-467"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="36bd3-468">Pokud je hodnota `false` (výchozí), middleware nastaví nebo přepíše `Cache-Control` `Expires` hlavičky, a, `Pragma` aby nedocházelo k ukládání odpovědí do mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="36bd3-468">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="36bd3-469">Pokud je hodnota `true` , middleware neupraví hlavičky mezipaměti odpovědi.</span><span class="sxs-lookup"><span data-stu-id="36bd3-469">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="36bd3-470">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="36bd3-470">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="36bd3-471">Přizpůsobení výstupu</span><span class="sxs-lookup"><span data-stu-id="36bd3-471">Customize output</span></span>

<span data-ttu-id="36bd3-472"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter>Možnost Získá nebo nastaví delegáta použitý k zápisu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="36bd3-472">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="36bd3-473">Výchozí delegát zapisuje minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="36bd3-473">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="36bd3-474">V `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="36bd3-474">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="36bd3-475">Výchozí delegát zapisuje minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="36bd3-475">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="36bd3-476">Následující vlastní delegát `WriteResponse` vytvoří výstup vlastní odpovědi JSON:</span><span class="sxs-lookup"><span data-stu-id="36bd3-476">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

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

<span data-ttu-id="36bd3-477">Systém kontroly stavu neposkytuje integrovanou podporu pro komplexní formáty vrácených hodnot JSON, protože formát je specifický pro váš výběr systému monitorování.</span><span class="sxs-lookup"><span data-stu-id="36bd3-477">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="36bd3-478">V předchozím příkladu si můžete přizpůsobit, `JObject` jak je to nutné pro splnění vašich požadavků.</span><span class="sxs-lookup"><span data-stu-id="36bd3-478">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="36bd3-479">Test databáze</span><span class="sxs-lookup"><span data-stu-id="36bd3-479">Database probe</span></span>

<span data-ttu-id="36bd3-480">Kontrola stavu může určit databázový dotaz, který se má spustit jako logický test, aby označoval, zda databáze obvykle reaguje.</span><span class="sxs-lookup"><span data-stu-id="36bd3-480">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="36bd3-481">Ukázková aplikace pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), knihovny kontroly stavu pro aplikace ASP.NET Core, provede kontrolu stavu databáze SQL Server.</span><span class="sxs-lookup"><span data-stu-id="36bd3-481">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="36bd3-482">`AspNetCore.Diagnostics.HealthChecks`spustí `SELECT 1` dotaz na databázi, aby se ověřilo, že připojení k databázi je v pořádku.</span><span class="sxs-lookup"><span data-stu-id="36bd3-482">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="36bd3-483">Při kontrole připojení k databázi pomocí dotazu vyberte dotaz, který se rychle vrátí.</span><span class="sxs-lookup"><span data-stu-id="36bd3-483">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="36bd3-484">Přístup k dotazům spouští riziko přetížení databáze a snížení výkonu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-484">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="36bd3-485">Ve většině případů není nutné spustit testovací dotaz.</span><span class="sxs-lookup"><span data-stu-id="36bd3-485">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="36bd3-486">Stačí pouze provést úspěšné připojení k databázi.</span><span class="sxs-lookup"><span data-stu-id="36bd3-486">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="36bd3-487">Pokud zjistíte, že je nutné spustit dotaz, zvolte jednoduchý dotaz SELECT, například `SELECT 1` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-487">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="36bd3-488">Zahrňte odkaz na balíček do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span><span class="sxs-lookup"><span data-stu-id="36bd3-488">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="36bd3-489">Zadejte platný připojovací řetězec databáze do souboru *appSettings. JSON* ukázkové aplikace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-489">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="36bd3-490">Aplikace používá SQL Server databázi s názvem `HealthCheckSample` :</span><span class="sxs-lookup"><span data-stu-id="36bd3-490">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="36bd3-491">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-491">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="36bd3-492">Ukázková aplikace volá `AddSqlServer` metodu s připojovacím řetězcem databáze (*DbHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bd3-492">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="36bd3-493">Ověřování stavu volání middlewaru v kanálu zpracování aplikace v `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="36bd3-493">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="36bd3-494">Pokud chcete spustit scénář testu databáze pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="36bd3-494">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="36bd3-495">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="36bd3-495">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="36bd3-496">Test Entity Framework Core DbContext</span><span class="sxs-lookup"><span data-stu-id="36bd3-496">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="36bd3-497">Tato `DbContext` kontrolu potvrdí, že aplikace může komunikovat s databází nakonfigurovanou pro EF Core `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-497">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="36bd3-498">Tato `DbContext` kontrolu je podporovaná v aplikacích, které:</span><span class="sxs-lookup"><span data-stu-id="36bd3-498">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="36bd3-499">Použijte [jádro Entity Framework (EF)](/ef/core/).</span><span class="sxs-lookup"><span data-stu-id="36bd3-499">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="36bd3-500">Zahrňte odkaz na balíček [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="36bd3-500">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="36bd3-501">`AddDbContextCheck<TContext>`zaregistruje kontrolu stavu pro `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-501">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="36bd3-502">`DbContext`Je dodán jako `TContext` Metoda metodě.</span><span class="sxs-lookup"><span data-stu-id="36bd3-502">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="36bd3-503">K dispozici je přetížení pro konfiguraci stavu selhání, značek a vlastního testovacího dotazu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-503">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="36bd3-504">Ve výchozím nastavení:</span><span class="sxs-lookup"><span data-stu-id="36bd3-504">By default:</span></span>

* <span data-ttu-id="36bd3-505">`DbContextHealthCheck`Volá `CanConnectAsync` metodu EF Core.</span><span class="sxs-lookup"><span data-stu-id="36bd3-505">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="36bd3-506">Můžete přizpůsobit, která operace je spuštěna při kontrole stavu pomocí `AddDbContextCheck` přetížení metod.</span><span class="sxs-lookup"><span data-stu-id="36bd3-506">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="36bd3-507">Název kontroly stavu je název `TContext` typu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-507">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="36bd3-508">V ukázkové aplikaci `AppDbContext` je k dispozici `AddDbContextCheck` a zaregistrována jako služba `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bd3-508">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="36bd3-509">V ukázkové aplikaci `UseHealthChecks` přidá modul pro kontrolu stavu middleware v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-509">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="36bd3-510">Pokud chcete spustit `DbContext` scénář sondy pomocí ukázkové aplikace, zkontrolujte, že databáze určená připojovacím řetězcem v instanci SQL Server neexistuje.</span><span class="sxs-lookup"><span data-stu-id="36bd3-510">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="36bd3-511">Pokud databáze existuje, odstraňte ji.</span><span class="sxs-lookup"><span data-stu-id="36bd3-511">If the database exists, delete it.</span></span>

<span data-ttu-id="36bd3-512">Ze složky projektu v příkazovém prostředí spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="36bd3-512">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="36bd3-513">Po spuštění aplikace ověřte stav tak, že v prohlížeči vytvoříte požadavek na `/health` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="36bd3-513">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="36bd3-514">Databáze a `AppDbContext` neexistuje, takže aplikace nabízí následující odpověď:</span><span class="sxs-lookup"><span data-stu-id="36bd3-514">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="36bd3-515">Aktivujte ukázkovou aplikaci pro vytvoření databáze.</span><span class="sxs-lookup"><span data-stu-id="36bd3-515">Trigger the sample app to create the database.</span></span> <span data-ttu-id="36bd3-516">Vytvořte žádost na `/createdatabase` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-516">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="36bd3-517">Aplikace odpoví:</span><span class="sxs-lookup"><span data-stu-id="36bd3-517">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="36bd3-518">Vytvořte požadavek na `/health` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="36bd3-518">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="36bd3-519">Databáze a kontext existují, takže aplikace reaguje:</span><span class="sxs-lookup"><span data-stu-id="36bd3-519">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="36bd3-520">Aktivujte ukázkovou aplikaci, aby se odstranila databáze.</span><span class="sxs-lookup"><span data-stu-id="36bd3-520">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="36bd3-521">Vytvořte žádost na `/deletedatabase` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-521">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="36bd3-522">Aplikace odpoví:</span><span class="sxs-lookup"><span data-stu-id="36bd3-522">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="36bd3-523">Vytvořte požadavek na `/health` koncový bod.</span><span class="sxs-lookup"><span data-stu-id="36bd3-523">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="36bd3-524">Aplikace poskytuje poškozenou reakci:</span><span class="sxs-lookup"><span data-stu-id="36bd3-524">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="36bd3-525">Samostatné sondy pro připravenost a živý provoz</span><span class="sxs-lookup"><span data-stu-id="36bd3-525">Separate readiness and liveness probes</span></span>

<span data-ttu-id="36bd3-526">V některých scénářích hostování se používá pár kontrol stavu, který rozlišuje dva stavy aplikace:</span><span class="sxs-lookup"><span data-stu-id="36bd3-526">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="36bd3-527">Aplikace funguje, ale ještě není připravená na příjem požadavků.</span><span class="sxs-lookup"><span data-stu-id="36bd3-527">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="36bd3-528">Tento stav je *připravenost*aplikace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-528">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="36bd3-529">Aplikace funguje a reaguje na požadavky.</span><span class="sxs-lookup"><span data-stu-id="36bd3-529">The app is functioning and responding to requests.</span></span> <span data-ttu-id="36bd3-530">Tento stav je *živými*aplikacemi.</span><span class="sxs-lookup"><span data-stu-id="36bd3-530">This state is the app's *liveness*.</span></span>

<span data-ttu-id="36bd3-531">Kontrola připravenosti obvykle provádí rozsáhlejší a časově náročnou sadu kontrol, aby bylo možné zjistit, zda jsou k dispozici všechny subsystémy a prostředky aplikace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-531">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="36bd3-532">K dispozici je pouze rychlá kontrolu, která určuje, zda je aplikace k dispozici pro zpracování požadavků.</span><span class="sxs-lookup"><span data-stu-id="36bd3-532">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="36bd3-533">Po úspěšném provedení kontroly připravenosti aplikace nemusíte aplikaci navýšit dál s nákladnými sadami kontrol připravenosti, které &mdash; vyžadují kontrolu živého ověření.</span><span class="sxs-lookup"><span data-stu-id="36bd3-533">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="36bd3-534">Ukázková aplikace obsahuje kontrolu stavu, která oznamuje dokončení dlouhotrvající úlohy po spuštění v [hostované službě](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="36bd3-534">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="36bd3-535">`StartupHostedServiceHealthCheck`Zpřístupňuje vlastnost, `StartupTaskCompleted` která hostovaná služba může nastavit na hodnotu `true` po dokončení její dlouhotrvající úlohy (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bd3-535">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="36bd3-536">Dlouhotrvající úloha na pozadí se spouští v [hostované službě](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span><span class="sxs-lookup"><span data-stu-id="36bd3-536">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="36bd3-537">Při uzavírání úkolu `StartupHostedServiceHealthCheck.StartupTaskCompleted` je nastaveno na `true` :</span><span class="sxs-lookup"><span data-stu-id="36bd3-537">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="36bd3-538">Kontroly stavu jsou registrovány <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> ve `Startup.ConfigureServices` službě společně s hostovanou službou.</span><span class="sxs-lookup"><span data-stu-id="36bd3-538">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="36bd3-539">Vzhledem k tomu, že hostovaná služba musí nastavit vlastnost pro kontrolu stavu, je tato kontrolu také zaregistrována v kontejneru služby (*LivenessProbeStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bd3-539">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="36bd3-540">V rámci kanálu zpracování aplikace v nástroji se kontrolují stav volání middleware `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-540">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="36bd3-541">V ukázkové aplikaci jsou koncové body kontroly stavu vytvořeny v `/health/ready` pro kontrolu připravenosti a `/health/live` pro kontrolu živého provozu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-541">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="36bd3-542">Kontrola připravenosti filtruje kontrolu stavu na kontrolu stavu se `ready` značkou.</span><span class="sxs-lookup"><span data-stu-id="36bd3-542">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="36bd3-543">Kontrola živého odfiltruje `StartupHostedServiceHealthCheck` vrácení se `false` změnami v [HealthCheckOptions. predikátu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Další informace najdete v tématu [filtrování kontrol stavu](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="36bd3-543">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

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

<span data-ttu-id="36bd3-544">Pokud chcete spustit scénář konfigurace připravenosti/živých aplikací pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="36bd3-544">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="36bd3-545">V prohlížeči navštivte několikrát, `/health/ready` dokud neuplyne 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="36bd3-545">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="36bd3-546">Zprávy o kontrole stavu nejsou v *pořádku* po dobu prvních 15 sekund.</span><span class="sxs-lookup"><span data-stu-id="36bd3-546">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="36bd3-547">Po 15 sekundách koncový bod hlásí stav *v pořádku*, který odráží dokončení dlouhotrvající úlohy hostované službou.</span><span class="sxs-lookup"><span data-stu-id="36bd3-547">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="36bd3-548">V tomto příkladu se vytvoří také Vydavatel kontroly stavu ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace), který spouští první kontrolu připravenosti se dvěma sekundami.</span><span class="sxs-lookup"><span data-stu-id="36bd3-548">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="36bd3-549">Další informace najdete v části [Vydavatel kontroly stavu](#health-check-publisher) .</span><span class="sxs-lookup"><span data-stu-id="36bd3-549">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="36bd3-550">Příklad Kubernetes</span><span class="sxs-lookup"><span data-stu-id="36bd3-550">Kubernetes example</span></span>

<span data-ttu-id="36bd3-551">Používání samostatné kontroly připravenosti a živých kontrol je užitečné v prostředí, jako je [Kubernetes](https://kubernetes.io/).</span><span class="sxs-lookup"><span data-stu-id="36bd3-551">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="36bd3-552">V Kubernetes může být aplikace nutná k provádění časově náročné práce při spuštění před přijetím požadavků, jako je například test dostupnosti podkladové databáze.</span><span class="sxs-lookup"><span data-stu-id="36bd3-552">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="36bd3-553">Použití samostatných kontrol umožňuje nástroji Orchestrator rozlišit, jestli aplikace funguje, ale ještě není připravená, nebo jestli se aplikace nedala spustit.</span><span class="sxs-lookup"><span data-stu-id="36bd3-553">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="36bd3-554">Další informace o testování připravenosti a provozní sondy v Kubernetes najdete v tématu [Konfigurace živých a připravených sond](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) v dokumentaci k Kubernetes.</span><span class="sxs-lookup"><span data-stu-id="36bd3-554">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="36bd3-555">Následující příklad ukazuje konfiguraci testu připravenosti na Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="36bd3-555">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="36bd3-556">Test založený na metrikách s vlastním zapisovačem odpovědí</span><span class="sxs-lookup"><span data-stu-id="36bd3-556">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="36bd3-557">Ukázková aplikace ukazuje kontrolu stavu paměti pomocí vlastního zapisovače odpovědí.</span><span class="sxs-lookup"><span data-stu-id="36bd3-557">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="36bd3-558">`MemoryHealthCheck`hlásí stav není v pořádku, pokud aplikace používá více než danou prahovou hodnotu paměti (1 GB v ukázkové aplikaci).</span><span class="sxs-lookup"><span data-stu-id="36bd3-558">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="36bd3-559"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>Zahrnuje informace o uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bd3-559">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="36bd3-560">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-560">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="36bd3-561">Místo povolení kontroly stavu předáním <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `MemoryHealthCheck` jako služby je zaregistrována jako služba.</span><span class="sxs-lookup"><span data-stu-id="36bd3-561">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="36bd3-562">Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrované služby jsou k dispozici pro služby kontroly stavu a middleware.</span><span class="sxs-lookup"><span data-stu-id="36bd3-562">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="36bd3-563">Službu pro kontrolu stavu doporučujeme zaregistrovat jako služby typu singleton.</span><span class="sxs-lookup"><span data-stu-id="36bd3-563">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="36bd3-564">V ukázkové aplikaci (*CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bd3-564">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="36bd3-565">V rámci kanálu zpracování aplikace v nástroji se kontrolují stav volání middleware `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-565">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="36bd3-566">`WriteResponse`Delegát poskytne `ResponseWriter` vlastnost pro výstup vlastní odpovědi JSON, když se spustí kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-566">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

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

<span data-ttu-id="36bd3-567">`WriteResponse`Metoda zformátuje `CompositeHealthCheckResult` objekt do objektu JSON a poskytne výstup JSON pro odpověď kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-567">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="36bd3-568">Pokud chcete spustit test založený na metrikách s vlastním výstupem zapisovače odpovědí pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="36bd3-568">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="36bd3-569">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje scénáře kontroly stavu založené na metrikách, včetně úložného prostoru na disku a maximální hodnoty živé kontroly.</span><span class="sxs-lookup"><span data-stu-id="36bd3-569">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="36bd3-570">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="36bd3-570">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="36bd3-571">Filtrovat podle portu</span><span class="sxs-lookup"><span data-stu-id="36bd3-571">Filter by port</span></span>

<span data-ttu-id="36bd3-572">Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> s portem omezí požadavky na kontrolu stavu na zadaný port.</span><span class="sxs-lookup"><span data-stu-id="36bd3-572">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="36bd3-573">Obvykle se používá v prostředí kontejneru k vystavení portu pro monitorovací služby.</span><span class="sxs-lookup"><span data-stu-id="36bd3-573">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="36bd3-574">Ukázková aplikace nakonfiguruje port pomocí [poskytovatele konfigurace proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="36bd3-574">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="36bd3-575">Port je nastaven v souboru *launchSettings. JSON* a předán poskytovateli konfigurace prostřednictvím proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="36bd3-575">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="36bd3-576">Také je nutné nakonfigurovat server tak, aby naslouchal žádosti na portu pro správu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-576">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="36bd3-577">Pokud chcete ukázkovou aplikaci použít k předvedení konfigurace portů pro správu, vytvořte soubor *launchSettings. JSON* ve složce *Properties (vlastnosti* ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-577">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="36bd3-578">Následující *vlastnosti/soubor launchSettings. JSON* v ukázkové aplikaci nejsou součástí souborů projektu ukázkové aplikace a je nutné je vytvořit ručně:</span><span class="sxs-lookup"><span data-stu-id="36bd3-578">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

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

<span data-ttu-id="36bd3-579">Registrovat služby kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v nástroji v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="36bd3-579">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="36bd3-580">Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> Určuje port pro správu (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="36bd3-580">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="36bd3-581">Vytvoření souboru *launchSettings. JSON* v ukázkové aplikaci se vyhnete tak, že v kódu nastavíte adresy URL a port pro správu explicitně.</span><span class="sxs-lookup"><span data-stu-id="36bd3-581">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="36bd3-582">V *program.cs* , kde <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> je vytvořena, přidejte volání do <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> a poskytněte normální koncový bod odpovědi aplikace a koncový bod portu pro správu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-582">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="36bd3-583">V *ManagementPortStartup.cs* <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> , kde je volána, určete explicitně port pro správu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-583">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="36bd3-584">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="36bd3-584">*Program.cs*:</span></span>
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
> <span data-ttu-id="36bd3-585">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="36bd3-585">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="36bd3-586">Pokud chcete spustit scénář konfigurace portu pro správu pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="36bd3-586">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="36bd3-587">Distribuce knihovny kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-587">Distribute a health check library</span></span>

<span data-ttu-id="36bd3-588">Postup při distribuci kontroly stavu jako knihovny:</span><span class="sxs-lookup"><span data-stu-id="36bd3-588">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="36bd3-589">Zapište kontrolu stavu, která implementuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní jako samostatnou třídu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-589">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="36bd3-590">Třída může spoléhat na [vkládání závislostí (di)](xref:fundamentals/dependency-injection), aktivovat typ a [pojmenované možnosti](xref:fundamentals/configuration/options) pro přístup k datům konfigurace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-590">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="36bd3-591">V logice kontroly stavu pro `CheckHealthAsync` :</span><span class="sxs-lookup"><span data-stu-id="36bd3-591">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="36bd3-592">`data1`a `data2` jsou použity v metodě pro spuštění logiky kontroly stavu testu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-592">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="36bd3-593">`AccessViolationException`je zpracována.</span><span class="sxs-lookup"><span data-stu-id="36bd3-593">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="36bd3-594">V případě <xref:System.AccessViolationException> , že dojde k chybě, se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> vrátí s a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> umožní uživatelům konfigurovat stav selhání kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-594">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

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

1. <span data-ttu-id="36bd3-595">Zapište metodu rozšíření s parametry, které aplikace spotřebovávají ve své `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="36bd3-595">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="36bd3-596">V následujícím příkladu předpokládejme následující signatura metody kontroly stavu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-596">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="36bd3-597">Předchozí signatura indikuje, že `ExampleHealthCheck` pro zpracování logiky sondy kontroly stavu vyžaduje další data.</span><span class="sxs-lookup"><span data-stu-id="36bd3-597">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="36bd3-598">Data jsou k dispozici delegátovi použitému k vytvoření instance kontroly stavu při registraci kontroly stavu s metodou rozšíření.</span><span class="sxs-lookup"><span data-stu-id="36bd3-598">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="36bd3-599">V následujícím příkladu volající určuje nepovinné:</span><span class="sxs-lookup"><span data-stu-id="36bd3-599">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="36bd3-600">název kontroly stavu ( `name` ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-600">health check name (`name`).</span></span> <span data-ttu-id="36bd3-601">Pokud `null` se `example_health_check` používá.</span><span class="sxs-lookup"><span data-stu-id="36bd3-601">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="36bd3-602">datový bod řetězce pro kontrolu stavu ( `data1` ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-602">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="36bd3-603">celočíselný datový bod pro kontrolu stavu ( `data2` ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-603">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="36bd3-604">Pokud `null` se `1` používá.</span><span class="sxs-lookup"><span data-stu-id="36bd3-604">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="36bd3-605">stav selhání ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-605">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="36bd3-606">Výchozí formát je `null`.</span><span class="sxs-lookup"><span data-stu-id="36bd3-606">The default is `null`.</span></span> <span data-ttu-id="36bd3-607">Pokud `null` je [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) není v pořádku, je hlášen stav selhání.</span><span class="sxs-lookup"><span data-stu-id="36bd3-607">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="36bd3-608">značky ( `IEnumerable<string>` ).</span><span class="sxs-lookup"><span data-stu-id="36bd3-608">tags (`IEnumerable<string>`).</span></span>

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

## <a name="health-check-publisher"></a><span data-ttu-id="36bd3-609">Vydavatel kontroly stavu</span><span class="sxs-lookup"><span data-stu-id="36bd3-609">Health Check Publisher</span></span>

<span data-ttu-id="36bd3-610">Když <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> se přidá do kontejneru služby, systém kontroly stavu pravidelně spouští kontroly stavu a volání `PublishAsync` s výsledkem.</span><span class="sxs-lookup"><span data-stu-id="36bd3-610">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="36bd3-611">To je užitečné ve scénáři sledování stavu založeném na nabízených oznámeních, který očekává, že každý proces bude pravidelně volat monitorovací systém, aby se zjistil stav.</span><span class="sxs-lookup"><span data-stu-id="36bd3-611">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="36bd3-612"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>Rozhraní má jedinou metodu:</span><span class="sxs-lookup"><span data-stu-id="36bd3-612">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="36bd3-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>umožňuje nastavit:</span><span class="sxs-lookup"><span data-stu-id="36bd3-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="36bd3-614"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Počáteční zpoždění použité po spuštění aplikace před spuštěním <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instancí.</span><span class="sxs-lookup"><span data-stu-id="36bd3-614"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="36bd3-615">Zpoždění se použije jednou při spuštění a nevztahuje se na další iterace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-615">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="36bd3-616">Výchozí hodnota je pět sekund.</span><span class="sxs-lookup"><span data-stu-id="36bd3-616">The default value is five seconds.</span></span>
* <span data-ttu-id="36bd3-617"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Období <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> provádění.</span><span class="sxs-lookup"><span data-stu-id="36bd3-617"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="36bd3-618">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="36bd3-618">The default value is 30 seconds.</span></span>
* <span data-ttu-id="36bd3-619"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> je `null` (výchozí), služba Vydavatel kontroly stavu spustí všechny zaregistrované kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-619"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="36bd3-620">Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která filtruje sadu kontrol.</span><span class="sxs-lookup"><span data-stu-id="36bd3-620">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="36bd3-621">Predikát je vyhodnocen každou periodu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-621">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="36bd3-622"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Časový limit pro spuštění kontrol stavu pro všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance.</span><span class="sxs-lookup"><span data-stu-id="36bd3-622"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="36bd3-623">Použijte <xref:System.Threading.Timeout.InfiniteTimeSpan> k provedení bez časového limitu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-623">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="36bd3-624">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="36bd3-624">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="36bd3-625">Ve verzi ASP.NET Core 2,2 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> není nastavení dodrženo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementací. nastavuje hodnotu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> .</span><span class="sxs-lookup"><span data-stu-id="36bd3-625">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="36bd3-626">Tento problém byl vyřešen v ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="36bd3-626">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="36bd3-627">V ukázkové aplikaci `ReadinessPublisher` je <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace.</span><span class="sxs-lookup"><span data-stu-id="36bd3-627">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="36bd3-628">Stav kontroly stavu se zaznamená do protokolu pro každé vrácení se změnami buď:</span><span class="sxs-lookup"><span data-stu-id="36bd3-628">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="36bd3-629">Informace ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ), pokud je stav kontrol stavu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .</span><span class="sxs-lookup"><span data-stu-id="36bd3-629">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="36bd3-630">Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ), pokud je stav buď <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> nebo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .</span><span class="sxs-lookup"><span data-stu-id="36bd3-630">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="36bd3-631">V příkladu ukázkové aplikace `LivenessProbeStartup` `StartupHostedService` má kontroler připravenosti dvě sekundy na spuštění a spustí kontrolu každých 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="36bd3-631">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="36bd3-632">Chcete-li aktivovat <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementaci, ukázka se registruje `ReadinessPublisher` jako služba s jedním prvkem v kontejneru [Injektáže (di) pro vkládání závislostí](xref:fundamentals/dependency-injection) :</span><span class="sxs-lookup"><span data-stu-id="36bd3-632">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="36bd3-633">Následující alternativní řešení povoluje přidání <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance do kontejneru služby, pokud již byla do aplikace přidána jedna nebo více dalších hostovaných služeb.</span><span class="sxs-lookup"><span data-stu-id="36bd3-633">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="36bd3-634">Toto řešení nebude v ASP.NET Core 3,0 vyžadováno.</span><span class="sxs-lookup"><span data-stu-id="36bd3-634">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
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
> <span data-ttu-id="36bd3-635">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="36bd3-635">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="36bd3-636">[AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.</span><span class="sxs-lookup"><span data-stu-id="36bd3-636">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="36bd3-637">Omezení kontrol stavu pomocí MapWhen</span><span class="sxs-lookup"><span data-stu-id="36bd3-637">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="36bd3-638">Použijte <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> k podmíněně větvení kanálu žádostí pro koncové body kontroly stavu.</span><span class="sxs-lookup"><span data-stu-id="36bd3-638">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="36bd3-639">V následujícím příkladu `MapWhen` větví kanál žádosti o aktivaci middlewaru pro kontrolu stavu, pokud se pro koncový bod přijme požadavek Get `api/HealthCheck` :</span><span class="sxs-lookup"><span data-stu-id="36bd3-639">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="36bd3-640">Další informace naleznete v tématu <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="36bd3-640">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
