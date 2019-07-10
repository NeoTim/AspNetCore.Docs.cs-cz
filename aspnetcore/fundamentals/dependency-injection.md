---
title: Vkládání závislostí v ASP.NET Core
author: guardrex
description: Zjistěte, jak ASP.NET Core implementuje vkládání závislostí a jak se používá.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: 1455aa9ce4ea24eaeb396134f91b6d089b346c17
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724437"
---
# <a name="dependency-injection-in-aspnet-core"></a>Vkládání závislostí v ASP.NET Core

Podle [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), a [Luke Latham](https://github.com/guardrex)

ASP.NET Core podporuje návrhový vzor vkládání závislostí (Dependency Injection, DI), což je technika používaná pro dosažení [inverze řízení (Inversion of Control, IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.

Další informace specifické pro vkládání závislostí do kontrolerů MVC najdete v tématu <xref:mvc/controllers/dependency-injection>.

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

* Použití rozhraní nebo základní třídy abstraktní implementace závislostí.
* Registrace závislosti v kontejneru služeb. ASP.NET Core nabízí integrovanou službu kontejneru, <xref:System.IServiceProvider>. Služby jsou registrované v metodě `Startup.ConfigureServices` aplikace.
* *Vkládání* služeb do konstruktoru třídy, ve které se používá. Framework přebírá zodpovědnost za vytváření instancí závislostí a jejich uvolňování, kdy již nejsou dále potřebné.

V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definuje rozhraní `IMyDependency` metody, které poskytuje služba aplikaci:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Toto rozhraní je implementováno konkrétním typem `MyDependency`:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` Požadavky <xref:Microsoft.Extensions.Logging.ILogger`1> 've svém konstruktoru. Není neobvyklé používat zřetězené vkládání závislostí. Každá požadovaná závislost může v zápětí požadovat své vlastní závislosti. Kontejner řeší závislosti v grafu a vrací plně vyřešené služby. Množina závislostí, které musí být rozhodnuty, se obvykle označuje jako *strom závislostí*, *graf závislostí*, nebo *graf objektů*.

`IMyDependency` a `ILogger<TCategoryName>` musí být zaregistrovány v kontejneru služeb. `IMyDependency` je zaregistrovaný v `Startup.ConfigureServices`. `ILogger<TCategoryName>` je registrován infrastrukturou pro abstrakci protokolování, takže je [službou poskytovanou frameworkem](#framework-provided-services) registrovanou ve výchozím nastavení frameworkem.

Odstraňuje kontejner `ILogger<TCategoryName>` s využitím [(Obecné) otevřete typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), tím eliminuje nutnost zaregistrovat každý [(Obecné) konstruovaný typ.](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<T>), typeof(Logger<T>));
```

V ukázkové aplikaci je služba `IMyDependency` registrována s konkrétním typem `MyDependency`. Registrace specifikuje rámec životnosti služby na životnost jednoho požadavku. [Životnosti služby](#service-lifetimes) jsou popsány dále v tomto tématu.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Každá rozšiřující metoda `services.Add{SERVICE_NAME}` přidává (a potenciálně konfiguruje) služby. Například `services.AddMvc()` přidává služby Stránek Razor a MVC. Doporučujeme, aby v aplikacích byla tato konvence dodržena. Rozšiřující metody v umístění <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> obor názvů pro zapouzdření skupiny registrací služby.

Pokud konstruktor služby vyžaduje [předdefinovaný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), jako `string`, typ lze vloženy pomocí [konfigurace](xref:fundamentals/configuration/index) nebo [možnosti vzor](xref:fundamentals/configuration/options):

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

Pokud je dostupná rozšiřující metoda rozšiřující kolekci služeb o registraci služby (případě jejích závislých služeb, je-li to požadováno), je zvykem použít jedinou rozšiřující metodu `Add{SERVICE_NAME}` k registraci všech služeb vyžadovaných danou službu. Následující kód je příklad toho, jak přidat další služby do kontejneru pomocí metody rozšíření <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*>, <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>, a <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>:

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

Další informace najdete v tématu <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> clss v dokumentaci k rozhraní API.

## <a name="service-lifetimes"></a>Životnost služby

Zvolte odpovídající životnost pro každou registrovanou službu. Služby ASP.NET Core můžete nakonfigurovat s následujícími životnostmi:

### <a name="transient"></a>Přechodná

Přechodná doba platnosti služby (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) se vytváří pokaždé, když jste vyžádaný z kontejneru služby. Tato životnost je vhodná pro jednoduché, bezstavové služby.

### <a name="scoped"></a>Obor

Vymezené služby životního cyklu (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) se vytvoří jednou za žádost klienta (připojení).

> [!WARNING]
> Při použití služby s vymezenou živostností v middlewaru vkládejte službu do metody `Invoke` nebo `InvokeAsync`. Nevkládejte službu prostřednictvím konstruktoru, protože se služba bude chovat se jako singleton. Další informace naleznete v tématu <xref:fundamentals/middleware/index>.

### <a name="singleton"></a>Singleton

Deklarace služeb typu singleton životnost (<xref:Microsoft.AspNet.OData.Builder.ODataModelBuilder.AddSingleton*>) se vytvoří při prvním jste žádali (nebo když `Startup.ConfigureServices` spuštění a instance je zadán s registrací služby). Každý další požadavek použije stejnou instanci. Pokud je v aplikaci vyžadováno chování ve stylu návrhového vzoru Singleton, doporučuje se použít kontejner služeb ke správě životnosti takového objektu. Neposkytujte vlastní implementaci návrhového vzoru Singleton a umožněte tak uživatelskému kódu spravovat životnost objektu v rámci třídy.

> [!WARNING]
> Je nebezpečné získávat vymezené služby ze singletonů. Může to způsobit nesprávný stav služby při zpracování následujících požadavků.

## <a name="service-registration-methods"></a>Metody registrace služby

Každá metoda rozšíření registrace služby nabízí přetížení, které jsou užitečné v konkrétních scénářích.

| Metoda | Automatické<br>odkazy objektů<br>vyřazení | Několik<br>implementace | Předání argumentů |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Příklad:<br>`services.AddScoped<IMyDep, MyDep>();` | Ano | Ano | Ne |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddScoped<IMyDep>(sp => new MyDep());`<br>`services.AddScoped<IMyDep>(sp => new MyDep("A string!"));` | Ano | Ano | Ano |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Příklad:<br>`services.AddScoped<MyDep>();` | Ano | Ne | Ne |
| `Add{LIFETIME}<{SERVICE}>(new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddScoped<IMyDep>(new MyDep());`<br>`services.AddScoped<IMyDep>(new MyDep("A string!"));` | Ne | Ano | Ano |
| `Add{LIFETIME}(new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddScoped(new MyDep());`<br>`services.AddScoped(new MyDep("A string!"));` | Ne | Ne | Ano |

Další informace o vyřazení typu, najdete v článku [vyřazení služby](#disposal-of-services) oddílu. Je běžným scénářem, více implementací [napodobování typy pro testování](xref:test/integration-tests#inject-mock-services).

`TryAdd{LIFETIME}` metody pouze registraci služby, pokud ještě není zaregistrované implementace.

V následujícím příkladu první řádek registruje `MyDependency` pro `IMyDependency`. Druhý řádek nemá žádný vliv, protože `IMyDependency` už má registrovanou implementaci:

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

[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) metody zaregistrovat službu pouze, pokud ještě není implementace *stejného typu*. Několik služeb, které jsou vyřešeny prostřednictvím `IEnumerable<{SERVICE}>`. Při registraci služby, Vývojář pouze chce přidat instanci, pokud jeden z stejného typu už se nepřidal. Obecně platí tato metoda se používá autory knihoven, aby registraci dvě kopie instance v kontejneru.

V následujícím příkladu první řádek registruje `MyDep` pro `IMyDep1`. Druhý řádek registruje `MyDep` pro `IMyDep2`. Třetí řádek nemá žádný vliv, protože `IMyDep1` již má registrovaný provádění `MyDep`:

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
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; Umožňuje vytvoření objektu bez registrace služby v kontejneru pro vkládání závislostí. `ActivatorUtilities` se používá s uživatelsky orientovanými abstrakcemi, jako jsou například Tag Helpery, kontrolery MVC a bindery modelů.

Konstruktory mohou přijímat argumenty, které nejsou poskytovány v rámci vkládání závislostí, ale takové argumenty musí mít přiřazené výchozí hodnoty.

Pokud jsou služby řešeny pomocí `IServiceProvider` nebo `ActivatorUtilities`, pak je vyžadován *veřejný* konstruktor.

Když jsou vyřešeny služby `ActivatorUtilities`, vkládání konstruktor vyžaduje, že pouze jeden použít konstruktor existuje. Přetížení konstruktoru je podporováno, ale může existovat pouze jedno přetížením, jehož argumenty jsou splnitelné vkládáním závislostí.

## <a name="entity-framework-contexts"></a>Kontexty Entity Frameworku

Entity Framework kontexty jsou obvykle přidány do služby kontejneru pomocí [s vymezeným oborem životnost](#service-lifetimes) protože operací databáze webové aplikace jsou obvykle vymezené požadavku klienta. Výchozí doba života má obor, pokud se nezadá životnost <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*> přetížení při registraci kontext databáze. Služby danou dobu života neměli používat kontext databáze s životností kratší než služba.

## <a name="lifetime-and-registration-options"></a>Životnosti a možnosti registrace

Na ukázku rozdílů mezi životnostmi a možnostmi registrace si prohlédněte následující rozhraní reprezentující úlohy jako operace s jedinečným identifikátorem `OperationId`. V závislosti na tom, jak je životnost této služby nakonfigurována pro následující rozhraní, poskytne kontejner stejnou nebo rozdílnou instanci služby během vyžádání třídou:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Rozhraní jsou implementovány ve třídě `Operation`. Konstruktor `Operation` vytvoří identifikátor GUID, pokud není explicitně poskytnut:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

Služba `OperationService` je zaregistrována tak, aby závisela na jednotlivých typech tříd `Operation`. Když je `OperationService` vyžádána pomocí vkládání závislostí, obdrží buď novou nebo stávající instanci třídy jednotlivých služeb v závislosti na životnosti závislých služeb.

* Při vytváření přechodné služby na požádání v kontejneru `OperationId` z `IOperationTransient` služby se liší od `OperationId` z `OperationService`. `OperationService` obdrží novou instanci třídy `IOperationTransient`. Nová instance implikuje rozdílné `OperationId`.
* Při vytváření služby s vymezeným oborem každý požadavek klienta `OperationId` z `IOperationScoped` služba je stejné jako u `OperationService` v požadavku klienta. Mezi požadavky klientů, obě služby sdílené složky jiný `OperationId` hodnotu.
* Když jsou služby typu singleton a instanci typu singleton vytvořit jednou a použít v rámci všech požadavků klientů a všemi službami, `OperationId` je konstantní napříč všemi požadavky služby.

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
Nastavit rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Operace `OperationService`:

Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Nastavit rozsah: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

**Druhý požadavek:**

Operace kontroleru:

Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Nastavit rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Operace `OperationService`:

Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Nastavit rozsah: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Jednotlivý prvek: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Všimněte si, které hodnoty `OperationId` se liší v rámci požadavku a mezi požadavky:

* Objekty s *přechodnou* životností jsou vždy rozdílné. Přechodná `OperationId` hodnotu prvního a druhého klienta požadavky se liší pro obě `OperationService` operace napříč požadavky klientů. Novou instanci se poskytuje pro každou žádost o službu a požadavek klienta.
* *Obor* objekty jsou stejné v požadavku klienta, ale jiné napříč požadavky klienta.
* Objekty se živostností typu *singleton jsou* stejné pro všechny objekty a všechny požadavky bez ohledu na to, jestli je instance `Operation` poskytnuta v `Startup.ConfigureServices`.

## <a name="call-services-from-main"></a>Volání služeb z main

Vytvoření <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> s [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) vyřešit vymezené služby v rámci oboru aplikace. Tento způsob je užitečný pro přístup k službám s vymezenou životností při provádění inicializačních úloh během spuštění. Následující příklad ukazuje, jak získat kontext pro `MyScopedService` v `Program.Main`:

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

Poskytovatel služeb root je vytvořen, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> je volána. Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace/serveru, tedy poskytovatel vzniká se startem aplikace a je uvolněn při ukončení aplikace.

Služby s vymezenou životností jsou uvolňovány kontejnerem, který je vytvořil. Pokud jsou služby s vymezenou životností vytvořeny v kořenovém kontejneru, životnost služby je efektivně povýšen na singleton, protože je uvolněn pomocí kořenového kontejneru pouze při vypnutí serveru/aplikace. Validací rámce životnosti služeb ošetřuje tyto situace při volání `BuildServiceProvider`.

Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Služby požadavků

Služby dostupné v rámci požadavků ASP.NET Core z `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices).

Služby požadavků představují služby nakonfigurované a požadováné v rámci aplikace. Pokud objekty specifikují závislosti, jsou řešeny pomocí typů dostupných v `RequestServices`, nikoli `ApplicationServices`.

Aplikace by obvykle neměly používat tyto vlastnosti přímo. Místo toho získávejte požadované typy pomocí konstruktoru třídy a nechte framework vložit odpovídající závislosti. To poskytuje třídy, které usnadňuje testování.

> [!NOTE]
> Pro přístup ke kolekci `RequestServices` upřednostněte získávání závislostí jako parametr konstruktoru.

## <a name="design-services-for-dependency-injection"></a>Návrh služeb pro vkládání závislostí

Osvědčené postupy jsou následující:

* Navrhujte služby tak, aby využívaly vkládání závislostí pro získání jejich závislostí.
* Vyhněte se volání stavová a statické metody.
* Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb. Přímé vytváření instancí způsobuje přímou závislost na konkrétní implementaci.
* Malé, skvěle a snadno otestované, vytvořit třídy aplikace.

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
* Vkládání podle názvu
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

Vytvoření deklarace služeb typu singleton bezpečné pro vlákna. Pokud služby typu singleton obsahuje závislost na přechodné služby, přechodné služba může také požadovat bezpečnost vlákna v závislosti, jak se používají v typu singleton.

Metoda factory jedné služby, jako je například druhý argument [AddSingleton\<TService > (IServiceCollection, Func\<IServiceProvider, TService >)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečné pro vlákna. Stejně jako u typového (`static`) konstruktoru je zaručeno, že je volán jednou jediným vláknem.

## <a name="recommendations"></a>Doporučení

* `async/await` a `Task` závislosti služby rozlišení není podporováno. C#nepodporuje asynchronní konstruktory; doporučený model je proto používání asynchronních metod po synchronně překladu služby.

* Vyhněte se ukládání dat a konfigurace přímo do kontejneru služeb. Například nákupní košík uživatele by neměl být přidáván do kontejneru služeb. Konfigurace by měla používat [vzor možností (options pattern)](xref:fundamentals/configuration/options). Podobně se vystříhejte použití objektů "držící data", jejichž jediným účelem je poskytnutí přístupu k některému jinému objektu. Je lepší požádat o skutečné položky prostřednictvím DI.

* Vyhněte se statickému přístupu ke službám (například staticky typovaným [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).

* Vyhněte se použití *služby lokátoru vzor*. Není třeba vyvolat <xref:System.IServiceProvider.GetService*> při DI místo toho můžete získat instanci služby:

  **Nesprávný:**

  ```csharp
  public void MyMethod()
  {
      var options = 
          _services.GetService<IOptionsMonitor<MyOptions>>();
      var option = options.CurrentValue.Option;

      ...
  }
  ```

  **Správné**:

  ```csharp
  private readonly MyOptions _options;

  public MyClass(IOptionsMonitor<MyOptions> options)
  {
      _options = options.CurrentValue;
  }

  public void MyMethod()
  {
      var option = _options.Option;

      ...
  }
  ```

* Další variantou Lokátor služby, aby se vkládá objekt factory, který řeší závislosti za běhu. Obě tyto postupy kombinace [ovládacího prvku inverzi](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategie.

* Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Stejně jako u všech doporučení mohou nastat situace, ve kterých je možné tato doporučení ignorovat. Výjimky se vyskytují jen vzácně &mdash; nejčastěji jsou to speciální případy uvnitř frameworku samotného.

DI je *alternativní* na vzorech přístupu statická/globální objekt. Nebudete moci využít výhod DI, jsou-li zkombinovány s přístupem statický objekt.

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
