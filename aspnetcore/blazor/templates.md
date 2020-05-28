---
title: ' ASP.NET Core Blazor templates ' Author: Description: ' informace o ASP.NET Core Blazor šablon aplikací a Blazor struktuře projektu. '
monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- SignalRUID: 

---
# <a name="aspnet-core-blazor-templates"></a>BlazorŠablony ASP.NET Core

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

BlazorRozhraní poskytuje šablony pro vývoj aplikací pro každý Blazor model hostování:

* BlazorWebAssembly ( `blazorwasm` )
* BlazorServer ( `blazorserver` )

Další informace o Blazor modelech hostování naleznete v tématu <xref:blazor/hosting-models> .

Podrobné pokyny k vytvoření Blazor aplikace ze šablony naleznete v tématu <xref:blazor/get-started> .

Možnosti šablony jsou k dispozici předáním `--help` Možnosti příkazového řádku [dotnet New](/dotnet/core/tools/dotnet-new) CLI:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="blazor-project-structure"></a>Blazorstruktura projektu

Následující soubory a složky tvoří Blazor aplikaci vygenerovanou ze Blazor šablony:

* *Program.cs*: vstupní bod aplikace, který nastavuje:

  * [Hostitel](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server)
  * Hostitel WebAssembly ( Blazor WebAssembly): kód v tomto souboru je jedinečný pro aplikace vytvořené ze Blazor šablony WebAssembly ( `blazorwasm` ).
    * `App`Komponenta, která je kořenovou komponentou aplikace, je zadána jako `app` prvek modelu DOM pro `Add` metodu.
    * Služby lze konfigurovat pomocí `ConfigureServices` metody v Tvůrci hostitele (například `builder.Services.AddSingleton<IMyDependency, MyDependency>();` ).
    * Konfiguraci lze dodávat prostřednictvím tvůrce hostitele ( `builder.Configuration` ).

* *Startup.cs* ( Blazor Server): obsahuje logiku spouštění aplikace. `Startup`Třída definuje dvě metody:

  * `ConfigureServices`: Konfiguruje služby pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace. V Blazor serverových aplikacích se služby přidávají voláním <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> a `WeatherForecastService` přidá se do kontejneru služby pro použití v ukázkové `FetchData` součásti.
  * `Configure`: Konfiguruje kanál pro zpracování žádostí aplikace:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A>je volána k nastavení koncového bodu pro připojení v reálném čase pomocí prohlížeče. Připojení se vytvoří pomocí [SignalR](xref:signalr/introduction) , což je rozhraní, které umožňuje přidávat do aplikací webové funkce v reálném čase.
    * [MapFallbackToPage ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) se zavolá, aby se nastavila Kořenová stránka aplikace (*pages/_Host. cshtml*) a povolila se navigace.

* *wwwroot/index.html* ( Blazor WebAssembly): Kořenová stránka aplikace IMPLEMENTOVANÉ jako stránka HTML:
  * Po prvním vyžádání stránky aplikace se tato stránka vykreslí a vrátí v odpovědi.
  * Stránka určuje, kde `App` je vygenerována kořenová komponenta. `App`Součást (*App. Razor*) je zadána jako `app` prvek modelu DOM k `AddComponent` metodě v `Startup.Configure` .
  * `_framework/blazor.webassembly.js`Načte se soubor JavaScriptu, který:
    * Stáhne rozhraní .NET runtime, aplikaci a závislosti aplikace.
    * Inicializuje modul runtime pro spuštění aplikace.

* *App. Razor*: kořenová komponenta aplikace, která nastaví směrování na straně klienta pomocí <xref:Microsoft.AspNetCore.Components.Routing.Router> komponenty. <xref:Microsoft.AspNetCore.Components.Routing.Router>Komponenta zachycuje navigaci v prohlížeči a vykreslí stránku, která odpovídá požadované adrese.

* Složka *stránky* : obsahuje směrovatelný komponenty/stránky (*. Razor*), které tvoří Blazor aplikaci a kořenovou Razor stránku Blazor serverové aplikace. Trasa pro každou stránku je určena pomocí [`@page`](xref:mvc/views/razor#page) direktivy. Šablona obsahuje následující:
  * *_Host. cshtml* ( Blazor Server): Kořenová stránka aplikace byla implementována jako Razor stránka:
    * Po prvním vyžádání stránky aplikace se tato stránka vykreslí a vrátí v odpovědi.
    * `_framework/blazor.server.js`Načte se soubor JavaScriptu, který nastaví připojení v reálném čase SignalR mezi prohlížečem a serverem.
    * Stránka hostitel Určuje, kde `App` je vygenerována kořenová součást (*App. Razor*).
  * `Counter`(*Counter. Razor*): implementuje stránku čítače.
  * `Error`(*Error. Razor*, Blazor Jenom serverová aplikace): vykreslí se, když v aplikaci dojde k neošetřené výjimce.
  * `FetchData`(*FetchData. Razor*): implementuje stránku načíst data.
  * `Index`(*Index. Razor*): implementuje domovskou stránku.

* *Sdílená* složka: obsahuje další součásti uživatelského rozhraní (*. Razor*) používané aplikací:
  * `MainLayout`(*MainLayout. Razor*): součást rozložení aplikace
  * `NavMenu`(*NavMenu. Razor*): implementuje navigaci bočním panelem. Zahrnuje [komponentu NavLink](xref:blazor/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), která vykresluje navigační odkazy na jiné Razor součásti. <xref:Microsoft.AspNetCore.Components.Routing.NavLink>Komponenta automaticky indikuje vybraný stav při načtení jeho komponenty, což pomáhá uživateli pochopit, která součást se aktuálně zobrazuje.

* *_Imports. Razor*: obsahuje společné Razor direktivy pro zahrnutí do komponent aplikace (*. Razor*), jako jsou například [`@using`](xref:mvc/views/razor#using) direktivy pro obory názvů.

* Složka *dat* ( Blazor Server): obsahuje `WeatherForecast` třídu a implementaci rozhraní `WeatherForecastService` , které poskytuje ukázková data o počasí součásti aplikace `FetchData` .

* *wwwroot*: [Webová kořenová](xref:fundamentals/index#web-root) složka pro aplikaci, která obsahuje veřejné statické prostředky aplikace.

* *appSettings. JSON* ( Blazor Server): nastavení konfigurace pro aplikaci.
