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
# <a name="health-checks-in-aspnet-core"></a>Kontroly stavu v ASP.NET Core

Od [Luke Latham](https://github.com/guardrex) a [Glenn Condron](https://github.com/glennc)

ASP.NET Core nabízí middleware pro kontrolu stavu a knihovny pro vytváření sestav stavu součástí infrastruktury aplikací.

Kontroly stavu jsou zpřístupněné aplikací jako koncové body HTTP. Koncové body kontroly stavu je možné nakonfigurovat pro nejrůznější scénáře monitorování v reálném čase:

* Sondy stavu můžou použít orchestrace kontejnerů a nástroje pro vyrovnávání zatížení ke kontrole stavu aplikace. Například produkt Orchestrator může reagovat na selhání kontroly stavu zastavením zavedení nasazení nebo restartováním kontejneru. Nástroj pro vyrovnávání zatížení může reagovat na poškozenou aplikaci směrováním provozu mimo instanci selhání do stavu v pořádku.
* Použití paměti, disku a dalších prostředků fyzického serveru se dá monitorovat v dobrém stavu.
* Kontroly stavu můžou testovat závislosti aplikace, jako jsou databáze a externí koncové body služby, aby se potvrdila dostupnost a normální fungování.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([stažení](xref:index#how-to-download-a-sample))

Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu. Pokud chcete pro daný scénář spustit ukázkovou aplikaci, použijte příkaz [dotnet Run](/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí. Podrobnosti o tom, jak používat ukázkovou aplikaci, najdete v souboru *Readme.MD* ukázkové aplikace a v popisech scénářů v tomto tématu.

## <a name="prerequisites"></a>Požadavky

Kontroly stavu se obvykle používají s externí službou monitorování nebo nástrojem Orchestrator Container Service ke kontrole stavu aplikace. Před přidáním kontrol stavu do aplikace se rozhodněte, který monitorovací systém se má použít. Monitorovací systém vyhodnotí, jaké typy kontrol stavu se mají vytvořit a jak nakonfigurovat jejich koncové body.

Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) .

Ukázková aplikace poskytuje spouštěcí kód k předvedení kontrol stavu pro několik scénářů. Scénář [testu databáze](#database-probe) kontroluje stav připojení k databázi pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). Scénář [testu DbContext](#entity-framework-core-dbcontext-probe) zkontroluje databázi pomocí EF Core `DbContext`. K prozkoumání scénářů databáze se jedná o ukázkovou aplikaci:

* Vytvoří databázi a poskytne připojovací řetězec v souboru *appSettings. JSON* .
* Obsahuje následující odkazy na balíček v souboru projektu:
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) je portem [BeatPulse](https://github.com/xabaril/beatpulse) , který společnost Microsoft neudržuje ani nepodporuje.

Další scénář kontroly stavu ukazuje, jak filtrovat kontroly stavu na port pro správu. Ukázková aplikace vyžaduje, abyste vytvořili soubor *Properties/launchSettings. JSON* , který obsahuje adresu URL pro správu a port pro správu. Další informace naleznete v části [Filter by port](#filter-by-port) .

## <a name="basic-health-probe"></a>Základní sonda stavu

U mnoha aplikací je základní konfigurace sondy stavu, která oznamuje dostupnost aplikace pro zpracování požadavků (*živý*), ke zjištění stavu aplikace dostačující.

Základní konfigurace registruje služby kontroly stavu a volá middleware kontroly stavu, aby reagoval na koncový bod adresy URL s odpovědí na stav. Ve výchozím nastavení nejsou k otestování konkrétní závislosti nebo subsystému registrovány žádné konkrétní kontroly stavu. Aplikace se považuje za v pořádku, pokud dokáže reagovat na adresu URL koncového bodu stavu. Výchozí<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>zapisovač odpovědí zapisuje stav () jako odpověď ve formátu prostého textu zpátky klientovi, což značí buď [funkčnosti. dobrý](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)stav, [funkčnosti. Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status není v pořádku.

Registrovat služby kontroly stavu v <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> nástroji `Startup.ConfigureServices`v. Přidejte middleware pro <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> kontrolu stavu v `Startup.Configure`kanálu zpracování požadavků.

V ukázkové aplikaci se koncový bod kontroly stavu vytvoří `/health` (*BasicStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/BasicStartup.cs?name=snippet1&highlight=5,10)]

Pokud chcete spustit scénář základní konfigurace pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```console
dotnet run --scenario basic
```

### <a name="docker-example"></a>Ukázka Docker

[Docker](xref:host-and-deploy/docker/index) nabízí vestavěnou `HEALTHCHECK` direktivu, která se dá použít ke kontrole stavu aplikace, která používá konfiguraci základní kontroly stavu:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Vytvořit kontroly stavu

Kontroly stavu jsou vytvářeny implementací <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> Metoda `Healthy` vrátíhodnotu,která`Degraded`označuje stav jako, nebo. `Unhealthy` `>` `Task<` <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> Výsledek je zapsán jako odpověď v podobě prostého textu s konfigurovatelným stavovým kódem (konfigurace je popsána v části [Možnosti kontroly stavu](#health-check-options) ). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>může také vracet volitelné páry klíč-hodnota.

### <a name="example-health-check"></a>Příklad kontroly stavu

Následující `ExampleHealthCheck` třída demonstruje rozložení kontroly stavu:

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

### <a name="register-health-check-services"></a>Registrovat služby kontroly stavu

Typ se přidá do služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>: `ExampleHealthCheck`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck<ExampleHealthCheck>("example_health_check");
}
```

Přetížení znázorněné v následujícím příkladu nastaví stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) na hodnotu ohlásit, když při kontrole stavu nahlásí chybu. <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> Pokud je stav selhání nastavený na `null` (výchozí), funkčnosti se hlásí jako [chybné](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) . Toto přetížení je užitečným scénářem pro autory knihoven, kde stav selhání uvedený v knihovně vynutila aplikace, když dojde k selhání kontroly stavu, pokud se při implementaci kontroly stavu splní nastavení.

*Značky* lze použít k filtrování kontrol stavu (popsaných dále v oddílu [Filtr kontrol stavu](#filter-health-checks) ).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>lze také spustit funkci lambda. V následujícím příkladu je název kontroly stavu zadán jako `Example` a zaškrtávací políčka vždy vrátí dobrý stav:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Example", () =>
            HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" })
}
```

### <a name="use-health-checks-middleware"></a>Použití middlewaru pro kontroly stavu

V `Startup.Configure`aplikaci volejte <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> kanál zpracování s adresou URL koncového bodu nebo relativní cestou:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health");
}
```

Pokud by kontroly stavu měly naslouchat na konkrétním portu, použijte přetížení <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> pro nastavení portu (popsáno dále v části [Filter by port](#filter-by-port) ):

```csharp
app.UseHealthChecks("/health", port: 8000);
```

Middleware pro kontrolu stavu je middleware *terminálu* v kanálu zpracování požadavků aplikace. První koncový bod kontroly stavu, který se přesně shoduje s adresou URL požadavku, je spuštěný a má krátké okruhy ve zbývající části kanálu middlewaru. Při krátkém okruhu se neprovede žádné middleware, které následují po odpovídající kontrole stavu.

## <a name="health-check-options"></a>Možnosti kontroly stavu

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>Poskytněte možnost přizpůsobení chování kontroly stavu:

* [Filtrovat kontroly stavu](#filter-health-checks)
* [Přizpůsobení stavového kódu HTTP](#customize-the-http-status-code)
* [Potlačit hlavičky mezipaměti](#suppress-cache-headers)
* [Přizpůsobení výstupu](#customize-output)

### <a name="filter-health-checks"></a>Filtrovat kontroly stavu

Ve výchozím nastavení middleware pro kontrolu stavu spouští všechny zaregistrované kontroly stavu. Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která vrací logickou <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> hodnotu pro možnost. V `Bar` následujícím příkladu je kontrole stavu Filtrováno podle značky ( `true` `bar_tag`) v podmíněném příkazu funkce, kde <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> je vrácena pouze v případě, že odpovídá `foo_tag` vlastnost kontroly stavu nebo `baz_tag`:

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

### <a name="customize-the-http-status-code"></a>Přizpůsobení stavového kódu HTTP

Slouží <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> k přizpůsobení mapování stavu na stavové kódy HTTP. Následující <xref:Microsoft.AspNetCore.Http.StatusCodes> přiřazení jsou výchozí hodnoty používané middlewarem. Změňte hodnoty stavového kódu tak, aby splňovaly vaše požadavky.

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

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>Určuje, jestli middleware pro kontrolu stavu přidá hlavičky HTTP do odezvy testu, aby nedocházelo k ukládání odpovědí do mezipaměti. Pokud je `false` hodnota (výchozí), middleware nastaví nebo `Cache-Control`přepíše hlavičky, `Expires`a `Pragma` , aby nedocházelo k ukládání odpovědí do mezipaměti. Pokud je `true`hodnota, middleware neupraví hlavičky mezipaměti odpovědi.

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

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> Možnost Získá nebo nastaví delegáta použitý k zápisu odpovědi. Výchozí delegát zapisuje minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).

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

Kontrola stavu může určit databázový dotaz, který se má spustit jako logický test, aby označoval, zda databáze obvykle reaguje.

Ukázková aplikace pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), knihovny kontroly stavu pro aplikace ASP.NET Core, provede kontrolu stavu databáze SQL Server. `AspNetCore.Diagnostics.HealthChecks``SELECT 1` spustí dotaz na databázi, aby se ověřilo, že připojení k databázi je v pořádku.

> [!WARNING]
> Při kontrole připojení k databázi pomocí dotazu vyberte dotaz, který se rychle vrátí. Přístup k dotazům spouští riziko přetížení databáze a snížení výkonu. Ve většině případů není nutné spustit testovací dotaz. Stačí pouze provést úspěšné připojení k databázi. Pokud zjistíte, že je nutné spustit dotaz, zvolte jednoduchý dotaz SELECT, například `SELECT 1`.

Zahrňte odkaz na balíček do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Zadejte platný připojovací řetězec databáze do souboru *appSettings. JSON* ukázkové aplikace. Aplikace používá SQL Server databázi s názvem `HealthCheckSample`:

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

Registrovat služby kontroly stavu v <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> nástroji `Startup.ConfigureServices`v. Ukázková aplikace volá `AddSqlServer` metodu s připojovacím řetězcem databáze (*DbHealthStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Zavolá middleware pro kontrolu stavu v kanálu zpracování `Startup.Configure`aplikace v:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_Configure)]

Pokud chcete spustit scénář testu databáze pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```console
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) je portem [BeatPulse](https://github.com/xabaril/beatpulse) , který společnost Microsoft neudržuje ani nepodporuje.

## <a name="entity-framework-core-dbcontext-probe"></a>Test Entity Framework Core DbContext

Tato `DbContext` kontrolu potvrdí, že aplikace může komunikovat s databází nakonfigurovanou pro EF Core `DbContext`. Tato `DbContext` kontrolu je podporovaná v aplikacích, které:

* Použijte [jádro Entity Framework (EF)](/ef/core/).
* Zahrňte odkaz na balíček [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>`zaregistruje kontrolu stavu pro `DbContext`. `DbContext` Je dodán`TContext` jako metoda metodě. K dispozici je přetížení pro konfiguraci stavu selhání, značek a vlastního testovacího dotazu.

Ve výchozím nastavení:

* `DbContextHealthCheck` Volá metodu`CanConnectAsync` EF Core. Můžete přizpůsobit, která operace je spuštěna při kontrole stavu pomocí `AddDbContextCheck` přetížení metod.
* Název kontroly stavu je název `TContext` typu.

V ukázkové aplikaci `AppDbContext` je k `AddDbContextCheck` dispozici a zaregistrováno jako služba v `Startup.ConfigureServices`nástroji.

*DbContextHealthStartup.cs*:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

V ukázkové aplikaci `UseHealthChecks` přidá middleware pro kontrolu stavu v `Startup.Configure`.

*DbContextHealthStartup.cs*:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_Configure)]

Pokud chcete spustit `DbContext` scénář sondy pomocí ukázkové aplikace, zkontrolujte, že databáze určená připojovacím řetězcem v instanci SQL Server neexistuje. Pokud databáze existuje, odstraňte ji.

Ze složky projektu v příkazovém prostředí spusťte následující příkaz:

```console
dotnet run --scenario dbcontext
```

Po spuštění aplikace ověřte stav tak, že v prohlížeči vytvoříte požadavek na `/health` koncový bod. Databáze a `AppDbContext` neexistuje, takže aplikace nabízí následující odpověď:

```
Unhealthy
```

Aktivujte ukázkovou aplikaci pro vytvoření databáze. Vytvořte žádost na `/createdatabase`. Aplikace odpoví:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Vytvořte požadavek na `/health` koncový bod. Databáze a kontext existují, takže aplikace reaguje:

```
Healthy
```

Aktivujte ukázkovou aplikaci, aby se odstranila databáze. Vytvořte žádost na `/deletedatabase`. Aplikace odpoví:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Vytvořte požadavek na `/health` koncový bod. Aplikace poskytuje poškozenou reakci:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Samostatné sondy pro připravenost a živý provoz

V některých scénářích hostování se používá pár kontrol stavu, který rozlišuje dva stavy aplikace:

* Aplikace funguje, ale ještě není připravená na příjem požadavků. Tento stav je *připravenost*aplikace.
* Aplikace funguje a reaguje na požadavky. Tento stav je živými aplikacemi.

Kontrola připravenosti obvykle provádí rozsáhlejší a časově náročnou sadu kontrol, aby bylo možné zjistit, zda jsou k dispozici všechny subsystémy a prostředky aplikace. K dispozici je pouze rychlá kontrolu, která určuje, zda je aplikace k dispozici pro zpracování požadavků. Po úspěšném provedení kontroly připravenosti aplikace nemusíte aplikaci navýšit dál s nákladnými sadami kontrol&mdash;připravenosti, které vyžadují kontrolu živého ověření.

Ukázková aplikace obsahuje kontrolu stavu, která oznamuje dokončení dlouhotrvající úlohy po spuštění v [hostované službě](xref:fundamentals/host/hosted-services). Zpřístupňuje `true` vlastnost, která hostovaná služba může nastavit na hodnotu po dokončení její dlouhotrvající úlohy (StartupHostedServiceHealthCheck.cs): `StartupTaskCompleted` `StartupHostedServiceHealthCheck`

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Dlouhotrvající úloha na pozadí se spouští v [hostované službě](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*). Při uzavírání úkolu `StartupHostedServiceHealthCheck.StartupTaskCompleted` je nastaveno na `true`:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Kontroly stavu jsou registrovány <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> ve `Startup.ConfigureServices` službě společně s hostovanou službou. Vzhledem k tomu, že hostovaná služba musí nastavit vlastnost pro kontrolu stavu, je tato kontrolu také zaregistrována v kontejneru služby (*LivenessProbeStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

V kanálu pro zpracování aplikací v `Startup.Configure`se zavolá middleware pro kontrolu stavu. V ukázkové aplikaci jsou koncové body kontroly stavu vytvořeny v `/health/ready` pro kontrolu připravenosti a `/health/live` pro kontrolu živého provozu. Kontrola připravenosti filtruje kontrolu stavu na kontrolu stavu se `ready` značkou. Kontrola živého odfiltruje vrácení `StartupHostedServiceHealthCheck` `false` se změnami v [HealthCheckOptions. predikátu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Další informace najdete v tématu [filtrování kontrol stavu](#filter-health-checks)):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_Configure)]

Pokud chcete spustit scénář konfigurace připravenosti/živých aplikací pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```console
dotnet run --scenario liveness
```

V prohlížeči navštivte `/health/ready` několikrát, dokud neuplyne 15 sekund. Zprávy o kontrole stavu nejsou v *pořádku* po dobu prvních 15 sekund. Po 15 sekundách koncový bod hlásí stav *v pořádku*, který odráží dokončení dlouhotrvající úlohy hostované službou.

V tomto příkladu se vytvoří také Vydavatel kontroly stavu<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> (implementace), který spouští první kontrolu připravenosti se dvěma sekundami. Další informace najdete v části [Vydavatel kontroly stavu](#health-check-publisher) .

### <a name="kubernetes-example"></a>Příklad Kubernetes

Používání samostatné kontroly připravenosti a živých kontrol je užitečné v prostředí, jako je [Kubernetes](https://kubernetes.io/). V Kubernetes může být aplikace nutná k provádění časově náročné práce při spuštění před přijetím požadavků, jako je například test dostupnosti podkladové databáze. Použití samostatných kontrol umožňuje nástroji Orchestrator rozlišit, jestli aplikace funguje, ale ještě není připravená, nebo jestli se aplikace nedala spustit. Další informace o testování připravenosti a provozní sondy v Kubernetes najdete v tématu [Konfigurace živých a připravených sond](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) v dokumentaci k Kubernetes.

Následující příklad ukazuje konfiguraci testu připravenosti na Kubernetes:

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Test založený na metrikách s vlastním zapisovačem odpovědí

Ukázková aplikace ukazuje kontrolu stavu paměti pomocí vlastního zapisovače odpovědí.

`MemoryHealthCheck`hlásí stav není v pořádku, pokud aplikace používá více než danou prahovou hodnotu paměti (1 GB v ukázkové aplikaci). Zahrnuje informace o uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs):* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Registrovat služby kontroly stavu v <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> nástroji `Startup.ConfigureServices`v. Místo povolení kontroly stavu předáním <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `MemoryHealthCheck` jako služby je zaregistrována jako služba. Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrované služby jsou k dispozici pro služby kontroly stavu a middleware. Službu pro kontrolu stavu doporučujeme zaregistrovat jako služby typu singleton.

*CustomWriterStartup.cs*:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

V kanálu pro zpracování aplikací v `Startup.Configure`se zavolá middleware pro kontrolu stavu. `WriteResponse` Delegát poskytne`ResponseWriter` vlastnost pro výstup vlastní odpovědi JSON, když se spustí kontroly stavu:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_Configure&highlight=6)]

`WriteResponse` Metoda zformátujeobjektdoobjektuJSONaposkytnevýstupJSON`CompositeHealthCheckResult` pro odpověď kontroly stavu:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Pokud chcete spustit test založený na metrikách s vlastním výstupem zapisovače odpovědí pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```console
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje scénáře kontroly stavu založené na metrikách, včetně úložného prostoru na disku a maximální hodnoty živé kontroly.
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) je portem [BeatPulse](https://github.com/xabaril/beatpulse) , který společnost Microsoft neudržuje ani nepodporuje.

## <a name="filter-by-port"></a>Filtrovat podle portu

Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> s portem omezí požadavky na kontrolu stavu na zadaný port. Obvykle se používá v prostředí kontejneru k vystavení portu pro monitorovací služby.

Ukázková aplikace nakonfiguruje port pomocí [poskytovatele konfigurace proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider). Port je nastaven v souboru *launchSettings. JSON* a předán poskytovateli konfigurace prostřednictvím proměnné prostředí. Také je nutné nakonfigurovat server tak, aby naslouchal žádosti na portu pro správu.

Pokud chcete ukázkovou aplikaci použít k předvedení konfigurace portů pro správu, vytvořte soubor *launchSettings. JSON* ve složce *Properties (vlastnosti* ).

Následující soubor *launchSettings. JSON* není zahrnutý v souborech projektu ukázkové aplikace a je nutné ho vytvořit ručně.

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

Registrovat služby kontroly stavu v <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> nástroji `Startup.ConfigureServices`v. Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> Určuje port pro správu (*ManagementPortStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=12,18)]

> [!NOTE]
> Vytvoření souboru *launchSettings. JSON* v ukázkové aplikaci se vyhnete tak, že v kódu nastavíte adresy URL a port pro správu explicitně. V *program.cs* , kde <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> je vytvořena, přidejte volání do <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> a poskytněte normální koncový bod odpovědi aplikace a koncový bod portu pro správu. V *ManagementPortStartup.cs* , <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> kde je volána, určete explicitně port pro správu.
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

Pokud chcete spustit scénář konfigurace portu pro správu pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```console
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Distribuce knihovny kontroly stavu

Postup při distribuci kontroly stavu jako knihovny:

1. Zapište kontrolu stavu, která implementuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> rozhraní jako samostatnou třídu. Třída může spoléhat na [vkládání závislostí (di)](xref:fundamentals/dependency-injection), aktivovat typ a [pojmenované možnosti](xref:fundamentals/configuration/options) pro přístup k datům konfigurace.

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

1. Zapište metodu rozšíření s parametry, které aplikace spotřebovávají ve své `Startup.Configure` metodě. V následujícím příkladu předpokládejme následující signatura metody kontroly stavu:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Předchozí signatura indikuje, že `ExampleHealthCheck` pro zpracování logiky sondy kontroly stavu vyžaduje další data. Data jsou k dispozici delegátovi použitému k vytvoření instance kontroly stavu při registraci kontroly stavu s metodou rozšíření. V následujícím příkladu volající určuje nepovinné:

   * název kontroly stavu (`name`). Pokud `null`sepoužívá. `example_health_check`
   * datový bod řetězce pro kontrolu stavu (`data1`).
   * celočíselný datový bod pro kontrolu stavu (`data2`). Pokud `null`sepoužívá. `1`
   * stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). Výchozí hodnota je `null`. Pokud `null`je [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) není v pořádku, je hlášen stav selhání.
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

Když se přidá do kontejneru služby, systém kontroly stavu pravidelně spouští kontroly stavu a volání `PublishAsync` s výsledkem. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> To je užitečné ve scénáři sledování stavu založeném na nabízených oznámeních, který očekává, že každý proces bude pravidelně volat monitorovací systém, aby se zjistil stav.

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Rozhraní má jedinou metodu:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>umožňuje nastavit:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>Počáteční zpoždění použité po spuštění aplikace před spuštěním <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instancí. &ndash; Zpoždění se použije jednou při spuštění a nevztahuje se na další iterace. Výchozí hodnota je pět sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; Doba spuštění.<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Výchozí hodnota je 30 sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; Pokud je(`null` výchozí), spustí služba Vydavatel kontroly stavu všechny zaregistrované kontroly stavu. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která filtruje sadu kontrol. Predikát je vyhodnocen každou periodu.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>Časový limit pro spuštění kontrol stavu pro všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance. &ndash; Použijte <xref:System.Threading.Timeout.InfiniteTimeSpan> k provedení bez časového limitu. Výchozí hodnota je 30 sekund.

::: moniker range="= aspnetcore-2.2"

> [!WARNING]
> Ve verzi ASP.NET Core 2,2 není nastavení <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> dodrženo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementací. nastavuje hodnotu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>. Tento problém bude opravený v ASP.NET Core 3,0. Další informace naleznete v tématu [HealthCheckPublisherOptions. period nastaví hodnotu. Zpoždění](https://github.com/aspnet/Extensions/issues/1041).

::: moniker-end

V ukázkové aplikaci `ReadinessPublisher` <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> je implementace. Stav kontroly stavu se zaznamenává `Entries` a protokoluje pro každou kontrolu:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=20,22-23)]

V `LivenessProbeStartup` příkladu`StartupHostedService` ukázkové aplikace má kontroler připravenosti dvě sekundy na spuštění a spustí kontrolu každých 30 sekund. Chcete-li <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> aktivovat implementaci, ukázka se `ReadinessPublisher` registruje jako služba s jedním prvkem v kontejneru [Injektáže (di) pro vkládání závislostí](xref:fundamentals/dependency-injection) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

::: moniker range="= aspnetcore-2.2"

> [!NOTE]
> Následující alternativní řešení povoluje přidání <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance do kontejneru služby, pokud již byla do aplikace přidána jedna nebo více dalších hostovaných služeb. Toto řešení se nebude vyžadovat u vydání ASP.NET Core 3,0. Další informace najdete v tématu: https://github.com/aspnet/Extensions/issues/639.
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
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) je portem [BeatPulse](https://github.com/xabaril/beatpulse) , který společnost Microsoft neudržuje ani nepodporuje.

## <a name="restrict-health-checks-with-mapwhen"></a>Omezení kontrol stavu pomocí MapWhen

Použijte <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> k podmíněně větvení kanálu žádostí pro koncové body kontroly stavu.

V následujícím příkladu větví kanál `MapWhen` žádosti o aktivaci middlewaru kontroly stavu, pokud se `api/HealthCheck` pro koncový bod přijme požadavek GET:

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

Další informace naleznete v tématu <xref:fundamentals/middleware/index#use-run-and-map>.
