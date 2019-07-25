---
title: Vkládání závislostí v ASP.NET Core
author: guardrex
description: Zjistěte, jak ASP.NET Core implementuje vkládání závislostí a jak se používá.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/24/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: 100eab0bdee12a6e61ac26538c83aa997f8eaee3
ms.sourcegitcommit: 16502797ea749e2690feaa5e652a65b89c007c89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68483195"
---
# <a name="dependency-injection-in-aspnet-core"></a>Vkládání závislostí v ASP.NET Core

[Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)a [Luke Latham](https://github.com/guardrex)

ASP.NET Core podporuje návrhový vzor vkládání závislostí (Dependency Injection, DI), což je technika používaná pro dosažení [inverze řízení (Inversion of Control, IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.

Další informace, které jsou specifické pro vkládání závislostí v rámci <xref:mvc/controllers/dependency-injection>řadičů MVC, najdete v tématu.

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

Pro zpřístupnění metody `WriteMessage` v jiné třídě je možné vytvořit instanci třídy `MyDependency`. Třída `MyDependency` je závislostí třídy `IndexModel`:

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
* Registrace závislosti v kontejneru služeb. ASP.NET Core poskytuje integrovaný kontejner <xref:System.IServiceProvider>služeb. Služby jsou registrované v metodě `Startup.ConfigureServices` aplikace.
* *Vkládání* služeb do konstruktoru třídy, ve které se používá. Framework přebírá zodpovědnost za vytváření instancí závislostí a jejich uvolňování, kdy již nejsou dále potřebné.

V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definuje rozhraní `IMyDependency` metody, které poskytuje služba aplikaci:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Toto rozhraní je implementováno konkrétním typem `MyDependency`:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency`vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru. Není neobvyklé používat zřetězené vkládání závislostí. Každá požadovaná závislost může v zápětí požadovat své vlastní závislosti. Kontejner řeší závislosti v grafu a vrací plně vyřešené služby. Množina závislostí, které musí být rozhodnuty, se obvykle označuje jako *strom závislostí*, *graf závislostí*, nebo *graf objektů*.

`IMyDependency` a `ILogger<TCategoryName>` musí být zaregistrovány v kontejneru služeb. `IMyDependency`je zaregistrován v `Startup.ConfigureServices`. `ILogger<TCategoryName>` je registrován infrastrukturou pro abstrakci protokolování, takže je [službou poskytovanou frameworkem](#framework-provided-services) registrovanou ve výchozím nastavení frameworkem.

Kontejner řeší `ILogger<TCategoryName>` s využitím [(generických) otevřených typů](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), čímž eliminuje nutnost registrovat každý [(generický) konstruovaný typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<T>), typeof(Logger<T>));
```

V ukázkové aplikaci je služba `IMyDependency` registrována s konkrétním typem `MyDependency`. Registrace specifikuje rámec životnosti služby na životnost jednoho požadavku. [Životnosti služby](#service-lifetimes) jsou popsány dále v tomto tématu.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Každá rozšiřující metoda `services.Add{SERVICE_NAME}` přidává (a potenciálně konfiguruje) služby. Například `services.AddMvc()` přidává služby Stránek Razor a MVC. Doporučujeme, aby v aplikacích byla tato konvence dodržena. Umístěte rozšiřující metody do jmenného prostoru [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) pro zapouzdření skupin registrací služeb.

Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string`,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):

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

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="framework-provided-services"></a>Služby poskytované frameworkem

Metoda `Startup.ConfigureServices` zodpovídá za definování služeb používaných aplikací, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC. Zpočátku jsou v `IServiceCollection`, který je poskytován metodě `ConfigureServices`, definovány následující služby (v závislosti na [konfiguraci hostitele](xref:fundamentals/index#host)):

| Typ služby | Životnost |
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

Pokud je dostupná rozšiřující metoda rozšiřující kolekci služeb o registraci služby (případě jejích závislých služeb, je-li to požadováno), je zvykem použít jedinou rozšiřující metodu `Add{SERVICE_NAME}` k registraci všech služeb vyžadovaných danou službu. Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [\<AddDbContext TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>a <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddMvc();
}
```

Další informace najdete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> ve třídě v dokumentaci k rozhraní API.

## <a name="service-lifetimes"></a>Životnost služeb

Zvolte odpovídající životnost pro každou registrovanou službu. Služby ASP.NET Core můžete nakonfigurovat s následujícími životnostmi:

### <a name="transient"></a>Přechodná

Služba přechodných životností (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se vytvoří pokaždé, když se požadují z kontejneru služby. Tato životnost je vhodná pro jednoduché, bezstavové služby.

### <a name="scoped"></a>Obor

Oborové služby ()<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>s rozsahem platnosti () se vytvoří jednou za požadavek klienta (připojení).

> [!WARNING]
> Při použití služby s vymezenou živostností v middlewaru vkládejte službu do metody `Invoke` nebo `InvokeAsync`. Nevkládejte službu prostřednictvím konstruktoru, protože se služba bude chovat se jako singleton. Další informace naleznete v tématu <xref:fundamentals/middleware/index>.

### <a name="singleton"></a>Singleton

Služba singleton životnosti<xref:Microsoft.AspNet.OData.Builder.ODataModelBuilder.AddSingleton*>() je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby). Každý další požadavek použije stejnou instanci. Pokud je v aplikaci vyžadováno chování ve stylu návrhového vzoru Singleton, doporučuje se použít kontejner služeb ke správě životnosti takového objektu. Neposkytujte vlastní implementaci návrhového vzoru Singleton a umožněte tak uživatelskému kódu spravovat životnost objektu v rámci třídy.

> [!WARNING]
> Je nebezpečné získávat vymezené služby ze singletonů. Může to způsobit nesprávný stav služby při zpracování následujících požadavků.

## <a name="service-registration-methods"></a>Metody registrace služby

Každá metoda rozšíření registrace služby nabízí přetížení, které jsou užitečné v konkrétních scénářích.

| Metoda | Automatické<br>odkazy objektů<br>odvod | Několik<br>implementace | Pass – argumenty |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Příklad:<br>`services.AddScoped<IMyDep, MyDep>();` | Ano | Ano | Ne |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddScoped<IMyDep>(sp => new MyDep());`<br>`services.AddScoped<IMyDep>(sp => new MyDep("A string!"));` | Ano | Ano | Ano |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Příklad:<br>`services.AddScoped<MyDep>();` | Ano | Ne | Ne |
| `Add{LIFETIME}<{SERVICE}>(new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddScoped<IMyDep>(new MyDep());`<br>`services.AddScoped<IMyDep>(new MyDep("A string!"));` | Ne | Ano | Ano |
| `Add{LIFETIME}(new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddScoped(new MyDep());`<br>`services.AddScoped(new MyDep("A string!"));` | Ne | Ne | Ano |

Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) . Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).

`TryAdd{LIFETIME}`metody registrují službu jenom v případě, že už není zaregistrovaná implementace.

V následujícím příkladu se první řádek registruje `MyDependency` pro. `IMyDependency` Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Další informace naleznete v tématu:

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*. Několik služeb je vyřešeno `IEnumerable<{SERVICE}>`prostřednictvím. Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný. Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.

V následujícím příkladu se první řádek registruje `MyDep` pro. `IMyDep1` Druhý řádek se `MyDep` registruje `IMyDep2`pro. Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou `MyDep`implementaci:

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
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>&ndash; Povoluje vytvoření objektu bez registrace služby v kontejneru pro vkládání závislostí. `ActivatorUtilities` se používá s uživatelsky orientovanými abstrakcemi, jako jsou například Tag Helpery, kontrolery MVC a bindery modelů.

Konstruktory mohou přijímat argumenty, které nejsou poskytovány v rámci vkládání závislostí, ale takové argumenty musí mít přiřazené výchozí hodnoty.

Pokud jsou služby řešeny pomocí `IServiceProvider` nebo `ActivatorUtilities`, pak je vyžadován *veřejný* konstruktor.

Pokud jsou služby řešeny pomocí `ActivatorUtilities`, konstruktorová injekce vyžaduje, aby existoval pouze jediný použitelný konstruktor. Přetížení konstruktoru je podporováno, ale může existovat pouze jedno přetížení, jehož argumenty jsou splnitelné vkládáním závislostí.

## <a name="entity-framework-contexts"></a>Kontexty Entity Frameworku

Kontexty Entity Frameworku jsou obvykle přidány do kontejneru služeb s [vymezenou životností](#service-lifetimes), protože operace webové aplikace nad databází jsou obvykle vymezeny na požadavek klienta. Výchozí doba životnosti je vymezená, pokud není specifikována v přetížení metody [AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) při registraci databázového kontextu. Služby s danou životností by neměly používat databázový kontext s životností kratší, než je životnost služby.

## <a name="lifetime-and-registration-options"></a>Životnosti a možnosti registrace

Na ukázku rozdílů mezi životnostmi a možnostmi registrace si prohlédněte následující rozhraní reprezentující úlohy jako operace s jedinečným identifikátorem `OperationId`. V závislosti na tom, jak je životnost této služby nakonfigurována pro následující rozhraní, poskytne kontejner stejnou nebo rozdílnou instanci služby během vyžádání třídou:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Rozhraní jsou implementovány ve třídě `Operation`. Konstruktor `Operation` vytvoří identifikátor GUID, pokud není explicitně poskytnut:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Služba `OperationService` je zaregistrována tak, aby závisela na jednotlivých typech tříd `Operation`. Když je `OperationService` vyžádána pomocí vkládání závislostí, obdrží buď novou nebo stávající instanci třídy jednotlivých služeb v závislosti na životnosti závislých služeb.

* Když se v případě vyžádání z kontejneru `OperationId` vytvoří přechodné služby, `IOperationTransient` `OperationService`liší `OperationId` se od služby. `OperationService` obdrží novou instanci třídy `IOperationTransient`. Nová instance implikuje rozdílné `OperationId`.
* Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta. V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.
* Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, je `OperationId` v rámci všech žádostí o službu konstantní.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

V `Startup.ConfigureServices` je každý typ přidán do kontejneru na základě svého pojmenování podle životnosti:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

Služba `IOperationSingletonInstance` používá konkrétní instanci se známým ID nastaveným na `Guid.Empty`. Je pak zřejmé, kdy je tento typ použit (jeho identifikátor GUID obsahuje samé nuly).

Ukázková aplikace demonstruje živostnosti objektů v rámci jednotlivých požadavků a mezi nimi. Třída `IndexModel` ukázkové aplikace vyžaduje každý druh typu `IOperation` a službu `OperationService`. Stránka následně vypisuje všechny hodnoty `OperationId` modelu stránky a služby prostřednictvím přiřazených vlastností:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Následující výpis ukazuje výsledek dvou HTTP požadavků:

**První požadavek:**

Operace kontroleru:

Transient: d233e165-f417-469b-a866-1cf1935d2518  
Obor 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Případě 00000000-0000-0000-0000-000000000000

Operace `OperationService`:

Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Obor 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Případě 00000000-0000-0000-0000-000000000000

**Druhý požadavek:**

Operace kontroleru:

Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Obor 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Případě 00000000-0000-0000-0000-000000000000

Operace `OperationService`:

Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Obor 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Případě 00000000-0000-0000-0000-000000000000

Všimněte si, které hodnoty `OperationId` se liší v rámci požadavku a mezi požadavky:

* Objekty s *přechodnou* životností jsou vždy rozdílné. Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů. Každé žádosti o službu a žádosti klienta se poskytne nová instance.
* *Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.
* Objekty se živostností typu *singleton jsou* stejné pro všechny objekty a všechny požadavky bez ohledu na to, jestli je instance `Operation` poskytnuta v `Startup.ConfigureServices`.

## <a name="call-services-from-main"></a>Volání služeb z main

Vytvořte pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace. <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> Tento způsob je užitečný pro přístup k službám s vymezenou životností při provádění inicializačních úloh během spuštění. Následující příklad ukazuje, jak získat kontext pro `MyScopedService`: `Program.Main`

```csharp
public static void Main(string[] args)
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

    host.Run();
}
```

## <a name="scope-validation"></a>Ověření rámce životnosti

Pokud aplikace běží ve vývojovém prostředí, výchozí poskytovatel služeb provádí kontroly pro ověření toho, že:

* Služby s vymezenou živostností nejsou přímo nebo nepřímo rozhodovány z kořenového poskytovatele služeb.
* Služby s vymezenou živostností nejsou přímo nebo nepřímo vkládány do singletonů.

Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá. Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace/serveru, tedy poskytovatel vzniká se startem aplikace a je uvolněn při ukončení aplikace.

Služby s vymezenou životností jsou uvolňovány kontejnerem, který je vytvořil. Pokud jsou služby s vymezenou životností vytvořeny v kořenovém kontejneru, životnost služby je efektivně povýšen na singleton, protože je uvolněn pomocí kořenového kontejneru pouze při vypnutí serveru/aplikace. Validací rámce životnosti služeb ošetřuje tyto situace při volání `BuildServiceProvider`.

Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Služby požadavků

Služby dostupné v rámci požadavků ASP.NET Core z `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).

Služby požadavků představují služby nakonfigurované a požadováné v rámci aplikace. Pokud objekty specifikují závislosti, jsou řešeny pomocí typů dostupných v `RequestServices`, nikoli `ApplicationServices`.

Aplikace by obvykle neměly používat tyto vlastnosti přímo. Místo toho získávejte požadované typy pomocí konstruktoru třídy a nechte framework vložit odpovídající závislosti. To poskytuje třídy, které jsou snáze testovány.

> [!NOTE]
> Pro přístup ke kolekci `RequestServices` upřednostněte získávání závislostí jako parametr konstruktoru.

## <a name="design-services-for-dependency-injection"></a>Návrh služeb pro vkládání závislostí

Osvědčené postupy:

* Navrhujte služby tak, aby využívaly vkládání závislostí pro získání jejich závislostí.
* Vyhněte se stavovým a statickým voláním metody.
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

Integrovaný kontejner služeb je primárně určen pro naplnění potřeb frameworku a většiny uživatelských aplikací. Doporučujeme používat integrovaný kontejner, dokud nebudete potřebovat specifické funkce nepodporované kontejnerem. Některé z funkcí podporovaných v kontejnerech 3. stran neobsažených ve výchozím kontejneru jsou:

* Vkládání pomocí vlastností
* Vkládání na základě názvu
* Vnořené kontejnery
* Vlastní správa životnosti
* Podpora `Func<T>` pro línou inicializaci

Pro seznam některých kontejnerů podporujících adaptéry, vizte [Soubor readme.md ke Vkládání závislostí](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection).

Následující příklad nahrazuje integrovaný kontejner kontejnerem [Autofac](https://autofac.org/):

* Nainstalujte odpovídající balíčky kontejneru:

  * [Autofac](https://www.nuget.org/packages/Autofac/)
  * [Autofac.Extensions.DependencyInjection](https://www.nuget.org/packages/Autofac.Extensions.DependencyInjection/)

* Nakonfigurujte kontejner v `Startup.ConfigureServices` a vraťte `IServiceProvider`:

    ```csharp
    public IServiceProvider ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        // Add other framework services

        // Add Autofac
        var containerBuilder = new ContainerBuilder();
        containerBuilder.RegisterModule<DefaultModule>();
        containerBuilder.Populate(services);
        var container = containerBuilder.Build();
        return new AutofacServiceProvider(container);
    }
    ```

    `Startup.ConfigureServices` musí vracet `IServiceProvider` pro použití kontejneru 3. stran.

* Konfigurace Autofacu v `DefaultModule`:

    ```csharp
    public class DefaultModule : Module
    {
        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
        }
    }
    ```

Za běhu je použit Autofac pro rozhodování typů a vkládání závislostí. Další informace o používání Autofac s ASP.NET Core naleznete v [dokumentaci Autofac](https://docs.autofac.org/en/latest/integration/aspnetcore.html).

### <a name="thread-safety"></a>Bezpečný přístup z více vláken

Vytvořte služby, které jsou v bezpečí pro přístup z více vláken. Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.

Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton\<TService > (\<IServiceCollection, Func IServiceProvider, TService >)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken. Stejně jako u typového (`static`) konstruktoru je zaručeno, že je volán jednou jediným vláknem.

## <a name="recommendations"></a>Doporučení

* `async/await`řešení `Task` služeb a se nepodporuje. C#nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.

* Vyhněte se ukládání dat a konfigurace přímo do kontejneru služeb. Například nákupní košík uživatele by neměl být přidáván do kontejneru služeb. Konfigurace by měla používat [vzor možností (options pattern)](xref:fundamentals/configuration/options). Podobně se vystříhejte použití objektů "držící data", jejichž jediným účelem je poskytnutí přístupu k některému jinému objektu. Je lepší žádat o skutečnou položku přes DI.

* Vyhněte se statickému přístupu ke službám (například staticky typovaným [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).

* Vyhněte se použití *vzoru lokátoru služby*. Například Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:

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

## <a name="additional-resources"></a>Další zdroje

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
