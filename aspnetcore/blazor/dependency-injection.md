---
title: Vkládání Blazor závislostí ASP.NET Core
author: guardrex
description: Podívejte se Blazor , jak můžou aplikace vkládat služby do součástí.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: e96698bd0bd8f3f3b290ba24bc8169efb16f1d03
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967529"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>Vkládání závislostí ASP.NET Core Blazor

Od [Rainer Stropek](https://www.timecockpit.com) a [Jan Rousos](https://github.com/mjrousos)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor podporuje [vkládání závislostí (di)](xref:fundamentals/dependency-injection). Aplikace mohou používat vestavěné služby jejich vložením do komponent. Aplikace můžou také definovat a registrovat vlastní služby a zpřístupnit je v celé aplikaci přes DI.

DI je technika přístupu ke službám nakonfigurovaným v centrálním umístění. To může být užitečné v aplikacích Blazor k těmto akcím:

* Sdílejte jednu instanci třídy služby napříč mnoha komponentami, která se označuje jako služba typu *singleton* .
* Oddělit komponenty od konkrétních tříd služeb pomocí abstrakcí odkazů. Představte si třeba rozhraní `IDataAccess` pro přístup k datům v aplikaci. Rozhraní je implementováno konkrétní `DataAccess` třídou a registrováno jako služba v kontejneru služby aplikace. Pokud komponenta používá DI pro příjem `IDataAccess` implementace, komponenta není spojena se konkrétním typem. Implementaci je možné prohodit, třeba pro podrobnější implementaci v testování částí.

## <a name="default-services"></a>Výchozí služby

Výchozí služby se automaticky přidají do kolekce služeb aplikace.

| Služba | Doba platnosti | Popis |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Dočasný | Poskytuje metody pro posílání požadavků HTTP a příjem odpovědí HTTP z prostředku identifikovaného identifikátorem URI.<br><br>Instance `HttpClient` v aplikaci WebAssembly v Blazor používá prohlížeč pro zpracování provozu http na pozadí.<br><br>Aplikace Blazor Server ve výchozím nastavení `HttpClient` nezahrnují službu nakonfigurovanou jako službu. `HttpClient` Poskytněte aplikaci Blazor Server.<br><br>Další informace naleznete v tématu <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton (Blazor WebAssembly)<br>Vymezený obor (Blazor Server) | Představuje instanci modulu runtime jazyka JavaScript, kde jsou odesílána volání jazyka JavaScript. Další informace naleznete v tématu <xref:blazor/call-javascript-from-dotnet>. |
| `NavigationManager` | Singleton (Blazor WebAssembly)<br>Vymezený obor (Blazor Server) | Obsahuje nápovědu pro práci s identifikátory URI a stavem navigace. Další informace najdete v tématu věnovaném [identifikátorům URI a nápovědě k informacím o stavu navigace](xref:blazor/routing#uri-and-navigation-state-helpers). |

Vlastní zprostředkovatel služeb automaticky neposkytuje výchozí služby uvedené v tabulce. Pokud používáte vlastního poskytovatele služeb a potřebujete některou ze služeb zobrazených v tabulce, přidejte požadované služby k novému poskytovateli služeb.

## <a name="add-services-to-an-app"></a>Přidání služeb do aplikace

### <a name="blazor-webassembly"></a>Blazor WebAssembly

Nakonfigurujte služby pro kolekci služeb aplikace v `Main` metodě *program.cs*. V následujícím příkladu je `MyDependency` implementace zaregistrována pro: `IMyDependency`

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

Hostitel taky poskytuje centrální instanci konfigurace pro aplikaci. V předchozím příkladu je adresa URL služby počasí předána z výchozího zdroje konfigurace (například *appSettings. JSON*) `InitializeWeatherAsync`:

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

Po vytvoření nové aplikace si Projděte tuto `Startup.ConfigureServices` metodu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

`ConfigureServices` Metoda je předána <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, což je seznam objektů deskriptoru služby (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>). Služby se přidávají tím, že se do kolekce služeb poskytují popisovače služby. Následující příklad ukazuje koncept s `IDataAccess` rozhraním a jeho konkrétní implementací: `DataAccess`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Doba života služby

Služby je možné konfigurovat s životností, která jsou uvedená v následující tabulce.

| Doba platnosti | Popis |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | BlazorAplikace pro WebAssembly aktuálně nemají koncept typu DI obory. `Scoped`– registrované služby se chovají jako `Singleton` služby. Model hostování Blazor serveru však podporuje `Scoped` dobu života. V Blazor rámci serverových aplikací je vymezená registrace služby vymezená na *připojení*. Z tohoto důvodu je vhodnější použití oboru služeb pro služby, které by měly být vymezeny na aktuálního uživatele, a to i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI vytvoří *jednu instanci* služby. Všechny součásti, které `Singleton` vyžadují službu, obdrží instanci stejné služby. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Pokaždé, když komponenta získá instanci `Transient` služby z kontejneru služby, obdrží *novou instanci* služby. |

Systém DI je založený na systému DI v ASP.NET Core. Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Vyžádání služby v součásti

Po přidání služeb do kolekce služeb tyto služby vloží do součástí pomocí direktivy [ \@vložení](xref:mvc/views/razor#inject) Razor . `@inject`má dva parametry:

* Zadejte &ndash; typ služby, kterou chcete vložit.
* Vlastnost &ndash; název vlastnosti, která přijímá vloženou službu App Service. Vlastnost nevyžaduje ruční vytvoření. Kompilátor vytvoří vlastnost.

Další informace naleznete v tématu <xref:mvc/views/dependency-injection>.

Pro vložení `@inject` různých služeb použijte více příkazů.

Následující příklad ukazuje, jak použít `@inject`. Implementace `Services.IDataAccess` služby je vložena do vlastnosti `DataRepository`komponenty. Všimněte si, jak kód používá `IDataAccess` abstrakci:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Interně vygenerovaná vlastnost (`DataRepository`) používá `InjectAttribute` atribut. Obvykle se tento atribut nepoužívá přímo. Pokud je vyžadována základní třída pro součásti a vložené vlastnosti jsou také požadovány pro základní třídu, přidejte ručně `InjectAttribute`:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

V součástech odvozených ze základní třídy není `@inject` direktiva vyžadována. `InjectAttribute` Základní třída je dostačující:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Použití DI v službách

Komplexní služby můžou vyžadovat další služby. V předchozím příkladu `DataAccess` může vyžadovat `HttpClient` výchozí službu. `@inject`(nebo) `InjectAttribute`nejsou k dispozici pro použití v rámci služeb. Místo toho se musí použít *Injektáže konstruktoru* . Požadované služby jsou přidány přidáním parametrů do konstruktoru služby. Když DI vytvoří službu, rozpoznává služby, které vyžaduje v konstruktoru, a odpovídajícím způsobem je poskytne.

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

V aplikacích ASP.NET Core jsou oborové služby obvykle vymezeny na aktuální požadavek. Po dokončení žádosti se v systému DI odstraní všechny obory nebo přechodné služby. V Blazor rámci serverových aplikací je rozsah požadavků po dobu trvání připojení klienta, což může vést k přechodným a oborovým službám, které jsou delší, než se očekávalo. V Blazor aplikacích pro WebAssembly se služby zaregistrované s vymezeným životním cyklem považují za singleton, takže v typických aplikacích ASP.NET Core za provozu déle než vymezené služby.

Přístup, který omezuje dobu života služby v Blazor aplikacích, se používá pro `OwningComponentBase` daný typ. `OwningComponentBase`je abstraktní typ odvozený z `ComponentBase` , který vytvoří obor typu di odpovídající životnosti komponenty. Pomocí tohoto oboru je možné používat DI Services s vymezeným životním cyklem a mít je živý, dokud bude komponenta. Po zničení součásti budou všechny služby z oboru poskytovatele služeb komponent odstraněny také. To může být užitečné pro služby, které:

* By měl být znovu použit v rámci součásti, protože přechodná životnost je nevhodná.
* By neměl být sdílen napříč komponentami, protože životnost singleton je nevhodná.

K dispozici jsou `OwningComponentBase` dvě verze typu:

* `OwningComponentBase`je abstraktní podřízený objekt `ComponentBase` typu s chráněnou `ScopedServices` vlastností typu. `IServiceProvider` Tento zprostředkovatel lze použít k překladu služeb, které jsou vymezeny na dobu života součásti.

  DI Services vložené do komponenty pomocí `@inject` nebo `InjectAttribute` (`[Inject]`) nejsou vytvořeny v oboru součásti. Chcete-li použít rozsah komponenty, musí být služby přeloženy `ScopedServices.GetRequiredService` pomocí `ScopedServices.GetService`nebo. Všechny služby vyřešené pomocí `ScopedServices` poskytovatele mají své závislosti ze stejného oboru.

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

* `OwningComponentBase<T>`je odvozen z `OwningComponentBase` a přidává vlastnost `Service` , která vrací instanci z oboru `T` typu di. Tento typ je pohodlný způsob přístupu k oboru služeb bez použití instance, `IServiceProvider` Pokud existuje jedna primární služba, kterou aplikace vyžaduje z kontejneru di pomocí oboru komponenty. `ScopedServices` Vlastnost je k dispozici, takže aplikace může v případě potřeby získat služby jiných typů.

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

Jeden běžný typ služby k načtení z DI ve Web Apps je Entity Framework objektů (EF `DbContext` ). Při registraci služeb EF `IServiceCollection.AddDbContext` pomocí se `DbContext` ve výchozím nastavení přidá služba jako vymezená. Registrace jako vymezená služba může vést k problémům v Blazor aplikacích, protože způsobí `DbContext` , že se instance budou dlouhodobě a sdílet napříč aplikací. `DbContext`není bezpečná pro přístup z více vláken a nesmí se používat současně.

V závislosti na aplikaci *může* být problém `OwningComponentBase` vyřešen pomocí omezení rozsahu a `DbContext` na jednu komponentu. Pokud `DbContext` komponenta nepoužívá paralelně, odvozování komponenty `OwningComponentBase` z a načtení `DbContext` z `ScopedServices` je dostačující, protože zajišťuje:

* Samostatné součásti nesdílejí `DbContext`.
* `DbContext` Žije pouze tak dlouho, dokud je komponenta v závislosti na ní.

Může-li jedna součást současně použít `DbContext` (například pokaždé, když uživatel vybere tlačítko), a to i v `OwningComponentBase` případě, že se nevyhnete problémům s souběžnými operacemi EF. V takovém případě použijte jiný `DbContext` pro každou operaci logického EF. Použijte některý z následujících přístupů:

* Vytvořte `DbContext` přímo pomocí `DbContextOptions<TContext>` argumentu, který lze načíst z typu di a je bezpečný pro přístup z více vláken.

    ```razor
    @page "/example"
    @inject DbContextOptions<AppDbContext> DbContextOptions

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
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
  * Při registraci kontextu použijte `ServiceLifetime.Transient`. Metoda `AddDbContext` rozšíření používá dva nepovinné parametry typu `ServiceLifetime`. Chcete-li použít tento přístup, `contextLifetime` musí být `ServiceLifetime.Transient`pouze parametr. `optionsLifetime`může zachovat výchozí hodnotu `ServiceLifetime.Scoped`.

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Přechodný `DbContext` lze vložit jako normální (pomocí `@inject`) do komponent, které nespustí paralelní provádění více operací EF. Ty, které mohou provádět více operací EF současně, mohou `DbContext` požadovat samostatné objekty pro každou paralelní `IServiceProvider.GetRequiredService`operaci pomocí.

    ```razor
    @page "/example"
    @using Microsoft.Extensions.DependencyInjection
    @inject IServiceProvider ServiceProvider

    <ul>
        @foreach (var item in data)
        {
            <li>@item</li>
        }
    </ul>

    <button @onclick="LoadData">Load Data</button>

    @code {
        private List<string> data = new List<string>();

        private async Task LoadData()
        {
            data = await GetAsync();
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

## <a name="additional-resources"></a>Další materiály a zdroje informací

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
