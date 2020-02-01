---
title: Vkládání závislostí v ASP.NET Core
author: guardrex
description: Zjistěte, jak ASP.NET Core implementuje vkládání závislostí a jak se používá.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/30/2020
uid: fundamentals/dependency-injection
ms.openlocfilehash: a9d268489ebcef69d64c6fd65087bc38a3581821
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928409"
---
# <a name="dependency-injection-in-aspnet-core"></a>Vkládání závislostí v ASP.NET Core

[Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)a [Luke Latham](https://github.com/guardrex)

ASP.NET Core podporuje návrhový vzor vkládání závislostí (Dependency Injection, DI), což je technika používaná pro dosažení [inverze řízení (Inversion of Control, IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.

Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection>.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([stažení](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Přehled vkládání závislostí

*Závislost* je libovolný objekt, který je vyžadován jiným objektem. Prohlédněte si následující třídu `MyDependency` s metodou `WriteMessage`, na které závisí jiné třídy v aplikaci:

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

Pro zpřístupnění metody `MyDependency` v jiné třídě je možné vytvořit instanci třídy `WriteMessage`. Třída `MyDependency` je závislost třídy `IndexModel`:

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

Třída vytváří instanci třídy `MyDependency`, na které přímo závisí. Přímé závislosti v kódu (jako ta v předchozím příkladu) jsou problematické a měli byste se jim vyhnout z následujících důvodů:

* Chcete-li nahradit `MyDependency` jinou implementací, musí být modifikována třída, která na ní závisí.
* Pokud má třída `MyDependency` sama závislosti, musí být taktéž nakonfigurovány v dané třídě. Ve velkých projektech s více třídami závislých na `MyDependency` je tak konfigurační kód roztroušen na různých místech aplikace.
* Tento způsob implementace je obtížný na jednotkové testování. Aplikace by měla používat mock nebo stub třídy `MyDependency`, což však není možné s tímto přístupem.

Vkládání závislostí řeší tyto problémy prostřednictvím:

* Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.
* Registrace závislosti v kontejneru služeb. ASP.NET Core poskytuje integrovaný kontejner služby <xref:System.IServiceProvider>. Služby jsou registrované v metodě `Startup.ConfigureServices` aplikace.
* *Vkládání* služeb do konstruktoru třídy, ve které se používá. Framework přebírá zodpovědnost za vytváření instancí závislostí a jejich uvolňování, kdy již nejsou dále potřebné.

V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definuje rozhraní `IMyDependency` metody, které poskytuje služba aplikaci:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

::: moniker-end

Toto rozhraní je implementováno konkrétním typem `MyDependency`:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

::: moniker-end

`MyDependency` vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> ve svém konstruktoru. Není neobvyklé používat zřetězené vkládání závislostí. Každá požadovaná závislost může v zápětí požadovat své vlastní závislosti. Kontejner řeší závislosti v grafu a vrací plně vyřešené služby. Množina závislostí, které musí být rozhodnuty, se obvykle označuje jako *strom závislostí*, *graf závislostí*, nebo *graf objektů*.

`IMyDependency` a `ILogger<TCategoryName>` musí být zaregistrovány v kontejneru služeb. `IMyDependency` je zaregistrován v `Startup.ConfigureServices`. `ILogger<TCategoryName>` je registrován infrastrukturou pro abstrakci protokolování, takže je [službou poskytovanou frameworkem](#framework-provided-services) registrovanou ve výchozím nastavení frameworkem.

Kontejner řeší `ILogger<TCategoryName>` s využitím [(generických) otevřených typů](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), čímž eliminuje nutnost registrovat každý [(generický) konstruovaný typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

V ukázkové aplikaci je služba `IMyDependency` registrována s konkrétním typem `MyDependency`. Registrace specifikuje rámec životnosti služby na životnost jednoho požadavku. [Životnosti služby](#service-lifetimes) jsou popsány dále v tomto tématu.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

> [!NOTE]
> Každá rozšiřující metoda `services.Add{SERVICE_NAME}` přidává (a potenciálně konfiguruje) služby. Například `services.AddMvc()` přidává služby Stránek Razor a MVC. Doporučujeme, aby v aplikacích byla tato konvence dodržena. Umístěte rozšiřující metody do jmenného prostoru [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pro zapouzdření skupin registrací služeb.

Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string`, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):

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

Instance služby je požadována prostřednictvím konstruktoru třídy, kde se služba využívá a přiřazuje do privátního pole. Toto pole se používá pro přístup ke službě podle potřeby v rámci celé třídy.

V ukázkové aplikaci je požadována instance `IMyDependency` a posléze použita k volání metody `WriteMessage` dané služby:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

::: moniker-end

## <a name="services-injected-into-startup"></a>Služby vložené do spuštění

Při použití obecného hostitele (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) lze do konstruktoru `Startup` vložit pouze následující typy služeb:

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Služby je možné vložit do `Startup.Configure`:

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Další informace najdete v tématu <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Služby poskytované frameworkem

Metoda `Startup.ConfigureServices` zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC. Zpočátku `IServiceCollection` poskytnutý pro `ConfigureServices` má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host). Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní. V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.

::: moniker range=">= aspnetcore-3.0"

| Typ služby | Doba platnosti |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Přechodná |
| `IHostApplicationLifetime` | Singleton |
| `IWebHostEnvironment` | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Přechodná |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Přechodná |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Přechodná |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Typ služby | Doba platnosti |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Přechodná |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Přechodná |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Přechodná |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Přechodná |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

::: moniker-end

## <a name="register-additional-services-with-extension-methods"></a>Registrace dalších služeb pomocí rozšiřujících metod

Pokud je dostupná rozšiřující metoda rozšiřující kolekci služeb o registraci služby (případě jejích závislých služeb, je-li to požadováno), je zvykem použít jedinou rozšiřující metodu `Add{SERVICE_NAME}` k registraci všech služeb vyžadovaných danou službu. Následující kód je příkladem přidání dalších služeb do kontejneru pomocí rozšiřujících metod [AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:

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

Další informace najdete v dokumentaci k rozhraní API v tématu Třída <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection>.

## <a name="service-lifetimes"></a>Životnost služeb

Zvolte odpovídající životnost pro každou registrovanou službu. Služby ASP.NET Core můžete nakonfigurovat s následujícími životnostmi:

### <a name="transient"></a>Přechodná

Služba přechodných dob života (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se vytvoří pokaždé, když se požadují z kontejneru služby. Tato životnost je vhodná pro jednoduché, bezstavové služby.

### <a name="scoped"></a>Obor

Oborové služby (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) s vymezeným rozsahem se vytvoří jednou za požadavek klienta (připojení).

> [!WARNING]
> Při použití služby s vymezenou živostností v middlewaru vkládejte službu do metody `Invoke` nebo `InvokeAsync`. Nevkládejte službu prostřednictvím konstruktoru, protože se služba bude chovat se jako singleton. Další informace najdete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Služba singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) je vytvořena při prvním vyžádání (nebo při spuštění `Startup.ConfigureServices` a instance se zadává s registrací služby). Každý další požadavek použije stejnou instanci. Pokud je v aplikaci vyžadováno chování ve stylu návrhového vzoru Singleton, doporučuje se použít kontejner služeb ke správě životnosti takového objektu. Neposkytujte vlastní implementaci návrhového vzoru Singleton a umožněte tak uživatelskému kódu spravovat životnost objektu v rámci třídy.

> [!WARNING]
> Je nebezpečné získávat vymezené služby ze singletonů. Může to způsobit nesprávný stav služby při zpracování následujících požadavků.

## <a name="service-registration-methods"></a>Metody registrace služby

Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.

| Metoda | Automatické<br>Objekt nástroje<br>odvod | Více<br>implementace | Pass – argumenty |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Příklad:<br>`services.AddSingleton<IMyDep, MyDep>();` | Ano | Ano | Ne |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Ano | Ano | Ano |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Příklad:<br>`services.AddSingleton<MyDep>();` | Ano | Ne | Ne |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Ne | Ano | Ano |
| `AddSingleton(new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Ne | Ne | Ano |

Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) . Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).

metody `TryAdd{LIFETIME}` registrují pouze v případě, že není zaregistrována implementace.

V následujícím příkladu se první řádek registruje `MyDependency` pro `IMyDependency`. Druhý řádek nemá žádný vliv, protože `IMyDependency` už má registrovanou implementaci:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Další informace najdete v části .

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*. Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>`. Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný. Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.

V následujícím příkladu se první řádek registruje `MyDep` pro `IMyDep1`. Druhý řádek registruje `MyDep` pro `IMyDep2`. Třetí řádek nemá žádný vliv, protože `IMyDep1` již má registrovanou implementaci `MyDep`:

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Chování vkládání pomocí konstruktoru

Služby mohou být řešeny pomocí dvou mechanismů:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; povoluje vytvoření objektu bez registrace služby v kontejneru pro vkládání závislostí. `ActivatorUtilities` se používá s uživatelsky orientovanými abstrakcemi, jako jsou například Tag Helpery, kontrolery MVC a bindery modelů.

Konstruktory mohou přijímat argumenty, které nejsou poskytovány v rámci vkládání závislostí, ale takové argumenty musí mít přiřazené výchozí hodnoty.

Pokud jsou služby řešeny pomocí `IServiceProvider` nebo `ActivatorUtilities`, pak je vyžadován *veřejný* konstruktor.

Pokud jsou služby řešeny pomocí `ActivatorUtilities`, konstruktorová injekce vyžaduje, aby existoval pouze jediný použitelný konstruktor. Přetížení konstruktoru je podporováno, ale může existovat pouze jedno přetížení, jehož argumenty jsou splnitelné vkládáním závislostí.

## <a name="entity-framework-contexts"></a>Kontexty Entity Frameworku

Kontexty Entity Frameworku jsou obvykle přidány do kontejneru služeb s [vymezenou životností](#service-lifetimes), protože operace webové aplikace nad databází jsou obvykle vymezeny na požadavek klienta. Výchozí doba životnosti je vymezená, pokud není specifikována v přetížení metody [AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) při registraci databázového kontextu. Služby s danou životností by neměly používat databázový kontext s životností kratší, než je životnost služby.

## <a name="lifetime-and-registration-options"></a>Životnosti a možnosti registrace

Na ukázku rozdílů mezi životnostmi a možnostmi registrace si prohlédněte následující rozhraní reprezentující úlohy jako operace s jedinečným identifikátorem `OperationId`. V závislosti na tom, jak je životnost této služby nakonfigurována pro následující rozhraní, poskytne kontejner stejnou nebo rozdílnou instanci služby během vyžádání třídou:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

Rozhraní jsou implementovány ve třídě `Operation`. Konstruktor `Operation` vytvoří identifikátor GUID, pokud není explicitně poskytnut:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

Služba `OperationService` je zaregistrována tak, aby závisela na jednotlivých typech tříd `Operation`. Když je `OperationService` vyžádána pomocí vkládání závislostí, obdrží buď novou nebo stávající instanci třídy jednotlivých služeb v závislosti na životnosti závislých služeb.

* Když se v případě vyžádání z kontejneru vytvoří přechodné služby, `OperationId` `IOperationTransient` služby se liší od `OperationId` `OperationService`. `OperationService` obdrží novou instanci třídy `IOperationTransient`. Nová instance implikuje rozdílné `OperationId`.
* Když se vytvoří vymezené služby na žádost klienta, `OperationId` služby `IOperationScoped` stejné jako u `OperationService` v rámci žádosti klienta. V rámci požadavků klientů obě služby sdílejí jinou hodnotu `OperationId`.
* Když se v rámci všech požadavků klientů a všech služeb vytvoří a použije tento počet služeb instance singleton a singleton, je `OperationId` v rámci všech žádostí o službu konstantní.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

V `Startup.ConfigureServices` je každý typ přidán do kontejneru na základě svého pojmenování podle životnosti:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

Služba `IOperationSingletonInstance` používá konkrétní instanci se známým ID nastaveným na `Guid.Empty`. Je pak zřejmé, kdy je tento typ použit (jeho identifikátor GUID obsahuje samé nuly).

Ukázková aplikace demonstruje živostnosti objektů v rámci jednotlivých požadavků a mezi nimi. Třída `IndexModel` ukázkové aplikace vyžaduje každý druh typu `IOperation` a službu `OperationService`. Stránka následně vypisuje všechny hodnoty `OperationId` modelu stránky a služby prostřednictvím přiřazených vlastností:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

::: moniker-end

Následující výpis ukazuje výsledek dvou HTTP požadavků:

**První požadavek:**

Operace kontroleru:

Transient: d233e165-f417-469b-a866-1cf1935d2518  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Operace `OperationService`:

Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

**Druhý požadavek:**

Operace kontroleru:

Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Operace `OperationService`:

Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Všimněte si, které hodnoty `OperationId` se liší v rámci požadavku a mezi požadavky:

* Objekty s *přechodnou* životností jsou vždy rozdílné. Přechodný `OperationId` hodnota pro první i druhý požadavek klienta se liší pro operace `OperationService` a mezi požadavky klientů. Každé žádosti o službu a žádosti klienta se poskytne nová instance.
* *Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.
* Objekty se živostností typu *singleton jsou* stejné pro všechny objekty a všechny požadavky bez ohledu na to, jestli je instance `Operation` poskytnuta v `Startup.ConfigureServices`.

## <a name="call-services-from-main"></a>Volání služeb z main

Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace. Tento způsob je užitečný pro přístup k službám s vymezenou životností při provádění inicializačních úloh během spuštění. Následující příklad ukazuje, jak získat kontext pro `MyScopedService` v `Program.Main`:

::: moniker range=">= aspnetcore-3.0"

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

## <a name="scope-validation"></a>Ověření rámce životnosti

Pokud aplikace běží ve vývojovém prostředí, výchozí poskytovatel služeb provádí kontroly pro ověření toho, že:

* Služby s vymezenou živostností nejsou přímo nebo nepřímo rozhodovány z kořenového poskytovatele služeb.
* Služby s vymezenou živostností nejsou přímo nebo nepřímo vkládány do singletonů.

Poskytovatel kořenové služby se vytvoří, když se zavolá <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>. Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace/serveru, tedy poskytovatel vzniká se startem aplikace a je uvolněn při ukončení aplikace.

Služby s vymezenou životností jsou uvolňovány kontejnerem, který je vytvořil. Pokud jsou služby s vymezenou životností vytvořeny v kořenovém kontejneru, životnost služby je efektivně povýšen na singleton, protože je uvolněn pomocí kořenového kontejneru pouze při vypnutí serveru/aplikace. Validací rámce životnosti služeb ošetřuje tyto situace při volání `BuildServiceProvider`.

Další informace najdete v tématu <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Služby požadavků

Služby dostupné v rámci požadavků ASP.NET Core z `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).

Služby požadavků představují služby nakonfigurované a požadováné v rámci aplikace. Pokud objekty specifikují závislosti, jsou řešeny pomocí typů dostupných v `RequestServices`, nikoli `ApplicationServices`.

Aplikace by obvykle neměly používat tyto vlastnosti přímo. Místo toho získávejte požadované typy pomocí konstruktoru třídy a nechte framework vložit odpovídající závislosti. To poskytuje třídy, které jsou snáze testovány.

> [!NOTE]
> Pro přístup ke kolekci `RequestServices` upřednostněte získávání závislostí jako parametr konstruktoru.

## <a name="design-services-for-dependency-injection"></a>Návrh služeb pro vkládání závislostí

Osvědčené postupy:

* Navrhujte služby tak, aby využívaly vkládání závislostí pro získání jejich závislostí.
* Vyhněte se stavovým, statickým třídám a členům. Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.
* Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb. Přímé vytváření instancí způsobuje přímou závislost na konkrétní implementaci.
* Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.

Pokud má třída nepřiměřeně mnoho vložených závislostí, je to obecně znakem toho, že má třída příliš mnoho zodpovědností a porušuje tak [zásadu jediné zodpovědnosti (Single Responsibility Principle, SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). V tom případě se pokuste refaktorovat třídu tak, že některé z jeho zodpovědností přesunete do nové třídy. Mějte na paměti, že třídy modelů stránek Razor a třídy kontrolerů MVC by měly být zaměřeny na aspekty uživatelského rozhraní. Business pravidla a konkrétní implementace přístupu k datům by měly být v odpovídajících třídách respektující [princip oddělení zopovědnosti](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Uvolnění služeb

Kontejner volá metodu <xref:System.IDisposable.Dispose*> u všech typů, které vytvořil a které implementují rozhraní <xref:System.IDisposable>. Instance přidané do kontejneru pomocí uživatelské kódu nejsou automaticky uvolňovány.

```csharp
// Services that implement IDisposable:
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}
public class Service3 : IDisposable {}

public interface ISomeService {}
public class SomeServiceImplementation : ISomeService, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    // The container creates the following instances and disposes them automatically:
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<ISomeService>(sp => new SomeServiceImplementation());

    // The container doesn't create the following instances, so it doesn't dispose of
    // the instances automatically:
    services.AddSingleton<Service3>(new Service3());
    services.AddSingleton(new Service3());
}
```

## <a name="default-service-container-replacement"></a>Nahrazení výchozího kontejneru služeb

Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací. Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:

* Vkládání pomocí vlastností
* Vkládání na základě názvu
* Vnořené kontejnery
* Vlastní správa životnosti
* Podpora `Func<T>` pro línou inicializaci
* Registrace založená na konvencích

S ASP.NET Coremi aplikacemi je možné používat následující kontejnery třetích stran:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Integrit](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Bezpečný přístup z více vláken

Vytvořte služby, které jsou v bezpečí pro přístup z více vláken. Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.

Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton\<TService > (IServiceCollection, Func\<IServiceProvider, TService >)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken. Stejně jako u typového (`static`) konstruktoru je zaručeno, že je volán jednou jediným vláknem.

## <a name="recommendations"></a>Doporučení

* řešení služeb `async/await` a `Task` na základě se nepodporuje. C#nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.

* Vyhněte se ukládání dat a konfigurace přímo do kontejneru služeb. Například nákupní košík uživatele by neměl být přidáván do kontejneru služeb. Konfigurace by měla používat [vzor možností (options pattern)](xref:fundamentals/configuration/options). Podobně se vystříhejte použití objektů "držící data", jejichž jediným účelem je poskytnutí přístupu k některému jinému objektu. Je lepší žádat o skutečnou položku přes DI.

* Vyhněte se statickému přístupu ke službám (například staticky typovaným [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).

* Vyhněte se použití *vzoru lokátoru služby*. Například Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít DI:

  **Špatný**

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

  **Správné**:

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

* Další změnou lokátoru služby, aby se zabránilo, je vložení továrny, která vyřeší závislosti za běhu. Oba tyto postupy kombinují [inverze strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .

* Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Stejně jako u všech doporučení mohou nastat situace, ve kterých je možné tato doporučení ignorovat. Výjimky se vyskytují jen vzácně &mdash; nejčastěji jsou to speciální případy uvnitř frameworku samotného.

DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům. Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Psaní čistého kódu v ASP.NET Core pomocí vkládání závislostí (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Princip explicitních závislostí](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Kontejner inverze závislostí a vzor vkládání závislostí (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)
* [Postup pro registraci služeb s více rozhraními v ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)
