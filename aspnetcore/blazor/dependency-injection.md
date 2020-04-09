---
title: ASP.NET Blazor základní vkládání závislostí
author: guardrex
description: Podívejte Blazor se, jak můžou aplikace vstřikovat služby do komponent.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: 4cdde9ee8c9fd9adf00894a067d32965b180e5ec
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658072"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.NET Vkládání závislostí Core Blazor

Podle [Rainer Stropek](https://www.timecockpit.com) a [Mike Rousos](https://github.com/mjrousos)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor podporuje [vkládání závislostí (DI)](xref:fundamentals/dependency-injection). Aplikace můžou používat integrované služby jejich vložením do součástí. Aplikace mohou také definovat a zaregistrovat vlastní služby a zpřístupnit je v celé aplikaci prostřednictvím DI.

DI je technika pro přístup ke službám nakonfigurovaným v centrálním umístění. To může být užitečné v aplikacích Blazor:

* Sdílet jednu instanci třídy služby napříč mnoha součástmi, označované jako služba *singleton.*
* Odpojte součásti od konkrétních tříd služeb pomocí referenčních abstrakcí. Zvažte například `IDataAccess` rozhraní pro přístup k datům v aplikaci. Rozhraní je implementováno `DataAccess` konkrétní třídou a registrováno jako služba v kontejneru služeb aplikace. Když komponenta používá DI `IDataAccess` pro příjem implementace, komponenta není spojena s konkrétní typ. Implementace může být vyměněna, možná pro falešnou implementaci v testování částí.

## <a name="default-services"></a>Výchozí služby

Výchozí služby se automaticky přidají do kolekce služeb aplikace.

| Služba | Doba platnosti | Popis |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Singleton | Poskytuje metody pro odesílání požadavků HTTP a příjem odpovědí HTTP z prostředku identifikovaného identifikátorem URI.<br><br>Instance `HttpClient` aplikace Blazor WebAssembly používá prohlížeč pro zpracování http provozu na pozadí.<br><br>Aplikace Blazor Server ve `HttpClient` výchozím nastavení neobsahují nakonfigurovanou službu. `HttpClient` Poskytněte aplikaci Blazor Server.<br><br>Další informace naleznete v tématu <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton (Blazor WebAssembly)<br>S rozsahem (Blazor Server) | Představuje instanci runtime JavaScript, kam jsou odesílána volání JavaScriptu. Další informace naleznete v tématu <xref:blazor/call-javascript-from-dotnet>. |
| `NavigationManager` | Singleton (Blazor WebAssembly)<br>S rozsahem (Blazor Server) | Obsahuje pomocné servery pro práci s identifikátory URI a stav navigace. Další informace naleznete v tématu [URI a pomocníci stavu navigace](xref:blazor/routing#uri-and-navigation-state-helpers). |

Vlastní poskytovatel služeb automaticky neposkytuje výchozí služby uvedené v tabulce. Pokud používáte vlastního poskytovatele služeb a požadujete některou ze služeb uvedených v tabulce, přidejte požadované služby k novému poskytovateli služeb.

## <a name="add-services-to-an-app"></a>Přidání služeb do aplikace

### <a name="blazor-webassembly"></a>Blazor WebAssembly

Konfigurace služeb pro kolekci služeb `Main` aplikace v metodě *Program.cs*. V následujícím příkladu `MyDependency` je implementace `IMyDependency`registrována pro :

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

Po sestavování hostitele lze přistupovat ke službám z kořenového oboru DI před vykreslením všech součástí. To může být užitečné pro spuštění logiky inicializace před vykreslením obsahu:

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

Hostitel také poskytuje instanci centrální konfigurace pro aplikaci. V návaznosti na předchozí příklad je adresa URL meteorologické služby předána z výchozího zdroje `InitializeWeatherAsync`konfigurace (například *appsettings.json*) do :

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

Po vytvoření nové aplikace, `Startup.ConfigureServices` zkontrolujte metodu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Metoda `ConfigureServices` je předána <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, což je seznam objektů<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>popisovače služby ( ). Služby jsou přidány poskytováním popisovačů služby do kolekce služeb. Následující příklad ukazuje koncept s `IDataAccess` rozhraním a `DataAccess`jeho konkrétní implementaci :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Životnost

Služby lze konfigurovat s životností uvedenou v následující tabulce.

| Doba platnosti | Popis |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | BlazorAplikace WebAssembly aktuálně nemají koncept oborů DI. `Scoped`-registrované služby `Singleton` se chovají jako služby. Model hostování Blazor serveru však `Scoped` podporuje životnost. V Blazor aplikacích Server je registrace služby s vymezeným oborem pro *připojení*. Z tohoto důvodu použití služeb s vymezeným oborem je upřednostňováno pro služby, které by měly být vymezeny na aktuálního uživatele, i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI vytvoří *jednu instanci* služby. Všechny součásti, `Singleton` které vyžadují službu, obdrží instanci stejné služby. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Vždy, když komponenta získá `Transient` instanci služby z kontejneru služby, obdrží *novou instanci* služby. |

DI systém je založen na di systému v ASP.NET Core. Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Vyžádání služby v součásti

Po služby jsou přidány do kolekce služeb, vstřikovat služby do komponent pomocí [ \@směrnice inject](xref:mvc/views/razor#inject) Razor. `@inject`má dva parametry:

* Zadejte &ndash; Typ služby, kterou chcete vložit.
* Vlastnost &ndash; Název vlastnosti přijímající vstřikované služby aplikace. Vlastnost nevyžaduje ruční vytvoření. Kompilátor vytvoří vlastnost.

Další informace naleznete v tématu <xref:mvc/views/dependency-injection>.

Použijte `@inject` více příkazů k vložení různých služeb.

Následující příklad ukazuje, `@inject`jak používat . Implementace služby `Services.IDataAccess` je vložena do vlastnosti `DataRepository`komponenty . Všimněte si, jak `IDataAccess` kód používá pouze abstrakce:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Interně generované vlastnosti`DataRepository`( ) `InjectAttribute` používá atribut. Obvykle tento atribut není použit přímo. Pokud je pro komponenty požadována základní třída a pro základní třídu `InjectAttribute`jsou vyžadovány také vstřikované vlastnosti, ručně přidejte :

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

V komponentách odvozených ze základní `@inject` třídy není směrnice vyžadována. Základní `InjectAttribute` třída je dostatečná:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Použití DI ve službách

Komplexní služby mohou vyžadovat další služby. V předchozím příkladu `DataAccess` může `HttpClient` vyžadovat výchozí službu. `@inject`(nebo `InjectAttribute`) není k dispozici pro použití ve službách. Místo toho musí být *použitvstřižek konstruktoru.* Požadované služby jsou přidány přidáním parametrů do konstruktoru služby. Když DI vytvoří službu, rozpozná služby, které vyžaduje v konstruktoru a poskytuje je odpovídajícím způsobem.

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

Předpoklady pro vstřikování konstruktoru:

* Jeden konstruktor musí existovat, jehož argumenty mohou být splněny DI. Další parametry, na které se nevztahuje DI, jsou povoleny, pokud určují výchozí hodnoty.
* Příslušný konstruktor musí být *veřejný*.
* Jeden příslušný konstruktor musí existovat. V případě nejednoznačnosti DI vyvolá výjimku.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Třídy základních součástí nástroje pro správu oboru DI

V ASP.NET základních aplikací jsou služby s vymezeným oborem obvykle vymezeny podle aktuálního požadavku. Po dokončení požadavku jsou všechny oborové nebo přechodné služby vyřazeny systémem DI. V Blazor serverových aplikacích trvá obor požadavku po dobu trvání připojení klienta, což může mít za následek přechodné služby a služby s rozsahem, které žijí mnohem déle, než bylo očekáváno. V Blazor aplikacích WebAssembly jsou služby registrované s rozsahem životnosti považovány za singletons, takže žijí déle než oborové služby v typických ASP.NET základních aplikacích.

Přístup, který omezuje životnost Blazor služby v `OwningComponentBase` aplikacích je použití typu. `OwningComponentBase`je abstraktní typ odvozený od `ComponentBase` který vytváří obor DI odpovídající životnosti komponenty. Pomocí tohoto oboru je možné použít služby DI s rozsahem životnosta a mít je žít tak dlouho, jak součást. Při zničení součásti jsou uvolněny také služby od poskytovatele služeb s vymezenou s rozsahem komponenty. To může být užitečné pro služby, které:

* By měl být znovu použit v rámci součásti, protože přechodná životnost je nevhodná.
* By neměly být sdíleny mezi součástmi, protože životnost singleton je nevhodná.

K dispozici jsou `OwningComponentBase` dvě verze typu:

* `OwningComponentBase`je abstraktní, jednorázové podřízená vlastnost typu `ComponentBase` s chráněnou `ScopedServices` vlastností typu `IServiceProvider`. Tohoto zprostředkovatele lze vyřešit služby, které jsou vymezeny na životnost součásti.

  Služby DI vložené `@inject` do `InjectAttribute` komponenty pomocí nebo (`[Inject]`) nejsou vytvořeny v oboru komponenty. Chcete-li použít obor komponenty, musí `ScopedServices.GetRequiredService` `ScopedServices.GetService`být služby vyřešeny pomocí nebo . Všechny služby `ScopedServices` vyřešené pomocí zprostředkovatele mají své závislosti poskytované ze stejného oboru.

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

* `OwningComponentBase<T>`odvozuje `OwningComponentBase` a přidává `Service` vlastnost, která `T` vrací instanci z oboru Zprostředkovatel DI. Tento typ je pohodlný způsob, jak získat přístup `IServiceProvider` k službám s oborem bez použití instance, kdy existuje jedna primární služba, kterou aplikace vyžaduje z kontejneru DI pomocí oboru komponenty. Vlastnost `ScopedServices` je k dispozici, takže aplikace může získat služby jiných typů, v případě potřeby.

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a>Použití entity Framework DbContext z DI

Jeden běžný typ služby načíst z DI ve `DbContext` webových aplikacích je entity Framework (EF) objekty. Registrace služby `IServiceCollection.AddDbContext` EF `DbContext` pomocí přidá jako službu s oborem ve výchozím nastavení. Registrace jako služba s vymezeným oborem může vést k problémům v Blazor aplikacích, protože způsobuje, `DbContext` že instance budou dlouhodobé a sdílené v rámci aplikace. `DbContext`není bezpečný pro přístup z více vláken a nesmí být používán současně.

V závislosti na `OwningComponentBase` aplikaci může problém `DbContext` vyřešit *may* použití k omezení rozsahu a na jednu součást. Pokud `DbContext` součást nepoužívá paralelně, odvození komponenty z `OwningComponentBase` a načítání `DbContext` z `ScopedServices` je dostačující, protože zajišťuje, že:

* Samostatné součásti nesdílejí `DbContext`.
* Žije `DbContext` jen tak dlouho, jak součást závisí na tom.

Pokud jedna součást může `DbContext` používat souběžně (například pokaždé, když uživatel vybere `OwningComponentBase` tlačítko), i pomocí nezabrání problémům s souběžnými operacemi EF. V takovém případě použijte `DbContext` pro každou logickou operaci EF jiný. Použijte některý z následujících přístupů:

* Vytvořte `DbContext` přímo `DbContextOptions<TContext>` pomocí jako argument, který lze načíst z DI a je bezpečné pro přístup z více vláken.

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

* Zaregistrujte `DbContext` v kontejneru služby s přechodnou životností:
  * Při registraci kontextu `ServiceLifetime.Transient`použijte . Metoda `AddDbContext` rozšíření má dva volitelné `ServiceLifetime`parametry typu . Chcete-li použít tento `contextLifetime` přístup, `ServiceLifetime.Transient`pouze parametr musí být . `optionsLifetime`může zachovat výchozí `ServiceLifetime.Scoped`hodnotu aplikace .

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Přechodný `DbContext` může být vložen jako normální `@inject`(pomocí ) do komponent, které nebudou provádět více operací EF paralelně. Ty, které mohou provádět více operací `DbContext` EF současně můžete `IServiceProvider.GetRequiredService`požadovat samostatné objekty pro každou paralelní operaci pomocí .

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
