---
title: Injektáž závislostí v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak ASP.NET Core implementuje vkládání závislostí a jak ji používat.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
uid: fundamentals/dependency-injection
ms.openlocfilehash: 943ea30c2e4887638f69b6dcdb7e323bcee40240
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80405978"
---
# <a name="dependency-injection-in-aspnet-core"></a>Injektáž závislostí v ASP.NET Core

[Steve Smith](https://ardalis.com/) a [Scott Addie](https://scottaddie.com)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core podporuje vzor návrhu softwaru vkládání závislostí (DI), což je technika pro dosažení [inverze řízení (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.

Další informace specifické pro vkládání závislostí v rámci <xref:mvc/controllers/dependency-injection>řadičů MVC naleznete v tématu .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="overview-of-dependency-injection"></a>Přehled vkládání závislostí

*Závislost* je libovolný objekt, který vyžaduje jiný objekt. Prozkoumejte `MyDependency` následující třídu `WriteMessage` metodou, na které závisí jiné třídy v aplikaci:

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

Instance třídy `MyDependency` lze vytvořit tak, `WriteMessage` aby metoda k dispozici třídy. Třída `MyDependency` je závislost `IndexModel` třídy:

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

Třída vytvoří a přímo `MyDependency` závisí na instanci. Závislosti kódu (například v předchozím příkladu) jsou problematické a je třeba se vyhnout z následujících důvodů:

* Chcete-li nahradit `MyDependency` jinou implementaci, musí být třída změněna.
* Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou. Ve velkém projektu s více `MyDependency`tříd v závislosti na , konfigurační kód se rozptýlí v celé aplikaci.
* Tato implementace je obtížné testování částí. Aplikace by měla používat mock `MyDependency` nebo stub třídy, což není možné s tímto přístupem.

Vkládání závislostí řeší tyto problémy prostřednictvím:

* Použití rozhraní nebo základní třídy k abstrahovat implementaci závislostí.
* Registrace závislosti v kontejneru služby. ASP.NET Core poskytuje vestavěný kontejner <xref:System.IServiceProvider>služeb . Služby jsou registrovány `Startup.ConfigureServices` metodou aplikace.
* *Vstřikování* služby do konstruktoru třídy, kde se používá. Rámec přebírá odpovědnost za vytvoření instance závislosti a její likvidaci, když už není potřeba.

V [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` rozhraní definuje metodu, kterou služba poskytuje aplikaci:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Toto rozhraní je implementováno konkrétním typem: `MyDependency`:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency`požaduje v <xref:Microsoft.Extensions.Logging.ILogger`1> jeho konstruktoru. Není neobvyklé používat vkládání závislostí zřetězenou módou. Každá požadovaná závislost zase požaduje vlastní závislosti. Kontejner řeší závislosti v grafu a vrátí plně vyřešené služby. Kolektivní sada závislostí, které musí být vyřešeny, se obvykle označuje jako *strom závislostí*, *graf závislostí*nebo *graf objektů*.

`IMyDependency`a `ILogger<TCategoryName>` musí být registrovány v servisním kontejneru. `IMyDependency`je registrován `Startup.ConfigureServices`v . `ILogger<TCategoryName>`je registrována infrastrukturou protokolování abstrakcí, takže se jedná o službu poskytovanou v [rámci,](#framework-provided-services) kterou standardně registruje.

Kontejner řeší `ILogger<TCategoryName>` využitím [(obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), což eliminuje potřebu registrovat každý [(obecný) konstruovaný typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

V ukázkové aplikaci je `IMyDependency` služba registrována s konkrétním typem `MyDependency`. Registrace obory životnost i do provozní doby životnosti jednoho požadavku. [Životnost služby](#service-lifetimes) jsou popsány dále v tomto tématu.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Každá `services.Add{SERVICE_NAME}` metoda rozšíření přidává (a potenciálně konfiguruje) služby. Například `services.AddMvc()` přidá služby Razor Pages a MVC vyžadují. Doporučujeme, aby aplikace dodržovaly tuto konvenci. Umístěte metody rozšíření do oboru názvů [Microsoft.Extensions.DependencyInjection,](/dotnet/api/microsoft.extensions.dependencyinjection) abyste zapouzdřili skupiny registrací služby.

Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), `string`například , typ může být vložen pomocí [konfigurace](xref:fundamentals/configuration/index) nebo [vzoru voleb](xref:fundamentals/configuration/options):

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli. Toto pole se používá pro přístup ke službě podle potřeby v celé třídě.

V ukázkové aplikaci je `IMyDependency` instance požadována a `WriteMessage` použita k volání metody služby:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Služby vložené do startupu

Při použití obecného `Startup` hostitele (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Služby mohou být `Startup.Configure`vloženy do :

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Rámcové služby

Metoda `Startup.ConfigureServices` je zodpovědná za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Core framework entity a ASP.NET Core MVC. Zpočátku `IServiceCollection` poskytované `ConfigureServices` má služby definované v rámci v závislosti na [tom, jak byl nakonfigurován hostitel](xref:fundamentals/index#host). Není neobvyklé, že aplikace založená na šabloně ASP.NET Core má stovky služeb registrovaných v rámci. Malý vzorek služeb registrovaných v rámci je uveden v následující tabulce.

| Typ služby | Doba platnosti |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Přechodné |
| `IHostApplicationLifetime` | Singleton |
| `IWebHostEnvironment` | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Přechodné |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Přechodné |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Přechodné |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>Registrace doplňkových služeb pomocí rozšiřujících metod

Pokud je k dispozici metoda rozšíření kolekce služby pro registraci služby (a `Add{SERVICE_NAME}` její závislé služby, pokud je požadováno), konvence je použít metodu jednotné rozšíření zaregistrovat všechny služby požadované této služby. Následující kód je příkladem toho, jak přidat další služby do kontejneru pomocí <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>rozšiřujících metod [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

Další informace naleznete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> v části třída v dokumentaci k rozhraní API.

## <a name="service-lifetimes"></a>Životnost služby

Zvolte vhodnou dobu života pro každou registrovanou službu. ASP.NET základní služby lze konfigurovat s následujícími dobami života:

### <a name="transient"></a>Přechodné

Přechodné služby životnosti (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) jsou vytvořeny pokaždé, když jsou požadovány z kontejneru služby. Tato životnost funguje nejlépe pro lehké služby bez státní příslušnosti.

### <a name="scoped"></a>Rozsahem

Služby životnosti<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>s rozsahem ( ) jsou vytvořeny jednou za požadavek klienta (připojení).

> [!WARNING]
> Při použití služby s vymezeným oborem v `Invoke` `InvokeAsync` middleware, vložte službu do metody nebo. Nevstřikovat prostřednictvím vstřikování konstruktoru, protože nutí službu chovat jako singleton. Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Služby životnosti<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>Singleton ( ) jsou vytvořeny při `Startup.ConfigureServices` prvním požadavku (nebo při spuštění a instance je zadána s registrací služby). Každý následující požadavek používá stejnou instanci. Pokud aplikace vyžaduje singleton chování, povolení kontejneru služby ke správě životnosti služby se doporučuje. Neimplementujte návrhový vzor singleton a zadejte uživatelský kód pro správu životnosti objektu ve třídě.

> [!WARNING]
> Je nebezpečné vyřešit službu s rozsahem z singletonu. Může způsobit, že služba má nesprávný stav při zpracování následných požadavků.

## <a name="service-registration-methods"></a>Metody registrace služby

Metody rozšíření registrace služby nabízejí přetížení, které jsou užitečné v konkrétních scénářích.

| Metoda | Automaticky<br>objekt<br>K dispozici | Několik<br>implementace | Předat args |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Příklad:<br>`services.AddSingleton<IMyDep, MyDep>();` | Ano | Ano | Ne |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Ano | Ano | Ano |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Příklad:<br>`services.AddSingleton<MyDep>();` | Ano | Ne | Ne |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Ne | Ano | Ano |
| `AddSingleton(new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Ne | Ne | Ano |

Další informace o likvidaci typů naleznete v části [Likvidace služeb.](#disposal-of-services) Běžný scénář pro více implementací je [zesměšňování typů pro testování](xref:test/integration-tests#inject-mock-services).

`TryAdd{LIFETIME}`metody zaregistrujte službu pouze v případě, že již není registrována implementace.

V následujícím příkladu se první `MyDependency` `IMyDependency`řádek registruje pro . Druhý řádek nemá žádný `IMyDependency` vliv, protože již má registrovanou implementaci:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Další informace naleznete v tématu:

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

[Metody TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) zaregistrujte službu pouze v případě, že již neexistuje implementace *stejného typu*. Více služeb jsou `IEnumerable<{SERVICE}>`vyřešeny prostřednictvím . Při registraci služeb vývojář chce přidat instanci pouze v případě, že jeden ze stejného typu ještě nebyl přidán. Obecně tato metoda se používá autoři knihovny, aby se zabránilo registraci dvou kopií instance v kontejneru.

V následujícím příkladu se první `MyDep` `IMyDep1`řádek registruje pro . Druhý řádek se `MyDep` `IMyDep2`registruje pro . Třetí řádek nemá žádný `IMyDep1` účinek, protože `MyDep`již má registrovanou implementaci :

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Chování vstřikování konstruktoru

Služby lze vyřešit dvěma mechanismy:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Umožňuje vytváření objektů bez registrace služby v kontejneru vkládání závislostí. `ActivatorUtilities`používá se s abstrakcemi směřujícími k uživatelům, jako jsou pomocné spoje značek, řadiče MVC a pořadače modelů.

Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládání závislostí, ale argumenty musí přiřadit výchozí hodnoty.

Pokud jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities`, vstřikování konstruktoru vyžaduje *veřejný* konstruktor.

Pokud jsou služby vyřešeny `ActivatorUtilities`, vstřikování konstruktoru vyžaduje, aby existoval pouze jeden příslušný konstruktor. Přetížení konstruktoru jsou podporovány, ale může existovat pouze jedno přetížení, jehož argumenty mohou být splněny vkládáním závislostí.

## <a name="entity-framework-contexts"></a>Kontexty rozhraní entity

Kontexty entity Framework jsou obvykle přidány do kontejneru služby pomocí [životnosti oboru,](#service-lifetimes) protože operace databáze webové aplikace jsou obvykle vymezeny na požadavek klienta. Výchozí doba života je vymezena, pokud životnost není [zadána AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení při registraci kontextu databáze. Služby dané životnosti by neměly používat kontext databáze s kratší životností než služba.

## <a name="lifetime-and-registration-options"></a>Možnosti životnosti a registrace

Chcete-li demonstrovat rozdíl mezi možností životnosti a registrace, zvažte následující rozhraní, která představují úkoly jako operaci s jedinečným identifikátorem . `OperationId` V závislosti na tom, jak je nakonfigurována životnost provozní služby pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby na vyžádání třídou:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Rozhraní jsou implementovány `Operation` ve třídě. Konstruktor `Operation` generuje identifikátor GUID, pokud není zadán:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Je `OperationService` registrován, který závisí na `Operation` jednotlivých typech. Pokud `OperationService` je požadováno prostřednictvím vkládání závislostí, obdrží buď novou instanci každé služby nebo existující instance na základě životnosti závislé služby.

* Při přechodných služeb jsou vytvořeny na vyžádání `OperationId` z `IOperationTransient` kontejneru, `OperationId` služby `OperationService`se liší od . `OperationService`obdrží novou instanci `IOperationTransient` třídy. Nová instance poskytuje jiný `OperationId`.
* Při scoped služby jsou vytvořeny `OperationId` na `IOperationScoped` požadavek klienta, `OperationService` služby je stejný jako v rámci požadavku klienta. Napříč požadavky klientů obě služby sdílejí jinou `OperationId` hodnotu.
* Při singleton a singleton instance služby jsou vytvořeny jednou a používají `OperationId` se ve všech požadavcích klientů a všechny služby, je konstantní ve všech požadavcích na služby.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

V `Startup.ConfigureServices`, každý typ je přidán do kontejneru podle jeho pojmenované životnosti:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

Služba `IOperationSingletonInstance` používá konkrétní instanci se známým ID aplikace `Guid.Empty`. Je jasné, kdy je tento typ používán (jeho IDENTIFIKÁTOR GUID je všechny nuly).

Ukázková aplikace ukazuje životnost objektů v rámci jednotlivých požadavků a mezi nimi. Ukázkové `IndexModel` aplikace požaduje každý druh `IOperation` typu `OperationService`a . Stránka pak zobrazí všechny `OperationId` hodnoty třídy modelu stránky a služby prostřednictvím přiřazení vlastností:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Dva následující výstupy ukazují výsledky dvou požadavků:

**První žádost:**

Provoz řídicí jednotky:

Přechodné: d233e165-f417-469b-a866-1cf1935d2518  
Rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000

`OperationService`Operace:

Přechodné: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000

**Druhý požadavek:**

Provoz řídicí jednotky:

Přechodné: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000

`OperationService`Operace:

Přechodné: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000

Sledujte, `OperationId` které z hodnot se liší v rámci požadavku a mezi požadavky:

* *Přechodné* objekty se vždy liší. Přechodná `OperationId` hodnota pro první a druhé požadavky klienta `OperationService` se liší pro operace i napříč požadavky klientů. Pro každý požadavek na službu a požadavek klienta je k dispozici nová instance.
* *Scoped* objekty jsou stejné v rámci požadavku klienta, ale liší se mezi požadavky klienta.
* *Singleton* objekty jsou stejné pro každý objekt a `Operation` každý požadavek `Startup.ConfigureServices`bez ohledu na to, zda je k dispozici instance v .

## <a name="call-services-from-main"></a>Volejte služby z hlavního

Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> s [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) vyřešit službu s oborem v rámci oboru aplikace. Tento přístup je užitečný pro přístup ke službě s vymezeným oborem při spuštění ke spuštění úloh inicializace. Následující příklad ukazuje, jak získat `MyScopedService` kontext `Program.Main`pro in :

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

## <a name="scope-validation"></a>Ověření oboru

Když je aplikace spuštěná ve vývojovém prostředí a volá [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) k sestavení hostitele, výchozí poskytovatel služeb provádí kontroly, aby ověřil, že:

* Služby s oborem nejsou přímo ani nepřímo vyřešeny od kořenového poskytovatele služeb.
* Oborové služby nejsou přímo nebo nepřímo vloženy do singletons.

Kořenový poskytovatel služeb <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> je vytvořen při volání. Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace nebo serveru, když poskytovatel začne s aplikací a je uvolněn, když se aplikace vypne.

Služby s vymezeným oborem jsou uvolněny kontejnerem, který je vytvořil. Pokud je služba s vymezeným oborem vytvořena v kořenovém kontejneru, životnost služby je efektivně povýšena na singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru. Ověřování oborů služby zachytí tyto situace, když `BuildServiceProvider` je volána.

Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Vyžádat si služby

Služby, které jsou k `HttpContext` dispozici v rámci ASP.NET základní požadavek z jsou vystaveny prostřednictvím [Kolekce HttpContext.RequestServices.](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)

Služby Žádosti představují služby nakonfigurované a požadované jako součást aplikace. Když objekty určují závislosti, jsou splněny typy `RequestServices`nalezenými v , nikoli `ApplicationServices`.

Obecně platí, že aplikace by neměla používat tyto vlastnosti přímo. Místo toho požádejte o typy, které vyžadují třídy prostřednictvím konstruktorů třídy a povolit rozhraní pro vložení závislostí. To dává třídy, které jsou snadněji testovat.

> [!NOTE]
> Upřednostňovat požadování závislostí jako parametry `RequestServices` konstruktoru pro přístup k kolekci.

## <a name="design-services-for-dependency-injection"></a>Návrhové služby pro vkládání závislostí

Osvědčenými postupy jsou:

* Navrhnout služby pro použití vkládání závislostí k získání jejich závislosti.
* Vyhněte se stavové, statické třídy a členy. Navrhněte aplikace tak, aby místo toho používaly služby singleton, které se vyhýbají vytváření globálního stavu.
* Vyhněte se přímé konkretizovat závislé třídy v rámci služeb. Přímá instance propojuje kód s konkrétní implementací.
* Třídy aplikací jsou malé, dobře zapracované a snadno testované.

Pokud se zdá, že třída má příliš mnoho injekčně závislých, je to obecně známkou toho, že třída má příliš mnoho odpovědností a porušuje [zásadu jedné odpovědnosti (SRP).](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) Pokus o refaktorování třídy přesunutím některé z jeho odpovědností do nové třídy. Mějte na paměti, že třídy modelu stránky Razor Pages a třídy kontroleru MVC by se měly zaměřit na obavy o ui. Obchodní pravidla a podrobnosti implementace přístupu k údajům by měly být uchovávány ve třídách odpovídajících těmto [samostatným obavám](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Likvidace služeb

Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří. Pokud je instance přidána do kontejneru podle uživatelského kódu, není automaticky uvolněna.

V následujícím příkladu jsou služby vytvořeny kontejnerem služby a uvolněny automaticky:

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

V následujícím příkladu:

* Instance služby nejsou vytvořeny kontejnerem služby.
* Zamýšlené životnosti nejsou známy v rámci.
* Rozhraní framework nevyřazené služby automaticky.
* Pokud služby nejsou explicitně uvolněny v kódu vývojáře, přetrvávají, dokud se aplikace nevypne.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

Diskuse o možnostech likvidace služeb naleznete v tématu [Čtyři způsoby, jak zlikvidovat IDisposables v ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).

## <a name="default-service-container-replacement"></a>Výchozí nahrazení kontejneru služby

Integrovaný kontejner služeb je navržen tak, aby sloužil potřebám architektury a většiny spotřebitelských aplikací. Doporučujeme používat předdefinovaný kontejner, pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, například:

* Vstřikování majetku
* Injekce na základě názvu
* Dětské kontejnery
* Vlastní správa životnosti
* `Func<T>`podpora opožděné inicializace
* Registrace založená na úmluvách

Následující kontejnery třetích stran lze použít s aplikacemi ASP.NET Core:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Milost](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [Lehkývstřik](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Skrýš](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Bezpečnost vlákna

Vytvořte služby singleton bezpečné pro přístup z více vláken. Pokud je služba singleton závislá na přechodné službě, může přechodná služba také vyžadovat bezpečnost podprocesu v závislosti na tom, jak je používána singletonem.

Metoda výroby jedné služby, jako je například druhý argument [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken. Stejně jako`static`typ ( ) konstruktor, je zaručeno, že bude volán jednou jedním vláknem.

## <a name="recommendations"></a>Doporučení

* `async/await`a `Task` řešení služby založené na tom není podporováno. C# nepodporuje asynchronní konstruktory; proto je doporučenývzor použít asynchronní metody po synchronní řešení služby.

* Vyhněte se ukládání dat a konfigurace přímo v kontejneru služby. Například nákupní košík uživatele by obvykle neměl být přidán do kontejneru služeb. Konfigurace by měla používat [vzor voleb](xref:fundamentals/configuration/options). Podobně se vyhněte "držitel dat" objekty, které existují pouze umožnit přístup k některé jiné objekty. Je lepší požádat o skutečnou položku prostřednictvím DI.

* Vyhněte se statickému přístupu ke službám (například staticky zadáváním [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).

* Nepoužívejte *vzor servisního lokátoru*. Například nevyvolávejte <xref:System.IServiceProvider.GetService*> získat instanci služby, pokud můžete použít DI místo:

  **Nesprávné:**

  ```csharp
  public class MyClass()
  {
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

  **Správně**:

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```

* Další varianta lokátoru služby, které je třeba se vyhnout, je vstřikování mincovny, která řeší závislosti za běhu. Obě tyto postupy kombinují [inverzi strategií řízení.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)

* Vyhněte `HttpContext` se statickému přístupu (například [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Stejně jako všechny sady doporučení, může dojít k situacím, kdy je vyžadováno ignorování doporučení. Výjimky jsou&mdash;vzácné většinou zvláštní případy v rámci samotném.

DI je *alternativou* ke vzorům statického/globálního přístupu k objektům. Je možné, že nebudete moci realizovat výhody DI, pokud jej smícháte s přístupem ke statickému objektu.

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Zápis čistého kódu v ASP.NET jádra s vkládáním závislostí (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Princip explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Inverze řídicích kontejnerů a vzor vstřikování závislostí (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core podporuje vzor návrhu softwaru vkládání závislostí (DI), což je technika pro dosažení [inverze řízení (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.

Další informace specifické pro vkládání závislostí v rámci <xref:mvc/controllers/dependency-injection>řadičů MVC naleznete v tématu .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)

## <a name="overview-of-dependency-injection"></a>Přehled vkládání závislostí

*Závislost* je libovolný objekt, který vyžaduje jiný objekt. Prozkoumejte `MyDependency` následující třídu `WriteMessage` metodou, na které závisí jiné třídy v aplikaci:

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

Instance třídy `MyDependency` lze vytvořit tak, `WriteMessage` aby metoda k dispozici třídy. Třída `MyDependency` je závislost `IndexModel` třídy:

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

Třída vytvoří a přímo `MyDependency` závisí na instanci. Závislosti kódu (například v předchozím příkladu) jsou problematické a je třeba se vyhnout z následujících důvodů:

* Chcete-li nahradit `MyDependency` jinou implementaci, musí být třída změněna.
* Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou. Ve velkém projektu s více `MyDependency`tříd v závislosti na , konfigurační kód se rozptýlí v celé aplikaci.
* Tato implementace je obtížné testování částí. Aplikace by měla používat mock `MyDependency` nebo stub třídy, což není možné s tímto přístupem.

Vkládání závislostí řeší tyto problémy prostřednictvím:

* Použití rozhraní nebo základní třídy k abstrahovat implementaci závislostí.
* Registrace závislosti v kontejneru služby. ASP.NET Core poskytuje vestavěný kontejner <xref:System.IServiceProvider>služeb . Služby jsou registrovány `Startup.ConfigureServices` metodou aplikace.
* *Vstřikování* služby do konstruktoru třídy, kde se používá. Rámec přebírá odpovědnost za vytvoření instance závislosti a její likvidaci, když už není potřeba.

V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` rozhraní definuje metodu, kterou služba poskytuje aplikaci:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Toto rozhraní je implementováno konkrétním typem: `MyDependency`:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency`požaduje v <xref:Microsoft.Extensions.Logging.ILogger`1> jeho konstruktoru. Není neobvyklé používat vkládání závislostí zřetězenou módou. Každá požadovaná závislost zase požaduje vlastní závislosti. Kontejner řeší závislosti v grafu a vrátí plně vyřešené služby. Kolektivní sada závislostí, které musí být vyřešeny, se obvykle označuje jako *strom závislostí*, *graf závislostí*nebo *graf objektů*.

`IMyDependency`a `ILogger<TCategoryName>` musí být registrovány v servisním kontejneru. `IMyDependency`je registrován `Startup.ConfigureServices`v . `ILogger<TCategoryName>`je registrována infrastrukturou protokolování abstrakcí, takže se jedná o službu poskytovanou v [rámci,](#framework-provided-services) kterou standardně registruje.

Kontejner řeší `ILogger<TCategoryName>` využitím [(obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), což eliminuje potřebu registrovat každý [(obecný) konstruovaný typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

V ukázkové aplikaci je `IMyDependency` služba registrována s konkrétním typem `MyDependency`. Registrace obory životnost i do provozní doby životnosti jednoho požadavku. [Životnost služby](#service-lifetimes) jsou popsány dále v tomto tématu.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Každá `services.Add{SERVICE_NAME}` metoda rozšíření přidává (a potenciálně konfiguruje) služby. Například `services.AddMvc()` přidá služby Razor Pages a MVC vyžadují. Doporučujeme, aby aplikace dodržovaly tuto konvenci. Umístěte metody rozšíření do oboru názvů [Microsoft.Extensions.DependencyInjection,](/dotnet/api/microsoft.extensions.dependencyinjection) abyste zapouzdřili skupiny registrací služby.

Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), `string`například , typ může být vložen pomocí [konfigurace](xref:fundamentals/configuration/index) nebo [vzoru voleb](xref:fundamentals/configuration/options):

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli. Toto pole se používá pro přístup ke službě podle potřeby v celé třídě.

V ukázkové aplikaci je `IMyDependency` instance požadována a `WriteMessage` použita k volání metody služby:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Služby vložené do startupu

Při použití obecného `Startup` hostitele (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Služby mohou být `Startup.Configure`vloženy do :

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Rámcové služby

Metoda `Startup.ConfigureServices` je zodpovědná za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Core framework entity a ASP.NET Core MVC. Zpočátku `IServiceCollection` poskytované `ConfigureServices` má služby definované v rámci v závislosti na [tom, jak byl nakonfigurován hostitel](xref:fundamentals/index#host). Není neobvyklé, že aplikace založená na šabloně ASP.NET Core má stovky služeb registrovaných v rámci. Malý vzorek služeb registrovaných v rámci je uveden v následující tabulce.

| Typ služby | Doba platnosti |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Přechodné |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Přechodné |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Přechodné |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Přechodné |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>Registrace doplňkových služeb pomocí rozšiřujících metod

Pokud je k dispozici metoda rozšíření kolekce služby pro registraci služby (a `Add{SERVICE_NAME}` její závislé služby, pokud je požadováno), konvence je použít metodu jednotné rozšíření zaregistrovat všechny služby požadované této služby. Následující kód je příkladem toho, jak přidat další služby do kontejneru pomocí <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>rozšiřujících metod [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

Další informace naleznete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> v části třída v dokumentaci k rozhraní API.

## <a name="service-lifetimes"></a>Životnost služby

Zvolte vhodnou dobu života pro každou registrovanou službu. ASP.NET základní služby lze konfigurovat s následujícími dobami života:

### <a name="transient"></a>Přechodné

Přechodné služby životnosti (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) jsou vytvořeny pokaždé, když jsou požadovány z kontejneru služby. Tato životnost funguje nejlépe pro lehké služby bez státní příslušnosti.

### <a name="scoped"></a>Rozsahem

Služby životnosti<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>s rozsahem ( ) jsou vytvořeny jednou za požadavek klienta (připojení).

> [!WARNING]
> Při použití služby s vymezeným oborem v `Invoke` `InvokeAsync` middleware, vložte službu do metody nebo. Nevstřikovat prostřednictvím vstřikování konstruktoru, protože nutí službu chovat jako singleton. Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Služby životnosti<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>Singleton ( ) jsou vytvořeny při `Startup.ConfigureServices` prvním požadavku (nebo při spuštění a instance je zadána s registrací služby). Každý následující požadavek používá stejnou instanci. Pokud aplikace vyžaduje singleton chování, povolení kontejneru služby ke správě životnosti služby se doporučuje. Neimplementujte návrhový vzor singleton a zadejte uživatelský kód pro správu životnosti objektu ve třídě.

> [!WARNING]
> Je nebezpečné vyřešit službu s rozsahem z singletonu. Může způsobit, že služba má nesprávný stav při zpracování následných požadavků.

## <a name="service-registration-methods"></a>Metody registrace služby

Metody rozšíření registrace služby nabízejí přetížení, které jsou užitečné v konkrétních scénářích.

| Metoda | Automaticky<br>objekt<br>K dispozici | Několik<br>implementace | Předat args |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Příklad:<br>`services.AddSingleton<IMyDep, MyDep>();` | Ano | Ano | Ne |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Ano | Ano | Ano |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Příklad:<br>`services.AddSingleton<MyDep>();` | Ano | Ne | Ne |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Ne | Ano | Ano |
| `AddSingleton(new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Ne | Ne | Ano |

Další informace o likvidaci typů naleznete v části [Likvidace služeb.](#disposal-of-services) Běžný scénář pro více implementací je [zesměšňování typů pro testování](xref:test/integration-tests#inject-mock-services).

`TryAdd{LIFETIME}`metody zaregistrujte službu pouze v případě, že již není registrována implementace.

V následujícím příkladu se první `MyDependency` `IMyDependency`řádek registruje pro . Druhý řádek nemá žádný `IMyDependency` vliv, protože již má registrovanou implementaci:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Další informace naleznete v tématu:

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

[Metody TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) zaregistrujte službu pouze v případě, že již neexistuje implementace *stejného typu*. Více služeb jsou `IEnumerable<{SERVICE}>`vyřešeny prostřednictvím . Při registraci služeb vývojář chce přidat instanci pouze v případě, že jeden ze stejného typu ještě nebyl přidán. Obecně tato metoda se používá autoři knihovny, aby se zabránilo registraci dvou kopií instance v kontejneru.

V následujícím příkladu se první `MyDep` `IMyDep1`řádek registruje pro . Druhý řádek se `MyDep` `IMyDep2`registruje pro . Třetí řádek nemá žádný `IMyDep1` účinek, protože `MyDep`již má registrovanou implementaci :

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Chování vstřikování konstruktoru

Služby lze vyřešit dvěma mechanismy:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Umožňuje vytváření objektů bez registrace služby v kontejneru vkládání závislostí. `ActivatorUtilities`používá se s abstrakcemi směřujícími k uživatelům, jako jsou pomocné spoje značek, řadiče MVC a pořadače modelů.

Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládání závislostí, ale argumenty musí přiřadit výchozí hodnoty.

Pokud jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities`, vstřikování konstruktoru vyžaduje *veřejný* konstruktor.

Pokud jsou služby vyřešeny `ActivatorUtilities`, vstřikování konstruktoru vyžaduje, aby existoval pouze jeden příslušný konstruktor. Přetížení konstruktoru jsou podporovány, ale může existovat pouze jedno přetížení, jehož argumenty mohou být splněny vkládáním závislostí.

## <a name="entity-framework-contexts"></a>Kontexty rozhraní entity

Kontexty entity Framework jsou obvykle přidány do kontejneru služby pomocí [životnosti oboru,](#service-lifetimes) protože operace databáze webové aplikace jsou obvykle vymezeny na požadavek klienta. Výchozí doba života je vymezena, pokud životnost není [zadána AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení při registraci kontextu databáze. Služby dané životnosti by neměly používat kontext databáze s kratší životností než služba.

## <a name="lifetime-and-registration-options"></a>Možnosti životnosti a registrace

Chcete-li demonstrovat rozdíl mezi možností životnosti a registrace, zvažte následující rozhraní, která představují úkoly jako operaci s jedinečným identifikátorem . `OperationId` V závislosti na tom, jak je nakonfigurována životnost provozní služby pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby na vyžádání třídou:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Rozhraní jsou implementovány `Operation` ve třídě. Konstruktor `Operation` generuje identifikátor GUID, pokud není zadán:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Je `OperationService` registrován, který závisí na `Operation` jednotlivých typech. Pokud `OperationService` je požadováno prostřednictvím vkládání závislostí, obdrží buď novou instanci každé služby nebo existující instance na základě životnosti závislé služby.

* Při přechodných služeb jsou vytvořeny na vyžádání `OperationId` z `IOperationTransient` kontejneru, `OperationId` služby `OperationService`se liší od . `OperationService`obdrží novou instanci `IOperationTransient` třídy. Nová instance poskytuje jiný `OperationId`.
* Při scoped služby jsou vytvořeny `OperationId` na `IOperationScoped` požadavek klienta, `OperationService` služby je stejný jako v rámci požadavku klienta. Napříč požadavky klientů obě služby sdílejí jinou `OperationId` hodnotu.
* Při singleton a singleton instance služby jsou vytvořeny jednou a používají `OperationId` se ve všech požadavcích klientů a všechny služby, je konstantní ve všech požadavcích na služby.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

V `Startup.ConfigureServices`, každý typ je přidán do kontejneru podle jeho pojmenované životnosti:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

Služba `IOperationSingletonInstance` používá konkrétní instanci se známým ID aplikace `Guid.Empty`. Je jasné, kdy je tento typ používán (jeho IDENTIFIKÁTOR GUID je všechny nuly).

Ukázková aplikace ukazuje životnost objektů v rámci jednotlivých požadavků a mezi nimi. Ukázkové `IndexModel` aplikace požaduje každý druh `IOperation` typu `OperationService`a . Stránka pak zobrazí všechny `OperationId` hodnoty třídy modelu stránky a služby prostřednictvím přiřazení vlastností:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Dva následující výstupy ukazují výsledky dvou požadavků:

**První žádost:**

Provoz řídicí jednotky:

Přechodné: d233e165-f417-469b-a866-1cf1935d2518  
Rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000

`OperationService`Operace:

Přechodné: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000

**Druhý požadavek:**

Provoz řídicí jednotky:

Přechodné: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000

`OperationService`Operace:

Přechodné: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000

Sledujte, `OperationId` které z hodnot se liší v rámci požadavku a mezi požadavky:

* *Přechodné* objekty se vždy liší. Přechodná `OperationId` hodnota pro první a druhé požadavky klienta `OperationService` se liší pro operace i napříč požadavky klientů. Pro každý požadavek na službu a požadavek klienta je k dispozici nová instance.
* *Scoped* objekty jsou stejné v rámci požadavku klienta, ale liší se mezi požadavky klienta.
* *Singleton* objekty jsou stejné pro každý objekt a `Operation` každý požadavek `Startup.ConfigureServices`bez ohledu na to, zda je k dispozici instance v .

## <a name="call-services-from-main"></a>Volejte služby z hlavního

Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> s [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) vyřešit službu s oborem v rámci oboru aplikace. Tento přístup je užitečný pro přístup ke službě s vymezeným oborem při spuštění ke spuštění úloh inicializace. Následující příklad ukazuje, jak získat `MyScopedService` kontext `Program.Main`pro in :

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a>Ověření oboru

Když je aplikace spuštěná ve vývojovém prostředí, provádí výchozí poskytovatel služeb kontroly, aby ověřil, že:

* Služby s oborem nejsou přímo ani nepřímo vyřešeny od kořenového poskytovatele služeb.
* Oborové služby nejsou přímo nebo nepřímo vloženy do singletons.

Kořenový poskytovatel služeb <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> je vytvořen při volání. Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace nebo serveru, když poskytovatel začne s aplikací a je uvolněn, když se aplikace vypne.

Služby s vymezeným oborem jsou uvolněny kontejnerem, který je vytvořil. Pokud je služba s vymezeným oborem vytvořena v kořenovém kontejneru, životnost služby je efektivně povýšena na singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru. Ověřování oborů služby zachytí tyto situace, když `BuildServiceProvider` je volána.

Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Vyžádat si služby

Služby, které jsou k `HttpContext` dispozici v rámci ASP.NET základní požadavek z jsou vystaveny prostřednictvím [Kolekce HttpContext.RequestServices.](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)

Služby Žádosti představují služby nakonfigurované a požadované jako součást aplikace. Když objekty určují závislosti, jsou splněny typy `RequestServices`nalezenými v , nikoli `ApplicationServices`.

Obecně platí, že aplikace by neměla používat tyto vlastnosti přímo. Místo toho požádejte o typy, které vyžadují třídy prostřednictvím konstruktorů třídy a povolit rozhraní rozhraní vstříkne závislosti. To dává třídy, které jsou snadněji testovat.

> [!NOTE]
> Upřednostňovat požadování závislostí jako parametry `RequestServices` konstruktoru pro přístup k kolekci.

## <a name="design-services-for-dependency-injection"></a>Návrhové služby pro vkládání závislostí

Osvědčenými postupy jsou:

* Navrhnout služby pro použití vkládání závislostí k získání jejich závislosti.
* Vyhněte se stavové, statické třídy a členy. Navrhněte aplikace tak, aby místo toho používaly služby singleton, které se vyhýbají vytváření globálního stavu.
* Vyhněte se přímé konkretizovat závislé třídy v rámci služeb. Přímá instance propojuje kód s konkrétní implementací.
* Třídy aplikací jsou malé, dobře zapracované a snadno testované.

Pokud se zdá, že třída má příliš mnoho injekčně závislých, je to obecně známkou toho, že třída má příliš mnoho odpovědností a porušuje [zásadu jedné odpovědnosti (SRP).](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) Pokus o refaktorování třídy přesunutím některé z jeho odpovědností do nové třídy. Mějte na paměti, že třídy modelu stránky Razor Pages a třídy kontroleru MVC by se měly zaměřit na obavy o ui. Obchodní pravidla a podrobnosti implementace přístupu k údajům by měly být uchovávány ve třídách odpovídajících těmto [samostatným obavám](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Likvidace služeb

Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří. Pokud je instance přidána do kontejneru podle uživatelského kódu, není automaticky uvolněna.

V následujícím příkladu jsou služby vytvořeny kontejnerem služby a uvolněny automaticky:

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

V následujícím příkladu:

* Instance služby nejsou vytvořeny kontejnerem služby.
* Zamýšlené životnosti nejsou známy v rámci.
* Rozhraní framework nevyřazené služby automaticky.
* Pokud služby nejsou explicitně uvolněny v kódu vývojáře, přetrvávají, dokud se aplikace nevypne.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a>Výchozí nahrazení kontejneru služby

Integrovaný kontejner služeb je navržen tak, aby sloužil potřebám architektury a většiny spotřebitelských aplikací. Doporučujeme používat předdefinovaný kontejner, pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, například:

* Vstřikování majetku
* Injekce na základě názvu
* Dětské kontejnery
* Vlastní správa životnosti
* `Func<T>`podpora opožděné inicializace
* Registrace založená na úmluvách

Následující kontejnery třetích stran lze použít s aplikacemi ASP.NET Core:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Milost](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [Lehkývstřik](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Skrýš](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Bezpečnost vlákna

Vytvořte služby singleton bezpečné pro přístup z více vláken. Pokud je služba singleton závislá na přechodné službě, může přechodná služba také vyžadovat bezpečnost podprocesu v závislosti na tom, jak je používána singletonem.

Metoda výroby jedné služby, jako je například druhý argument [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken. Stejně jako`static`typ ( ) konstruktor, je zaručeno, že bude volán jednou jedním vláknem.

## <a name="recommendations"></a>Doporučení

* `async/await`a `Task` řešení služby založené na tom není podporováno. C# nepodporuje asynchronní konstruktory; proto je doporučenývzor použít asynchronní metody po synchronní řešení služby.

* Vyhněte se ukládání dat a konfigurace přímo v kontejneru služby. Například nákupní košík uživatele by obvykle neměl být přidán do kontejneru služeb. Konfigurace by měla používat [vzor voleb](xref:fundamentals/configuration/options). Podobně se vyhněte "držitel dat" objekty, které existují pouze umožnit přístup k některé jiné objekty. Je lepší požádat o skutečnou položku prostřednictvím DI.

* Vyhněte se statickému přístupu ke službám (například staticky zadáváním [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).

* Nepoužívejte *vzor lokátoru služby*, který kombinuje [inverzi strategií řízení.](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)

  * Nevyvolávejte <xref:System.IServiceProvider.GetService*> získat instanci služby, pokud můžete použít DI místo:

    **Nesprávné:**

    ```csharp
    public class MyClass()
    {
        public void MyMethod()
        {
            var optionsMonitor = 
                _services.GetService<IOptionsMonitor<MyOptions>>();
            var option = optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

    **Správně**:

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

  * Vyhněte se vkládání továrny, která <xref:System.IServiceProvider.GetService*>řeší závislosti za běhu pomocí .

* Vyhněte `HttpContext` se statickému přístupu (například [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Stejně jako všechny sady doporučení, může dojít k situacím, kdy je vyžadováno ignorování doporučení. Výjimky jsou&mdash;vzácné většinou zvláštní případy v rámci samotném.

DI je *alternativou* ke vzorům statického/globálního přístupu k objektům. Je možné, že nebudete moci realizovat výhody DI, pokud jej smícháte s přístupem ke statickému objektu.

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Zápis čistého kódu v ASP.NET jádra s vkládáním závislostí (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Princip explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Inverze řídicích kontejnerů a vzor vstřikování závislostí (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
