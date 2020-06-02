---
title: vkládání závislostí v ASP.NET Core autor: Rick-Anderson Popis: Přečtěte si, jak ASP.NET Core implementuje vkládání závislostí a jak ho používat.
monikerRange: ' >= aspnetcore-2,1 ' MS. Author: Riande MS. Custom: MVC MS. Date: 05/14/2020 No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: Základy/vkládání závislostí

---
# <a name="dependency-injection-in-aspnet-core"></a>Injektáž závislostí v ASP.NET Core

[Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com)a [Brandon Dahler](https://github.com/brandondahler)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.

Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection> .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Přehled injektáže závislosti

*Závislost* je libovolný objekt, který vyžaduje jiný objekt. Projděte si následující `MyDependency` třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy v aplikaci:

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

Instance `MyDependency` třídy může být vytvořena, aby byla `WriteMessage` Metoda k dispozici pro třídu. `MyDependency`Třída je závislostí `IndexModel` třídy:

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

Třída vytvoří a přímo závisí na `MyDependency` instanci. Závislosti kódu (například předchozí příklad) jsou problematické a je třeba se jim vyhnout z následujících důvodů:

* Chcete-li nahradit `MyDependency` jinou implementací, třída musí být upravena.
* Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou. Ve velkém projektu s více třídami, v závislosti na tom `MyDependency` , je kód konfigurace rozptýlen napříč aplikací.
* Tato implementace je obtížná pro testování částí. Aplikace by měla používat třídu typu "nebo zástupné procedury" `MyDependency` , což není u tohoto přístupu možné.

Vkládání závislostí řeší tyto problémy prostřednictvím:

* Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.
* Registrace závislosti v kontejneru služby. ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider> . Služby jsou registrovány v metodě aplikace `Startup.ConfigureServices` .
* *Vložení* služby do konstruktoru třídy, kde se používá. Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.

V [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` definuje rozhraní metodu, kterou služba poskytuje aplikaci:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Toto rozhraní je implementováno konkrétním typem `MyDependency` :

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency`vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru. Není neobvyklé používat vkládání závislostí v zřetězeném způsobem. Každá požadovaná závislost zase vyžádá své vlastní závislosti. Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu. Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.

`IMyDependency`a `ILogger<TCategoryName>` musí být registrována v kontejneru služby. `IMyDependency`je zaregistrován v `Startup.ConfigureServices` . `ILogger<TCategoryName>`je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.

Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

V ukázkové aplikaci `IMyDependency` je služba zaregistrovaná u konkrétního typu `MyDependency` . Registruje rozsah platnosti služby po dobu životnosti jediného požadavku. [Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Jednotlivé `services.Add{SERVICE_NAME}` metody rozšíření přidávají (a případně nakonfigurují) služby. Například `services.AddMvc()` přidá Razor stránky služeb a MVC vyžaduje. Doporučujeme, aby aplikace dodržovaly tuto konvenci. Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.

Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string` ,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):

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

Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli. Pole se používá pro přístup ke službě podle potřeby v celé třídě.

V ukázkové aplikaci `IMyDependency` je instance vyžádána a používána pro volání `WriteMessage` metody služby:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Služby vložené do spuštění

`Startup`Při použití obecného hostitele () mohou být do konstruktoru vloženy pouze následující typy služeb <xref:Microsoft.Extensions.Hosting.IHostBuilder> :

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Služby je možné vložit do `Startup.Configure` :

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Služby poskytované rozhraním

`Startup.ConfigureServices`Metoda zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC. Zpočátku `IServiceCollection` `ConfigureServices` služba má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host). Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní. V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.

| Typ služby | Doba platnosti |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Dočasný |
| `IHostApplicationLifetime` | Singleton |
| `IWebHostEnvironment` | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Dočasný |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Dočasný |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Dočasný |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>Registrace dalších služeb pomocí rozšiřujících metod

Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou `Add{SERVICE_NAME}` metodu rozšíření k registraci všech služeb vyžadovaných touto službou. Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :

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

Další informace najdete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> ve třídě v dokumentaci k rozhraní API.

## <a name="service-lifetimes"></a>Životnost služeb

Vyberte odpovídající dobu života pro každou registrovanou službu. Služby ASP.NET Core Services je možné nakonfigurovat s následujícími životnostmi:

### <a name="transient"></a>Dočasný

Služba přechodných životností ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ) se vytvoří pokaždé, když se požadují z kontejneru služby. Tato životnost funguje nejlépe pro odlehčené bezstavové služby.

### <a name="scoped"></a>Obor

Oborové služby () s rozsahem platnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) se vytvoří jednou za požadavek klienta (připojení).

> [!WARNING]
> Při použití oboru služby v middlewaru založit službu do `Invoke` `InvokeAsync` metody nebo. Nepoužívejte [vkládání pomocí injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) , protože vynutí, aby se služba chovala jako typ singleton. Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Služba singleton životnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby). Každý další požadavek používá stejnou instanci. Pokud aplikace vyžaduje chování singleton, doporučuje se povolit kontejneru služby spravovat dobu života služby. Neimplementujte vzor návrhu singleton a poskytněte uživatelský kód pro správu životního cyklu objektu ve třídě.

> [!WARNING]
> Není bezpečné přeložit oborovou službu z typu singleton. Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.

## <a name="service-registration-methods"></a>Metody registrace služby

Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.

| Metoda | Automaticky<br>odkazy objektů<br>odvod | Několik<br>implementace | Pass – argumenty |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Příklad:<br>`services.AddSingleton<IMyDep, MyDep>();` | Ano | Ano | No |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Ano | Ano | Ano |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Příklad:<br>`services.AddSingleton<MyDep>();` | Ano | Ne | Ne |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Ano | Ano |
| `AddSingleton(new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Ne | No | Ano |

Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) . Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).

`TryAdd{LIFETIME}`metody registrují službu jenom v případě, že už není zaregistrovaná implementace.

V následujícím příkladu se první řádek registruje `MyDependency` pro `IMyDependency` . Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:

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

Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*. Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>` . Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný. Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.

V následujícím příkladu se první řádek registruje `MyDep` pro `IMyDep1` . Druhý řádek se registruje `MyDep` pro `IMyDep2` . Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou implementaci `MyDep` :

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Chování injektáže konstruktoru

Služby je možné vyřešit pomocí dvou mechanismů:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Povoluje vytvoření objektu bez registrace služby v kontejneru vkládání závislostí. `ActivatorUtilities`se používá s uživateli s přístupem k uživatelům, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.

Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.

Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.

Když jsou služby vyřešeny nástrojem `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor. Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.

## <a name="entity-framework-contexts"></a>Entity Framework kontexty

Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta. Výchozí doba života je vymezena v případě, že při registraci kontextu databáze není zadána doba života při [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení. Služby pro danou dobu života by neměly používat kontext databáze s kratší dobou životnosti než služba.

## <a name="lifetime-and-registration-options"></a>Možnosti života a registrace

Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s jedinečným identifikátorem `OperationId` . V závislosti na tom, jak je životnost provozní služby nakonfigurována pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby, když je vyžádána třídou:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Rozhraní jsou implementována ve `Operation` třídě. `Operation`Konstruktor generuje identifikátor GUID, pokud není dodán:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

`OperationService`Je zaregistrován, který závisí na každém z ostatních `Operation` typů. Když `OperationService` je požadavek proveden prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.

* Když se v případě vyžádání z kontejneru vytvoří přechodné služby, `OperationId` liší se od `IOperationTransient` služby `OperationId` `OperationService` . `OperationService`přijme novou instanci `IOperationTransient` třídy. Nová instance vrací jinou instanci `OperationId` .
* Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta. V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.
* Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, `OperationId` je v rámci všech žádostí o službu konstantní.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

V `Startup.ConfigureServices` je každý typ přidán do kontejneru podle jeho pojmenované životnosti:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

`IOperationSingletonInstance`Služba používá konkrétní instanci se známým ID `Guid.Empty` . Je jasné, že pokud se tento typ používá (jeho identifikátor GUID je všechny nuly).

Ukázková aplikace ukazuje dobu života objektu v rámci a mezi jednotlivými požadavky. Ukázková aplikace `IndexModel` požaduje každý druh `IOperation` typu a `OperationService` . Stránka potom zobrazí všechny hodnoty třídy modelu stránky a `OperationId` hodnoty služby prostřednictvím přiřazení vlastností:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Výsledkem dvou následujících výstupů jsou výsledky dvou požadavků:

**První požadavek:**

Operace kontroleru:

Přechodný: d233e165-f417-469b-A866-1cf1935d2518  
Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

`OperationService`Operations

Přechodný: c6b049eb-1318-4E31-90f1-eb2dd849ff64  
Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

**Druhá žádost:**

Operace kontroleru:

Přechodný: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

`OperationService`Operations

Přechodný: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Pozor, které `OperationId` hodnoty se liší v rámci požadavku a mezi požadavky:

* *Přechodné* objekty jsou vždy odlišné. Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů. Každé žádosti o službu a žádosti klienta se poskytne nová instance.
* *Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.
* Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, zda `Operation` je instance uvedena v `Startup.ConfigureServices` .

## <a name="call-services-from-main"></a>Volat služby z Main

Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace. Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace. Následující příklad ukazuje, jak získat kontext pro `MyScopedService` `Program.Main` :

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

## <a name="scope-validation"></a>Ověřování oboru

Když je aplikace spuštěná ve vývojovém prostředí a volá [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) , aby se vytvořila hostitel, použije výchozí poskytovatel služeb kontroly, aby ověřil, že:

* Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.
* Oborové služby nejsou přímo nebo nepřímo vložené do singleton.

Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá. Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace nebo serveru, když se poskytovatel spustí s aplikací a je uvolněný při ukončení aplikace.

Oborové služby jsou uvolněny kontejnerem, který je vytvořil. Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru. Při ověřování oborů služeb se tyto situace zachytí `BuildServiceProvider` .

Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Žádosti o služby

Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core, `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .

Žádosti o služby reprezentují služby nakonfigurované a požadované v rámci aplikace. Když objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices` , ne `ApplicationServices` .

Obecně platí, že aplikace by tyto vlastnosti neměla používat přímo. Místo toho požádejte o typy, které třídy vyžadují prostřednictvím konstruktorů třídy, a umožněte rozhraní vkládat závislosti. To poskytuje třídy, které jsou snáze testovány.

> [!NOTE]
> Preferovat požadavky na závislosti jako parametry konstruktoru pro přístup ke `RequestServices` kolekci.

## <a name="design-services-for-dependency-injection"></a>Navrhnout služby pro vkládání závislostí

Osvědčené postupy:

* Navrhněte služby pro použití injektáže závislosti k získání jejich závislostí.
* Vyhněte se stavovým, statickým třídám a členům. Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.
* Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb. Přímá instance Couples kód na konkrétní implementaci.
* Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.

Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Pokuste se Refaktorovat třídu přesunutím některých odpovědností do nové třídy. Mějte na paměti, že Razor stránky tříd modelu stránky a třídy KONTROLERU MVC by se měly zaměřit na uživatelské rozhraní. Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Vyřazení služeb

Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří. Pokud je instance přidána do kontejneru pomocí uživatelského kódu, nebude uvolněna automaticky.

V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:

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
* Zamýšlené životnosti služeb nejsou rozhraním známy.
* Rozhraní neodstraní služby automaticky.
* Pokud se služby explicitně neodstraní v kódu pro vývojáře, zůstanou zachovány, dokud se aplikace neukončí.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>Doprovodné materiály k rozhraní IDisposable pro přechodné a sdílené instance

#### <a name="transient-limited-lifetime"></a>Přechodná, omezená doba života

**Scénář**

Aplikace vyžaduje <xref:System.IDisposable> instanci s přechodným životností pro některý z následujících scénářů:

* Instance je vyřešena v kořenovém oboru.
* Instance by měla být uvolněna před ukončením oboru.

**Řešení**

Pomocí modelu továrny vytvořte instanci mimo nadřazený obor. V takové situaci by aplikace měla obvykle `Create` metodu, která volá přímo konstruktor finálního typu. Pokud má konečný typ jiné závislosti, továrna může:

* Přijmout <xref:System.IServiceProvider> ve svém konstruktoru.
* Slouží <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> k vytvoření instance instance mimo kontejner, při použití kontejneru pro jeho závislosti.

#### <a name="shared-instance-limited-lifetime"></a>Sdílená instance, omezená doba života

**Scénář**

Aplikace vyžaduje sdílenou <xref:System.IDisposable> instanci napříč více službami, ale <xref:System.IDisposable> měla by mít omezené trvání.

**Řešení**

Zaregistrujte instanci s vymezenou životností. Použijte <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> k zahájení a vytvoření nového <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> . <xref:System.IServiceProvider>Požadované služby získáte pomocí tohoto oboru. Vyřadit rozsah, pokud by životnost měla končit.

#### <a name="general-guidelines"></a>Obecné pokyny

* Neregistrujte <xref:System.IDisposable> instance s přechodným oborem. Místo toho použijte výrobní model.
* V kořenovém oboru neřešte přechodné nebo vymezené <xref:System.IDisposable> instance. Jedinou obecnou výjimkou je, když aplikace vytvoří nebo znovu vytvoří a odstraní <xref:System.IServiceProvider> , což není ideální vzor.
* Příjem <xref:System.IDisposable> závislosti přes di nevyžaduje, aby se příjemce sám implementoval <xref:System.IDisposable> . Příjemce <xref:System.IDisposable> závislosti by neměl volat <xref:System.IDisposable.Dispose%2A> tuto závislost.
* Obory by měly sloužit k řízení životnosti služeb. Obory nejsou hierarchické a mezi obory není žádné zvláštní připojení.

## <a name="default-service-container-replacement"></a>Výchozí nahrazení kontejneru služby

Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací. Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:

* Vkládání vlastností
* Vkládání na základě názvu
* Podřízené kontejnery
* Vlastní Správa životního cyklu
* `Func<T>`Podpora opožděné inicializace
* Registrace založená na konvencích

S ASP.NET Coremi aplikacemi je možné používat následující kontejnery třetích stran:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Integrit](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Bezpečnost vlákna

Vytvořte služby, které jsou v bezpečí pro přístup z více vláken. Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.

Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken. Podobně jako konstruktor typu ( `static` ), je zaručeno, že bude volán jednou vláknem.

## <a name="recommendations"></a>Doporučení

* `async/await``Task`řešení služeb a se nepodporuje. Jazyk C# nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.

* Neukládejte data a konfiguraci přímo do kontejneru služby. Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby. Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options). Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu. Je lepší žádat o skutečnou položku přes DI.

* Vyhněte se statickému přístupu ke službám (například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).

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

* Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení. Výjimky jsou zřídka, většinou zvláštní případy v rámci samotného rozhraní.

DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům. Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a>Doporučené vzory pro víceklientské architektury v DI

[Sadu jader](https://github.com/OrchardCMS/OrchardCore) nabízí víceklientské architektury. Další informace najdete v dokumentaci k sadě [sad](https://docs.orchardcore.net/en/dev/).

V ukázkových aplikacích v tématu najdete https://github.com/OrchardCMS/OrchardCore.Samples příklady vytváření modulárních a víceklientské aplikací s využitím jenom sady Core Core Framework bez jakýchkoli funkcí specifických pro CMS.

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Čtyři způsoby, jak vyřadit IDisposable v ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Explicitní závislosti – princip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)](https://www.martinfowler.com/articles/injection.html)
* [Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.

Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection> .

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Přehled injektáže závislosti

*Závislost* je libovolný objekt, který vyžaduje jiný objekt. Projděte si následující `MyDependency` třídu s `WriteMessage` metodou, na které jsou závislé jiné třídy v aplikaci:

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

Instance `MyDependency` třídy může být vytvořena, aby byla `WriteMessage` Metoda k dispozici pro třídu. `MyDependency`Třída je závislostí `IndexModel` třídy:

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

Třída vytvoří a přímo závisí na `MyDependency` instanci. Závislosti kódu (například předchozí příklad) jsou problematické a je třeba se jim vyhnout z následujících důvodů:

* Chcete-li nahradit `MyDependency` jinou implementací, třída musí být upravena.
* Pokud `MyDependency` má závislosti, musí být nakonfigurovány třídou. Ve velkém projektu s více třídami, v závislosti na tom `MyDependency` , je kód konfigurace rozptýlen napříč aplikací.
* Tato implementace je obtížná pro testování částí. Aplikace by měla používat třídu typu "nebo zástupné procedury" `MyDependency` , což není u tohoto přístupu možné.

Vkládání závislostí řeší tyto problémy prostřednictvím:

* Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.
* Registrace závislosti v kontejneru služby. ASP.NET Core poskytuje integrovaný kontejner služeb <xref:System.IServiceProvider> . Služby jsou registrovány v metodě aplikace `Startup.ConfigureServices` .
* *Vložení* služby do konstruktoru třídy, kde se používá. Rozhraní přebírá zodpovědnost za vytvoření instance závislosti a její odstranění, když už ji nepotřebujete.

V [ukázkové aplikaci](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) `IMyDependency` definuje rozhraní metodu, kterou služba poskytuje aplikaci:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Toto rozhraní je implementováno konkrétním typem `MyDependency` :

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency`vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> v jeho konstruktoru. Není neobvyklé používat vkládání závislostí v zřetězeném způsobem. Každá požadovaná závislost zase vyžádá své vlastní závislosti. Kontejner vyřeší závislosti v grafu a vrátí plně přeloženou službu. Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.

`IMyDependency`a `ILogger<TCategoryName>` musí být registrována v kontejneru služby. `IMyDependency`je zaregistrován v `Startup.ConfigureServices` . `ILogger<TCategoryName>`je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.

Kontejner se překládá `ILogger<TCategoryName>` tím, že využije [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

V ukázkové aplikaci `IMyDependency` je služba zaregistrovaná u konkrétního typu `MyDependency` . Registruje rozsah platnosti služby po dobu životnosti jediného požadavku. [Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Jednotlivé `services.Add{SERVICE_NAME}` metody rozšíření přidávají (a případně nakonfigurují) služby. Například `services.AddMvc()` přidá Razor stránky služeb a MVC vyžaduje. Doporučujeme, aby aplikace dodržovaly tuto konvenci. Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.

Pokud konstruktor služby vyžaduje [vestavěný typ](/dotnet/csharp/language-reference/keywords/built-in-types-table), například `string` ,, může být typ vložen pomocí [Konfigurace](xref:fundamentals/configuration/index) nebo [vzoru možností](xref:fundamentals/configuration/options):

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

Instance služby je požadována prostřednictvím konstruktoru třídy, kde je služba používána a přiřazena k soukromému poli. Pole se používá pro přístup ke službě podle potřeby v celé třídě.

V ukázkové aplikaci `IMyDependency` je instance vyžádána a používána pro volání `WriteMessage` metody služby:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>Služby vložené do spuštění

`Startup`Při použití obecného hostitele () mohou být do konstruktoru vloženy pouze následující typy služeb <xref:Microsoft.Extensions.Hosting.IHostBuilder> :

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Služby je možné vložit do `Startup.Configure` :

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Služby poskytované rozhraním

`Startup.ConfigureServices`Metoda zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC. Zpočátku `IServiceCollection` `ConfigureServices` služba má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host). Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní. V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.

| Typ služby | Doba platnosti |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Dočasný |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Dočasný |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Dočasný |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Dočasný |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>Registrace dalších služeb pomocí rozšiřujících metod

Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou `Add{SERVICE_NAME}` metodu rozšíření k registraci všech služeb vyžadovaných touto službou. Následující kód je příkladem přidání dalších služeb do kontejneru pomocí metod rozšíření [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> :

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

Další informace najdete <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> ve třídě v dokumentaci k rozhraní API.

## <a name="service-lifetimes"></a>Životnost služeb

Vyberte odpovídající dobu života pro každou registrovanou službu. Služby ASP.NET Core Services je možné nakonfigurovat s následujícími životnostmi:

### <a name="transient"></a>Dočasný

Služba přechodných životností ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*> ) se vytvoří pokaždé, když se požadují z kontejneru služby. Tato životnost funguje nejlépe pro odlehčené bezstavové služby.

### <a name="scoped"></a>Obor

Oborové služby () s rozsahem platnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*> ) se vytvoří jednou za požadavek klienta (připojení).

> [!WARNING]
> Při použití oboru služby v middlewaru založit službu do `Invoke` `InvokeAsync` metody nebo. Nepoužívejte [vkládání pomocí injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) , protože vynutí, aby se služba chovala jako typ singleton. Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Služba singleton životnosti ( <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> ) je vytvořena při prvním vyžádání (nebo při `Startup.ConfigureServices` spuštění a instance je určena pro registraci služby). Každý další požadavek používá stejnou instanci. Pokud aplikace vyžaduje chování singleton, doporučuje se povolit kontejneru služby spravovat dobu života služby. Neimplementujte vzor návrhu singleton a poskytněte uživatelský kód pro správu životního cyklu objektu ve třídě.

> [!WARNING]
> Není bezpečné přeložit oborovou službu z typu singleton. Může dojít k tomu, že služba má při zpracování dalších požadavků špatný stav.

## <a name="service-registration-methods"></a>Metody registrace služby

Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.

| Metoda | Automaticky<br>odkazy objektů<br>odvod | Několik<br>implementace | Pass – argumenty |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Příklad:<br>`services.AddSingleton<IMyDep, MyDep>();` | Ano | Ano | No |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Ano | Ano | Ano |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Příklad:<br>`services.AddSingleton<MyDep>();` | Ano | Ne | Ne |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Ano | Ano |
| `AddSingleton(new {IMPLEMENTATION})`<br>Příklady:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Ne | No | Ano |

Další informace o vyřazení typů najdete v části věnované [vyřazení služeb](#disposal-of-services) . Běžným scénářem pro více implementací je vytvoření [typů pro testování](xref:test/integration-tests#inject-mock-services).

`TryAdd{LIFETIME}`metody registrují službu jenom v případě, že už není zaregistrovaná implementace.

V následujícím příkladu se první řádek registruje `MyDependency` pro `IMyDependency` . Druhý řádek nemá žádný účinek, protože `IMyDependency` už má registrovanou implementaci:

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

Metody [TryAddEnumerable (ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) registrují službu pouze v případě, že již není implementace *stejného typu*. Několik služeb je vyřešeno prostřednictvím `IEnumerable<{SERVICE}>` . Při registraci služeb chce vývojář přidat instanci jenom v případě, že už jeden ze stejného typu není přidaný. Obecně je tato metoda používána autory knihovny k tomu, aby nedocházelo k registraci dvou kopií instance v kontejneru.

V následujícím příkladu se první řádek registruje `MyDep` pro `IMyDep1` . Druhý řádek se registruje `MyDep` pro `IMyDep2` . Třetí řádek nemá žádný vliv, protože `IMyDep1` už má registrovanou implementaci `MyDep` :

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Chování injektáže konstruktoru

Služby je možné vyřešit pomocí dvou mechanismů:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Povoluje vytvoření objektu bez registrace služby v kontejneru vkládání závislostí. `ActivatorUtilities`se používá s uživateli s přístupem k uživatelům, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.

Konstruktory mohou přijímat argumenty, které nejsou poskytovány vkládáním závislostí, ale argumenty musí přiřadit výchozí hodnoty.

Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje *veřejný* konstruktor.

Když jsou služby vyřešeny nástrojem `ActivatorUtilities` , [Injektáže konstruktoru](xref:mvc/controllers/dependency-injection#constructor-injection) vyžaduje, aby existoval pouze jeden příslušný konstruktor. Přetížení konstruktoru jsou podporovaná, ale může existovat jenom jedno přetížení, jehož argumenty můžou být splněné vkládáním závislostí.

## <a name="entity-framework-contexts"></a>Entity Framework kontexty

Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta. Výchozí doba života je vymezena v případě, že při registraci kontextu databáze není zadána doba života při [AddDbContext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení. Služby pro danou dobu života by neměly používat kontext databáze s kratší dobou životnosti než služba.

## <a name="lifetime-and-registration-options"></a>Možnosti života a registrace

Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s jedinečným identifikátorem `OperationId` . V závislosti na tom, jak je životnost provozní služby nakonfigurována pro následující rozhraní, kontejner poskytuje buď stejnou nebo jinou instanci služby, když je vyžádána třídou:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Rozhraní jsou implementována ve `Operation` třídě. `Operation`Konstruktor generuje identifikátor GUID, pokud není dodán:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

`OperationService`Je zaregistrován, který závisí na každém z ostatních `Operation` typů. Když `OperationService` je požadavek proveden prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.

* Když se v případě vyžádání z kontejneru vytvoří přechodné služby, `OperationId` liší se od `IOperationTransient` služby `OperationId` `OperationService` . `OperationService`přijme novou instanci `IOperationTransient` třídy. Nová instance vrací jinou instanci `OperationId` .
* Když se vytvoří vymezené služby pro jednotlivé požadavky klienta, `OperationId` `IOperationScoped` služba je stejná jako `OperationService` v rámci žádosti klienta. V rámci požadavků klientů obě služby sdílejí jinou `OperationId` hodnotu.
* Pokud se služby instance singleton a singleton vytvoří jednou a použije se pro všechny požadavky klientů a všechny služby, `OperationId` je v rámci všech žádostí o službu konstantní.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

V `Startup.ConfigureServices` je každý typ přidán do kontejneru podle jeho pojmenované životnosti:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

`IOperationSingletonInstance`Služba používá konkrétní instanci se známým ID `Guid.Empty` . Je jasné, že pokud se tento typ používá (jeho identifikátor GUID je všechny nuly).

Ukázková aplikace ukazuje dobu života objektu v rámci a mezi jednotlivými požadavky. Ukázková aplikace `IndexModel` požaduje každý druh `IOperation` typu a `OperationService` . Stránka potom zobrazí všechny hodnoty třídy modelu stránky a `OperationId` hodnoty služby prostřednictvím přiřazení vlastností:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Výsledkem dvou následujících výstupů jsou výsledky dvou požadavků:

**První požadavek:**

Operace kontroleru:

Přechodný: d233e165-f417-469b-A866-1cf1935d2518  
Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

`OperationService`Operations

Přechodný: c6b049eb-1318-4E31-90f1-eb2dd849ff64  
Vymezené: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

**Druhá žádost:**

Operace kontroleru:

Přechodný: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

`OperationService`Operations

Přechodný: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Vymezené: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Pozor, které `OperationId` hodnoty se liší v rámci požadavku a mezi požadavky:

* *Přechodné* objekty jsou vždy odlišné. Přechodná `OperationId` hodnota pro první i druhý požadavek klienta se liší pro obě `OperationService` operace i mezi požadavky klientů. Každé žádosti o službu a žádosti klienta se poskytne nová instance.
* *Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.
* Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, zda `Operation` je instance uvedena v `Startup.ConfigureServices` .

## <a name="call-services-from-main"></a>Volat služby z Main

Vytvořte <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> pomocí [IServiceScopeFactory. CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) pro řešení oboru služby v rámci rozsahu aplikace. Tento přístup je užitečný pro přístup k oboruované službě při spuštění za účelem spouštění úloh inicializace. Následující příklad ukazuje, jak získat kontext pro `MyScopedService` `Program.Main` :

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

## <a name="scope-validation"></a>Ověřování oboru

Když je aplikace spuštěná ve vývojovém prostředí, provede výchozí poskytovatel služeb kontroly, aby ověřil, že:

* Oborové služby nejsou přímo nebo nepřímo vyřešeny od poskytovatele kořenové služby.
* Oborové služby nejsou přímo nebo nepřímo vložené do singleton.

Poskytovatel kořenové služby se vytvoří, když <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> se zavolá. Doba života poskytovatele kořenové služby odpovídá době životnosti aplikace nebo serveru, když se poskytovatel spustí s aplikací a je uvolněný při ukončení aplikace.

Oborové služby jsou uvolněny kontejnerem, který je vytvořil. Pokud je v kořenovém kontejneru vytvořena vymezená služba, doba života služby je efektivně povýšena na typ singleton, protože je uvolněna pouze kořenovým kontejnerem při vypnutí aplikace nebo serveru. Při ověřování oborů služeb se tyto situace zachytí `BuildServiceProvider` .

Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Žádosti o služby

Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core, `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .

Žádosti o služby reprezentují služby nakonfigurované a požadované v rámci aplikace. Když objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices` , ne `ApplicationServices` .

Obecně platí, že aplikace by tyto vlastnosti neměla používat přímo. Místo toho požádejte o typy, které třídy vyžadují prostřednictvím konstruktorů třídy, a umožněte rozhraní vložit závislosti. To poskytuje třídy, které jsou snáze testovány.

> [!NOTE]
> Preferovat požadavky na závislosti jako parametry konstruktoru pro přístup ke `RequestServices` kolekci.

## <a name="design-services-for-dependency-injection"></a>Navrhnout služby pro vkládání závislostí

Osvědčené postupy:

* Navrhněte služby pro použití injektáže závislosti k získání jejich závislostí.
* Vyhněte se stavovým, statickým třídám a členům. Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.
* Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb. Přímá instance Couples kód na konkrétní implementaci.
* Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.

Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). Pokuste se Refaktorovat třídu přesunutím některých odpovědností do nové třídy. Mějte na paměti, že Razor stránky tříd modelu stránky a třídy KONTROLERU MVC by se měly zaměřit na uživatelské rozhraní. Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Vyřazení služeb

Kontejner volá <xref:System.IDisposable.Dispose*> typy, které <xref:System.IDisposable> vytvoří. Pokud je instance přidána do kontejneru pomocí uživatelského kódu, nebude uvolněna automaticky.

V následujícím příkladu jsou služby vytvořeny pomocí kontejneru služby a automaticky odstraněny:

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
* Zamýšlené životnosti služeb nejsou rozhraním známy.
* Rozhraní neodstraní služby automaticky.
* Pokud se služby explicitně neodstraní v kódu pro vývojáře, zůstanou zachovány, dokud se aplikace neukončí.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>Doprovodné materiály k rozhraní IDisposable pro přechodné a sdílené instance

#### <a name="transient-limited-lifetime"></a>Přechodná, omezená doba života

**Scénář**

Aplikace vyžaduje <xref:System.IDisposable> instanci s přechodným životností pro některý z následujících scénářů:

* Instance je vyřešena v kořenovém oboru.
* Instance by měla být uvolněna před ukončením oboru.

**Řešení**

Pomocí modelu továrny vytvořte instanci mimo nadřazený obor. V takové situaci by aplikace měla obvykle `Create` metodu, která volá přímo konstruktor finálního typu. Pokud má konečný typ jiné závislosti, továrna může:

* Přijmout <xref:System.IServiceProvider> ve svém konstruktoru.
* Slouží <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> k vytvoření instance instance mimo kontejner, při použití kontejneru pro jeho závislosti.

#### <a name="shared-instance-limited-lifetime"></a>Sdílená instance, omezená doba života

**Scénář**

Aplikace vyžaduje sdílenou <xref:System.IDisposable> instanci napříč více službami, ale <xref:System.IDisposable> měla by mít omezené trvání.

**Řešení**

Zaregistrujte instanci s vymezenou životností. Použijte <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> k zahájení a vytvoření nového <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> . <xref:System.IServiceProvider>Požadované služby získáte pomocí tohoto oboru. Vyřadit rozsah, pokud by životnost měla končit.

#### <a name="general-guidelines"></a>Obecné pokyny

* Neregistrujte <xref:System.IDisposable> instance s přechodným oborem. Místo toho použijte výrobní model.
* V kořenovém oboru neřešte přechodné nebo vymezené <xref:System.IDisposable> instance. Jedinou obecnou výjimkou je, když aplikace vytvoří nebo znovu vytvoří a odstraní <xref:System.IServiceProvider> , což není ideální vzor.
* Příjem <xref:System.IDisposable> závislosti přes di nevyžaduje, aby se příjemce sám implementoval <xref:System.IDisposable> . Příjemce <xref:System.IDisposable> závislosti by neměl volat <xref:System.IDisposable.Dispose%2A> tuto závislost.
* Obory by měly sloužit k řízení životnosti služeb. Obory nejsou hierarchické a mezi obory není žádné zvláštní připojení.

## <a name="default-service-container-replacement"></a>Výchozí nahrazení kontejneru služby

Integrovaný kontejner služeb je navržený tak, aby sloužil potřebám architektury a většině spotřebitelských aplikací. Pokud nepotřebujete určitou funkci, kterou integrovaný kontejner nepodporuje, doporučujeme použít vestavěný kontejner, například:

* Vkládání vlastností
* Vkládání na základě názvu
* Podřízené kontejnery
* Vlastní Správa životního cyklu
* `Func<T>`Podpora opožděné inicializace
* Registrace založená na konvencích

S ASP.NET Coremi aplikacemi je možné používat následující kontejnery třetích stran:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Integrit](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Bezpečnost vlákna

Vytvořte služby, které jsou v bezpečí pro přístup z více vláken. Pokud má služba typu Singleton závislost na přechodné službě, přechodná služba může také vyžadovat zabezpečení vlákna v závislosti na tom, jak je používá typ singleton.

Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton \<TService> (IServiceCollection, Func \<IServiceProvider,TService> )](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken. Podobně jako konstruktor typu ( `static` ), je zaručeno, že bude volán jednou vláknem.

## <a name="recommendations"></a>Doporučení

* `async/await``Task`řešení služeb a se nepodporuje. Jazyk C# nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.

* Neukládejte data a konfiguraci přímo do kontejneru služby. Například nákupní košík uživatele by neměl být obvykle přidán do kontejneru služby. Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options). Podobně nepoužívejte objekty "držitel dat", které existují pouze k povolení přístupu k jinému objektu. Je lepší žádat o skutečnou položku přes DI.

* Vyhněte se statickému přístupu ke službám (například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).

* Vyhněte se použití *vzoru lokátoru služby*, který kombinuje [inverzi strategií řízení](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) .

  * Nevolejte <xref:System.IServiceProvider.GetService*> pro získání instance služby, když můžete místo toho použít di:

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

  * Vyhněte se vkládání továrny, která řeší závislosti za běhu pomocí <xref:System.IServiceProvider.GetService*> .

* Vyhněte se statickému přístupu k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Podobně jako u všech sad doporučení se může stát, že se vyžaduje ignorování doporučení. Výjimky jsou zřídka, většinou zvláštní případy v rámci samotného rozhraní.

DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům. Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Čtyři způsoby, jak vyřadit IDisposable v ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Explicitní závislosti – princip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)](https://www.martinfowler.com/articles/injection.html)
* [Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
