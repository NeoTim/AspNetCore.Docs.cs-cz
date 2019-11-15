---
title: Kontroly stavu v ASP.NET Core
author: guardrex
description: Naučte se, jak nastavit kontroly stavu pro infrastrukturu ASP.NET Core, jako jsou aplikace a databáze.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 11/13/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: 4a4606a58178018f0d71d467d4c8b6c9982c09dc
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74115991"
---
# <a name="health-checks-in-aspnet-core"></a>Kontroly stavu v ASP.NET Core

Od [Luke Latham](https://github.com/guardrex) a [Glenn Condron](https://github.com/glennc)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core nabízí kontrolu stavu middlewaru a knihovny pro vytváření sestav stavu součástí infrastruktury aplikace.

Kontroly stavu jsou zpřístupněné aplikací jako koncové body HTTP. Koncové body kontroly stavu je možné nakonfigurovat pro nejrůznější scénáře monitorování v reálném čase:

* Sondy stavu můžou použít orchestrace kontejnerů a nástroje pro vyrovnávání zatížení ke kontrole stavu aplikace. Například produkt Orchestrator může reagovat na selhání kontroly stavu zastavením zavedení nasazení nebo restartováním kontejneru. Nástroj pro vyrovnávání zatížení může reagovat na poškozenou aplikaci směrováním provozu mimo instanci selhání do stavu v pořádku.
* Použití paměti, disku a dalších prostředků fyzického serveru se dá monitorovat v dobrém stavu.
* Kontroly stavu můžou testovat závislosti aplikace, jako jsou databáze a externí koncové body služby, aby se potvrdila dostupnost a normální fungování.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu. Pokud chcete pro daný scénář spustit ukázkovou aplikaci, použijte příkaz [dotnet Run](/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí. Podrobnosti o tom, jak používat ukázkovou aplikaci, najdete v souboru *Readme.MD* ukázkové aplikace a v popisech scénářů v tomto tématu.

## <a name="prerequisites"></a>Požadavky

Kontroly stavu se obvykle používají s externí službou monitorování nebo nástrojem Orchestrator Container Service ke kontrole stavu aplikace. Před přidáním kontrol stavu do aplikace se rozhodněte, který monitorovací systém se má použít. Monitorovací systém vyhodnotí, jaké typy kontrol stavu se mají vytvořit a jak nakonfigurovat jejich koncové body.

Na balíček [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) se odkazuje implicitně pro ASP.NET Core aplikace. Chcete-li provádět kontroly stavu pomocí Entity Framework Core, přidejte odkaz na balíček do balíčku [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) .

Ukázková aplikace poskytuje spouštěcí kód k předvedení kontrol stavu pro několik scénářů. Scénář [testu databáze](#database-probe) kontroluje stav připojení k databázi pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). Scénář [testu DbContext](#entity-framework-core-dbcontext-probe) zkontroluje databázi pomocí `DbContext`EF Core. K prozkoumání scénářů databáze se jedná o ukázkovou aplikaci:

* Vytvoří databázi a poskytne připojovací řetězec v souboru *appSettings. JSON* .
* Obsahuje následující odkazy na balíček v souboru projektu:
  * [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.

Další scénář kontroly stavu ukazuje, jak filtrovat kontroly stavu na port pro správu. Ukázková aplikace vyžaduje, abyste vytvořili soubor *Properties/launchSettings. JSON* , který obsahuje adresu URL pro správu a port pro správu. Další informace naleznete v části [Filter by port](#filter-by-port) .

## <a name="basic-health-probe"></a>Základní sonda stavu

U mnoha aplikací je základní konfigurace sondy stavu, která oznamuje dostupnost aplikace pro zpracování požadavků (*živý*), ke zjištění stavu aplikace dostačující.

Základní konfigurace registruje služby kontroly stavu a volá middleware kontroly stavu, aby reagovala na koncový bod adresy URL s odpovědí na stav. Ve výchozím nastavení nejsou k otestování konkrétní závislosti nebo subsystému registrovány žádné konkrétní kontroly stavu. Aplikace se považuje za v pořádku, pokud dokáže reagovat na adresu URL koncového bodu stavu. Výchozí zapisovač odpovědí zapisuje stav (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) jako odpověď v podobě prostého textu zpátky klientovi, což značí buď [funkčnosti. dobrý](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)stav, [funkčnosti. Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status není v pořádku.

Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`. Vytvořte koncový bod kontroly stavu voláním `MapHealthChecks` v `Startup.Configure`.

V ukázkové aplikaci se koncový bod kontroly stavu vytvoří při `/health` (*BasicStartup.cs*):

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

Pokud chcete spustit scénář základní konfigurace pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Ukázka Docker

[Docker](xref:host-and-deploy/docker/index) nabízí integrovanou `HEALTHCHECK` direktivu, která se dá použít ke kontrole stavu aplikace, která používá konfiguraci základní kontroly stavu:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Vytvořit kontroly stavu

Kontroly stavu jsou vytvářeny implementací rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>. Metoda <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> vrátí <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, která označuje stav jako `Healthy`, `Degraded`nebo `Unhealthy`. Výsledek je zapsán jako odpověď v podobě prostého textu s konfigurovatelným stavovým kódem (konfigurace je popsána v části [Možnosti kontroly stavu](#health-check-options) ). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> může také vracet volitelné páry klíč-hodnota.

Následující třída `ExampleHealthCheck` ukazuje rozložení kontroly stavu. Logika kontrol stavu je umístěna v metodě `CheckHealthAsync`. Následující příklad nastaví fiktivní proměnnou `healthCheckResultHealthy``true`. Pokud je hodnota `healthCheckResultHealthy` nastavena na `false`, vrátí [HealthCheckResult.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) stav není v pořádku.

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

## <a name="register-health-check-services"></a>Registrovat služby kontroly stavu

`ExampleHealthCheck` typ se přidá do služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> v `Startup.ConfigureServices`:

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

Přetížení <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> znázorněné v následujícím příkladu nastaví stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) na hlášení, že při kontrole stavu hlásí chybu. Je-li stav selhání nastaven na `null` (výchozí), [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) je hlášena hodnota není v pořádku. Toto přetížení je užitečným scénářem pro autory knihoven, kde stav selhání uvedený v knihovně vynutila aplikace, když dojde k selhání kontroly stavu, pokud se při implementaci kontroly stavu splní nastavení.

*Značky* lze použít k filtrování kontrol stavu (popsaných dále v oddílu [Filtr kontrol stavu](#filter-health-checks) ).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> může také spustit funkci lambda. V následujícím příkladu je název kontroly stavu zadán jako `Example` a funkce check vždy vrátí dobrý stav:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

Zavolejte <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> k předání argumenty do implementace kontroly stavu. V následujícím příkladu `TestHealthCheckWithArgs` přijímá celočíselnou hodnotu a řetězec, který se použije při volání <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>:

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

`TestHealthCheckWithArgs` je zaregistrován voláním `AddTypeActivatedCheck` s celým číslem a řetězcem předaným do implementace:

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a>Použití směrování kontrol stavu

V `Startup.Configure`volejte `MapHealthChecks` v Tvůrci koncových bodů s adresou URL koncového bodu nebo relativní cestou:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a>Vyžadovat hostitele

Zavolejte `RequireHost` k zadání jednoho nebo více povolených hostitelů pro koncový bod kontroly stavu. Hostitelé by měli být místo punycodei Unicode a můžou zahrnovat port. Pokud není zadaná kolekce, bude přijat libovolný hostitel.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

Další informace naleznete v části [Filter by port](#filter-by-port) .

### <a name="require-authorization"></a>Vyžadovat autorizaci

Zavolejte `RequireAuthorization` pro spuštění autorizačního middlewaru na koncovém bodu žádosti o kontrolu stavu. Přetížení `RequireAuthorization` přijímá jednu nebo více zásad autorizace. Pokud zásada není zadaná, použije se výchozí zásada autorizace.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a>Povolení žádostí nepůvodního zdroje (CORS)

I když ruční provádění kontrol stavu z prohlížeče není běžným scénářem použití, je možné middleware CORS povolit voláním `RequireCors` v koncových bodech pro kontroly stavu. Přetížení `RequireCors` přijímá delegáta zásad CORS (`CorsPolicyBuilder`) nebo název zásady. Pokud zásada není zadaná, použije se výchozí zásada CORS. Další informace najdete v tématu <xref:security/cors>.

## <a name="health-check-options"></a>Možnosti kontroly stavu

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> poskytují příležitost k přizpůsobení chování kontroly stavu:

* [Filtrovat kontroly stavu](#filter-health-checks)
* [Přizpůsobení stavového kódu HTTP](#customize-the-http-status-code)
* [Potlačit hlavičky mezipaměti](#suppress-cache-headers)
* [Přizpůsobení výstupu](#customize-output)

### <a name="filter-health-checks"></a>Filtrovat kontroly stavu

Ve výchozím nastavení middleware pro kontrolu stavu spouští všechny zaregistrované kontroly stavu. Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která vrací logickou hodnotu pro <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> možnost. V následujícím příkladu je `Bar` kontrole stavu vyfiltrováno pomocí značky (`bar_tag`) v podmíněném příkazu funkce, kde `true` je vrácena pouze v případě, že <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> vlastnost pro kontrolu stavu odpovídá `foo_tag` nebo `baz_tag`:

V `Startup.ConfigureServices`:

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

V `Startup.Configure``Predicate` filtruje kontrolu stavu "bar". Pouze foo a Baz Execute:

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

### <a name="customize-the-http-status-code"></a>Přizpůsobení stavového kódu HTTP

K přizpůsobení mapování stavu na stavové kódy HTTP použijte <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>. Následující přiřazení <xref:Microsoft.AspNetCore.Http.StatusCodes> jsou výchozími hodnotami, které používá middleware. Změňte hodnoty stavového kódu tak, aby splňovaly vaše požadavky.

V `Startup.Configure`:

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

### <a name="suppress-cache-headers"></a>Potlačit hlavičky mezipaměti

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> určuje, zda middleware pro kontrolu stavu přidává hlavičky HTTP do odezvy testu, aby nedocházelo k ukládání odpovědí do mezipaměti. Pokud je hodnota `false` (výchozí), middleware nastaví nebo přepíše hlavičky `Cache-Control`, `Expires`a `Pragma`, aby nedocházelo k ukládání odpovědí do mezipaměti. Pokud je hodnota `true`, middleware neupraví hlavičky mezipaměti odpovědi.

V `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a>Přizpůsobení výstupu

Možnost <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> Získá nebo nastaví delegáta použitý k zápisu odpovědi.

V `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

Výchozí delegát zapisuje minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Následující vlastní delegát, `WriteResponse`, vypíše vlastní odpověď JSON:

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

Systém kontroly stavu neposkytuje integrovanou podporu pro komplexní formáty vrácených hodnot JSON, protože formát je specifický pro váš výběr systému monitorování. V předchozím příkladu si můžete přizpůsobit `JObject` podle potřeby tak, aby vyhovovaly vašim potřebám.

## <a name="database-probe"></a>Test databáze

Kontrola stavu může určit databázový dotaz, který se má spustit jako logický test, aby označoval, zda databáze obvykle reaguje.

Ukázková aplikace pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), knihovny kontroly stavu pro aplikace ASP.NET Core, provede kontrolu stavu databáze SQL Server. `AspNetCore.Diagnostics.HealthChecks` spustí dotaz `SELECT 1` na databázi, aby bylo možné potvrdit, že připojení k databázi je v pořádku.

> [!WARNING]
> Při kontrole připojení k databázi pomocí dotazu vyberte dotaz, který se rychle vrátí. Přístup k dotazům spouští riziko přetížení databáze a snížení výkonu. Ve většině případů není nutné spustit testovací dotaz. Stačí pouze provést úspěšné připojení k databázi. Pokud zjistíte, že je nutné spustit dotaz, zvolte jednoduchý dotaz SELECT, například `SELECT 1`.

Zahrňte odkaz na balíček do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Zadejte platný připojovací řetězec databáze do souboru *appSettings. JSON* ukázkové aplikace. Aplikace používá databázi SQL Server s názvem `HealthCheckSample`:

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`. Ukázková aplikace volá metodu `AddSqlServer` s připojovacím řetězcem databáze (*DbHealthStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Koncový bod kontroly stavu je vytvořen voláním `MapHealthChecks` v `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Pokud chcete spustit scénář testu databáze pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.

## <a name="entity-framework-core-dbcontext-probe"></a>Test Entity Framework Core DbContext

`DbContext` ověří, jestli aplikace může komunikovat s databází nakonfigurovanou pro EF Core `DbContext`. Ověření `DbContext` je podporované v aplikacích, které:

* Použijte [jádro Entity Framework (EF)](/ef/core/).
* Zahrňte odkaz na balíček [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>` registruje kontrolu stavu pro `DbContext`. `DbContext` je dodána jako `TContext` metody. K dispozici je přetížení pro konfiguraci stavu selhání, značek a vlastního testovacího dotazu.

Ve výchozím nastavení:

* `DbContextHealthCheck` volá metodu `CanConnectAsync` EF Core. Můžete přizpůsobit, která operace je spuštěna při kontrole stavu pomocí přetížení metod `AddDbContextCheck`.
* Název kontroly stavu je název `TContext`ho typu.

V ukázkové aplikaci je `AppDbContext` k dispozici `AddDbContextCheck` a zaregistrováno jako služba v `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

Koncový bod kontroly stavu je vytvořen voláním `MapHealthChecks` v `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Pokud chcete spustit scénář `DbContext` testu pomocí ukázkové aplikace, zkontrolujte, že databáze určená připojovacím řetězcem v instanci SQL Server neexistuje. Pokud databáze existuje, odstraňte ji.

Ze složky projektu v příkazovém prostředí spusťte následující příkaz:

```dotnetcli
dotnet run --scenario dbcontext
```

Po spuštění aplikace ověřte stav tak, že v prohlížeči vytvoříte požadavek na koncový bod `/health`. Databáze a `AppDbContext` neexistují, takže aplikace nabízí následující odpověď:

```
Unhealthy
```

Aktivujte ukázkovou aplikaci pro vytvoření databáze. Vytvořte žádost o `/createdatabase`. Aplikace odpoví:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Vytvořte požadavek na koncový bod `/health`. Databáze a kontext existují, takže aplikace reaguje:

```
Healthy
```

Aktivujte ukázkovou aplikaci, aby se odstranila databáze. Vytvořte žádost o `/deletedatabase`. Aplikace odpoví:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Vytvořte požadavek na koncový bod `/health`. Aplikace poskytuje poškozenou reakci:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Samostatné sondy pro připravenost a živý provoz

V některých scénářích hostování se používá pár kontrol stavu, který rozlišuje dva stavy aplikace:

* Aplikace funguje, ale ještě není připravená na příjem požadavků. Tento stav je *připravenost*aplikace.
* Aplikace funguje a reaguje na požadavky. Tento stav je *živými*aplikacemi.

Kontrola připravenosti obvykle provádí rozsáhlejší a časově náročnou sadu kontrol, aby bylo možné zjistit, zda jsou k dispozici všechny subsystémy a prostředky aplikace. K dispozici je pouze rychlá kontrolu, která určuje, zda je aplikace k dispozici pro zpracování požadavků. Po úspěšném provedení kontroly připravenosti aplikace nemusíte aplikaci dále zatěžovat s nákladným nastavením kontrol připravenosti&mdash;další kontroly vyžadují jenom kontrolu živého provozu.

Ukázková aplikace obsahuje kontrolu stavu, která oznamuje dokončení dlouhotrvající úlohy po spuštění v [hostované službě](xref:fundamentals/host/hosted-services). `StartupHostedServiceHealthCheck` zpřístupňuje vlastnost, `StartupTaskCompleted`, kterou může hostovaná služba nastavit na `true` po dokončení dlouhotrvající úlohy (*StartupHostedServiceHealthCheck.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Dlouhotrvající úloha na pozadí se spouští v [hostované službě](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*). Po uzavření úlohy je `StartupHostedServiceHealthCheck.StartupTaskCompleted` nastaveno na `true`:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Tato kontrolu stavu je zaregistrovaná ve službě <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> v `Startup.ConfigureServices` spolu s hostovanou službou. Vzhledem k tomu, že hostovaná služba musí nastavit vlastnost pro kontrolu stavu, je tato kontrolu také zaregistrována v kontejneru služby (*LivenessProbeStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Koncový bod kontroly stavu je vytvořen voláním `MapHealthChecks` v `Startup.Configure`. V ukázkové aplikaci jsou koncové body kontroly stavu vytvořeny v:

* `/health/ready` pro kontrolu připravenosti. Kontrola připravenosti filtruje kontrolu stavu na kontrolu stavu pomocí značky `ready`.
* `/health/live` pro kontrolu živého provozu. Kontrola živého odfiltruje `StartupHostedServiceHealthCheck` vrácením `false` v [HealthCheckOptions. predikátu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Další informace najdete v tématu [filtrování kontrol stavu](#filter-health-checks)).

V následujícím příkladu kódu:

* Kontrola připravenosti používá všechny registrované kontroly se značkou ' Read '.
* `Predicate` vyloučí všechny kontroly a vrátí 200 – OK.

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

Pokud chcete spustit scénář konfigurace připravenosti/živých aplikací pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario liveness
```

V prohlížeči navštivte `/health/ready` několikrát, dokud neuplyne 15 sekund. Zprávy o kontrole stavu nejsou v *pořádku* po dobu prvních 15 sekund. Po 15 sekundách koncový bod hlásí stav *v pořádku*, který odráží dokončení dlouhotrvající úlohy hostované službou.

V tomto příkladu se vytvoří také Vydavatel kontroly stavu (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace), který spouští první kontrolu připravenosti se dvěma sekundami. Další informace najdete v části [Vydavatel kontroly stavu](#health-check-publisher) .

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

`MemoryHealthCheck` hlásí snížený stav, pokud aplikace používá více než určenou prahovou hodnotu paměti (1 GB v ukázkové aplikaci). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> obsahuje informace o uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`. Místo povolení kontroly stavu předáním do <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>je `MemoryHealthCheck` zaregistrován jako služba. Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrované služby jsou k dispozici pro služby kontroly stavu a middleware. Službu pro kontrolu stavu doporučujeme zaregistrovat jako služby typu singleton.

V ukázkové aplikaci (*CustomWriterStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Koncový bod kontroly stavu je vytvořen voláním `MapHealthChecks` v `Startup.Configure`. `WriteResponse` delegátovi je poskytnuta vlastnost `ResponseWriter` pro výstup vlastní odpovědi JSON, když se spustí kontroly stavu:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

Metoda `WriteResponse` formátuje `CompositeHealthCheckResult` na objekt JSON a poskytuje výstup JSON pro odpověď kontroly stavu:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Pokud chcete spustit test založený na metrikách s vlastním výstupem zapisovače odpovědí pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje scénáře kontroly stavu založené na metrikách, včetně úložného prostoru na disku a maximální hodnoty živé kontroly.
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.

## <a name="filter-by-port"></a>Filtrovat podle portu

Zavolejte `RequireHost` na `MapHealthChecks` pomocí vzoru adresy URL, který určuje port pro omezení požadavků na kontrolu stavu na zadaný port. Obvykle se používá v prostředí kontejneru k vystavení portu pro monitorovací služby.

Ukázková aplikace nakonfiguruje port pomocí [poskytovatele konfigurace proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider). Port je nastaven v souboru *launchSettings. JSON* a předán poskytovateli konfigurace prostřednictvím proměnné prostředí. Také je nutné nakonfigurovat server tak, aby naslouchal žádosti na portu pro správu.

Pokud chcete ukázkovou aplikaci použít k předvedení konfigurace portů pro správu, vytvořte soubor *launchSettings. JSON* ve složce *Properties (vlastnosti* ).

Následující *vlastnosti/soubor launchSettings. JSON* v ukázkové aplikaci nejsou součástí souborů projektu ukázkové aplikace a je nutné je vytvořit ručně:

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

Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`. Vytvořte koncový bod kontroly stavu voláním `MapHealthChecks` v `Startup.Configure`.

V ukázkové aplikaci volání `RequireHost` na koncovém bodu v `Startup.Configure` Určuje port pro správu z konfigurace:

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

Koncové body se vytvářejí v ukázkové aplikaci v `Startup.Configure`. V následujícím příkladu kódu:

* Kontrola připravenosti používá všechny registrované kontroly se značkou ' Read '.
* `Predicate` vyloučí všechny kontroly a vrátí 200 – OK.

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
> Vytvoření souboru *launchSettings. JSON* v ukázkové aplikaci se vyhnete tak, že v kódu nastavíte port pro správu explicitně. V *program.cs* , kde se vytvoří <xref:Microsoft.Extensions.Hosting.HostBuilder>, přidejte volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> a zadejte koncový bod portu pro správu aplikace. V `Configure` *ManagementPortStartup.cs*zadejte port pro správu `RequireHost`:
>
> *Program.cs*:
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
> *ManagementPortStartup.cs*:
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

Pokud chcete spustit scénář konfigurace portu pro správu pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Distribuce knihovny kontroly stavu

Postup při distribuci kontroly stavu jako knihovny:

1. Zapište kontrolu stavu, která implementuje rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> jako samostatnou třídu. Třída může spoléhat na [vkládání závislostí (di)](xref:fundamentals/dependency-injection), aktivovat typ a [pojmenované možnosti](xref:fundamentals/configuration/options) pro přístup k datům konfigurace.

   V `CheckHealthAsync`logiky kontroly stavu:

   * `data1` a `data2` se používají v metodě ke spuštění logiky kontroly stavu testu.
   * `AccessViolationException` se zpracovává.

   Když dojde k <xref:System.AccessViolationException>, vrátí se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, aby uživatelé mohli konfigurovat stav selhání kontroly stavu.

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

1. Napište metodu rozšíření s parametry, které aplikace spotřebovávají v metodě `Startup.Configure`. V následujícím příkladu předpokládejme následující signatura metody kontroly stavu:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Předchozí signatura indikuje, že `ExampleHealthCheck` vyžaduje další data pro zpracování logiky sondy kontroly stavu. Data jsou k dispozici delegátovi použitému k vytvoření instance kontroly stavu při registraci kontroly stavu s metodou rozšíření. V následujícím příkladu volající určuje nepovinné:

   * název kontroly stavu (`name`). Při `null`se používá `example_health_check`.
   * datový bod řetězce pro kontrolu stavu (`data1`).
   * celočíselný datový bod pro kontrolu stavu (`data2`). Při `null`se používá `1`.
   * stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). Výchozí hodnota je `null`. Pokud `null`, [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) není v pořádku, je hlášen stav selhání.
   * značky (`IEnumerable<string>`).

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

## <a name="health-check-publisher"></a>Vydavatel kontroly stavu

Když se do kontejneru služby přidá <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, systém kontroly stavu pravidelně spouští kontroly stavu a volání `PublishAsync` s výsledkem. To je užitečné ve scénáři sledování stavu založeném na nabízených oznámeních, který očekává, že každý proces bude pravidelně volat monitorovací systém, aby se zjistil stav.

Rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> má jedinou metodu:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> vám umožní nastavit:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; počáteční prodlevu použitou po spuštění aplikace před spuštěním <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instancí. Zpoždění se použije jednou při spuštění a nevztahuje se na další iterace. Výchozí hodnota je pět sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; období provádění <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Výchozí hodnota je 30 sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; Pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` (výchozí), služba Vydavatel kontroly stavu spustí všechny zaregistrované kontroly stavu. Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která filtruje sadu kontrol. Predikát je vyhodnocen každou periodu.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; časový limit pro provádění kontrol stavu pro všechny instance <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Použijte <xref:System.Threading.Timeout.InfiniteTimeSpan> k provedení bez časového limitu. Výchozí hodnota je 30 sekund.

V ukázkové aplikaci `ReadinessPublisher` je implementace <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Stav kontroly stavu se protokoluje pro každou kontrolu na úrovni protokolu:

* Informace (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>), pokud je stav kontrol stavu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.
* Chyba (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>), pokud je stav buď <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> nebo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

V ukázkovém `LivenessProbeStartup` ukázkové aplikace má `StartupHostedService` kontrolu připravenosti dvě druhé zpoždění při spuštění a tato kontrolu proběhne každých 30 sekund. Chcete-li aktivovat <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementaci, ukázky registruje `ReadinessPublisher` jako službu s jedním prvkem v kontejneru [Injektáže (di) pro vkládání závislostí](xref:fundamentals/dependency-injection) :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.

## <a name="restrict-health-checks-with-mapwhen"></a>Omezení kontrol stavu pomocí MapWhen

Použijte <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> k podmíněně větvení kanálu požadavků pro koncové body kontroly stavu.

V následujícím příkladu `MapWhen` větvení kanálu žádosti o aktivaci middlewaru pro kontrolu stavu, pokud se přijme požadavek GET pro `api/HealthCheck` koncový bod:

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

Další informace najdete v tématu <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core nabízí kontrolu stavu middlewaru a knihovny pro vytváření sestav stavu součástí infrastruktury aplikace.

Kontroly stavu jsou zpřístupněné aplikací jako koncové body HTTP. Koncové body kontroly stavu je možné nakonfigurovat pro nejrůznější scénáře monitorování v reálném čase:

* Sondy stavu můžou použít orchestrace kontejnerů a nástroje pro vyrovnávání zatížení ke kontrole stavu aplikace. Například produkt Orchestrator může reagovat na selhání kontroly stavu zastavením zavedení nasazení nebo restartováním kontejneru. Nástroj pro vyrovnávání zatížení může reagovat na poškozenou aplikaci směrováním provozu mimo instanci selhání do stavu v pořádku.
* Použití paměti, disku a dalších prostředků fyzického serveru se dá monitorovat v dobrém stavu.
* Kontroly stavu můžou testovat závislosti aplikace, jako jsou databáze a externí koncové body služby, aby se potvrdila dostupnost a normální fungování.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu. Pokud chcete pro daný scénář spustit ukázkovou aplikaci, použijte příkaz [dotnet Run](/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí. Podrobnosti o tom, jak používat ukázkovou aplikaci, najdete v souboru *Readme.MD* ukázkové aplikace a v popisech scénářů v tomto tématu.

## <a name="prerequisites"></a>Požadavky

Kontroly stavu se obvykle používají s externí službou monitorování nebo nástrojem Orchestrator Container Service ke kontrole stavu aplikace. Před přidáním kontrol stavu do aplikace se rozhodněte, který monitorovací systém se má použít. Monitorovací systém vyhodnotí, jaké typy kontrol stavu se mají vytvořit a jak nakonfigurovat jejich koncové body.

Odkaz na balíček [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček do balíčku [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) .

Ukázková aplikace poskytuje spouštěcí kód k předvedení kontrol stavu pro několik scénářů. Scénář [testu databáze](#database-probe) kontroluje stav připojení k databázi pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). Scénář [testu DbContext](#entity-framework-core-dbcontext-probe) zkontroluje databázi pomocí `DbContext`EF Core. K prozkoumání scénářů databáze se jedná o ukázkovou aplikaci:

* Vytvoří databázi a poskytne připojovací řetězec v souboru *appSettings. JSON* .
* Obsahuje následující odkazy na balíček v souboru projektu:
  * [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.

Další scénář kontroly stavu ukazuje, jak filtrovat kontroly stavu na port pro správu. Ukázková aplikace vyžaduje, abyste vytvořili soubor *Properties/launchSettings. JSON* , který obsahuje adresu URL pro správu a port pro správu. Další informace naleznete v části [Filter by port](#filter-by-port) .

## <a name="basic-health-probe"></a>Základní sonda stavu

U mnoha aplikací je základní konfigurace sondy stavu, která oznamuje dostupnost aplikace pro zpracování požadavků (*živý*), ke zjištění stavu aplikace dostačující.

Základní konfigurace registruje služby kontroly stavu a volá middleware kontroly stavu, aby reagovala na koncový bod adresy URL s odpovědí na stav. Ve výchozím nastavení nejsou k otestování konkrétní závislosti nebo subsystému registrovány žádné konkrétní kontroly stavu. Aplikace se považuje za v pořádku, pokud dokáže reagovat na adresu URL koncového bodu stavu. Výchozí zapisovač odpovědí zapisuje stav (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) jako odpověď v podobě prostého textu zpátky klientovi, což značí buď [funkčnosti. dobrý](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)stav, [funkčnosti. Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status není v pořádku.

Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`. Přidejte koncový bod pro kontrolu stavu middleware s <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> v kanálu zpracování žádosti `Startup.Configure`.

V ukázkové aplikaci se koncový bod kontroly stavu vytvoří při `/health` (*BasicStartup.cs*):

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

Pokud chcete spustit scénář základní konfigurace pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Ukázka Docker

[Docker](xref:host-and-deploy/docker/index) nabízí integrovanou `HEALTHCHECK` direktivu, která se dá použít ke kontrole stavu aplikace, která používá konfiguraci základní kontroly stavu:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Vytvořit kontroly stavu

Kontroly stavu jsou vytvářeny implementací rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>. Metoda <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> vrátí <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, která označuje stav jako `Healthy`, `Degraded`nebo `Unhealthy`. Výsledek je zapsán jako odpověď v podobě prostého textu s konfigurovatelným stavovým kódem (konfigurace je popsána v části [Možnosti kontroly stavu](#health-check-options) ). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> může také vracet volitelné páry klíč-hodnota.

### <a name="example-health-check"></a>Příklad kontroly stavu

Následující třída `ExampleHealthCheck` ukazuje rozložení kontroly stavu. Logika kontrol stavu je umístěna v metodě `CheckHealthAsync`. Následující příklad nastaví fiktivní proměnnou `healthCheckResultHealthy``true`. Pokud je hodnota `healthCheckResultHealthy` nastavena na `false`, vrátí [HealthCheckResult.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) stav není v pořádku.

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

### <a name="register-health-check-services"></a>Registrovat služby kontroly stavu

`ExampleHealthCheck` typ se přidá do služby kontroly stavu v `Startup.ConfigureServices` s <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

Přetížení <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> znázorněné v následujícím příkladu nastaví stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) na hlášení, že při kontrole stavu hlásí chybu. Je-li stav selhání nastaven na `null` (výchozí), [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) je hlášena hodnota není v pořádku. Toto přetížení je užitečným scénářem pro autory knihoven, kde stav selhání uvedený v knihovně vynutila aplikace, když dojde k selhání kontroly stavu, pokud se při implementaci kontroly stavu splní nastavení.

*Značky* lze použít k filtrování kontrol stavu (popsaných dále v oddílu [Filtr kontrol stavu](#filter-health-checks) ).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> může také spustit funkci lambda. V následujícím příkladu `Startup.ConfigureServices` název kontroly stavu zadaný jako `Example` a zaškrtávací políčka vždy vrátí dobrý stav:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a>Použití middlewaru pro kontroly stavu

V `Startup.Configure`volejte <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> v kanálu zpracování s adresou URL koncového bodu nebo relativní cestou:

```csharp
app.UseHealthChecks("/health");
```

Pokud mají kontroly stavu naslouchat na konkrétním portu, použijte přetížení <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> k nastavení portu (popsáno dále v části [Filter by port](#filter-by-port) ):

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a>Možnosti kontroly stavu

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> poskytují příležitost k přizpůsobení chování kontroly stavu:

* [Filtrovat kontroly stavu](#filter-health-checks)
* [Přizpůsobení stavového kódu HTTP](#customize-the-http-status-code)
* [Potlačit hlavičky mezipaměti](#suppress-cache-headers)
* [Přizpůsobení výstupu](#customize-output)

### <a name="filter-health-checks"></a>Filtrovat kontroly stavu

Ve výchozím nastavení middleware pro kontrolu stavu spouští všechny zaregistrované kontroly stavu. Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která vrací logickou hodnotu pro <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> možnost. V následujícím příkladu je `Bar` kontrole stavu vyfiltrováno pomocí značky (`bar_tag`) v podmíněném příkazu funkce, kde `true` je vrácena pouze v případě, že <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> vlastnost pro kontrolu stavu odpovídá `foo_tag` nebo `baz_tag`:

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

### <a name="customize-the-http-status-code"></a>Přizpůsobení stavového kódu HTTP

K přizpůsobení mapování stavu na stavové kódy HTTP použijte <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>. Následující přiřazení <xref:Microsoft.AspNetCore.Http.StatusCodes> jsou výchozími hodnotami, které používá middleware. Změňte hodnoty stavového kódu tak, aby splňovaly vaše požadavky.

V `Startup.Configure`:

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

### <a name="suppress-cache-headers"></a>Potlačit hlavičky mezipaměti

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> určuje, zda middleware pro kontrolu stavu přidává hlavičky HTTP do odezvy testu, aby nedocházelo k ukládání odpovědí do mezipaměti. Pokud je hodnota `false` (výchozí), middleware nastaví nebo přepíše hlavičky `Cache-Control`, `Expires`a `Pragma`, aby nedocházelo k ukládání odpovědí do mezipaměti. Pokud je hodnota `true`, middleware neupraví hlavičky mezipaměti odpovědi.

V `Startup.Configure`:

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a>Přizpůsobení výstupu

Možnost <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> Získá nebo nastaví delegáta použitý k zápisu odpovědi. Výchozí delegát zapisuje minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).

V `Startup.Configure`:

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

Výchozí delegát zapisuje minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Následující vlastní delegát, `WriteResponse`, vypíše vlastní odpověď JSON:

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

Systém kontroly stavu neposkytuje integrovanou podporu pro komplexní formáty vrácených hodnot JSON, protože formát je specifický pro váš výběr systému monitorování. V předchozím příkladu si můžete přizpůsobit `JObject` podle potřeby tak, aby vyhovovaly vašim potřebám.

## <a name="database-probe"></a>Test databáze

Kontrola stavu může určit databázový dotaz, který se má spustit jako logický test, aby označoval, zda databáze obvykle reaguje.

Ukázková aplikace pomocí [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), knihovny kontroly stavu pro aplikace ASP.NET Core, provede kontrolu stavu databáze SQL Server. `AspNetCore.Diagnostics.HealthChecks` spustí dotaz `SELECT 1` na databázi, aby bylo možné potvrdit, že připojení k databázi je v pořádku.

> [!WARNING]
> Při kontrole připojení k databázi pomocí dotazu vyberte dotaz, který se rychle vrátí. Přístup k dotazům spouští riziko přetížení databáze a snížení výkonu. Ve většině případů není nutné spustit testovací dotaz. Stačí pouze provést úspěšné připojení k databázi. Pokud zjistíte, že je nutné spustit dotaz, zvolte jednoduchý dotaz SELECT, například `SELECT 1`.

Zahrňte odkaz na balíček do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Zadejte platný připojovací řetězec databáze do souboru *appSettings. JSON* ukázkové aplikace. Aplikace používá databázi SQL Server s názvem `HealthCheckSample`:

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`. Ukázková aplikace volá metodu `AddSqlServer` s připojovacím řetězcem databáze (*DbHealthStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Kontrola stavu volání middlewaru v kanálu zpracování aplikace v `Startup.Configure`:

```csharp
app.UseHealthChecks("/health");
```

Pokud chcete spustit scénář testu databáze pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.

## <a name="entity-framework-core-dbcontext-probe"></a>Test Entity Framework Core DbContext

`DbContext` ověří, jestli aplikace může komunikovat s databází nakonfigurovanou pro EF Core `DbContext`. Ověření `DbContext` je podporované v aplikacích, které:

* Použijte [jádro Entity Framework (EF)](/ef/core/).
* Zahrňte odkaz na balíček [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>` registruje kontrolu stavu pro `DbContext`. `DbContext` je dodána jako `TContext` metody. K dispozici je přetížení pro konfiguraci stavu selhání, značek a vlastního testovacího dotazu.

Ve výchozím nastavení:

* `DbContextHealthCheck` volá metodu `CanConnectAsync` EF Core. Můžete přizpůsobit, která operace je spuštěna při kontrole stavu pomocí přetížení metod `AddDbContextCheck`.
* Název kontroly stavu je název `TContext`ho typu.

V ukázkové aplikaci je `AppDbContext` k dispozici `AddDbContextCheck` a zaregistrováno jako služba v `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

V ukázkové aplikaci `UseHealthChecks` přidává do `Startup.Configure`middleware kontroly stavu.

```csharp
app.UseHealthChecks("/health");
```

Pokud chcete spustit scénář `DbContext` testu pomocí ukázkové aplikace, zkontrolujte, že databáze určená připojovacím řetězcem v instanci SQL Server neexistuje. Pokud databáze existuje, odstraňte ji.

Ze složky projektu v příkazovém prostředí spusťte následující příkaz:

```dotnetcli
dotnet run --scenario dbcontext
```

Po spuštění aplikace ověřte stav tak, že v prohlížeči vytvoříte požadavek na koncový bod `/health`. Databáze a `AppDbContext` neexistují, takže aplikace nabízí následující odpověď:

```
Unhealthy
```

Aktivujte ukázkovou aplikaci pro vytvoření databáze. Vytvořte žádost o `/createdatabase`. Aplikace odpoví:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Vytvořte požadavek na koncový bod `/health`. Databáze a kontext existují, takže aplikace reaguje:

```
Healthy
```

Aktivujte ukázkovou aplikaci, aby se odstranila databáze. Vytvořte žádost o `/deletedatabase`. Aplikace odpoví:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Vytvořte požadavek na koncový bod `/health`. Aplikace poskytuje poškozenou reakci:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Samostatné sondy pro připravenost a živý provoz

V některých scénářích hostování se používá pár kontrol stavu, který rozlišuje dva stavy aplikace:

* Aplikace funguje, ale ještě není připravená na příjem požadavků. Tento stav je *připravenost*aplikace.
* Aplikace funguje a reaguje na požadavky. Tento stav je *živými*aplikacemi.

Kontrola připravenosti obvykle provádí rozsáhlejší a časově náročnou sadu kontrol, aby bylo možné zjistit, zda jsou k dispozici všechny subsystémy a prostředky aplikace. K dispozici je pouze rychlá kontrolu, která určuje, zda je aplikace k dispozici pro zpracování požadavků. Po úspěšném provedení kontroly připravenosti aplikace nemusíte aplikaci dále zatěžovat s nákladným nastavením kontrol připravenosti&mdash;další kontroly vyžadují jenom kontrolu živého provozu.

Ukázková aplikace obsahuje kontrolu stavu, která oznamuje dokončení dlouhotrvající úlohy po spuštění v [hostované službě](xref:fundamentals/host/hosted-services). `StartupHostedServiceHealthCheck` zpřístupňuje vlastnost, `StartupTaskCompleted`, kterou může hostovaná služba nastavit na `true` po dokončení dlouhotrvající úlohy (*StartupHostedServiceHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Dlouhotrvající úloha na pozadí se spouští v [hostované službě](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*). Po uzavření úlohy je `StartupHostedServiceHealthCheck.StartupTaskCompleted` nastaveno na `true`:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Tato kontrolu stavu je zaregistrovaná ve službě <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> v `Startup.ConfigureServices` spolu s hostovanou službou. Vzhledem k tomu, že hostovaná služba musí nastavit vlastnost pro kontrolu stavu, je tato kontrolu také zaregistrována v kontejneru služby (*LivenessProbeStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Ověřování stavu volání middlewaru v kanálu zpracování aplikace v `Startup.Configure`. V ukázkové aplikaci jsou koncové body kontroly stavu vytvářeny na `/health/ready` pro kontrolu připravenosti a `/health/live` pro kontrolu živých. Kontrola připravenosti filtruje kontrolu stavu na kontrolu stavu pomocí značky `ready`. Kontrola živého odfiltruje `StartupHostedServiceHealthCheck` vrácením `false` v [HealthCheckOptions. predikátu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Další informace najdete v tématu [filtrování kontrol stavu](#filter-health-checks)):

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

Pokud chcete spustit scénář konfigurace připravenosti/živých aplikací pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario liveness
```

V prohlížeči navštivte `/health/ready` několikrát, dokud neuplyne 15 sekund. Zprávy o kontrole stavu nejsou v *pořádku* po dobu prvních 15 sekund. Po 15 sekundách koncový bod hlásí stav *v pořádku*, který odráží dokončení dlouhotrvající úlohy hostované službou.

V tomto příkladu se vytvoří také Vydavatel kontroly stavu (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementace), který spouští první kontrolu připravenosti se dvěma sekundami. Další informace najdete v části [Vydavatel kontroly stavu](#health-check-publisher) .

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

`MemoryHealthCheck` hlásí stav není v pořádku, pokud aplikace používá více než určenou prahovou hodnotu paměti (1 GB v ukázkové aplikaci). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> obsahuje informace o uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`. Místo povolení kontroly stavu předáním do <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>je `MemoryHealthCheck` zaregistrován jako služba. Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrované služby jsou k dispozici pro služby kontroly stavu a middleware. Službu pro kontrolu stavu doporučujeme zaregistrovat jako služby typu singleton.

V ukázkové aplikaci (*CustomWriterStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Ověřování stavu volání middlewaru v kanálu zpracování aplikace v `Startup.Configure`. `WriteResponse` delegátovi je poskytnuta vlastnost `ResponseWriter` pro výstup vlastní odpovědi JSON, když se spustí kontroly stavu:

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

Metoda `WriteResponse` formátuje `CompositeHealthCheckResult` na objekt JSON a poskytuje výstup JSON pro odpověď kontroly stavu:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Pokud chcete spustit test založený na metrikách s vlastním výstupem zapisovače odpovědí pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje scénáře kontroly stavu založené na metrikách, včetně úložného prostoru na disku a maximální hodnoty živé kontroly.
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.

## <a name="filter-by-port"></a>Filtrovat podle portu

Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> s portem omezuje požadavky na kontrolu stavu na zadaný port. Obvykle se používá v prostředí kontejneru k vystavení portu pro monitorovací služby.

Ukázková aplikace nakonfiguruje port pomocí [poskytovatele konfigurace proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider). Port je nastaven v souboru *launchSettings. JSON* a předán poskytovateli konfigurace prostřednictvím proměnné prostředí. Také je nutné nakonfigurovat server tak, aby naslouchal žádosti na portu pro správu.

Pokud chcete ukázkovou aplikaci použít k předvedení konfigurace portů pro správu, vytvořte soubor *launchSettings. JSON* ve složce *Properties (vlastnosti* ).

Následující *vlastnosti/soubor launchSettings. JSON* v ukázkové aplikaci nejsou součástí souborů projektu ukázkové aplikace a je nutné je vytvořit ručně:

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

Registrovat služby kontroly stavu pomocí <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> v `Startup.ConfigureServices`. Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> Určuje port pro správu (*ManagementPortStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> Vytvoření souboru *launchSettings. JSON* v ukázkové aplikaci se vyhnete tak, že v kódu nastavíte adresy URL a port pro správu explicitně. V *program.cs* , kde je vytvořena <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>, přidejte volání <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> a zadejte normální koncový bod odpovědi aplikace a koncový bod portu pro správu. V *ManagementPortStartup.cs* , kde se volá <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>, zadejte port pro správu explicitně.
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

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Distribuce knihovny kontroly stavu

Postup při distribuci kontroly stavu jako knihovny:

1. Zapište kontrolu stavu, která implementuje rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> jako samostatnou třídu. Třída může spoléhat na [vkládání závislostí (di)](xref:fundamentals/dependency-injection), aktivovat typ a [pojmenované možnosti](xref:fundamentals/configuration/options) pro přístup k datům konfigurace.

   V `CheckHealthAsync`logiky kontroly stavu:

   * `data1` a `data2` se používají v metodě ke spuštění logiky kontroly stavu testu.
   * `AccessViolationException` se zpracovává.

   Když dojde k <xref:System.AccessViolationException>, vrátí se <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>, aby uživatelé mohli konfigurovat stav selhání kontroly stavu.

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

1. Napište metodu rozšíření s parametry, které aplikace spotřebovávají v metodě `Startup.Configure`. V následujícím příkladu předpokládejme následující signatura metody kontroly stavu:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Předchozí signatura indikuje, že `ExampleHealthCheck` vyžaduje další data pro zpracování logiky sondy kontroly stavu. Data jsou k dispozici delegátovi použitému k vytvoření instance kontroly stavu při registraci kontroly stavu s metodou rozšíření. V následujícím příkladu volající určuje nepovinné:

   * název kontroly stavu (`name`). Při `null`se používá `example_health_check`.
   * datový bod řetězce pro kontrolu stavu (`data1`).
   * celočíselný datový bod pro kontrolu stavu (`data2`). Při `null`se používá `1`.
   * stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>). Výchozí hodnota je `null`. Pokud `null`, [funkčnosti.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) není v pořádku, je hlášen stav selhání.
   * značky (`IEnumerable<string>`).

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

## <a name="health-check-publisher"></a>Vydavatel kontroly stavu

Když se do kontejneru služby přidá <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>, systém kontroly stavu pravidelně spouští kontroly stavu a volání `PublishAsync` s výsledkem. To je užitečné ve scénáři sledování stavu založeném na nabízených oznámeních, který očekává, že každý proces bude pravidelně volat monitorovací systém, aby se zjistil stav.

Rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> má jedinou metodu:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> vám umožní nastavit:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; počáteční prodlevu použitou po spuštění aplikace před spuštěním <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instancí. Zpoždění se použije jednou při spuštění a nevztahuje se na další iterace. Výchozí hodnota je pět sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; období provádění <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Výchozí hodnota je 30 sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; Pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` (výchozí), služba Vydavatel kontroly stavu spustí všechny zaregistrované kontroly stavu. Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která filtruje sadu kontrol. Predikát je vyhodnocen každou periodu.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; časový limit pro provádění kontrol stavu pro všechny instance <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Použijte <xref:System.Threading.Timeout.InfiniteTimeSpan> k provedení bez časového limitu. Výchozí hodnota je 30 sekund.

> [!WARNING]
> V ASP.NET Core vydání 2,2 se nastavení <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> nedodržuje v <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementaci; nastaví hodnotu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>. Tento problém byl vyřešen v ASP.NET Core 3,0.

V ukázkové aplikaci `ReadinessPublisher` je implementace <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>. Stav kontroly stavu se zaznamená do protokolu pro každé vrácení se změnami buď:

* Informace (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>), pokud je stav kontrol stavu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.
* Chyba (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>), pokud je stav buď <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> nebo <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

V ukázkovém `LivenessProbeStartup` ukázkové aplikace má `StartupHostedService` kontrolu připravenosti dvě druhé zpoždění při spuštění a tato kontrolu proběhne každých 30 sekund. Chcete-li aktivovat <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementaci, ukázky registruje `ReadinessPublisher` jako službu s jedním prvkem v kontejneru [Injektáže (di) pro vkládání závislostí](xref:fundamentals/dependency-injection) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> Následující alternativní řešení povoluje přidání instance <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> do kontejneru služby, pokud již byla do aplikace přidána jedna nebo více dalších hostovaných služeb. Toto řešení nebude v ASP.NET Core 3,0 vyžadováno.
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
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není od Microsoftu zachovaná ani podporovaná.

## <a name="restrict-health-checks-with-mapwhen"></a>Omezení kontrol stavu pomocí MapWhen

Použijte <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> k podmíněně větvení kanálu požadavků pro koncové body kontroly stavu.

V následujícím příkladu `MapWhen` větvení kanálu žádosti o aktivaci middlewaru pro kontrolu stavu, pokud se přijme požadavek GET pro `api/HealthCheck` koncový bod:

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

Další informace najdete v tématu <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end
