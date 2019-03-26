---
title: Doplněk pro kontroly stavu v ASP.NET Core
author: guardrex
description: Další informace o nastavení kontroly stavu pro ASP.NET Core infrastruktury, jako jsou aplikace a databáze.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: 0bb80a5fccc8240c6f1fb8e59b379766bfd90d9e
ms.sourcegitcommit: 687ffb15ebe65379f75c84739ea851d5a0d788b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488712"
---
# <a name="health-checks-in-aspnet-core"></a>Doplněk pro kontroly stavu v ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex) a [Glenn Condron](https://github.com/glennc)

ASP.NET Core nabízí Middleware zkontrolovat stav a knihovny pro vytváření sestav stavu komponent infrastruktury aplikace.

Kontroly stavu jsou vystaveny aplikací jako koncových bodů HTTP. Kontroly stavu koncových bodů lze nakonfigurovat pro různé scénáře monitorování v reálném čase:

* Sondy stavu služby mohou být využívána orchestrátorů kontejnerů a zkontrolovat stav vaší aplikace Vyrovnávání zatížení. Například orchestrátor kontejnerů může reagovat na stav selhání zkontrolovat, že zastavení nasazení se zajištěním provozu nebo kontejner se restartuje. Nástroj pro vyrovnávání zatížení může reagovat na není v pořádku aplikace můžete provoz nasměrovat od selhání instance na instanci v pořádku.
* Použití paměti, disku a další prostředky fyzického serveru je možné monitorovat stav v pořádku.
* Kontroly stavu můžete otestovat závislostí vaší aplikace, jako jsou databáze a externí služby koncových bodů, abyste zkontrolovali dostupnost a správně fungovat.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([stažení](xref:index#how-to-download-a-sample))

Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu. Chcete-li spustit ukázkovou aplikaci v daném scénáři, použijte [dotnet spustit](/dotnet/core/tools/dotnet-run) příkaz ze složky projektu v příkazovém řádku. Zobrazit ukázkovou aplikaci *README.md* souboru a popisy scénářů v tomto tématu informace o tom, jak použít ukázkovou aplikaci.

## <a name="prerequisites"></a>Požadavky

Kontroly stavu se obvykle používají s externí monitorování orchestrator služby nebo kontejneru a zkontrolujte stav aplikace. Před přidáním kontroly stavu aplikace, rozhodněte, na který monitorovací systém použít. Systém monitorování Určuje, jaké typy kontroly stavu k vytvoření a konfigurace jejich koncové body.

Odkaz [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) nebo přidat odkaz na balíček [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) balíčku.

Ukázková aplikace poskytuje při spuštění kód pro demonstraci kontroly stavu pro několik scénářů. [Databáze sondy](#database-probe) scénář zkontroluje stav připojení databáze pomocí [BeatPulse](https://github.com/Xabaril/BeatPulse). [DbContext sondy](#entity-framework-core-dbcontext-probe) scénář zkontroluje databázi pomocí EF Core `DbContext`. Prozkoumat scénáře databázi, ukázková aplikace:

* Vytvoří databázi a obsahuje ve svém připojovacím řetězci *appsettings.json* souboru.
* Má následující odkazy na balíčky v jeho souboru projektu:
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [BeatPulse](https://github.com/Xabaril/BeatPulse) není zachována nebo podporovaný společností Microsoft.

Další možností kontroly stavu ukazuje, jak filtrovat kontroly stavu port pro správu. Ukázková aplikace je potřeba vytvořit *Properties/launchSettings.json* soubor, který obsahuje adresu URL pro správu a port pro správu. Další informace najdete v tématu [filtrovat podle port](#filter-by-port) oddílu.

## <a name="basic-health-probe"></a>Sonda stavu základní

U mnoha aplikací konfiguraci sondy základní stav, který bude hlásit dostupnost aplikace ke zpracování požadavků (*aktivity*) ke zjištění stavu aplikace.

Základní konfigurace zaregistruje služeb kontroly stavu a volá Middleware Kontrola stavu reagovat na koncový bod adresy URL odpovědí stavu. Ve výchozím nastavení jsou registrovány žádné konkrétní stav kontroly testování žádné konkrétní závislost nebo subsystému. Aplikace se považuje za v pořádku, pokud je schopný reagovat na adresu URL koncového bodu stavu. Zapíše zapisovače odpovědí výchozí stav (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) jako odpověď ve formátu prostého textu zpět do klienta, která [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [ HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) stav.

Registrace služby kontroly stavu s <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`. Přidat stav zkontrolujte Middleware s <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> v kanálu zpracování žádosti o `Startup.Configure`.

V ukázkové aplikaci, je vytvořen koncový bod stavu zaškrtnutí v `/health` (*BasicStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/BasicStartup.cs?name=snippet1&highlight=5,10)]

Ke spuštění základní konfiguraci scénář s využitím ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém řádku:

```console
dotnet run --scenario basic
```

### <a name="docker-example"></a>Příklad dockeru

[Docker](xref:host-and-deploy/docker/index) nabízí integrované `HEALTHCHECK` směrnice, kterého chcete zkontrolovat stav, který používá základní stav Zkontrolujte konfiguraci aplikace:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Vytvoření kontroly stavu

Doplněk pro kontroly stavu jsou vytvořeny pomocí implementace <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> Metoda vrátí hodnotu `Task<` <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> `>` , který označuje stav jako `Healthy`, `Degraded`, nebo `Unhealthy`. Výsledkem je zapsán jako odpověď ve formátu prostého textu se dají konfigurovat stavovým kódem (konfigurace je popsaná v [možnosti kontroly stavu](#health-check-options) části). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> Můžete také vrátit volitelné páry klíč hodnota.

### <a name="example-health-check"></a>Kontrola stavu příklad

Následující `ExampleHealthCheck` třídy ukazuje rozložení kontrolou stavu:

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

### <a name="register-health-check-services"></a>Registrace služeb kontroly stavu

`ExampleHealthCheck` Typ je přidat do služby kontroly stavu s <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck<ExampleHealthCheck>("example_health_check");
}
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> Přetížení je znázorněno v následujícím příkladu nastaví stav chyby (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) k sestavě, když je kontrola stavu hlásí selhání. Pokud je nastaven stav selhání `null` (výchozí), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) se použije v hlášení. Toto přetížení je užitečný scénář pro autory knihoven, kde stav selhání indikován knihovny vynucuje aplikace, když dojde k selhání kontroly stavu, pokud provádění kontroly stavu respektuje nastavení.

*Značky* můžete použít k filtrování kontroly stavu (popsáno dále v [filtrovat kontroly stavu](#filter-health-checks) části).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> Můžete také spustit funkci lambda. V následujícím příkladu je zadán název kontroly stavu jako `Example` a kontrola vždy vrátí hodnotu stavu v pořádku:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Example", () =>
            HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" })
}
```

### <a name="use-health-checks-middleware"></a>Použití middlewaru kontroly stavu

V `Startup.Configure`, volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> v kanálu zpracování se adresa URL koncového bodu nebo relativní cesta:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health");
}
```

Pokud kontroly stavu naslouchat požadavkům na konkrétní port, použijte přetížení <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> nastavení portu (popsány dále v [filtrovat podle port](#filter-by-port) části):

```csharp
app.UseHealthChecks("/health", port: 8000);
```

Middleware ověří stavu je *terminálu middleware* v kanálu zpracování žádosti o aplikace. První stavu vrácení koncového bodu došlo k, který přesně odpovídá adrese URL požadavku spustí a zkratům middleware zbytku. Dojde-li krátký cyklus, spustí žádné middleware následující kontroly stavu odpovídající.

## <a name="health-check-options"></a>Možnosti kontroly stavu

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> poskytnout příležitosti k přizpůsobení chování kontrolu stavu:

* [Filtrovat kontroly stavu](#filter-health-checks)
* [Přizpůsobení stavový kód HTTP](#customize-the-http-status-code)
* [Potlačit hlavičky mezipaměti](#suppress-cache-headers)
* [Přizpůsobení výstupu](#customize-output)

### <a name="filter-health-checks"></a>Filtrovat kontroly stavu

Ve výchozím nastavení spustí všechny kontroly stavu registrované Middleware zkontrolovat stav. Pro spuštění podmnožiny kontroly stavu, poskytují funkce, která vrátí logickou hodnotu k <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> možnost. V následujícím příkladu `Bar` Kontrola stavu je odfiltrována podle jeho značky (`bar_tag`) v podmíněném příkazu funkce, kde `true` je vrácena pouze v případě kontrola stavu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> odpovídá vlastnosti `foo_tag` nebo `baz_tag`:

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

### <a name="customize-the-http-status-code"></a>Přizpůsobení stavový kód HTTP

Použití <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> přizpůsobit mapování stavu stavové kódy HTTP. Následující <xref:Microsoft.AspNetCore.Http.StatusCodes> přiřazení jsou výchozí hodnoty pro daný middleware. Změňte hodnoty stavu kódu podle svých požadavků.

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

### <a name="suppress-cache-headers"></a>Potlačit hlavičky mezipaměti

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> Určuje, zda Middleware zkontrolovat stav přidá do odpovědi test zabránit ukládání do mezipaměti odpovědi hlavičky protokolu HTTP. Pokud je hodnota `false` (výchozí), middleware Nastaví nebo přepíše `Cache-Control`, `Expires`, a `Pragma` záhlaví zabránit ukládání odpovědí do mezipaměti. Pokud je hodnota `true`, middleware nemění mezipaměti hlaviček odpovědi.

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

### <a name="customize-output"></a>Přizpůsobení výstupu

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> Možnost získá nebo nastaví delegáta použitý k zápisu odpovědi. Výchozí delegáta zapíše odpověď o minimální ve formátu prostého textu s řetězcovou hodnotu [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).

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

## <a name="database-probe"></a>Test databáze

Kontrola stavu můžete zadat dotaz na databázi pro spuštění jako logický test k označení, pokud je databáze obvykle reagovat.

Tato ukázková aplikace používá [BeatPulse](https://github.com/Xabaril/BeatPulse), knihovna kontroly stavu pro aplikace ASP.NET Core, chcete-li provést kontrolu stavu v databázi serveru SQL Server. Spustí BeatPulse `SELECT 1` dotaz na databázi a zkontrolujte připojení k databázi je v pořádku.

> [!WARNING]
> Při kontrole připojení databáze pomocí dotazu, vyberte dotaz, který vrací rychle. Přístup dotaz spustí riziko přetížení databáze a snížení jeho výkon. Ve většině případů není nutné spuštění testu dotazu. Stačí pouze provedení úspěšného připojení k databázi. Pokud zjistíte potřebné ke spuštění dotazu, zvolte jednoduchého dotazu SELECT, jako například `SELECT 1`.

Chcete-li použít knihovnu BeatPulse zahrnout odkaz na balíček pro [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Zadejte platnou databázi připojovacího řetězce v *appsettings.json* souboru ukázkovou aplikaci. Aplikace používá databázi systému SQL Server s názvem `HealthCheckSample`:

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

Registrace služby kontroly stavu s <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`. Ukázková aplikace volá na BeatPulse `AddSqlServer` metoda připojovacím řetězcem databázi (*DbHealthStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Volat stavu zkontrolujte Middleware v kanálu zpracování aplikace v `Startup.Configure`:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_Configure)]

Pokud chcete spustit scénář testu databáze pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém řádku:

```console
dotnet run --scenario db
```

> [!NOTE]
> [BeatPulse](https://github.com/Xabaril/BeatPulse) není zachována nebo podporovaný společností Microsoft.

## <a name="entity-framework-core-dbcontext-probe"></a>Entity Framework Core DbContext testu

`DbContext` Kontrola potvrdí, že aplikace mohla komunikovat s databází, které jsou nakonfigurované pro EF Core `DbContext`. `DbContext` Kontrola se podporuje v aplikacích, které:

* Použití [Entity Framework (EF) Core](/ef/core/).
* Zahrnout odkaz na balíček pro [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>` zaregistruje kontrolu stavu `DbContext`. `DbContext` Je předána jako `TContext` metody. Přetížení je možné konfigurovat stav chyby, značky a vlastní testovat dotaz.

Ve výchozím nastavení:

* `DbContextHealthCheck` Volá EF Core `CanConnectAsync` metody. Můžete přizpůsobit, jaké operace je spuštěna při kontrole stavu pomocí `AddDbContextCheck` přetížení metody.
* Název kontroly stavu je název `TContext` typu.

V ukázkové aplikaci `AppDbContext` je poskytnuta `AddDbContextCheck` a zaregistrováno jako služba v `Startup.ConfigureServices`.

*DbContextHealthStartup.cs*:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

V ukázkové aplikaci `UseHealthChecks` přidá Middleware zkontrolovat stav v `Startup.Configure`.

*DbContextHealthStartup.cs*:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_Configure)]

Ke spuštění `DbContext` scénář s využitím ukázkové aplikace pro zjišťování, potvrďte, že zadaná pomocí připojovacího řetězce databáze neexistuje v instanci systému SQL Server. Pokud databáze existuje, odstraňte ho.

Ze složky projektu v příkazovém řádku spusťte následující příkaz:

```console
dotnet run --scenario dbcontext
```

Jakmile je aplikace spuštěna, kontroluje stav tak, že požadavek na `/health` koncový bod v prohlížeči. Databáze a `AppDbContext` neexistují, takže aplikace poskytuje odpovědi na následující:

```
Unhealthy
```

Spusťte ukázkovou aplikaci k vytvoření databáze. Vytvořit žádost o `/createdatabase`. Reakce aplikace:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Vytvořit žádost o `/health` koncového bodu. Databáze a kontext existují, takže reakce aplikace:

```
Healthy
```

Spusťte ukázkovou aplikaci odstranit databázi. Vytvořit žádost o `/deletedatabase`. Reakce aplikace:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Vytvořit žádost o `/health` koncového bodu. Aplikace poskytuje odpověď není v pořádku:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Samostatné připravenost a testům aktivity

V některých scénářích hostování se používají pár kontroly stavu, který odlišit dvou stavů – stav aplikace:

* Aplikace je funkční, ale není ještě připraven pro příjem požadavků. Tento stav se aplikace *připravenosti*.
* Fungovat a reagovat na požadavky aplikace. Tento stav se aplikace *aktivity*.

Kontrola připravenosti obvykle provádí rozsáhlé a časově náročné sadu kontroly, aby zjistil, zda všechny subsystémy aplikace a prostředky jsou k dispozici. Kontrola aktivity provádí pouze Rychlá kontrola k určení, zda je k dispozici pro zpracování požadavků aplikace. Po aplikaci předává jeho kontroly připravenosti, je nemusí ovlivnit aplikaci dále sadou nákladné kontroly připravenosti&mdash;další kontroly vyžadují jenom tehdy, vyhledávají se aktivity.

Ukázková aplikace obsahuje kontrolou stavu k dokončení dlouho běžící úloha po spuštění v hlášení [hostovanou službu](xref:fundamentals/host/hosted-services). `StartupHostedServiceHealthCheck` Zpřístupňuje vlastnost, `StartupTaskCompleted`, který hostovanou službu, můžete nastavit na `true` po dokončení jeho dlouho běžící úlohy (*StartupHostedServiceHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Dlouho běžící úlohy na pozadí se spustí [hostovanou službu](xref:fundamentals/host/hosted-services) (*služby/StartupHostedService*). Na závěr úkolu `StartupHostedServiceHealthCheck.StartupTaskCompleted` je nastavena na `true`:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Kontrola stavu je registrovaný pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> v `Startup.ConfigureServices` spolu s hostovanou službu. Protože hostovanou službu, musíte nastavit vlastnost na kontrolu stavu, kontrola stavu se registruje v kontejneru služby (*LivenessProbeStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Volat stavu zkontrolujte Middleware v kanálu zpracování aplikace v `Startup.Configure`. V ukázkové aplikaci koncových bodů kontroly stavu jsou vytvářeny na `/health/ready` pro kontrolu připravenosti a `/health/live` pro kontroly aktivity. Filtry kontroly připravenosti stav doplněk pro kontroly stavu, obraťte se `ready` značky. Kontrola aktivity filtruje `StartupHostedServiceHealthCheck` vrácením `false` v [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Další informace najdete v tématu [filtrovat kontroly stavu](#filter-health-checks)):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_Configure)]

Pokud chcete spustit scénář konfigurace připravenosti/aktivity pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém řádku:

```console
dotnet run --scenario liveness
```

V prohlížeči, navštivte `/health/ready` několikrát až do 15 sekund prošly. Zkontrolujte stav sestavy *není v pořádku* po dobu prvních 15 sekund. Po 15 sekundách koncový bod sestav *pořádku*, která odráží dokončení dlouho běžící úlohy v hostované službě.

Tento příklad také vytvoří vydavatel zkontrolovat stav (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace), který spustí první Kontrola připravenosti s dvěma druhý zpoždění. Další informace najdete v tématu [vydavatele zkontrolovat stav](#health-check-publisher) oddílu.

### <a name="kubernetes-example"></a>Příklad Kubernetes

Použití samostatné kontroly připravenosti a aktivity je užitečná v prostředí, jako [Kubernetes](https://kubernetes.io/). V systému Kubernetes může být aplikace vyžadují k provedení práce časově náročné spuštění před přijetím požadavků, jako je test podkladové databázi dostupnosti. Použití samostatné kontroly umožňuje orchestrator k rozlišení, zda je funkční, ale není ještě připraven aplikace nebo pokud aplikace se nepodařilo spustit. Další informace o připravenosti a testům aktivity v Kubernetes najdete v tématu [konfigurace aktivity a testy připravenosti](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) v dokumentaci ke Kubernetes.

Následující příklad ukazuje konfiguraci sondy připravenosti Kubernetes:

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Kontrolu založenou na metriky se zapisovačem vlastní odpovědi

Ukázková aplikace předvádí kontrolou stavu paměti se zapisovačem vlastní odpovědi.

`MemoryHealthCheck` sestavy snížený výkon, pokud aplikace používá více než dané prahové hodnoty paměti (v ukázkové aplikaci 1 GB). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> Obsahuje informace o systému uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Registrace služby kontroly stavu s <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`. Místo povolení stav zkontrolovat, že předáte <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, `MemoryHealthCheck` je registrována jako služba. Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrovaných služeb jsou k dispozici služby kontroly stavu a middlewarem. Doporučujeme, abyste registrace služby kontroly stavu jako deklarace služeb typu Singleton.

*CustomWriterStartup.cs*:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Volat stavu zkontrolujte Middleware v kanálu zpracování aplikace v `Startup.Configure`. A `WriteResponse` delegáta je k dispozici na `ResponseWriter` vlastnost výstup vlastní odpověď ve formátu JSON, kdy se spustí Kontrola stavu:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_Configure&highlight=6)]

`WriteResponse` Metoda formáty `CompositeHealthCheckResult` do JSON objekt a vrátí výstup JSON pro odpověď na kontrolu stavu:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Pokud chcete spustit kontrolu založenou na metriku s výstupní zapisovač vlastní odpovědi pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém řádku:

```console
dotnet run --scenario writer
```

> [!NOTE]
> [BeatPulse](https://github.com/Xabaril/BeatPulse) zahrnuje na základě metrik stavu zaškrtnutí scénářů, včetně disk storage a maximální hodnota aktivity kontroly.
>
> [BeatPulse](https://github.com/Xabaril/BeatPulse) není zachována nebo podporovaný společností Microsoft.

## <a name="filter-by-port"></a>Filtrovat podle portu

Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> s portem omezuje žádostí o kontrolu stavu na zadaný port. To se obvykle používá v prostředí kontejneru zpřístupňuje porty pro monitorování služby.

Ukázková aplikace se nakonfiguruje port pomocí [poskytovatele konfigurace proměnných prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider). Port je nastavena v *launchSettings.json* souboru a předána zprostředkovateli konfigurace přes proměnnou prostředí. Musíte také nakonfigurovat server tak, aby naslouchala na žádosti na portu pro správu.

Ukázková aplikace použít k předvedení správy konfigurace portů, vytvořte *launchSettings.json* ve *vlastnosti* složky.

Následující *launchSettings.json* soubor není zahrnutý v souborech projektu ukázkovou aplikaci a musí být vytvořeny ručně.

*Properties/launchSettings.json*:

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

Registrace služby kontroly stavu s <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`. Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> Určuje port pro správu (*ManagementPortStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=12,18)]

> [!NOTE]
> Vytváří se můžete vyhnout *launchSettings.json* soubor v ukázkové aplikaci tak, že nastavíte adresy URL a port pro správu explicitně v kódu. V *Program.cs* kde <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> je vytvořen, přidejte volání do <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> a poskytovat normální odpovědi koncového bodu aplikace a portu bodu správy. V *ManagementPortStartup.cs* kde <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> je volána, explicitně zadat port pro správu.
>
> *Program.cs*:
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
> *ManagementPortStartup.cs*:
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

Pokud chcete spustit scénář konfigurace portu správy pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém řádku:

```console
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Distribuovat knihovny kontroly stavu

Kontrola stavu distribuce jako knihovna:

1. Zápis kontrolou stavu, který implementuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní jako samostatná třída. Třídu můžete spolehnout na [injektáž závislostí (DI)](xref:fundamentals/dependency-injection), zadejte aktivace, a [s názvem možnosti](xref:fundamentals/configuration/options) pro přístup k datům konfigurace.

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

1. Zápis metody rozšíření s parametry, které náročné aplikace volá operaci v jeho `Startup.Configure` metoda. V následujícím příkladu předpokládejme následující podpis metody kontroly stavu:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Předchozí signatura Určuje, který `ExampleHealthCheck` vyžaduje další data k procesu stav zkontrolujte logiku testu. Data se poskytuje pro delegáta, používá k vytvoření instance kontroly stavu, když je kontrola stavu registrovaný pomocí metody rozšíření. V následujícím příkladu Určuje volitelný volající:

   * Název kontroly stavu (`name`). Pokud `null`, `example_health_check` se používá.
   * řetězcový datový bod pro kontroly stavu (`data1`).
   * celočíselný datový bod pro kontroly stavu (`data2`). Pokud `null`, `1` se používá.
   * stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). Výchozí hodnota je `null`. Pokud `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) se použije v hlášení stavu selhání.
   * značky (`IEnumerable<string>`).

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

## <a name="health-check-publisher"></a>Vydavatel kontroly stavu

Když <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> se přidá do kontejneru služby systém kontroly stavu pravidelně provádí kontroly stavu a volání `PublishAsync` k výsledku. To je užitečné v monitorování systému scénář, který očekává, že každý proces pro volání systém sledování pravidelně aby bylo možné zjistit stav nabízené stavu.

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Rozhraní obsahuje jedinou metodu:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> Můžete tak nastavit:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; Počáteční zpoždění byla aplikována za aplikace se spustí před spuštěním <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instancí. Zpoždění je použít jednou při spuštění a neplatí pro dalších iteracích. Výchozí hodnota je pět sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; Období <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> spuštění. Výchozí hodnota je 30 sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; Pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> je `null` (výchozí), služba stavu zaškrtnutí vydavatele běží všechny kontroly stavu registrované. Pro spuštění podmnožiny kontroly stavu, poskytují funkce, která filtruje sadu kontroly. Predikát je vyhodnocen jednotlivých období.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; Časový limit pro provedení stavu kontroluje všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instancí. Použití <xref:System.Threading.Timeout.InfiniteTimeSpan> provádět bez časového limitu. Výchozí hodnota je 30 sekund.

::: moniker range="= aspnetcore-2.2"

> [!WARNING]
> Ve verzi 2.2 technologie ASP.NET Core, nastavení <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> není kompilátorem respektovány <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> provádění; nastaví hodnotu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>. Tento problém bude opraven v ASP.NET Core 3.0. Další informace najdete v tématu [HealthCheckPublisherOptions.Period nastaví hodnotu vlastnosti. Zpoždění](https://github.com/aspnet/Extensions/issues/1041).

::: moniker-end

V ukázkové aplikaci `ReadinessPublisher` je <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace. Kontrola stavu se zaznamená do `Entries` nebude úspěšné a pro každou kontrolu:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=20,22-23)]

V ukázkové aplikaci `LivenessProbeStartup` například `StartupHostedService` kontroly připravenosti má dvě druhý zpoždění spuštění a spustí kontrolu každých 30 sekund. K aktivaci <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> zaregistruje implementaci vzorku `ReadinessPublisher` jako služba typu singleton v [injektáž závislostí (DI)](xref:fundamentals/dependency-injection) kontejneru:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

::: moniker range="= aspnetcore-2.2"

> [!NOTE]
> Následující alternativní řešení umožňuje přidat <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance služby kontejneru, když jeden nebo více jiných hostované služby již byly přidány do aplikace. Toto řešení nevyžaduje verzi technologie ASP.NET Core 3.0. Další informace najdete v tématu: https://github.com/aspnet/Extensions/issues/639.
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
> [BeatPulse](https://github.com/Xabaril/BeatPulse) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).
>
> [BeatPulse](https://github.com/Xabaril/BeatPulse) není zachována nebo podporovaný společností Microsoft.
