---
title: Vkládání závislostí ASP.NET Core Blazor
author: guardrex
description: Podívejte se, jak Blazor aplikace můžou vkládat služby do součástí.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 4cdde9ee8c9fd9adf00894a067d32965b180e5ec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78658072"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>Vkládání závislostí ASP.NET Core Blazor

Od [Rainer Stropek](https://www.timecockpit.com) a [Jan Rousos](https://github.com/mjrousos)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor podporuje [vkládání závislostí (di)](xref:fundamentals/dependency-injection). Aplikace mohou používat vestavěné služby jejich vložením do komponent. Aplikace můžou také definovat a registrovat vlastní služby a zpřístupnit je v celé aplikaci přes DI.

DI je technika přístupu ke službám nakonfigurovaným v centrálním umístění. To může být užitečné v aplikacích Blazor k těmto akcím:

* Sdílejte jednu instanci třídy služby napříč mnoha komponentami, která se označuje jako služba typu *singleton* .
* Oddělit komponenty od konkrétních tříd služeb pomocí abstrakcí odkazů. Představte si třeba rozhraní `IDataAccess` pro přístup k datům v aplikaci. Rozhraní je implementováno konkrétní `DataAccess`ou třídou a registrováno jako služba v kontejneru služby aplikace. Pokud komponenta používá DI pro příjem `IDataAccess` implementace, komponenta není spojena se konkrétním typem. Implementaci je možné prohodit, třeba pro podrobnější implementaci v testování částí.

## <a name="default-services"></a>Výchozí služby

Výchozí služby se automaticky přidají do kolekce služeb aplikace.

| Služba | Životnost | Popis |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Singleton | Poskytuje metody pro posílání požadavků HTTP a příjem odpovědí HTTP z prostředku identifikovaného identifikátorem URI.<br><br>Instance `HttpClient` v aplikaci WebAssembly v Blazor používá prohlížeč pro zpracování provozu HTTP na pozadí.<br><br>Aplikace Blazor Server ve výchozím nastavení nezahrnují `HttpClient` nakonfigurovanou jako službu. Poskytněte `HttpClient` aplikaci Blazor serveru.<br><br>Další informace naleznete v tématu <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton (Blazor WebAssembly)<br>Vymezený obor (Blazor Server) | Představuje instanci modulu runtime jazyka JavaScript, kde jsou odesílána volání jazyka JavaScript. Další informace naleznete v tématu <xref:blazor/call-javascript-from-dotnet>. |
| `NavigationManager` | Singleton (Blazor WebAssembly)<br>Vymezený obor (Blazor Server) | Obsahuje nápovědu pro práci s identifikátory URI a stavem navigace. Další informace najdete v tématu věnovaném [identifikátorům URI a nápovědě k informacím o stavu navigace](xref:blazor/routing#uri-and-navigation-state-helpers). |

Vlastní zprostředkovatel služeb automaticky neposkytuje výchozí služby uvedené v tabulce. Pokud používáte vlastního poskytovatele služeb a potřebujete některou ze služeb zobrazených v tabulce, přidejte požadované služby k novému poskytovateli služeb.

## <a name="add-services-to-an-app"></a>Přidání služeb do aplikace

### <a name="blazor-webassembly"></a>Blazor WebAssembly

Nakonfigurujte služby pro kolekci služeb aplikace v metodě `Main` *program.cs*. V následujícím příkladu je `MyDependency` implementace registrována pro `IMyDependency`:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

Po sestavení hostitele je možné služby získávat z kořenového oboru DI před vykreslením všech komponent. To může být užitečné pro spuštění logiky inicializace před vykreslením obsahu:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

Hostitel taky poskytuje centrální instanci konfigurace pro aplikaci. V předchozím příkladu je adresa URL služby počasí předána z výchozího zdroje konfigurace (například *appSettings. JSON*) do `InitializeWeatherAsync`:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### <a name="blazor-server"></a>Blazor Server

Po vytvoření nové aplikace Projděte metodu `Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Metodě `ConfigureServices` se předává <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, což je seznam objektů deskriptoru služby (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>). Služby se přidávají tím, že se do kolekce služeb poskytují popisovače služby. Následující příklad ukazuje koncept s rozhraním `IDataAccess` a jeho konkrétní implementací `DataAccess`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Doba života služby

Služby je možné konfigurovat s životností, která jsou uvedená v následující tabulce.

| Životnost | Popis |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | aplikace Blazor WebAssembly aktuálně nemají koncept typu DI scopes. služby registrované `Scoped`se chovají jako služby `Singleton`. Model hostování Blazor serveru však podporuje `Scoped` životního cyklu. V Blazorch serverových aplikacích je vymezená registrace služby vymezená na *připojení*. Z tohoto důvodu je vhodnější použití oboru služeb pro služby, které by měly být vymezeny na aktuálního uživatele, a to i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI vytvoří *jednu instanci* služby. Všechny součásti, které vyžadují službu `Singleton`, obdrží instanci stejné služby. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Pokaždé, když komponenta získá instanci `Transient` služby z kontejneru služby, obdrží *novou instanci* služby. |

Systém DI je založený na systému DI v ASP.NET Core. Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Vyžádání služby v součásti

Po přidání služeb do kolekce služeb tyto služby vloží do součástí pomocí direktivy [\@vkládání](xref:mvc/views/razor#inject) Razor. `@inject` má dva parametry:

* Zadejte &ndash; typ služby, kterou chcete vložit.
* Vlastnost &ndash; název vlastnosti, která přijímá vloženou službu App Service. Vlastnost nevyžaduje ruční vytvoření. Kompilátor vytvoří vlastnost.

Další informace naleznete v tématu <xref:mvc/views/dependency-injection>.

Pro vložení různých služeb použijte více příkazů `@inject`.

Následující příklad ukazuje, jak použít `@inject`. Služba implementující `Services.IDataAccess` je vložená do `DataRepository`vlastností komponenty. Všimněte si, jak kód používá `IDataAccess` abstrakce:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Interně, generovaná vlastnost (`DataRepository`) používá atribut `InjectAttribute`. Obvykle se tento atribut nepoužívá přímo. Pokud je vyžadována základní třída pro součásti a vložené vlastnosti jsou také požadovány pro základní třídu, přidejte `InjectAttribute`ručně:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

V součástech odvozených ze základní třídy není `@inject` direktiva vyžadována. `InjectAttribute` základní třídy jsou dostatečné:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Použití DI v službách

Komplexní služby můžou vyžadovat další služby. V předchozím příkladu `DataAccess` může vyžadovat `HttpClient` výchozí službu. `@inject` (nebo `InjectAttribute`) nejsou k dispozici pro použití v rámci služeb. Místo toho se musí použít *Injektáže konstruktoru* . Požadované služby jsou přidány přidáním parametrů do konstruktoru služby. Když DI vytvoří službu, rozpoznává služby, které vyžaduje v konstruktoru, a odpovídajícím způsobem je poskytne.

```csharp
public class DataAccess : IDataAccess
{
    // The constructor receives an HttpClient via dependency
    // injection. HttpClient is a default service.
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

Předpoklady pro vložení konstruktoru:

* Je nutné, aby jeden konstruktor existoval, jehož argumenty mohou být splněny pomocí DI. Další parametry, které nejsou pokryty parametrem DI, jsou povoleny, pokud určují výchozí hodnoty.
* Příslušný konstruktor musí být *veřejný*.
* Musí existovat jeden použitelný konstruktor. V případě nejednoznačnosti Vyvolá příkaz DI výjimku.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Základní třídy komponenty nástroje pro správu oboru DI

V aplikacích ASP.NET Core jsou oborové služby obvykle vymezeny na aktuální požadavek. Po dokončení žádosti se v systému DI odstraní všechny obory nebo přechodné služby. V Blazorch serverových aplikací je rozsah požadavků po dobu trvání připojení klienta, což může vést k přechodným a oborovým službám, které jsou mnohem delší, než se očekávalo. V Blazorch aplikacích WebAssembly se služby zaregistrované s vymezeným životním cyklem považují za singleton, takže v typických ASP.NET Corech aplikacích budou živě fungovat déle než vymezené služby.

Přístup, který omezuje dobu života služby v aplikacích Blazor, používá typ `OwningComponentBase`. `OwningComponentBase` je abstraktní typ odvozený z `ComponentBase`, který vytváří obor DI odpovídající životnosti komponenty. Pomocí tohoto oboru je možné používat DI Services s vymezeným životním cyklem a mít je živý, dokud bude komponenta. Po zničení součásti budou všechny služby z oboru poskytovatele služeb komponent odstraněny také. To může být užitečné pro služby, které:

* By měl být znovu použit v rámci součásti, protože přechodná životnost je nevhodná.
* By neměl být sdílen napříč komponentami, protože životnost singleton je nevhodná.

K dispozici jsou dvě verze `OwningComponentBase` typu:

* `OwningComponentBase` je abstraktní podřízený typ `ComponentBase` typu s chráněnou `ScopedServices`ou vlastností typu `IServiceProvider`. Tento zprostředkovatel lze použít k překladu služeb, které jsou vymezeny na dobu života součásti.

  DI Services vložené do komponenty pomocí `@inject` nebo `InjectAttribute` (`[Inject]`) nejsou vytvořeny v oboru součásti. Chcete-li použít rozsah komponenty, je nutné služby přeložit pomocí `ScopedServices.GetRequiredService` nebo `ScopedServices.GetService`. Všechny služby vyřešené pomocí poskytovatele `ScopedServices` mají své závislosti ze stejného oboru.

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* `OwningComponentBase<T>` je odvozen z `OwningComponentBase` a přidá vlastnost `Service`, která vrací instanci `T` z oboru DI Provider. Tento typ je pohodlným způsobem, jak přistupovat k vymezeným službám bez použití instance `IServiceProvider`, pokud existuje jedna primární služba, kterou aplikace vyžaduje z kontejneru DI pomocí oboru komponenty. Vlastnost `ScopedServices` je k dispozici, takže aplikace může v případě potřeby získat služby jiných typů.

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-entity-framework-dbcontext-from-di"></a>Použití Entity Framework DbContext z DI

Jeden běžný typ služby k načtení z DI ve Web Apps je Entity Framework (EF) `DbContext` objektů. Registrace služeb EF pomocí `IServiceCollection.AddDbContext` ve výchozím nastavení přidá `DbContext` jako vymezenou službu. Registrace jako vymezená služba může vést k problémům v Blazorch aplikacích, protože způsobí, že `DbContext` instance budou dlouhodobé a sdílené v rámci aplikace. `DbContext` není bezpečná pro přístup z více vláken a nesmí se používat současně.

V závislosti na aplikaci *může* tento problém vyřešit použití `OwningComponentBase` k omezení rozsahu `DbContext` na jednu komponentu. Pokud komponenta nepoužívá `DbContext` paralelně, je odvozena komponenta z `OwningComponentBase` a načítání `DbContext` z `ScopedServices` je dostačující, protože zajišťuje:

* Samostatné součásti nesdílejí `DbContext`.
* `DbContext` žije pouze tak dlouho, jak je komponenta závislá.

Pokud jedna součást může použít `DbContext` souběžně (například pokaždé, když uživatel vybere tlačítko), i když pomocí `OwningComponentBase` se vyhnete problémům s souběžnými operacemi EF. V takovém případě použijte pro každou logickou operaci EF jiný `DbContext`. Použijte některý z následujících přístupů:

* Vytvořte `DbContext` přímo pomocí `DbContextOptions<TContext>` jako argument, který lze načíst z typu DI a je bezpečný pro přístup z více vláken.

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = new AppDbContext(DbContextOptions))
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

* Zaregistrujte `DbContext` v kontejneru služby s přechodným trváním:
  * Při registraci kontextu použijte `ServiceLifetime.Transient`. Metoda rozšíření `AddDbContext` přebírá dva volitelné parametry typu `ServiceLifetime`. Chcete-li použít tento přístup, je nutné `ServiceLifetime.Transient`pouze parametr `contextLifetime`. `optionsLifetime` může zachovat výchozí hodnotu `ServiceLifetime.Scoped`.

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Přechodný `DbContext` lze vložit jako normální (pomocí `@inject`) do komponent, které nespustí paralelní provádění více operací EF. Ty, které mohou provádět více operací EF současně, mohou požadovat samostatné `DbContext` objekty pro každou paralelní operaci pomocí `IServiceProvider.GetRequiredService`.

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in _data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> _data = new List<string>();

        private async Task LoadData()
        {
            _data = await GetAsync();
            StateHasChanged();
        }

        public async Task<List<string>> GetAsync()
        {
            using (var context = ServiceProvider.GetRequiredService<AppDbContext>())
            {
                return await context.Products.Select(p => p.Name).ToListAsync();
            }
        }
    }
    ```

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
