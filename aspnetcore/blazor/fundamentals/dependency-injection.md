---
title: BlazorVkládání závislostí ASP.NET Core
author: guardrex
description: Přečtěte si, jak můžou Blazor aplikace vkládat služby do součástí.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: c8209b9374b448562c173f9f879e75a5da5f2301
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014409"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a>BlazorVkládání závislostí ASP.NET Core

Od [Rainer Stropek](https://www.timecockpit.com) a [Jan Rousos](https://github.com/mjrousos)

Blazorpodporuje [vkládání závislostí (di)](xref:fundamentals/dependency-injection). Aplikace mohou používat vestavěné služby jejich vložením do komponent. Aplikace můžou také definovat a registrovat vlastní služby a zpřístupnit je v celé aplikaci přes DI.

DI je technika přístupu ke službám nakonfigurovaným v centrálním umístění. To může být užitečné v Blazor aplikacích:

* Sdílejte jednu instanci třídy služby napříč mnoha komponentami, která se označuje jako služba typu *singleton* .
* Oddělit komponenty od konkrétních tříd služeb pomocí abstrakcí odkazů. Představte si třeba rozhraní `IDataAccess` pro přístup k datům v aplikaci. Rozhraní je implementováno konkrétní `DataAccess` třídou a registrováno jako služba v kontejneru služby aplikace. Pokud komponenta používá DI pro příjem `IDataAccess` implementace, komponenta není spojena se konkrétním typem. Implementaci je možné prohodit, třeba pro podrobnější implementaci v testování částí.

## <a name="default-services"></a>Výchozí služby

Výchozí služby se automaticky přidají do kolekce služeb aplikace.

| Služba | Doba platnosti | Popis |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Obor | Poskytuje metody pro posílání požadavků HTTP a příjem odpovědí HTTP z prostředku identifikovaného identifikátorem URI.<br><br>Instance <xref:System.Net.Http.HttpClient> v Blazor WebAssembly aplikaci používá prohlížeč pro zpracování provozu http na pozadí.<br><br>Blazor Serverve výchozím nastavení neobsahují aplikace <xref:System.Net.Http.HttpClient> nakonfigurované jako služby. Poskytněte <xref:System.Net.Http.HttpClient> Blazor Server aplikaci.<br><br>Další informace naleznete v tématu <xref:blazor/call-web-api>. |
| <xref:Microsoft.JSInterop.IJSRuntime> | Singleton ( Blazor WebAssembly )<br>S vymezeným oborem ( Blazor Server ) | Představuje instanci modulu runtime jazyka JavaScript, kde jsou odesílána volání jazyka JavaScript. Další informace naleznete v tématu <xref:blazor/call-javascript-from-dotnet>. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | Singleton ( Blazor WebAssembly )<br>S vymezeným oborem ( Blazor Server ) | Obsahuje nápovědu pro práci s identifikátory URI a stavem navigace. Další informace najdete v tématu věnovaném [identifikátorům URI a nápovědě k informacím o stavu navigace](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers). |

Vlastní zprostředkovatel služeb automaticky neposkytuje výchozí služby uvedené v tabulce. Pokud používáte vlastního poskytovatele služeb a potřebujete některou ze služeb zobrazených v tabulce, přidejte požadované služby k novému poskytovateli služeb.

## <a name="add-services-to-an-app"></a>Přidání služeb do aplikace

### Blazor WebAssembly

Nakonfigurujte služby pro kolekci služeb aplikace v `Main` metodě `Program.cs` . V následujícím příkladu `MyDependency` je implementace zaregistrována pro `IMyDependency` :

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

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
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

Hostitel taky poskytuje centrální instanci konfigurace pro aplikaci. V předchozím příkladu je adresa URL služby počasí předána z výchozího zdroje konfigurace (například `appsettings.json` ) na `InitializeWeatherAsync` :

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

Po vytvoření nové aplikace si Projděte tuto `Startup.ConfigureServices` metodu:

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>Metoda je předána <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> , což je seznam objektů deskriptoru služby ( <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> ). Služby jsou přidány do `ConfigureServices` metody poskytováním popisovačů služby pro kolekci služeb. Následující příklad ukazuje koncept s `IDataAccess` rozhraním a jeho konkrétní implementací `DataAccess` :

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
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssemblyaplikace momentálně neobsahují koncept DI oborů. `Scoped`– registrované služby se chovají jako `Singleton` služby. Nicméně Blazor Server model hostování podporuje `Scoped` dobu života. V Blazor Server aplikacích je vymezená registrace služby vymezená na *připojení*. Z tohoto důvodu je vhodnější použití oboru služeb pro služby, které by měly být vymezeny na aktuálního uživatele, a to i v případě, že aktuální záměr je spustit na straně klienta v prohlížeči. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI vytvoří *jednu instanci* služby. Všechny součásti, které vyžadují `Singleton` službu, obdrží instanci stejné služby. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Pokaždé, když komponenta získá instanci `Transient` služby z kontejneru služby, obdrží *novou instanci* služby. |

Systém DI je založený na systému DI v ASP.NET Core. Další informace naleznete v tématu <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Vyžádání služby v součásti

Po přidání služeb do kolekce služeb tyto služby vloží do součástí pomocí direktivy [ \@ vložení](xref:mvc/views/razor#inject) Razor . [`@inject`](xref:mvc/views/razor#inject)má dva parametry:

* Typ: typ služby, která se má vložit.
* Vlastnost: název vlastnosti přijímající vloženou službu App Service. Vlastnost nevyžaduje ruční vytvoření. Kompilátor vytvoří vlastnost.

Další informace naleznete v tématu <xref:mvc/views/dependency-injection>.

[`@inject`](xref:mvc/views/razor#inject)Pro vložení různých služeb použijte více příkazů.

Následující příklad ukazuje, jak použít [`@inject`](xref:mvc/views/razor#inject) . Implementace služby `Services.IDataAccess` je vložena do vlastnosti komponenty `DataRepository` . Všimněte si, jak kód používá `IDataAccess` abstrakci:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

Interně vygenerovaná vlastnost ( `DataRepository` ) používá [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atribut. Obvykle se tento atribut nepoužívá přímo. Pokud je vyžadována základní třída pro součásti a vložené vlastnosti jsou také požadovány pro základní třídu, přidejte ručně [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atribut:

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

V součástech odvozených ze základní třídy [`@inject`](xref:mvc/views/razor#inject) není direktiva vyžadována. <xref:Microsoft.AspNetCore.Components.InjectAttribute>Základní třída je dostačující:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Použití DI v službách

Komplexní služby můžou vyžadovat další služby. V předchozím příkladu `DataAccess` může vyžadovat <xref:System.Net.Http.HttpClient> výchozí službu. [`@inject`](xref:mvc/views/razor#inject)(nebo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atribut) není k dispozici pro použití ve službách. Místo toho se musí použít *Injektáže konstruktoru* . Požadované služby jsou přidány přidáním parametrů do konstruktoru služby. Když DI vytvoří službu, rozpoznává služby, které vyžaduje v konstruktoru, a odpovídajícím způsobem je poskytne. V následujícím příkladu konstruktor obdrží <xref:System.Net.Http.HttpClient> přes di. <xref:System.Net.Http.HttpClient>je výchozí služba.

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

Předpoklady pro vložení konstruktoru:

* Je nutné, aby jeden konstruktor existoval, jehož argumenty mohou být splněny pomocí DI. Další parametry, které nejsou pokryty parametrem DI, jsou povoleny, pokud určují výchozí hodnoty.
* Příslušný konstruktor musí být `public` .
* Musí existovat jeden použitelný konstruktor. V případě nejednoznačnosti Vyvolá příkaz DI výjimku.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Základní třídy komponenty nástroje pro správu oboru DI

V aplikacích ASP.NET Core jsou oborové služby obvykle vymezeny na aktuální požadavek. Po dokončení žádosti se v systému DI odstraní všechny obory nebo přechodné služby. V Blazor Server aplikacích aplikace rozsah požadavků trvá po dobu trvání připojení klienta, což může vést k přechodným a oborovým službám, které jsou delší, než se očekávalo. V Blazor WebAssembly aplikacích se služby zaregistrované s vymezeným životním cyklem považují za jednoznačné, takže v typických aplikacích ASP.NET Core za provozu déle než vymezené služby.

> [!NOTE]
> Pokud chcete detekovat přechodné služby na jedno použití v aplikaci, přečtěte si část [rozpoznání přechodného použití](#detect-transient-disposables) v části.

Přístup, který omezuje dobu života služby v Blazor aplikacích, se používá pro daný <xref:Microsoft.AspNetCore.Components.OwningComponentBase> typ. <xref:Microsoft.AspNetCore.Components.OwningComponentBase>je abstraktní typ odvozený z <xref:Microsoft.AspNetCore.Components.ComponentBase> , který vytvoří obor typu di odpovídající životnosti komponenty. Pomocí tohoto oboru je možné používat DI Services s vymezeným životním cyklem a mít je živý, dokud bude komponenta. Po zničení součásti budou všechny služby z oboru poskytovatele služeb komponent odstraněny také. To může být užitečné pro služby, které:

* By měl být znovu použit v rámci součásti, protože přechodná životnost je nevhodná.
* By neměl být sdílen napříč komponentami, protože životnost singleton je nevhodná.

<xref:Microsoft.AspNetCore.Components.OwningComponentBase>K dispozici jsou dvě verze typu:

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase>je abstraktní podřízený objekt <xref:Microsoft.AspNetCore.Components.ComponentBase> typu s chráněnou <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> vlastností typu <xref:System.IServiceProvider> . Tento zprostředkovatel lze použít k překladu služeb, které jsou vymezeny na dobu života součásti.

  DI Services vložené do komponenty pomocí [`@inject`](xref:mvc/views/razor#inject) nebo [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) atribut nejsou vytvořeny v oboru komponenty. Chcete-li použít rozsah komponenty, musí být služby přeloženy pomocí <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> nebo <xref:System.IServiceProvider.GetService%2A> . Všechny služby vyřešené pomocí <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> poskytovatele mají své závislosti ze stejného oboru.

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

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601>je odvozen z <xref:Microsoft.AspNetCore.Components.OwningComponentBase> a přidává <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> vlastnost, která vrací instanci `T` z oboru typu di. Tento typ je pohodlný způsob přístupu k oboru služeb bez použití instance, <xref:System.IServiceProvider> Pokud existuje jedna primární služba, kterou aplikace vyžaduje z kontejneru di pomocí oboru komponenty. <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>Vlastnost je k dispozici, takže aplikace může v případě potřeby získat služby jiných typů.

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

Jeden běžný typ služby k načtení z DI ve Web Apps je Entity Framework objektů (EF) <xref:Microsoft.EntityFrameworkCore.DbContext> . Při registraci služeb EF pomocí <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> se <xref:Microsoft.EntityFrameworkCore.DbContext> ve výchozím nastavení přidá služba jako vymezená. Registrace jako vymezená služba může vést k problémům v Blazor aplikacích, protože způsobí, že se <xref:Microsoft.EntityFrameworkCore.DbContext> instance budou dlouhodobě a sdílet napříč aplikací. <xref:Microsoft.EntityFrameworkCore.DbContext>není bezpečná pro přístup z více vláken a nesmí se používat současně.

V závislosti na aplikaci <xref:Microsoft.AspNetCore.Components.OwningComponentBase> může být problém vyřešen pomocí omezení rozsahu a <xref:Microsoft.EntityFrameworkCore.DbContext> na jednu komponentu. *may* Pokud komponenta nepoužívá <xref:Microsoft.EntityFrameworkCore.DbContext> paralelně, odvozování komponenty z <xref:Microsoft.AspNetCore.Components.OwningComponentBase> a načtení <xref:Microsoft.EntityFrameworkCore.DbContext> z <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> je dostačující, protože zajišťuje:

* Samostatné součásti nesdílejí <xref:Microsoft.EntityFrameworkCore.DbContext> .
* <xref:Microsoft.EntityFrameworkCore.DbContext>Žije pouze tak dlouho, dokud je komponenta v závislosti na ní.

Může-li jedna součást současně použít <xref:Microsoft.EntityFrameworkCore.DbContext> (například pokaždé, když uživatel vybere tlačítko), a to i v případě, že se <xref:Microsoft.AspNetCore.Components.OwningComponentBase> nevyhnete problémům s souběžnými operacemi EF. V takovém případě použijte jiný <xref:Microsoft.EntityFrameworkCore.DbContext> pro každou operaci logického EF. Použijte některý z následujících přístupů:

* Vytvořte <xref:Microsoft.EntityFrameworkCore.DbContext> přímo pomocí <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> argumentu, který lze načíst z typu di a je bezpečný pro přístup z více vláken.

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

* Zaregistrujte <xref:Microsoft.EntityFrameworkCore.DbContext> v kontejneru služby s přechodným trváním:
  * Při registraci kontextu použijte <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> . <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>Metoda rozšíření používá dva nepovinné parametry typu <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> . Chcete-li použít tento přístup, `contextLifetime` musí být pouze parametr <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> . `optionsLifetime`může zachovat výchozí hodnotu <xref:Microsoft.OData.ServiceLifetime.Scoped?displayProperty=nameWithType> .

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Přechodný <xref:Microsoft.EntityFrameworkCore.DbContext> lze vložit jako normální (pomocí [`@inject`](xref:mvc/views/razor#inject) ) do komponent, které nespustí paralelní provádění více operací EF. Ty, které mohou provádět více operací EF současně, mohou požadovat samostatné <xref:Microsoft.EntityFrameworkCore.DbContext> objekty pro každou paralelní operaci pomocí <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> .

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

## <a name="detect-transient-disposables"></a>Zjišťování přechodných jednorázových

Následující příklady ukazují, jak detekovat přechodné služby na jedno použití v aplikaci, která by měla používat <xref:Microsoft.AspNetCore.Components.OwningComponentBase> . Další informace naleznete v části [základní třídy komponenty nástroje pro správu oddílu di Scope](#utility-base-component-classes-to-manage-a-di-scope) .

### Blazor WebAssembly

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

`TransientDisposable`V následujícím příkladu je zjištěno ( `Program.cs` ):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

`Program`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

`TransientDependency`V následujícím příkladu je zjištěno ( `Startup.cs` ):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a>Další zdroje

* <xref:fundamentals/dependency-injection>
* [`IDisposable`doprovodné materiály k přechodným a sdíleným instancím](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
