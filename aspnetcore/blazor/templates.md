---
title: BlazorŠablony ASP.NET Core
author: guardrex
description: Přečtěte si o ASP.NET Core Blazor šablonách aplikací a Blazor struktuře projektu.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/templates
ms.openlocfilehash: 0069a6d25c1a8edcfaa454623a95702aae58ca22
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625892"
---
# <a name="aspnet-core-no-locblazor-templates"></a>BlazorŠablony ASP.NET Core

Od [Daniel Skořepa](https://github.com/danroth27) a [Luke Latham](https://github.com/guardrex)

BlazorRozhraní poskytuje šablony pro vývoj aplikací pro každý Blazor model hostování:

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Další informace o Blazor modelech hostování naleznete v tématu <xref:blazor/hosting-models> .

Možnosti šablony jsou k dispozici předáním `--help` možnosti [`dotnet new`](/dotnet/core/tools/dotnet-new) příkazu CLI:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a>Blazor struktura projektu

Následující soubory a složky tvoří Blazor aplikaci vygenerovanou ze Blazor šablony projektu:

* `Program.cs`: Vstupní bod aplikace, který nastavuje:

  * [Hostitel](xref:fundamentals/host/generic-host) ASP.NET Core ( Blazor Server )
  * Hostitel WebAssembly ( Blazor WebAssembly ): kód v tomto souboru je jedinečný pro aplikace vytvořené ze Blazor WebAssembly šablony ( `blazorwasm` ).
    * `App`Součást je kořenová komponenta aplikace. `App`Komponenta je určena jako `app` element modelu DOM ( `<app>...</app>` ) pro kolekci kořenových komponent ( `builder.RootComponents.Add<App>("app")` ).
    * [Služby](xref:blazor/fundamentals/dependency-injection) jsou přidány a nakonfigurovány (například `builder.Services.AddSingleton<IMyDependency, MyDependency>()` ).

* `Startup.cs` ( Blazor Server ): Obsahuje logiku spuštění aplikace. `Startup`Třída definuje dvě metody:

  * `ConfigureServices`: Konfiguruje služby pro [vkládání závislostí (di)](xref:fundamentals/dependency-injection) aplikace. V Blazor Server aplikacích se služby přidávají voláním <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> a přidá se `WeatherForecastService` do kontejneru služby pro použití v ukázkové `FetchData` součásti.
  * `Configure`: Konfiguruje kanál pro zpracování žádostí aplikace:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> je volána k nastavení koncového bodu pro připojení v reálném čase pomocí prohlížeče. Připojení se vytvoří pomocí [SignalR](xref:signalr/introduction) , což je rozhraní, které umožňuje přidávat do aplikací webové funkce v reálném čase.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) se volá, aby se nastavila Kořenová stránka aplikace ( `Pages/_Host.cshtml` ) a povolila se navigace.

* `wwwroot/index.html` ( Blazor WebAssembly ): Kořenová stránka aplikace implementovaná jako stránka HTML:
  * Po prvním vyžádání stránky aplikace se tato stránka vykreslí a vrátí v odpovědi.
  * Stránka určuje, kde `App` je vygenerována kořenová komponenta. Komponenta je vykreslena v umístění `app` elementu modelu DOM ( `<app>...</app>` ).
  * `_framework/blazor.webassembly.js`Načte se soubor JavaScriptu, který:
    * Stáhne rozhraní .NET runtime, aplikaci a závislosti aplikace.
    * Inicializuje modul runtime pro spuštění aplikace.

* `App.razor`: Kořenová komponenta aplikace, která nastaví směrování na straně klienta pomocí <xref:Microsoft.AspNetCore.Components.Routing.Router> komponenty. <xref:Microsoft.AspNetCore.Components.Routing.Router>Komponenta zachycuje navigaci v prohlížeči a vykreslí stránku, která odpovídá požadované adrese.

* `Pages` Složka: obsahuje směrovatelný komponenty/stránky ( `.razor` ), které tvoří Blazor aplikaci a kořenovou Razor stránku Blazor Server aplikace. Trasa pro každou stránku je určena pomocí [`@page`](xref:mvc/views/razor#page) direktivy. Šablona obsahuje následující:
  * `_Host.cshtml` ( Blazor Server ): Kořenová stránka aplikace byla implementována jako Razor Page
    * Po prvním vyžádání stránky aplikace se tato stránka vykreslí a vrátí v odpovědi.
    * `_framework/blazor.server.js`Načte se soubor JavaScriptu, který nastaví připojení v reálném čase SignalR mezi prohlížečem a serverem.
    * Stránka hostitel Určuje, kde `App` `App.razor` je vygenerována kořenová součást ().
  * `Counter` ( `Pages/Counter.razor` ): Implementuje stránku čítače.
  * `Error` ( `Error.razor` , Blazor Server jenom aplikace): vykreslí se, když v aplikaci dojde k neošetřené výjimce.
  * `FetchData` ( `Pages/FetchData.razor` ): Implementuje stránku načíst data.
  * `Index` ( `Pages/Index.razor` ): Implementuje domovskou stránku.
  
* `Properties/launchSettings.json`: Obsahuje [konfiguraci vývojového prostředí](xref:fundamentals/environments#development-and-launchsettingsjson).

* `Shared` Složka: obsahuje další součásti uživatelského rozhraní ( `.razor` ), které používá aplikace:
  * `MainLayout` ( `MainLayout.razor` ): [Součást rozložení](xref:blazor/layouts)aplikace
  * `NavMenu` ( `NavMenu.razor` ): Implementuje navigaci bočním panelem. Zahrnuje [ `NavLink` komponentu](xref:blazor/fundamentals/routing#navlink-component) ( <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ), která vykresluje navigační odkazy na jiné Razor součásti. <xref:Microsoft.AspNetCore.Components.Routing.NavLink>Komponenta automaticky indikuje vybraný stav při načtení jeho komponenty, což pomáhá uživateli pochopit, která součást se aktuálně zobrazuje.

* `_Imports.razor`: Obsahuje společné Razor direktivy, které se mají zahrnout do komponent aplikace (), jako jsou například `.razor` [`@using`](xref:mvc/views/razor#using) direktivy pro obory názvů.

* `Data` Folder ( Blazor Server ): obsahuje `WeatherForecast` třídu a implementaci rozhraní `WeatherForecastService` , které poskytuje ukázková data o počasí `FetchData` součásti aplikace.

* `wwwroot`: [Kořenová složka webu](xref:fundamentals/index#web-root) pro aplikaci, která obsahuje veřejné statické prostředky aplikace.

* `appsettings.json`: Uchovává [nastavení konfigurace](xref:blazor/fundamentals/configuration) pro aplikaci. V Blazor WebAssembly aplikaci je soubor nastavení aplikace umístěný ve `wwwroot` složce. V Blazor Server aplikaci je soubor nastavení aplikace umístěný v kořenovém adresáři projektu.
