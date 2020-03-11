---
title: Vkládání závislostí v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak ASP.NET Core implementuje vkládání závislostí a jak se používá.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2020
uid: fundamentals/dependency-injection
ms.openlocfilehash: 3080d1a19bb48996e2bc7a3ce824f48bfc1bcbce
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663763"
---
# <a name="dependency-injection-in-aspnet-core"></a>Vkládání závislostí v ASP.NET Core

[Steve Smith](https://ardalis.com/) a [Scott Addie](https://scottaddie.com)

ASP.NET Core podporuje vzor návrhu softwaru pro vkládání závislostí (DI), což je technika pro dosažení [inverze ovládacího prvku (IOC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.

Další informace, které jsou specifické pro vkládání závislostí v rámci řadičů MVC, najdete v tématu <xref:mvc/controllers/dependency-injection>.

[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Přehled vkládání závislostí

*Závislost* je libovolný objekt, který vyžaduje jiný objekt. Projděte si následující třídu `MyDependency` s metodou `WriteMessage`, na které jsou závislé jiné třídy v aplikaci:

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

Lze vytvořit instanci třídy `MyDependency`, aby byla metoda `WriteMessage` dostupná pro třídu. Třída `MyDependency` je závislost `IndexModel` třídy:

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

Třída vytvoří a přímo závisí na instanci `MyDependency`. Přímé závislosti v kódu (jako ta v předchozím příkladu) jsou problematické a měli byste se jim vyhnout z následujících důvodů:

* Chcete-li nahradit `MyDependency` odlišnou implementací, třída musí být upravena.
* Pokud `MyDependency` obsahuje závislosti, musí být nakonfigurovány třídou. Ve velkém projektu s více třídami v závislosti na `MyDependency`je kód konfigurace rozptýlen napříč aplikací.
* Tento způsob implementace je obtížný na jednotkové testování. Aplikace by měla použít třídu typu `MyDependency` nebo zástupnou proceduru, která není s tímto přístupem možná.

Vkládání závislostí řeší tyto problémy prostřednictvím:

* Použití rozhraní nebo základní třídy k abstrakci implementace závislosti.
* Registrace závislosti v kontejneru služeb. ASP.NET Core poskytuje integrovaný kontejner služby <xref:System.IServiceProvider>. Služby jsou zaregistrované v `Startup.ConfigureServices` metodě aplikace.
* *Vložení* služby do konstruktoru třídy, kde se používá. Framework přebírá zodpovědnost za vytváření instancí závislostí a jejich uvolňování, kdy již nejsou dále potřebné.

V [ukázkové aplikaci](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)definuje rozhraní `IMyDependency` metodu, kterou služba poskytuje aplikaci:

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

`MyDependency` vyžádá <xref:Microsoft.Extensions.Logging.ILogger`1> ve svém konstruktoru. Není neobvyklé používat zřetězené vkládání závislostí. Každá požadovaná závislost může v zápětí požadovat své vlastní závislosti. Kontejner řeší závislosti v grafu a vrací plně vyřešené služby. Kolektivní sada závislostí, které je třeba vyřešit, se obvykle označuje jako *strom závislosti*, *graf závislosti*nebo *graf objektů*.

`IMyDependency` a `ILogger<TCategoryName>` musí být registrovány v kontejneru služby. `IMyDependency` je zaregistrován v `Startup.ConfigureServices`. `ILogger<TCategoryName>` je zaregistrované v infrastruktuře abstrakce protokolování, takže se jedná o [službu poskytovanou rozhraním](#framework-provided-services) , která je zaregistrovaná ve výchozím rozhraní.

Kontejner překládá `ILogger<TCategoryName>` tím, že využívá [(Obecné) otevřené typy](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)a eliminuje nutnost registrace každého [(obecného) konstruovaného typu](/dotnet/csharp/language-reference/language-specification/types#constructed-types):

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

V ukázkové aplikaci je `IMyDependency` služba zaregistrovaná u konkrétního typu `MyDependency`. Registrace specifikuje rámec životnosti služby na životnost jednoho požadavku. [Doby platnosti služeb](#service-lifetimes) jsou popsány dále v tomto tématu.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

> [!NOTE]
> Jednotlivé metody rozšíření `services.Add{SERVICE_NAME}` přidávají (a případně nakonfigurují) služby. `services.AddMvc()` například přidá služby Razor Pages a MVC vyžaduje. Doporučujeme, aby v aplikacích byla tato konvence dodržena. Do oboru názvů [Microsoft. Extensions. DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) umístěte metody rozšíření pro zapouzdření skupin registrací služby.

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

V ukázkové aplikaci se vyžádá `IMyDependency` instance, která se používá k volání metody `WriteMessage` služby:

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

Další informace naleznete v tématu <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Služby poskytované frameworkem

Metoda `Startup.ConfigureServices` zodpovídá za definování služeb, které aplikace používá, včetně funkcí platformy, jako je například Entity Framework Core a ASP.NET Core MVC. Zpočátku `IServiceCollection` poskytnutý pro `ConfigureServices` má služby definované rozhraním v závislosti na [tom, jak byl hostitel nakonfigurovaný](xref:fundamentals/index#host). Pro aplikaci, která je založená na šabloně ASP.NET Core, není běžné, že mají stovky služeb zaregistrovaných v rámci rozhraní. V následující tabulce je uvedena malá ukázka služeb registrovaných v rozhraní.

::: moniker range=">= aspnetcore-3.0"

| Typ služby | Životnost |
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

::: moniker-end

## <a name="register-additional-services-with-extension-methods"></a>Registrace dalších služeb pomocí rozšiřujících metod

Pokud je k dispozici metoda rozšíření kolekce služeb pro registraci služby (a jejích závislých služeb, v případě potřeby), konvence používá jedinou metodu rozšíření `Add{SERVICE_NAME}` k registraci všech služeb vyžadovaných touto službou. Následující kód je příkladem přidání dalších služeb do kontejneru pomocí rozšiřujících metod [AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) a <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:

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
> Při použití oboru služby v middlewaru zahájí službu do metody `Invoke` nebo `InvokeAsync`. Nevkládejte službu prostřednictvím konstruktoru, protože se služba bude chovat se jako singleton. Další informace naleznete v tématu <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Služba singleton (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) je vytvořena při prvním vyžádání (nebo při spuštění `Startup.ConfigureServices` a instance se zadává s registrací služby). Každý další požadavek použije stejnou instanci. Pokud je v aplikaci vyžadováno chování ve stylu návrhového vzoru Singleton, doporučuje se použít kontejner služeb ke správě životnosti takového objektu. Neposkytujte vlastní implementaci návrhového vzoru Singleton a umožněte tak uživatelskému kódu spravovat životnost objektu v rámci třídy.

> [!WARNING]
> Je nebezpečné získávat vymezené služby ze singletonů. Může to způsobit nesprávný stav služby při zpracování následujících požadavků.

## <a name="service-registration-methods"></a>Metody registrace služby

Metody rozšíření registrace služby nabízejí přetížení, která jsou užitečná pro konkrétní scénáře.

| Metoda | Automaticky<br>objekt<br>odvod | Několik<br>implementace | Pass – argumenty |
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

Další informace naleznete v tématu:

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
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities> &ndash; povoluje vytvoření objektu bez registrace služby v kontejneru pro vkládání závislostí. `ActivatorUtilities` se používá s uživateli orientovanými na abstrakce, jako jsou například rutiny značek, řadiče MVC a pořadače modelů.

Konstruktory mohou přijímat argumenty, které nejsou poskytovány v rámci vkládání závislostí, ale takové argumenty musí mít přiřazené výchozí hodnoty.

Když jsou služby vyřešeny `IServiceProvider` nebo `ActivatorUtilities`, injektáže konstruktoru vyžaduje *veřejný* konstruktor.

Když jsou služby vyřešeny `ActivatorUtilities`, injektáže konstruktoru vyžaduje, aby existoval pouze jeden příslušný konstruktor. Přetížení konstruktoru je podporováno, ale může existovat pouze jedno přetížením, jehož argumenty jsou splnitelné vkládáním závislostí.

## <a name="entity-framework-contexts"></a>Kontexty Entity Frameworku

Entity Framework kontexty se obvykle přidávají do kontejneru služby pomocí [rozsahu životnosti](#service-lifetimes) , protože operace databáze webové aplikace jsou obvykle vymezeny na požadavky klienta. Výchozí doba života je vymezena v případě, že při registraci kontextu databáze není určena doba platnosti [AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) přetížení. Služby s danou životností by neměly používat databázový kontext s životností kratší, než je životnost služby.

## <a name="lifetime-and-registration-options"></a>Životnosti a možnosti registrace

Chcete-li předvést rozdíl mezi možnostmi životního cyklu a registrací, zvažte následující rozhraní, která představují úlohy jako operaci s jedinečným identifikátorem `OperationId`. V závislosti na tom, jak je životnost této služby nakonfigurována pro následující rozhraní, poskytne kontejner stejnou nebo rozdílnou instanci služby během vyžádání třídou:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

::: moniker-end

Rozhraní jsou implementována ve třídě `Operation`. Konstruktor `Operation` generuje identifikátor GUID, pokud není dodán:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

::: moniker-end

Je zaregistrována `OperationService`, která závisí na všech ostatních typech `Operation`. Pokud je požadováno `OperationService` prostřednictvím injektáže závislosti, obdrží buď novou instanci každé služby, nebo existující instanci na základě životnosti závislé služby.

* Když se v případě vyžádání z kontejneru vytvoří přechodné služby, `OperationId` `IOperationTransient` služby se liší od `OperationId` `OperationService`. `OperationService` obdrží novou instanci `IOperationTransient` třídy. Nová instance vrací jiný `OperationId`.
* Když se vytvoří vymezené služby na žádost klienta, `OperationId` služby `IOperationScoped` stejné jako u `OperationService` v rámci žádosti klienta. V rámci požadavků klientů obě služby sdílejí jinou hodnotu `OperationId`.
* Když se v rámci všech požadavků klientů a všech služeb vytvoří a použije tento počet služeb instance singleton a singleton, je `OperationId` v rámci všech žádostí o službu konstantní.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

::: moniker-end

V `Startup.ConfigureServices`je každý typ přidán do kontejneru podle jeho pojmenované životnosti:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

::: moniker-end

Služba `IOperationSingletonInstance` používá specifickou instanci se známým ID `Guid.Empty`. Je pak zřejmé, kdy je tento typ použit (jeho identifikátor GUID obsahuje samé nuly).

Ukázková aplikace demonstruje živostnosti objektů v rámci jednotlivých požadavků a mezi nimi. `IndexModel` ukázkové aplikace požaduje každý typ `IOperation` a `OperationService`. Na stránce se pak zobrazí všechny hodnoty `OperationId` třídy modelu stránky a hodnot služby prostřednictvím přiřazení vlastností:

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

`OperationService` operace:

Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

**Druhá žádost:**

Operace kontroleru:

Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

`OperationService` operace:

Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instance: 00000000-0000-0000-0000-000000000000

Pozor, který z `OperationId` hodnoty se liší v rámci požadavku a mezi požadavky:

* *Přechodné* objekty jsou vždy odlišné. Přechodný `OperationId` hodnota pro první i druhý požadavek klienta se liší pro operace `OperationService` a mezi požadavky klientů. Každé žádosti o službu a žádosti klienta se poskytne nová instance.
* *Vymezené* objekty jsou v požadavku klienta stejné, ale v rámci požadavků klientů se liší.
* Objekty *singleton* jsou stejné pro každý objekt a každý požadavek bez ohledu na to, jestli je `Operation` instance k dispozici v `Startup.ConfigureServices`.

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

::: moniker range=">= aspnetcore-3.0"

Když je aplikace spuštěná ve vývojovém prostředí a volá [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) , aby se vytvořila hostitel, použije výchozí poskytovatel služeb kontroly, aby ověřil, že:

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Když je aplikace spuštěná ve vývojovém prostředí a volá [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) , aby se vytvořila hostitel, použije výchozí poskytovatel služeb kontroly, aby ověřil, že:

::: moniker-end

* Služby s vymezenou živostností nejsou přímo nebo nepřímo rozhodovány z kořenového poskytovatele služeb.
* Služby s vymezenou živostností nejsou přímo nebo nepřímo vkládány do singletonů.

Poskytovatel kořenové služby se vytvoří, když se zavolá <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*>. Životnost kořenového poskytovatele služeb odpovídá životnosti aplikace/serveru, tedy poskytovatel vzniká se startem aplikace a je uvolněn při ukončení aplikace.

Služby s vymezenou životností jsou uvolňovány kontejnerem, který je vytvořil. Pokud jsou služby s vymezenou životností vytvořeny v kořenovém kontejneru, životnost služby je efektivně povýšen na singleton, protože je uvolněn pomocí kořenového kontejneru pouze při vypnutí serveru/aplikace. Při ověřování oborů služeb se tyto situace zachytí, když se zavolá `BuildServiceProvider`.

Další informace naleznete v tématu <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Služby požadavků

Služby, které jsou k dispozici v rámci žádosti o ASP.NET Core z `HttpContext` jsou zpřístupněny prostřednictvím kolekce [HttpContext. RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) .

Služby požadavků představují služby nakonfigurované a požadováné v rámci aplikace. Pokud objekty určují závislosti, jsou splněny typy nalezenými v `RequestServices`, nikoli `ApplicationServices`.

Aplikace by obvykle neměly používat tyto vlastnosti přímo. Místo toho získávejte požadované typy pomocí konstruktoru třídy a nechte framework vložit odpovídající závislosti. To poskytuje třídy, které jsou snáze testovány.

> [!NOTE]
> Pro přístup ke kolekci `RequestServices` preferovat požadavky závislosti jako na parametry konstruktoru.

## <a name="design-services-for-dependency-injection"></a>Návrh služeb pro vkládání závislostí

Osvědčené postupy:

* Navrhujte služby tak, aby využívaly vkládání závislostí pro získání jejich závislostí.
* Vyhněte se stavovým, statickým třídám a členům. Navrhněte aplikace, aby místo toho používaly služby s jedním prvkem, což se vyhne vytváření globálního stavu.
* Vyhněte se přímému vytváření instancí závislých tříd v rámci služeb. Přímé vytváření instancí způsobuje přímou závislost na konkrétní implementaci.
* Vytvářejte třídy aplikací malými, dobře faktoringně a snadno testované.

Pokud se zdá, že třída má příliš mnoho vložených závislostí, obvykle se jedná o znaménko, že třída má příliš mnoho zodpovědnosti a je v rozporu s [principem jediné zodpovědnosti (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility). V tom případě se pokuste refaktorovat třídu tak, že některé z jeho zodpovědností přesunete do nové třídy. Mějte na paměti, že třídy modelů stránek Razor a třídy kontrolerů MVC by měly být zaměřeny na aspekty uživatelského rozhraní. Obchodní pravidla a podrobnosti implementace přístupu k datům by měly být uchovávány ve třídách, které jsou vhodné pro tyto [samostatné obavy](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).

### <a name="disposal-of-services"></a>Uvolnění služeb

Kontejner volá <xref:System.IDisposable.Dispose*> pro typy <xref:System.IDisposable>, které vytvoří. Instance přidané do kontejneru pomocí uživatelské kódu nejsou automaticky uvolňovány.

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
* `Func<T>` podpora opožděné inicializace
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

Výrobní metoda samostatné služby, jako je například druhý argument pro [AddSingleton\<TService > (IServiceCollection, Func\<IServiceProvider, TService >)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), nemusí být bezpečná pro přístup z více vláken. Podobně jako konstruktor typu (`static`), je zaručeno, že bude volán jednou vláknem.

## <a name="recommendations"></a>Doporučení

* řešení služeb `async/await` a `Task` na základě se nepodporuje. C#nepodporuje asynchronní konstruktory; Proto je doporučeným vzorem použití asynchronních metod po synchronním překladu služby.

* Vyhněte se ukládání dat a konfigurace přímo do kontejneru služeb. Například nákupní košík uživatele by neměl být přidáván do kontejneru služeb. Konfigurace by měla používat [vzor možností](xref:fundamentals/configuration/options). Podobně se vystříhejte použití objektů "držící data", jejichž jediným účelem je poskytnutí přístupu k některému jinému objektu. Je lepší žádat o skutečnou položku přes DI.

* Vyhněte se statickému přístupu ke službám (například statickému psaní [IApplicationBuilder. ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) pro použití jinde).

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

* Nepoužívejte statický přístup k `HttpContext` (například [IHttpContextAccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Stejně jako u všech doporučení mohou nastat situace, ve kterých je možné tato doporučení ignorovat. Výjimky jsou zřídka&mdash;většinou zvláštní případy v rámci samotného rozhraní.

DI je *alternativou* ke vzorům statických nebo globálních přístupů k objektům. Je možné, že nebudete moci využít výhody DI, pokud je kombinujete se statickým přístupem k objektům.

## <a name="additional-resources"></a>Další zdroje

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Zápis čistého kódu v ASP.NET Core pomocí injektáže závislosti (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Explicitní závislosti – princip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Inverze řídicích kontejnerů a vzor injektáže závislosti (Martin Fowlera)](https://www.martinfowler.com/articles/injection.html)
* [Jak zaregistrovat službu s více rozhraními v ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)
