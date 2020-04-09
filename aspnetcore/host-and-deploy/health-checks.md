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
# <a name="health-checks-in-aspnet-core"></a>Zdravotní kontroly v ASP.NET Core

Podle [Glenn Condron](https://github.com/glennc)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core nabízí middlewar a knihovny kontroly stavu pro vykazování stavu součástí infrastruktury aplikací.

Kontroly stavu jsou vystaveny aplikací jako koncové body HTTP. Koncové body kontroly stavu lze nakonfigurovat pro různé scénáře monitorování v reálném čase:

* Sondy stavu lze použít orchestrátory kontejnerů a nástroj pro vyrovnávání zatížení ke kontrole stavu aplikace. Orchestrátor kontejneru může například reagovat na selhávající kontrolu stavu zastavením postupného nasazení nebo restartováním kontejneru. Vyrovnávání zatížení může reagovat na nefunkční aplikace směrováním provozu od instance selhání do instance v pořádku.
* Použití paměti, disku a dalších prostředků fyzického serveru lze sledovat z důvodu stavu v pořádku.
* Kontroly stavu můžete otestovat závislosti aplikace, jako jsou databáze a koncové body externí služby, k potvrzení dostupnosti a normální fungování.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu. Chcete-li spustit ukázkovou aplikaci pro daný scénář, použijte příkaz [dotnet run](/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí. Podrobnosti o tom, jak používat ukázkovou aplikaci, najdete v *README.md* souboru ukázkové aplikace a v popisech scénářů v tomto tématu.

## <a name="prerequisites"></a>Požadavky

Kontroly stavu se obvykle používají s externí monitorovací služby nebo kontejner orchestrátor u zkontrolovat stav aplikace. Před přidáním kontrolstavu do aplikace se rozhodněte, který monitorovací systém chcete použít. Systém monitorování určuje, jaké typy kontrol stavu chcete vytvořit a jak nakonfigurovat jejich koncové body.

Balíček [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) je implicitně odkazován pro aplikace ASP.NET Core. Chcete-li provádět kontroly stavu pomocí core entity frameworku, přidejte odkaz na balíček [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore.](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore)

Ukázková aplikace poskytuje spouštěcí kód pro demonstraci kontroly stavu pro několik scénářů. Scénář [databázové sondy](#database-probe) kontroluje stav připojení k databázi pomocí [aspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). [Scénář sondy DbContext](#entity-framework-core-dbcontext-probe) zkontroluje `DbContext`databázi pomocí EF Core . Chcete-li prozkoumat scénáře databáze, ukázková aplikace:

* Vytvoří databázi a poskytuje její připojovací řetězec v souboru *appsettings.json.*
* Má následující odkazy na balíček v souboru projektu:
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.

Jiný scénář kontroly stavu ukazuje, jak filtrovat kontroly stavu na port správy. Ukázková aplikace vyžaduje vytvoření souboru *Properties/launchSettings.json,* který obsahuje adresu URL pro správu a port pro správu. Další informace naleznete v části [Filtr podle portu.](#filter-by-port)

## <a name="basic-health-probe"></a>Základní zdravotní sonda

Pro mnoho aplikací stačí ke zjištění stavu aplikace základní konfigurace sondy stavu, která hlásí dostupnost aplikace pro zpracování požadavků *(živost).*

Základní konfigurace registruje služby kontroly stavu a volá middleware kontroly stavu, aby odpověděl v koncovém bodě adresy URL s odpovědí na stav. Ve výchozím nastavení nejsou registrovány žádné konkrétní kontroly stavu k testování jakékoli konkrétní závislosti nebo subsystému. Aplikace je považována za v pořádku, pokud je schopná reagovat na adresu URL koncového bodu stavu. Výchozí zapisovač odpovědí<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>zapíše stav ( ) jako odpověď ve formátu prostého textu zpět klientovi, což označuje stav [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [HealthStatus.Unhealthy.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)

Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v . Vytvořte koncový bod kontroly stavu voláním `MapHealthChecks` v `Startup.Configure`.

V ukázkové aplikaci se vytvoří koncový `/health` bod kontroly stavu na (*BasicStartup.cs*):

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

Chcete-li spustit základní scénář konfigurace pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Příklad Dockeru

[Docker](xref:host-and-deploy/docker/index) nabízí integrovanou `HEALTHCHECK` direktivu, kterou lze použít ke kontrole stavu aplikace, která používá základní konfiguraci kontroly stavu:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Vytvořit kontroly stavu

Kontroly stavu jsou vytvořeny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> implementací rozhraní. Metoda <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> vrátí, která označuje `Healthy`stav `Degraded`jako `Unhealthy`, nebo . Výsledek je zapsán jako odpověď ve formátu prostého textu s konfigurovatelným stavovým kódem (konfigurace je popsána v části [Možnosti kontroly stavu).](#health-check-options) <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>můžete také vrátit volitelné dvojice klíč-hodnota.

Následující `ExampleHealthCheck` třída ukazuje rozložení kontroly stavu. Logika kontroly stavu `CheckHealthAsync` je umístěn v metodě. Následující příklad nastaví fiktivní `healthCheckResultHealthy`proměnnou `true`, na . Pokud `healthCheckResultHealthy` je hodnota nastavena na `false`, je vrácen stav [HealthCheckResult.Unhealthy.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*)

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

## <a name="register-health-check-services"></a>Registrace služeb kontroly stavu

Typ `ExampleHealthCheck` je přidán do služeb <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `Startup.ConfigureServices`kontroly stavu s v :

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

Přetížení <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> uvedené v následujícím příkladu nastaví stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) hlásit, když kontrola stavu hlásí selhání. Pokud je stav selhání `null` nastaven na (výchozí), [healthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) je hlášena. Toto přetížení je užitečný scénář pro autory knihovny, kde stav selhání označený knihovnou je vynuceno aplikací při selhání kontroly stavu dojde, pokud implementace kontroly stavu respektuje nastavení.

*Značky* lze použít k filtrování kontrol stavu (popsané dále v části [Kontroly stavu filtru).](#filter-health-checks)

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>může také spustit funkci lambda. V následujícím příkladu je zadán název `Example` kontroly stavu a kontrola vždy vrátí stav v pořádku:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

Volání <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> předat argumenty implementace kontroly stavu. V následujícím příkladu `TestHealthCheckWithArgs` přijímá celé číslo a řetězec <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> pro použití při volání:

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

`TestHealthCheckWithArgs`je registrován `AddTypeActivatedCheck` voláním s celé číslo a řetězec předán do implementace:

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a>Použít směrování kontrol stavu

V `Startup.Configure`aplikace `MapHealthChecks` volejte tvůrce koncového bodu s adresou URL koncového bodu nebo relativní cestou:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a>Vyžadovat hostitele

Volání `RequireHost` k určení jednoho nebo více povolených hostitelů pro koncový bod kontroly stavu. Hostitelé by měl být Unicode spíše než punycode a může obsahovat port. Pokud kolekce není zadána, je přijat libovolný hostitel.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

Další informace naleznete v části [Filtr podle portu.](#filter-by-port)

### <a name="require-authorization"></a>Vyžadovat autorizaci

Volání `RequireAuthorization` ke spuštění nástroje Middleware autorizace v koncovém bodě požadavku na kontrolu stavu. Přetížení `RequireAuthorization` přijímá jednu nebo více zásad autorizace. Pokud zásada není k dispozici, použije se výchozí zásady autorizace.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a>Povolení žádostí nepůvodního zdroje (CORS)

Přestože ruční provádění kontrol stavu z prohlížeče není běžný mů e se používá, může být middlewar CORS povolen voláním `RequireCors` koncových bodů kontrol stavu. Přetížení `RequireCors` přijímá delegáta tvůrce zásad`CorsPolicyBuilder`CORS ( ) nebo název zásady. Pokud zásada není k dispozici, použije se výchozí zásada CORS. Další informace naleznete v tématu <xref:security/cors>.

## <a name="health-check-options"></a>Možnosti kontroly stavu

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>poskytnout možnost přizpůsobit chování kontroly stavu:

* [Filtrování kontrol stavu](#filter-health-checks)
* [Přizpůsobení stavového kódu HTTP](#customize-the-http-status-code)
* [Potlačit záhlaví mezipaměti](#suppress-cache-headers)
* [Přizpůsobit výstup](#customize-output)

### <a name="filter-health-checks"></a>Filtrování kontrol stavu

Ve výchozím nastavení middleware kontroly stavu spouští všechny registrované kontroly stavu. Chcete-li spustit podmnožinu kontrol stavu, zadejte <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> funkci, která vrátí logickou hodnotu této možnosti. V následujícím příkladu `Bar` je kontrola stavu odfiltrována`bar_tag`podle značky ( ) `true` v podmíněném příkazu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> funkce, kde je vrácena pouze v případě, že vlastnost kontroly stavu odpovídá `foo_tag` nebo `baz_tag`:

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

V `Startup.Configure`oblasti `Predicate` filtruje kontrolu stavu "Bar". Pouze Foo a Baz provést.:

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

Slouží <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> k přizpůsobení mapování stavu na stavové kódy HTTP. Následující <xref:Microsoft.AspNetCore.Http.StatusCodes> přiřazení jsou výchozí hodnoty používané middleware. Změňte hodnoty stavového kódu tak, aby vyhovovaly vašim požadavkům.

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

### <a name="suppress-cache-headers"></a>Potlačit záhlaví mezipaměti

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>určuje, zda middleware kontroly stavu přidá do odpovědi sondy hlavičky HTTP, aby se zabránilo ukládání odpovědi do mezipaměti. Pokud `false` je hodnota (výchozí), middleware nastaví `Cache-Control` `Expires`nebo `Pragma` přepíše , a záhlaví, aby se zabránilo ukládání odpovědi do mezipaměti. Pokud je `true`hodnota , middleware nemění cache záhlaví odpovědi.

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

### <a name="customize-output"></a>Přizpůsobit výstup

V `Startup.Configure`aplikaci nastavte možnost [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) na delegáta pro zápis odpovědi:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

Výchozí delegát zapíše minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [Stavu.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Následující vlastní delegáti výstup vlastní odpověď JSON.

První příklad ukázkové aplikace ukazuje, <xref:System.Text.Json?displayProperty=fullName>jak používat :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

Druhý příklad ukazuje, jak používat [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

V ukázkové aplikaci `SYSTEM_TEXT_JSON` zakomentujte [direktivu preprocesoru](xref:index#preprocessor-directives-in-sample-code) v *CustomWriterStartup.cs* povolit `Newtonsoft.Json` verzi aplikace `WriteResponse`.

Rozhraní API pro kontrolu stavu neposkytuje integrovanou podporu pro složité formáty vrácení JSON, protože formát je specifický pro váš výběr monitorovacího systému. Podle potřeby upravte odpověď v předchozích příkladech. Další informace o serializaci JSON pomocí `System.Text.Json`naleznete v [tématu Serializace a deserializace zařízení JSON v rozhraní .NET](/dotnet/standard/serialization/system-text-json-how-to).

## <a name="database-probe"></a>Databázová sonda

Kontrola stavu můžete zadat databázový dotaz spustit jako logický test k označení, zda databáze reaguje normálně.

Ukázková aplikace používá [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), knihovnu kontroly stavu pro ASP.NET aplikace Core, k provedení kontroly stavu v databázi serveru SQL Server. `AspNetCore.Diagnostics.HealthChecks`provede `SELECT 1` dotaz proti databázi k potvrzení připojení k databázi je v pořádku.

> [!WARNING]
> Při kontrole připojení k databázi s dotazem zvolte dotaz, který se vrací rychle. Přístup dotazu spustí riziko přetížení databáze a snížení výkonu. Ve většině případů není nutné spustit testovací dotaz. Pouze vytvoření úspěšného připojení k databázi je dostačující. Pokud zjistíte, že je nutné spustit dotaz, zvolte jednoduchý dotaz SELECT, například `SELECT 1`.

Zahrňte odkaz na balíček [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Zadej platný připojovací řetězec databáze do souboru *appsettings.json* ukázkové aplikace. Aplikace používá databázi SERVERU `HealthCheckSample`SQL Server s názvem :

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v . Ukázková aplikace `AddSqlServer` volá metodu s připojovacím řetězcem databáze (*DbHealthStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Koncový bod kontroly stavu `MapHealthChecks` se `Startup.Configure`vytvoří voláním :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Chcete-li spustit scénář databázové sondy pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.

## <a name="entity-framework-core-dbcontext-probe"></a>Sonda Core DbContext rozhraní entity frameworku

Kontrola `DbContext` potvrzuje, že aplikace může komunikovat s databází `DbContext`nakonfigurovanou pro jádro EF . Kontrola `DbContext` je podporována v aplikacích, které:

* Použít [jádro entity frameworku (EF).](/ef/core/)
* Zahrňte odkaz na balíček [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>`zaregistruje kontrolu stavu `DbContext`pro . Dodává `DbContext` se jako `TContext` metoda. Přetížení je k dispozici ke konfiguraci stavu selhání, značky a vlastní testovací dotaz.

Ve výchozím nastavení:

* Volání `DbContextHealthCheck` metody EF `CanConnectAsync` Core. Můžete přizpůsobit, co operace je spuštěna při kontrole stavu pomocí `AddDbContextCheck` přetížení metody.
* Název kontroly stavu je název `TContext` typu.

V ukázkové `AppDbContext` aplikaci je `AddDbContextCheck` poskytována a `Startup.ConfigureServices` registrována jako služba v (*DbContextHealthStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

Koncový bod kontroly stavu `MapHealthChecks` se `Startup.Configure`vytvoří voláním :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Chcete-li `DbContext` spustit scénář probe pomocí ukázkové aplikace, zkontrolujte, že databáze určená připojovacím řetězcem neexistuje v instanci serveru SQL Server. Pokud databáze existuje, odstraňte ji.

Proveďte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario dbcontext
```

Po spuštění aplikace zkontrolujte stav tím, že `/health` žádost o koncový bod v prohlížeči. Databáze a `AppDbContext` neexistují, takže aplikace poskytuje následující odpověď:

```
Unhealthy
```

Aktivujte ukázkovou aplikaci k vytvoření databáze. Požádejte o `/createdatabase`to, aby byla . Aplikace odpovídá:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Vytvořte požadavek `/health` na koncový bod. Databáze a kontext existují, takže aplikace odpovídá:

```
Healthy
```

Spusťte ukázkovou aplikaci k odstranění databáze. Požádejte o `/deletedatabase`to, aby byla . Aplikace odpovídá:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Vytvořte požadavek `/health` na koncový bod. Aplikace poskytuje nefunkční odpověď:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Samostatné sondy připravenosti a živosti

V některých scénářích hostování se používá dvojice kontrol stavu, které rozlišují dva stavy aplikace:

* Aplikace funguje, ale ještě není připravena přijímat požadavky. Tento stav je *připravenost*aplikace .
* Aplikace funguje a odpovídá na požadavky. Tento stav je *živost*aplikace .

Kontrola připravenosti obvykle provádí rozsáhlejší a časově náročnou sadu kontrol, aby zjistila, zda jsou k dispozici všechny subsystémy a prostředky aplikace. Kontrola živosti pouze provádí rychlou kontrolu, aby zjistila, zda je aplikace k dispozici pro zpracování požadavků. Poté, co aplikace projde kontrolou připravenosti, není třeba aplikaci dále zatěžovat nákladnou sadou kontrol&mdash;připravenosti, další kontroly vyžadují pouze kontrolu živosti.

Ukázková aplikace obsahuje kontrolu stavu, která hlásí dokončení dlouhotrvající úlohy spuštění v [hostované službě](xref:fundamentals/host/hosted-services). Zpřístupní `StartupHostedServiceHealthCheck` vlastnost, `StartupTaskCompleted`, že hostovaná služba `true` můžete nastavit, když je dokončena její dlouhotrvající úloha (*StartupHostedServiceHealthCheck.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Dlouhotrvající úloha na pozadí je [spuštěna hostovkou](xref:fundamentals/host/hosted-services) *(Services/StartupHostedService*). Na závěr úkolu, `StartupHostedServiceHealthCheck.StartupTaskCompleted` je nastavena na `true`:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Kontrola stavu je <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> registrována spolu `Startup.ConfigureServices` s hostovkou služby. Vzhledem k tomu, že hostovaná služba musí nastavit vlastnost při kontrole stavu, je kontrola stavu také registrována v kontejneru služby (*LivenessProbeStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Koncový bod kontroly stavu `MapHealthChecks` je `Startup.Configure`vytvořen voláním v . V ukázkové aplikaci jsou koncové body kontroly stavu vytvořeny na adrese:

* `/health/ready`pro kontrolu připravenosti. Kontrola připravenosti filtruje kontroly stavu `ready` na kontrolu stavu se značkou.
* `/health/live`pro kontrolu živosti. Kontrola živosti filtruje `StartupHostedServiceHealthCheck` zpět `false` v [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (další informace naleznete v [tématu Filtrování kontrol stavu](#filter-health-checks))

V následujícím příkladu kódu:

* Kontrola připravenosti používá všechny registrované šeky se značkou "ready".
* Nezahrnuje `Predicate` všechny kontroly a vrátit 200-Ok.

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

Chcete-li spustit scénář konfigurace připravenosti a živosti pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario liveness
```

V prohlížeči, `/health/ready` navštivte několikrát, dokud 15 sekund uplynulo. Kontrola stavu hlásí *není v pořádku* po dobu prvních 15 sekund. Po 15 sekundách koncový bod hlásí *v pořádku*, což odráží dokončení dlouhotrvající úlohy hostované služby.

Tento příklad také vytvoří vydavatel<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> kontroly stavu (implementace), který spustí první kontrolu připravenosti s dvousekundové zpoždění. Další informace naleznete v části [Health Check Publisher.](#health-check-publisher)

### <a name="kubernetes-example"></a>Příklad Kubernetes

Použití samostatné připravenosti a kontroly živosti je užitečné v prostředí, jako je [Kubernetes](https://kubernetes.io/). V Kubernetes může být aplikace vyžadována k provedení časově náročné práce při spuštění před přijetím požadavků, jako je například test dostupnosti podkladové databáze. Použití samostatných kontrol umožňuje orchestrator rozlišit, zda aplikace funguje, ale ještě není připravena nebo pokud se aplikace nepodařilo spustit. Další informace o sondách připravenosti a živosti v Kubernetes najdete v [tématu Konfigurace sond živosti a připravenosti](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) v dokumentaci Kubernetes.

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Sonda založená na metrikách s vlastním zapisovačem odpovědí

Ukázková aplikace ukazuje kontrolu stavu paměti s vlastní zapisovač odpovědí.

`MemoryHealthCheck`hlásí stav se zhoršenou hodnotou, pokud aplikace používá více než danou prahovou hodnotu paměti (1 GB v ukázkové aplikaci). Obsahuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> informace o uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v . Namísto povolení kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>předáním `MemoryHealthCheck` do , je registrován jako služba. Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrované služby jsou k dispozici pro služby kontroly stavu a middleware. Doporučujeme zaregistrovat služby kontroly stavu jako služby Singleton.

V *CustomWriterStartup.cs* ukázkové aplikace:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Koncový bod kontroly stavu `MapHealthChecks` je `Startup.Configure`vytvořen voláním v . Delegát `WriteResponse` je k dispozici <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> vlastnost k výstupu vlastní odezvy JSON při spuštění kontroly stavu:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

Delegát `WriteResponse` naformátuje `CompositeHealthCheckResult` do objektu JSON a dává výstup JSON pro odpověď kontroly stavu. Další informace naleznete v části [Přizpůsobit výstup.](#customize-output)

Chcete-li spustit sondu založenou na metrikách s výstupem pro zápis vlastní odpovědi pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje scénáře kontroly stavu založené na metrikách, včetně diskového úložiště a kontroly živosti s maximální hodnotou.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.

## <a name="filter-by-port"></a>Filtrovat podle portu

Volání `RequireHost` `MapHealthChecks` se vzorem adresy URL, který určuje port pro omezení požadavků na kontrolu stavu na zadaný port. To se obvykle používá v prostředí kontejneru vystavit port pro monitorování služeb.

Ukázková aplikace konfiguruje port pomocí [zprostředkovatele konfigurace proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider). Port je nastaven v souboru *launchSettings.json* a předán poskytovateli konfigurace prostřednictvím proměnné prostředí. Je také nutné nakonfigurovat server pro naslouchání požadavkům na portu pro správu.

Chcete-li pomocí ukázkové aplikace předvést konfiguraci portu pro správu, vytvořte soubor *launchSettings.json* ve složce *Vlastnosti.*

Následující soubor *Vlastnosti/launchSettings.json* v ukázkové aplikaci není součástí souborů projektu ukázkové aplikace a musí být vytvořen ručně:

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

Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v . Vytvořte koncový bod kontroly stavu voláním `MapHealthChecks` v `Startup.Configure`.

V ukázkové aplikaci `RequireHost` volání na koncovém bodu v `Startup.Configure` určuje port pro správu z konfigurace:

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

Koncové body se vytvářejí v `Startup.Configure`ukázkové aplikaci v aplikaci . V následujícím příkladu kódu:

* Kontrola připravenosti používá všechny registrované šeky se značkou "ready".
* Nezahrnuje `Predicate` všechny kontroly a vrátit 200-Ok.

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
> Můžete se vyhnout vytváření souboru *launchSettings.json* v ukázkové aplikaci nastavením portu pro správu explicitně v kódu. V *Program.cs,* kde <xref:Microsoft.Extensions.Hosting.HostBuilder> je vytvořen, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> přidejte volání a zadejte koncový bod portu pro správu aplikace. V `Configure` *ManagementPortStartup.cs*, uveďte `RequireHost`port pro správu s :
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

Chcete-li spustit scénář konfigurace portu správy pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Distribuce knihovny kontrolstavu

Distribuce kontroly stavu jako knihovny:

1. Napište kontrolu stavu, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> která implementuje rozhraní jako samostatnou třídu. Třída se může spolehnout na [vkládání závislostí (DI),](xref:fundamentals/dependency-injection)aktivaci typu a [pojmenované možnosti](xref:fundamentals/configuration/options) pro přístup ke konfiguračním datům.

   V logice zdravotních kontrol : `CheckHealthAsync`

   * `data1`a `data2` jsou použity v metodě ke spuštění logiky kontroly stavu sondy.
   * `AccessViolationException`je manipulováno.

   Dojde-li <xref:System.AccessViolationException> k, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> je vrácena s <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> umožnit uživatelům konfigurovat stav selhání kontroly stavu.

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

1. Napište metodu rozšíření s parametry, které `Startup.Configure` ve své metodě volá náročná aplikace. V následujícím příkladu předpokládejme následující podpis metody kontroly stavu:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Předchozí podpis označuje, `ExampleHealthCheck` že vyžaduje další data pro zpracování logiky sondy kontroly stavu. Data jsou poskytována delegátovi, který slouží k vytvoření instance kontroly stavu při registraci kontroly stavu pomocí metody rozšíření. V následujícím příkladu volající určuje volitelné:

   * název kontroly`name`stavu ( ). Pokud `null` `example_health_check` se používá .
   * datový bod řetězce pro`data1`kontrolu stavu ( ).
   * datový bod celého čísla pro`data2`kontrolu stavu ( ). Pokud `null` `1` se používá .
   * stav selhání<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ). Výchozí formát je `null`. Pokud `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) je hlášena pro stav selhání.
   * značky`IEnumerable<string>`( ).

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

Při <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> přidání kontejneru služby systém kontroly stavu pravidelně provádí kontroly stavu a volání `PublishAsync` s výsledkem. To je užitečné ve scénáři systému monitorování stavu na základě nabízených, který očekává, že každý proces bude pravidelně volat monitorovací systém za účelem určení stavu.

Rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> má jedinou metodu:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>umožňují nastavit:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Počáteční zpoždění použité po spuštění aplikace <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> před spuštěním instancí. Zpoždění se použije jednou při spuštění a nevztahuje se na následné iterace. Výchozí hodnota je pět sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; Doba popravy. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Výchozí hodnota je 30 sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; Pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` je (výchozí), služba vydavatele kontroly stavu spustí všechny kontroly registrovaného stavu. Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která filtruje sadu kontrol. Predikát se vyhodnocuje každé období.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Časový čas pro provádění kontroly <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> stavu pro všechny instance. Slouží <xref:System.Threading.Timeout.InfiniteTimeSpan> k provedení bez časového času. Výchozí hodnota je 30 sekund.

V ukázkové `ReadinessPublisher` aplikaci <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> je implementace. Stav kontroly stavu je zaznamenán pro každou kontrolu na úrovni protokolu:

* Informace<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ) je-li <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>stav zdravotních kontrol .
* Chyba<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ), pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> je <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>stav buď nebo .

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

V příkladu ukázkové `LivenessProbeStartup` aplikace `StartupHostedService` má kontrola připravenosti dvě sekundy zpoždění při spuštění a spustí kontrolu každých 30 sekund. Chcete-li <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> aktivovat implementaci, `ReadinessPublisher` ukázka registruje jako oslavá služba v kontejneru [vkládání závislostí (DI):](xref:fundamentals/dependency-injection)

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.

## <a name="restrict-health-checks-with-mapwhen"></a>Omezit kontroly stavu pomocí MapWhen

Slouží <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> k podmíněnému větvení kanálu požadavků pro koncové body kontroly stavu.

V následujícím příkladu `MapWhen` pobočky kanálu požadavku k aktivaci middleware kontroly `api/HealthCheck` stavu, pokud je přijat požadavek GET pro koncový bod:

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

Další informace naleznete v tématu <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core nabízí middlewar a knihovny kontroly stavu pro vykazování stavu součástí infrastruktury aplikací.

Kontroly stavu jsou vystaveny aplikací jako koncové body HTTP. Koncové body kontroly stavu lze nakonfigurovat pro různé scénáře monitorování v reálném čase:

* Sondy stavu lze použít orchestrátory kontejnerů a nástroj pro vyrovnávání zatížení ke kontrole stavu aplikace. Orchestrátor kontejneru může například reagovat na selhávající kontrolu stavu zastavením postupného nasazení nebo restartováním kontejneru. Vyrovnávání zatížení může reagovat na nefunkční aplikace směrováním provozu od instance selhání do instance v pořádku.
* Použití paměti, disku a dalších prostředků fyzického serveru lze sledovat z důvodu stavu v pořádku.
* Kontroly stavu můžete otestovat závislosti aplikace, jako jsou databáze a koncové body externí služby, k potvrzení dostupnosti a normální fungování.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

Ukázková aplikace obsahuje příklady scénářů popsaných v tomto tématu. Chcete-li spustit ukázkovou aplikaci pro daný scénář, použijte příkaz [dotnet run](/dotnet/core/tools/dotnet-run) ze složky projektu v příkazovém prostředí. Podrobnosti o tom, jak používat ukázkovou aplikaci, najdete v *README.md* souboru ukázkové aplikace a v popisech scénářů v tomto tématu.

## <a name="prerequisites"></a>Požadavky

Kontroly stavu se obvykle používají s externí monitorovací služby nebo kontejner orchestrátor u zkontrolovat stav aplikace. Před přidáním kontrolstavu do aplikace se rozhodněte, který monitorovací systém chcete použít. Systém monitorování určuje, jaké typy kontrol stavu chcete vytvořit a jak nakonfigurovat jejich koncové body.

Odkazna [metabalíček Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nebo přidejte odkaz na balíček [microsoft.aspNetCore.Diagnostics.HealthChecks.](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks)

Ukázková aplikace poskytuje spouštěcí kód pro demonstraci kontroly stavu pro několik scénářů. Scénář [databázové sondy](#database-probe) kontroluje stav připojení k databázi pomocí [aspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). [Scénář sondy DbContext](#entity-framework-core-dbcontext-probe) zkontroluje `DbContext`databázi pomocí EF Core . Chcete-li prozkoumat scénáře databáze, ukázková aplikace:

* Vytvoří databázi a poskytuje její připojovací řetězec v souboru *appsettings.json.*
* Má následující odkazy na balíček v souboru projektu:
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.

Jiný scénář kontroly stavu ukazuje, jak filtrovat kontroly stavu na port správy. Ukázková aplikace vyžaduje vytvoření souboru *Properties/launchSettings.json,* který obsahuje adresu URL pro správu a port pro správu. Další informace naleznete v části [Filtr podle portu.](#filter-by-port)

## <a name="basic-health-probe"></a>Základní zdravotní sonda

Pro mnoho aplikací stačí ke zjištění stavu aplikace základní konfigurace sondy stavu, která hlásí dostupnost aplikace pro zpracování požadavků *(živost).*

Základní konfigurace registruje služby kontroly stavu a volá middleware kontroly stavu, aby odpověděl v koncovém bodě adresy URL s odpovědí na stav. Ve výchozím nastavení nejsou registrovány žádné konkrétní kontroly stavu k testování jakékoli konkrétní závislosti nebo subsystému. Aplikace je považována za v pořádku, pokud je schopná reagovat na adresu URL koncového bodu stavu. Výchozí zapisovač odpovědí<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>zapíše stav ( ) jako odpověď ve formátu prostého textu zpět klientovi, což označuje stav [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) nebo [HealthStatus.Unhealthy.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)

Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v . Přidejte koncový bod pro kontroly stavu Middleware s <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> v kanálu zpracování požadavků . `Startup.Configure`

V ukázkové aplikaci se vytvoří koncový `/health` bod kontroly stavu na (*BasicStartup.cs*):

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

Chcete-li spustit základní scénář konfigurace pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Příklad Dockeru

[Docker](xref:host-and-deploy/docker/index) nabízí integrovanou `HEALTHCHECK` direktivu, kterou lze použít ke kontrole stavu aplikace, která používá základní konfiguraci kontroly stavu:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Vytvořit kontroly stavu

Kontroly stavu jsou vytvořeny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> implementací rozhraní. Metoda <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> vrátí, která označuje `Healthy`stav `Degraded`jako `Unhealthy`, nebo . Výsledek je zapsán jako odpověď ve formátu prostého textu s konfigurovatelným stavovým kódem (konfigurace je popsána v části [Možnosti kontroly stavu).](#health-check-options) <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>můžete také vrátit volitelné dvojice klíč-hodnota.

### <a name="example-health-check"></a>Příklad kontroly stavu

Následující `ExampleHealthCheck` třída ukazuje rozložení kontroly stavu. Logika kontroly stavu `CheckHealthAsync` je umístěn v metodě. Následující příklad nastaví fiktivní `healthCheckResultHealthy`proměnnou `true`, na . Pokud `healthCheckResultHealthy` je hodnota nastavena na `false`, je vrácen stav [HealthCheckResult.Unhealthy.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*)

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

### <a name="register-health-check-services"></a>Registrace služeb kontroly stavu

Typ `ExampleHealthCheck` je přidán do služeb `Startup.ConfigureServices` <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>kontroly stavu v:

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

Přetížení <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> uvedené v následujícím příkladu nastaví stav selhání (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) hlásit, když kontrola stavu hlásí selhání. Pokud je stav selhání `null` nastaven na (výchozí), [healthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) je hlášena. Toto přetížení je užitečný scénář pro autory knihovny, kde stav selhání označený knihovnou je vynuceno aplikací při selhání kontroly stavu dojde, pokud implementace kontroly stavu respektuje nastavení.

*Značky* lze použít k filtrování kontrol stavu (popsané dále v části [Kontroly stavu filtru).](#filter-health-checks)

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>může také spustit funkci lambda. V následujícím `Startup.ConfigureServices` příkladu je zadán název `Example` kontroly stavu a kontrola vždy vrátí stav v pořádku:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a>Použití middlewaru kontroly stavu

V `Startup.Configure`aplikace <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> volejte v kanálu zpracování s adresou URL koncového bodu nebo relativní cestou:

```csharp
app.UseHealthChecks("/health");
```

Pokud by měly kontroly stavu naslouchat na <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> konkrétním portu, použijte přetížení k nastavení portu (dále popsané v části [Filtr podle portu):](#filter-by-port)

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a>Možnosti kontroly stavu

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>poskytnout možnost přizpůsobit chování kontroly stavu:

* [Filtrování kontrol stavu](#filter-health-checks)
* [Přizpůsobení stavového kódu HTTP](#customize-the-http-status-code)
* [Potlačit záhlaví mezipaměti](#suppress-cache-headers)
* [Přizpůsobit výstup](#customize-output)

### <a name="filter-health-checks"></a>Filtrování kontrol stavu

Ve výchozím nastavení middleware kontroly stavu spouští všechny registrované kontroly stavu. Chcete-li spustit podmnožinu kontrol stavu, zadejte <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> funkci, která vrátí logickou hodnotu této možnosti. V následujícím příkladu `Bar` je kontrola stavu odfiltrována`bar_tag`podle značky ( ) `true` v podmíněném příkazu <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> funkce, kde je vrácena pouze v případě, že vlastnost kontroly stavu odpovídá `foo_tag` nebo `baz_tag`:

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

Slouží <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> k přizpůsobení mapování stavu na stavové kódy HTTP. Následující <xref:Microsoft.AspNetCore.Http.StatusCodes> přiřazení jsou výchozí hodnoty používané middleware. Změňte hodnoty stavového kódu tak, aby vyhovovaly vašim požadavkům.

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

### <a name="suppress-cache-headers"></a>Potlačit záhlaví mezipaměti

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>určuje, zda middleware kontroly stavu přidá do odpovědi sondy hlavičky HTTP, aby se zabránilo ukládání odpovědi do mezipaměti. Pokud `false` je hodnota (výchozí), middleware nastaví `Cache-Control` `Expires`nebo `Pragma` přepíše , a záhlaví, aby se zabránilo ukládání odpovědi do mezipaměti. Pokud je `true`hodnota , middleware nemění cache záhlaví odpovědi.

V `Startup.Configure`:

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a>Přizpůsobit výstup

Možnost <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> získá nebo nastaví delegáta, který se používá k zápisu odpovědi. Výchozí delegát zapíše minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [Stavu.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).

V `Startup.Configure`:

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

Výchozí delegát zapíše minimální odpověď ve formátu prostého textu s řetězcovou hodnotou [Stavu.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Následující vlastní delegát `WriteResponse`, výstupy vlastní odpověď JSON:

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

Systém kontrol stavu neposkytuje integrovanou podporu pro složité formáty vrácení JSON, protože formát je specifický pro váš výběr monitorovacího systému. Neváhejte a `JObject` přizpůsobit v předchozím příkladu podle potřeby, aby vyhovoval vašim potřebám.

## <a name="database-probe"></a>Databázová sonda

Kontrola stavu můžete zadat databázový dotaz spustit jako logický test k označení, zda databáze reaguje normálně.

Ukázková aplikace používá [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), knihovnu kontroly stavu pro ASP.NET aplikace Core, k provedení kontroly stavu v databázi serveru SQL Server. `AspNetCore.Diagnostics.HealthChecks`provede `SELECT 1` dotaz proti databázi k potvrzení připojení k databázi je v pořádku.

> [!WARNING]
> Při kontrole připojení k databázi s dotazem zvolte dotaz, který se vrací rychle. Přístup dotazu spustí riziko přetížení databáze a snížení výkonu. Ve většině případů není nutné spustit testovací dotaz. Pouze vytvoření úspěšného připojení k databázi je dostačující. Pokud zjistíte, že je nutné spustit dotaz, zvolte jednoduchý dotaz SELECT, například `SELECT 1`.

Zahrňte odkaz na balíček [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Zadej platný připojovací řetězec databáze do souboru *appsettings.json* ukázkové aplikace. Aplikace používá databázi SERVERU `HealthCheckSample`SQL Server s názvem :

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v . Ukázková aplikace `AddSqlServer` volá metodu s připojovacím řetězcem databáze (*DbHealthStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Volání kontroly stavu Middleware v `Startup.Configure`kanálu zpracování aplikace v :

```csharp
app.UseHealthChecks("/health");
```

Chcete-li spustit scénář databázové sondy pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.

## <a name="entity-framework-core-dbcontext-probe"></a>Sonda Core DbContext rozhraní entity frameworku

Kontrola `DbContext` potvrzuje, že aplikace může komunikovat s databází `DbContext`nakonfigurovanou pro jádro EF . Kontrola `DbContext` je podporována v aplikacích, které:

* Použít [jádro entity frameworku (EF).](/ef/core/)
* Zahrňte odkaz na balíček [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>`zaregistruje kontrolu stavu `DbContext`pro . Dodává `DbContext` se jako `TContext` metoda. Přetížení je k dispozici ke konfiguraci stavu selhání, značky a vlastní testovací dotaz.

Ve výchozím nastavení:

* Volání `DbContextHealthCheck` metody EF `CanConnectAsync` Core. Můžete přizpůsobit, co operace je spuštěna při kontrole stavu pomocí `AddDbContextCheck` přetížení metody.
* Název kontroly stavu je název `TContext` typu.

V ukázkové `AppDbContext` aplikaci je `AddDbContextCheck` poskytována a `Startup.ConfigureServices` registrována jako služba v (*DbContextHealthStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

V ukázkové `UseHealthChecks` aplikaci přidá middleware `Startup.Configure`kontroly stavu v aplikaci .

```csharp
app.UseHealthChecks("/health");
```

Chcete-li `DbContext` spustit scénář probe pomocí ukázkové aplikace, zkontrolujte, že databáze určená připojovacím řetězcem neexistuje v instanci serveru SQL Server. Pokud databáze existuje, odstraňte ji.

Proveďte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario dbcontext
```

Po spuštění aplikace zkontrolujte stav tím, že `/health` žádost o koncový bod v prohlížeči. Databáze a `AppDbContext` neexistují, takže aplikace poskytuje následující odpověď:

```
Unhealthy
```

Aktivujte ukázkovou aplikaci k vytvoření databáze. Požádejte o `/createdatabase`to, aby byla . Aplikace odpovídá:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Vytvořte požadavek `/health` na koncový bod. Databáze a kontext existují, takže aplikace odpovídá:

```
Healthy
```

Spusťte ukázkovou aplikaci k odstranění databáze. Požádejte o `/deletedatabase`to, aby byla . Aplikace odpovídá:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Vytvořte požadavek `/health` na koncový bod. Aplikace poskytuje nefunkční odpověď:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Samostatné sondy připravenosti a živosti

V některých scénářích hostování se používá dvojice kontrol stavu, které rozlišují dva stavy aplikace:

* Aplikace funguje, ale ještě není připravena přijímat požadavky. Tento stav je *připravenost*aplikace .
* Aplikace funguje a odpovídá na požadavky. Tento stav je *živost*aplikace .

Kontrola připravenosti obvykle provádí rozsáhlejší a časově náročnou sadu kontrol, aby zjistila, zda jsou k dispozici všechny subsystémy a prostředky aplikace. Kontrola živosti pouze provádí rychlou kontrolu, aby zjistila, zda je aplikace k dispozici pro zpracování požadavků. Poté, co aplikace projde kontrolou připravenosti, není třeba aplikaci dále zatěžovat nákladnou sadou kontrol&mdash;připravenosti, další kontroly vyžadují pouze kontrolu živosti.

Ukázková aplikace obsahuje kontrolu stavu, která hlásí dokončení dlouhotrvající úlohy spuštění v [hostované službě](xref:fundamentals/host/hosted-services). Zpřístupní `StartupHostedServiceHealthCheck` vlastnost, `StartupTaskCompleted`, že hostovaná služba `true` můžete nastavit, když je dokončena její dlouhotrvající úloha (*StartupHostedServiceHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Dlouhotrvající úloha na pozadí je [spuštěna hostovkou](xref:fundamentals/host/hosted-services) *(Services/StartupHostedService*). Na závěr úkolu, `StartupHostedServiceHealthCheck.StartupTaskCompleted` je nastavena na `true`:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Kontrola stavu je <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> registrována spolu `Startup.ConfigureServices` s hostovkou služby. Vzhledem k tomu, že hostovaná služba musí nastavit vlastnost při kontrole stavu, je kontrola stavu také registrována v kontejneru služby (*LivenessProbeStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Volání kontroly stavu Middleware v `Startup.Configure`kanálu zpracování aplikace v . V ukázkové aplikaci jsou vytvořeny koncové `/health/ready` body kontroly `/health/live` stavu pro kontrolu připravenosti a pro kontrolu živosti. Kontrola připravenosti filtruje kontroly stavu `ready` na kontrolu stavu se značkou. Kontrola živosti filtruje `StartupHostedServiceHealthCheck` zpět `false` v [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (další informace naleznete v [tématu Filtrování kontrol stavu](#filter-health-checks)):

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

Chcete-li spustit scénář konfigurace připravenosti a živosti pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario liveness
```

V prohlížeči, `/health/ready` navštivte několikrát, dokud 15 sekund uplynulo. Kontrola stavu hlásí *není v pořádku* po dobu prvních 15 sekund. Po 15 sekundách koncový bod hlásí *v pořádku*, což odráží dokončení dlouhotrvající úlohy hostované služby.

Tento příklad také vytvoří vydavatel<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> kontroly stavu (implementace), který spustí první kontrolu připravenosti s dvousekundové zpoždění. Další informace naleznete v části [Health Check Publisher.](#health-check-publisher)

### <a name="kubernetes-example"></a>Příklad Kubernetes

Použití samostatné připravenosti a kontroly živosti je užitečné v prostředí, jako je [Kubernetes](https://kubernetes.io/). V Kubernetes může být aplikace vyžadována k provedení časově náročné práce při spuštění před přijetím požadavků, jako je například test dostupnosti podkladové databáze. Použití samostatných kontrol umožňuje orchestrator rozlišit, zda aplikace funguje, ale ještě není připravena nebo pokud se aplikace nepodařilo spustit. Další informace o sondách připravenosti a živosti v Kubernetes najdete v [tématu Konfigurace sond živosti a připravenosti](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) v dokumentaci Kubernetes.

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Sonda založená na metrikách s vlastním zapisovačem odpovědí

Ukázková aplikace ukazuje kontrolu stavu paměti s vlastní zapisovač odpovědí.

`MemoryHealthCheck`hlásí stav není v pořádku, pokud aplikace používá více než daný práh paměti (1 GB v ukázkové aplikaci). Obsahuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> informace o uvolňování paměti (GC) pro aplikaci (*MemoryHealthCheck.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v . Namísto povolení kontroly stavu <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>předáním `MemoryHealthCheck` do , je registrován jako služba. Všechny <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrované služby jsou k dispozici pro služby kontroly stavu a middleware. Doporučujeme zaregistrovat služby kontroly stavu jako služby Singleton.

V ukázkové aplikaci (*CustomWriterStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Volání kontroly stavu Middleware v `Startup.Configure`kanálu zpracování aplikace v . Delegát `WriteResponse` je k dispozici `ResponseWriter` vlastnost k výstupu vlastní odpověď JSON při provádění kontroly stavu:

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

Metoda `WriteResponse` naformátuje `CompositeHealthCheckResult` do objektu JSON a dává výstup JSON pro odpověď kontroly stavu:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Chcete-li spustit sondu založenou na metrikách s výstupem pro zápis vlastní odpovědi pomocí ukázkové aplikace, proveďte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje scénáře kontroly stavu založené na metrikách, včetně diskového úložiště a kontroly živosti s maximální hodnotou.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.

## <a name="filter-by-port"></a>Filtrovat podle portu

Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> s portem omezuje požadavky na kontrolu stavu na zadaný port. To se obvykle používá v prostředí kontejneru vystavit port pro monitorování služeb.

Ukázková aplikace konfiguruje port pomocí [zprostředkovatele konfigurace proměnné prostředí](xref:fundamentals/configuration/index#environment-variables-configuration-provider). Port je nastaven v souboru *launchSettings.json* a předán poskytovateli konfigurace prostřednictvím proměnné prostředí. Je také nutné nakonfigurovat server pro naslouchání požadavkům na portu pro správu.

Chcete-li pomocí ukázkové aplikace předvést konfiguraci portu pro správu, vytvořte soubor *launchSettings.json* ve složce *Vlastnosti.*

Následující soubor *Vlastnosti/launchSettings.json* v ukázkové aplikaci není součástí souborů projektu ukázkové aplikace a musí být vytvořen ručně:

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

Zaregistrujte služby <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kontroly stavu v . Volání <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> určuje port pro správu (*ManagementPortStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> Vytvoření souboru *launchSettings.json* v ukázkové aplikaci se můžete vyhnout nastavením adres URL a portu pro správu explicitně v kódu. V *Program.cs,* kde <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> je vytvořen, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> přidejte volání a zadejte koncový bod normální odezvy aplikace a koncový bod portu pro správu. V *ManagementPortStartup.cs* ManagementPortStartup.cs <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> kde je volána, zadejte port pro správu explicitně.
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

Chcete-li spustit scénář konfigurace portu správy pomocí ukázkové aplikace, spusťte následující příkaz ze složky projektu v příkazovém prostředí:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Distribuce knihovny kontrolstavu

Distribuce kontroly stavu jako knihovny:

1. Napište kontrolu stavu, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> která implementuje rozhraní jako samostatnou třídu. Třída se může spolehnout na [vkládání závislostí (DI),](xref:fundamentals/dependency-injection)aktivaci typu a [pojmenované možnosti](xref:fundamentals/configuration/options) pro přístup ke konfiguračním datům.

   V logice zdravotních kontrol : `CheckHealthAsync`

   * `data1`a `data2` jsou použity v metodě ke spuštění logiky kontroly stavu sondy.
   * `AccessViolationException`je manipulováno.

   Dojde-li <xref:System.AccessViolationException> k, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> je vrácena s <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> umožnit uživatelům konfigurovat stav selhání kontroly stavu.

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

1. Napište metodu rozšíření s parametry, které `Startup.Configure` ve své metodě volá náročná aplikace. V následujícím příkladu předpokládejme následující podpis metody kontroly stavu:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Předchozí podpis označuje, `ExampleHealthCheck` že vyžaduje další data pro zpracování logiky sondy kontroly stavu. Data jsou poskytována delegátovi, který slouží k vytvoření instance kontroly stavu při registraci kontroly stavu pomocí metody rozšíření. V následujícím příkladu volající určuje volitelné:

   * název kontroly`name`stavu ( ). Pokud `null` `example_health_check` se používá .
   * datový bod řetězce pro`data1`kontrolu stavu ( ).
   * datový bod celého čísla pro`data2`kontrolu stavu ( ). Pokud `null` `1` se používá .
   * stav selhání<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ). Výchozí formát je `null`. Pokud `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) je hlášena pro stav selhání.
   * značky`IEnumerable<string>`( ).

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

Při <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> přidání kontejneru služby systém kontroly stavu pravidelně provádí kontroly stavu a volání `PublishAsync` s výsledkem. To je užitečné ve scénáři systému monitorování stavu na základě nabízených, který očekává, že každý proces bude pravidelně volat monitorovací systém za účelem určení stavu.

Rozhraní <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> má jedinou metodu:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>umožňují nastavit:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Počáteční zpoždění použité po spuštění aplikace <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> před spuštěním instancí. Zpoždění se použije jednou při spuštění a nevztahuje se na následné iterace. Výchozí hodnota je pět sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; Doba popravy. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Výchozí hodnota je 30 sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; Pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` je (výchozí), služba vydavatele kontroly stavu spustí všechny kontroly registrovaného stavu. Chcete-li spustit podmnožinu kontrol stavu, zadejte funkci, která filtruje sadu kontrol. Predikát se vyhodnocuje každé období.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Časový čas pro provádění kontroly <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> stavu pro všechny instance. Slouží <xref:System.Threading.Timeout.InfiniteTimeSpan> k provedení bez časového času. Výchozí hodnota je 30 sekund.

> [!WARNING]
> Ve verzi ASP.NET Core 2.2 není nastavení <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementováno; nastavuje hodnotu . <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> Tento problém byl vyřešen v ASP.NET Core 3.0.

V ukázkové `ReadinessPublisher` aplikaci <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> je implementace. Stav kontroly stavu je zaznamenán pro každou kontrolu buď takto:

* Informace<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ) je-li <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>stav zdravotních kontrol .
* Chyba<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ), pokud <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> je <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>stav buď nebo .

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

V příkladu ukázkové `LivenessProbeStartup` aplikace `StartupHostedService` má kontrola připravenosti dvě sekundy zpoždění při spuštění a spustí kontrolu každých 30 sekund. Chcete-li <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> aktivovat implementaci, `ReadinessPublisher` ukázka registruje jako oslavá služba v kontejneru [vkládání závislostí (DI):](xref:fundamentals/dependency-injection)

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> Následující řešení umožňuje přidání <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance do kontejneru služby, když jedna nebo více jiných hostovaných služeb již byly přidány do aplikace. Toto řešení není vyžadováno v ASP.NET Jádrem 3.0.
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
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zahrnuje vydavatele pro několik systémů, včetně [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) není udržována nebo podporována společností Microsoft.

## <a name="restrict-health-checks-with-mapwhen"></a>Omezit kontroly stavu pomocí MapWhen

Slouží <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> k podmíněnému větvení kanálu požadavků pro koncové body kontroly stavu.

V následujícím příkladu `MapWhen` pobočky kanálu požadavku k aktivaci middleware kontroly `api/HealthCheck` stavu, pokud je přijat požadavek GET pro koncový bod:

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

Další informace naleznete v tématu <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end
